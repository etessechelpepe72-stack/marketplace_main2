# Documentación del Proyecto — marketplace_main  
## Actualizaciones del Parcial 2

Este documento detalla todas las actualizaciones realizadas en el proyecto **marketplace_main** durante el segundo parcial. Cada sección incluye explicación y código separado utilizando bloques Markdown.

---

---

# 1. Actualización del archivo forms.py  
🔹 **LoginForm**

Formulario que permite que un usuario existente inicie sesión ingresando su nombre de usuario y contraseña.

```python
from django import forms
from django.contrib.auth.models import User
from .models import Item

class LoginForm(forms.Form):
    username = forms.CharField(max_length=150)
    password = forms.CharField(widget=forms.PasswordInput)

class SignupForm(forms.ModelForm):
    password = forms.CharField(widget=forms.PasswordInput)

    class Meta:
        model = User
        fields = ['username', 'email', 'password']

class NewItemForm(forms.ModelForm):
    class Meta:
        model = Item
        fields = ['name', 'description', 'price', 'image']
```

---

# 2. Actualización del archivo views.py  
🔹 **Vista login()**  
Procesa el inicio de sesión mediante LoginForm. Si las credenciales son correctas → redirige al home.

🔹 **Vista logout_user()**  
Cierra la sesión activa del usuario.

🔹 **Vista detail()**  
Muestra la información de un artículo individual.

🔹 **Vista add_item()**  
Permite agregar artículos (solo usuarios autenticados). Usa NewItemForm.

```python
from django.shortcuts import render, redirect
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required
from .forms import LoginForm, SignupForm, NewItemForm
from .models import Item

def login(request):
    form = LoginForm(request.POST or None)
    if request.method == 'POST' and form.is_valid():
        user = authenticate(
            username=form.cleaned_data['username'],
            password=form.cleaned_data['password']
        )
        if user:
            login(request, user)
            return redirect('store:home')
    return render(request, 'store/login.html', {'form': form})

def logout_user(request):
    logout(request)
    return redirect('store:home')

def detail(request, pk):
    item = Item.objects.get(id=pk)
    return render(request, 'store/item.html', {'item': item})

@login_required
def add_item(request):
    form = NewItemForm(request.POST or None, request.FILES or None)
    if request.method == 'POST' and form.is_valid():
        form.save()
        return redirect('store:home')
    return render(request, 'store/form.html', {'form': form})
```

---

# 3. Actualización del archivo urls.py  
Define todas las rutas principales para login, logout, detalles y agregar artículos.

```python
from django.urls import path
from . import views

app_name = 'store'

urlpatterns = [
    path('login/', views.login, name='login'),
    path('logout/', views.logout_user, name='logout'),
    path('item/<int:pk>/', views.detail, name='detail'),
    path('add/', views.add_item, name='add_item'),
]
```

---

# 4. Plantilla item.html  
Muestra los datos de un artículo individual (nombre, descripción, precio e imagen).

```html
<h1>{{ item.name }}</h1>
<p>{{ item.description }}</p>
<p>Precio: ${{ item.price }}</p>
<img src="{{ item.image.url }}" width="200">
```

---

# 5. Plantilla login.html  
Formulario visual para iniciar sesión.

```html
<h2>Iniciar Sesión</h2>
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Entrar</button>
</form>
```

---

# 6. Plantilla signup.html  
Formulario para crear una nueva cuenta.

```html
<h2>Crear Cuenta</h2>
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Registrarse</button>
</form>
```

---

# 7. Barra de navegación (base.html)  
La barra cambia dependiendo si el usuario ha iniciado sesión o no.

```html
<nav>
    <a href="/">Inicio</a>

    {% if user.is_authenticated %}
        <a href="/add/">Agregar Artículo</a>
        <a href="/logout/">Cerrar Sesión</a>
    {% else %}
        <a href="/login/">Iniciar Sesión</a>
        <a href="/signup/">Registrarse</a>
    {% endif %}
</nav>
```

---

# 8. Plantilla para agregar artículos (form.html)  
Permite subir un nuevo artículo con imagen.

```html
<h2>Nuevo Artículo</h2>
<form method="POST" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Guardar</button>
</form>
```

---
