# Documentación Django - Aplicación Marketplace

## Tabla de Contenidos

- [2. Introducción: ¿Por qué utilizar Django?](#introduccion)
- [3. Explicación de los comandos vistos en clase](#comandos)
- [4. Arquitectura MVT de Django](#arquitectura-mvt)
- [5. Explicación de los archivos principales](#archivos-principales)
- [6. Ejecución del proyecto](#ejecucion)
- [7. Actualización del archivo forms.py](#forms)
  - [Login Form](#login-form)
  - [SignupForm](#signup-form)
  - [ItemForm](#item-form)
- [8. Actualizaciones del archivo views.py](#views)
  - [login()](#login-function)
  - [logout_user()](#logout-function)
  - [detail()](#detail-function)
  - [add_item()](#add-item-function)
- [9. Actualizaciones del archivo urls.py](#urls)
- [10. Explicación del decorador @login_required](#login-required)
- [11. Actualizaciones en store/templates](#templates)
  - [item.html](#item-html)
  - [login.html](#login-html)
  - [signup.html](#signup-html)
  - [navigation.html](#navigation-html)
  - [form.html](#form-html)
- [12. Conclusiones](#conclusiones)

---

## <a id="introduccion"></a>2. Introducción: ¿Por qué utilizar Django para desarrollar aplicaciones web?

Django es un framework de desarrollo web basado en Python que nos permite crear aplicaciones de manera rápida, segura y eficiente. Su mayor ventaja es que ya incluye muchas herramientas listas para usar, como manejo de usuarios, panel de administración, conexión con bases de datos y control de URLs.

Además, Django sigue el principio "No te repitas" (DRY - Don't Repeat Yourself), lo que ayuda a mantener un código más limpio y fácil de mantener. Gracias a su estructura clara y su arquitectura MVT (Model-View-Template), es ideal tanto para principiantes como para proyectos profesionales de gran tamaño.

---

## <a id="comandos"></a>3. Explicación de los comandos vistos en clase

### 1. venv\Scripts\activate

Este comando sirve para activar el entorno virtual en Python. El entorno virtual es un espacio aislado donde instalamos Django y otras dependencias sin afectar el sistema principal. Cuando lo activamos, todos los paquetes se instalan solo dentro de ese entorno.

```bash
venv\Scripts\activate
```

### 2. pip install django

Este comando instala Django en nuestro entorno virtual usando el administrador de paquetes de Python (pip). Es necesario hacerlo antes de crear cualquier proyecto o aplicación.

```bash
pip install django
```

### 3. django-admin startproject marketplace_main

Este comando crea un nuevo proyecto Django llamado marketplace_main. Dentro de la carpeta generada se incluyen archivos base como manage.py, settings.py y urls.py, que son la base de la aplicación.

```bash
django-admin startproject marketplace_main
```

---

## <a id="arquitectura-mvt"></a>4. Arquitectura MVT de Django

### Explicación

- **Model (Modelo)**: Define la estructura de la base de datos. Cada modelo representa una tabla.

- **View (Vista)**: Contiene la lógica del programa. Decide qué datos mostrar y qué plantilla usar.

- **Template (Plantilla)**: Es la parte visual, es decir, el HTML que verá el usuario.

- **Web Browser (Navegador)**: El usuario final que interactúa con la aplicación.

El flujo es así: el usuario hace una solicitud → Django la envía a la vista → la vista consulta el modelo → envía los datos al template → y finalmente muestra la respuesta en el navegador.

---

## <a id="archivos-principales"></a>5. Explicación de los archivos principales

### settings.py

El archivo settings.py sirve para configurar todo el proyecto Django. Aquí se definen las aplicaciones instaladas, la configuración de la base de datos, archivos estáticos, y más.

```python
# marketplace_main/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'store',  # Nuestra aplicación
]
```

### urls.py

Define las rutas del proyecto, es decir, qué vista se ejecuta según la URL que el usuario visite.

```python
# marketplace_main/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('store.urls')),  # Enlaza las URLs de la app "store"
]
```

### models.py

Aquí se crean las tablas que estarán en la base de datos mediante clases de Python.

```python
# store/models.py
from django.db import models

class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=6, decimal_places=2)
    descripción = models.TextField()

    def __str__(self):
        return self.nombre
```

### views.py

Define la lógica que se ejecuta cuando un usuario entra a una página.

```python
# store/views.py
from django.shortcuts import render
from .models import Producto

def lista_productos(request):
    productos = Producto.objects.all()
    return render(request, 'store/lista_productos.html', {'productos': productos})
```

### Carpeta templates/store

Aquí van los archivos HTML que muestran la información al usuario. Cada plantilla se asocia a una vista.

```html
<!-- templates/store/lista_productos.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Lista de productos</title>
</head>
<body>
    <h1>Productos disponibles</h1>
    <ul>
        {% for producto in productos %}
            <li>{{ producto.nombre }} - ${{ producto.precio }}</li>
        {% endfor %}
    </ul>
</body>
</html>
```

---

## <a id="ejecucion"></a>6. Ejecución del proyecto

Para ejecutar el proyecto Django se utiliza el siguiente comando en la terminal:

```bash
python manage.py runserver
```

Después de eso, abrimos en el navegador la dirección:

```
http://127.0.0.1:8000/
```

Si todo está correcto, se mostrará la página principal de Django o nuestra lista de productos (dependiendo de la configuración).

---

## <a id="forms"></a>7. Actualización del archivo forms.py

### <a id="login-form"></a>Login Form

Permite que un usuario existente ingrese a la plataforma proporcionando su nombre de usuario y contraseña.

#### Código:

```python
from django import forms
from django.contrib.auth.forms import UserCreationForm, AuthenticationForm
from django.contrib.auth.models import User
from .models import Item

class LoginForm(AuthenticationForm):
    username = forms.CharField(widget=forms.TextInput(
        attrs={
            'placeholder': 'Tu usuario',
            'class': 'form-control'
        }
    ))

    password = forms.CharField(widget=forms.PasswordInput(
        attrs={
            'placeholder': 'password',
            'class': 'form-control'
        }
    ))
```

### <a id="signup-form"></a>SignupForm

Este formulario permite registrar nuevos usuarios en la plataforma.

#### Código:

```python
class SignupForm(UserCreationForm):
    class Meta:
        model = User
        fields = ('username', 'email', 'password1', 'password2')

    username = forms.CharField(widget=forms.TextInput(
        attrs={
            'placeholder': 'Tu Usuario',
            'class': 'form-control'
        }
    ))

    email = forms.CharField(widget=forms.EmailInput(
        attrs={
            'placeholder': 'Tu Email',
            'class': 'form-control'
        }
    ))

    password1 = forms.CharField(widget=forms.PasswordInput(
        attrs={
            'placeholder': 'Password',
            'class': 'form-control'
        }
    ))

    password2 = forms.CharField(widget=forms.PasswordInput(
        attrs={
            'placeholder': 'Repite Password',
            'class': 'form-control'
        }
    ))
```

### <a id="item-form"></a>ItemForm

Formulario para que los usuarios registrados puedan agregar nuevos artículos a la tienda.

#### Código:

```python
class NewItemForm(forms.ModelForm):
    class Meta:
        model = Item
        fields = ('category', 'name', 'description', 'price', 'image',)

        widgets = {
            'category': forms.Select(attrs={
                'class': 'form-select'
            }),
            'name': forms.TextInput(attrs={
                'class': 'form-control'
            }),
            'description': forms.Textarea(attrs={
                'class': 'form-control',
                'style': 'height: 100px'
            }),
            'price': forms.TextInput(attrs={
                'class': 'form-control',
            }),
            'image': forms.FileInput(attrs={
                'class': 'form-control',
            }),
        }
```

---

## <a id="views"></a>8. Actualizaciones del archivo views.py

### <a id="login-function"></a>login()

Se encarga de autenticar a un usuario usando LoginForm y signup().

#### Código:

```python
def register(request):
    if request.method == 'POST':
        form = SignupForm(request.POST)

        if form.is_valid():
            form.save()
            return redirect('login')
    else:
        form = SignupForm()

    context = {
        'form': form
    }
    return render(request, 'store/signup.html', context)
```

### <a id="logout-function"></a>logout_user()

Cierra la sesión del usuario actual.

#### Código:

```python
def logout_user(request):
    logout(request)
    return redirect('home')
```

### <a id="detail-function"></a>detail()

Muestra la información detallada de un artículo seleccionado.

#### Código:

```python
def detail(request, pk):
    item = get_object_or_404(Item, pk=pk)
    related_items = Item.objects.filter(category=item.category, is_sold=False).exclude(pk=pk)[0:3]
    context={
        'item': item,
        'related_items': related_items
    }
    return render(request, 'store/item.html', context)
```

### <a id="add-item-function"></a>add_item()

Función que permite agregar nuevos artículos, solo accesible para usuarios autenticados.

#### Código:

```python
@login_required
def add_item(request):
    if request.method == 'POST':
        form = NewItemForm(request.POST, request.FILES)

        if form.is_valid():
            item = form.save(commit=False)
            item.created_by = request.user
            item.save()

            return redirect('detail', pk=item.id)
    else:
        form = NewItemForm()
        context = {
            'form': form,
            'title': 'New Item'
        }

    return render(request, 'store/form.html', context)
```

---

## <a id="urls"></a>9. Actualizaciones del archivo urls.py

```python
from django.urls import path
from django.contrib.auth import views as auth_views
from .views import contact, detail, register, logout_user, add_item
from .forms import LoginForm

urlpatterns = [
    path('contact/', contact, name='contact'),
    path('register/', register, name='register'),
    path('login/', auth_views.LoginView.as_view(template_name='store/login.html', authentication_form=LoginForm), name='login'),
    path('logout/', logout_user, name='logout'),
    path('add_item/', add_item, name='add_item'),
    path('detail/<int:pk>/', detail, name='detail'),
]
```

---

## <a id="login-required"></a>10. Explicación del decorador @login_required

Este decorador evita que usuarios no autenticados entren a funciones específicas. Si un usuario intenta acceder a una vista protegida, Django lo redirige automáticamente a la página de inicio de sesión.

Se aplica antes de una función en views.py:

```python
@login_required
def add_item(request):
    ...
```

---

## <a id="templates"></a>11. Actualizaciones en store/templates

### <a id="item-html"></a>item.html

Template que muestra los detalles de un producto específico con imagen, precio y vendedor.

### <a id="login-html"></a>login.html

Página de inicio de sesión con diseño moderno y animaciones, incluye validación de errores y enlace a registro.

### <a id="signup-html"></a>signup.html

Formulario de registro con campos para usuario, email y contraseñas, con validación y diseño atractivo.

### <a id="navigation-html"></a>navigation.html

Barra de navegación que muestra opciones diferentes según si el usuario está autenticado o no (Login/Register vs Logout/Add Item).

### <a id="form-html"></a>form.html

Template genérico reutilizable para mostrar cualquier formulario Django con manejo de errores.

---

## <a id="conclusiones"></a>12. Conclusiones

Django es una buena herramienta para desarrollar aplicaciones web modernas, ya que simplifica el trabajo de programación con una estructura clara y organizada. Permite crear desde sitios pequeños hasta proyectos profesionales en poco tiempo.

Gracias a su arquitectura MVT, es muy fácil mantener el código separado y entendible: los modelos manejan los datos, las vistas la lógica, y las plantillas la parte visual. Esto ayuda a que los proyectos sean más escalables y fáciles de actualizar.

Aprender Django no solo mejora nuestras habilidades como desarrolladores, sino que también nos da una base sólida para construir aplicaciones seguras, rápidas y bien estructuradas, aprovechando toda la potencia de Python, Django, GitHub y todo lo visto anteriormente.

Tuvimos múltiples experiencias tanto individuales como en equipo a lo largo de estas prácticas, lo cual apoyarnos entre nosotros nos ayudó a fortalecer el conocimiento mutuamente entre el grupo. Tomando en cuenta códigos, resoluciones de problemas, logramos completar de manera eficiente, segura y más que nada logrando aprender no solo programación sino completarnos y apoyarnos como equipo.