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

![Arquitectura MVT](/img/img1.png)

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
![Arquitectura MVT](/img/img2.png)

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
![Arquitectura MVT](/img/img3.png)

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
![Arquitectura MVT](/img/img4.png)

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
![Arquitectura MVT](/img/img5.png)
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
![Arquitectura MVT](/img/img6.png)
![Arquitectura MVT](/img/img7.png)

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
![Arquitectura MVT](/img/img8.png)

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
```python
{% extends 'store/base.html' %}


{% block title %} {{ title }} {% endblock %}


{% block content%}
    <h4 class="mb-4 mt-4">{{ title }}</h4>
    <hr>
    <form action="." method="POST" enctype="multipart/form-data">
        {% csrf_token %}
        <div>
       
            {{ form.as_p }}
        </div>


        {% if form.errors or form.non_field_errors %}
            <div class="mb-4 p-6 bg-danger">
                {% for field in form %}
                    {{ field.errors }}
                {% endfor %}


                {{ form.non_field_errors }}
            </div>
        {% endif %}


        <button class="btn btn-primary mb-6">Register</button>
    </form>
{% endblock%}

```
![Arquitectura MVT](/img/img9.png)


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
```python
{% extends 'store/base.html' %}


{% block title %}Registro| {% endblock %}


{% block content %}


<style>
    .epic-login-container {
        min-height: 80vh;
        position: relative;
        overflow: hidden;
        background: #ffffff;
    }


    /* Partículas flotantes */
    .particle {
        position: absolute;
        border-radius: 50%;
        pointer-events: none;
        opacity: 0.3;
        animation: float 15s infinite;
    }


    @keyframes float {
        0%, 100% { transform: translateY(0) translateX(0) rotate(0deg); }
        25% { transform: translateY(-100px) translateX(50px) rotate(90deg); }
        50% { transform: translateY(-200px) translateX(-50px) rotate(180deg); }
        75% { transform: translateY(-100px) translateX(-100px) rotate(270deg); }
    }


    .login-card {
        background: rgba(255, 255, 255, 0.95);
        backdrop-filter: blur(20px);
        border-radius: 30px;
        box-shadow: 0 20px 80px rgba(0, 0, 0, 0.3);
        border: 2px solid rgba(255, 255, 255, 0.3);
        animation: cardEntrance 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
        position: relative;
        overflow: hidden;
    }


    @keyframes cardEntrance {
        from {
            opacity: 0;
            transform: translateY(50px) scale(0.9);
        }
        to {
            opacity: 1;
            transform: translateY(0) scale(1);
        }
    }


    .login-card::before {
        content: '';
        position: absolute;
        top: -50%;
        left: -50%;
        width: 200%;
        height: 200%;
        background: linear-gradient(45deg, transparent, rgba(255, 255, 255, 0.1), transparent);
        animation: shine 3s infinite;
    }


    @keyframes shine {
        0% { transform: rotate(0deg); }
        100% { transform: rotate(360deg); }
    }


    .login-title {
        font-size: 2.5rem;
        font-weight: 800;
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        background-clip: text;
        animation: titlePulse 2s ease-in-out infinite;
        text-shadow: 0 0 30px rgba(102, 126, 234, 0.3);
    }


    @keyframes titlePulse {
        0%, 100% { transform: scale(1); }
        50% { transform: scale(1.05); }
    }


    .form-floating {
        position: relative;
        margin-bottom: 2rem;
    }


    .form-floating input {
        border: 3px solid #e0e0e0;
        border-radius: 15px;
        padding: 1rem;
        font-size: 1rem;
        transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        background: white;
    }


    .form-floating input:focus {
        outline: none;
        border-color: #667eea;
        box-shadow: 0 0 0 4px rgba(102, 126, 234, 0.2), 0 8px 25px rgba(102, 126, 234, 0.3);
        transform: translateY(-2px);
    }


    .form-floating h6 {
        font-weight: 700;
        color: #333;
        margin-bottom: 0.5rem;
        font-size: 0.95rem;
        text-transform: uppercase;
        letter-spacing: 1px;
    }


    .epic-btn {
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        border: none;
        border-radius: 50px;
        padding: 15px 50px;
        font-size: 1.1rem;
        font-weight: 700;
        color: white;
        cursor: pointer;
        transition: all 0.3s ease;
        position: relative;
        overflow: hidden;
        box-shadow: 0 10px 30px rgba(102, 126, 234, 0.4);
        text-transform: uppercase;
        letter-spacing: 2px;
    }


    .epic-btn::before {
        content: '';
        position: absolute;
        top: 50%;
        left: 50%;
        width: 0;
        height: 0;
        border-radius: 50%;
        background: rgba(255, 255, 255, 0.3);
        transform: translate(-50%, -50%);
        transition: width 0.6s, height 0.6s;
    }


    .epic-btn:hover::before {
        width: 300px;
        height: 300px;
    }


    .epic-btn:hover {
        transform: translateY(-3px) scale(1.05);
        box-shadow: 0 15px 40px rgba(102, 126, 234, 0.6);
    }


    .epic-btn:active {
        transform: translateY(0) scale(0.98);
    }


    .register-link {
        color: #667eea;
        text-decoration: none;
        font-weight: 600;
        position: relative;
        transition: all 0.3s ease;
    }


    .register-link::after {
        content: '';
        position: absolute;
        bottom: -2px;
        left: 0;
        width: 0;
        height: 2px;
        background: linear-gradient(90deg, #667eea, #764ba2);
        transition: width 0.3s ease;
    }


    .register-link:hover {
        color: #764ba2;
        transform: translateX(5px);
    }


    .register-link:hover::after {
        width: 100%;
    }


    .error-box {
        background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
        border-radius: 15px;
        animation: errorShake 0.5s ease;
        box-shadow: 0 10px 30px rgba(255, 107, 107, 0.4);
    }


    @keyframes errorShake {
        0%, 100% { transform: translateX(0); }
        25% { transform: translateX(-10px); }
        75% { transform: translateX(10px); }
    }


    /* Efectos de hover en inputs */
    .form-floating input:not(:placeholder-shown) {
        border-color: #667eea;
    }


    /* Decoración adicional */
    .login-icon {
        width: 80px;
        height: 80px;
        margin: 0 auto 2rem;
        background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
        border-radius: 50%;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 2.5rem;
        color: white;
        box-shadow: 0 10px 30px rgba(102, 126, 234, 0.5);
        animation: iconPulse 2s ease-in-out infinite;
    }


    @keyframes iconPulse {
        0%, 100% { transform: scale(1) rotate(0deg); }
        50% { transform: scale(1.1) rotate(5deg); }
    }
</style>


<div class="epic-login-container row p-4 d-flex justify-content-center align-items-center">
    <!-- Partículas flotantes -->
    <div class="particle" style="width: 100px; height: 100px; background: rgba(102, 126, 234, 0.4); top: 10%; left: 10%; animation-delay: 0s;"></div>
    <div class="particle" style="width: 150px; height: 150px; background: rgba(118, 75, 162, 0.3); top: 50%; left: 80%; animation-delay: 2s;"></div>
    <div class="particle" style="width: 80px; height: 80px; background: rgba(102, 126, 234, 0.5); top: 70%; left: 20%; animation-delay: 4s;"></div>
    <div class="particle" style="width: 120px; height: 120px; background: rgba(118, 75, 162, 0.35); top: 30%; left: 70%; animation-delay: 1s;"></div>


    <div class="col-lg-6 col-md-8 col-sm-10">
        <div class="login-card p-5">
            <div class="login-icon">
                ✨
            </div>
            <h4 class="login-title mb-4 text-center">Crear Cuenta</h4>
            <hr style="border: 2px solid #667eea; width: 60%; margin: 0 auto 2rem;">
           
            <form action="." method="POST">
                {% csrf_token %}
                <div class="form-floating mb-3">
                    <h6>Usuario:</h6>
                    {{form.username}}
                </div>
                <div class="form-floating mb-3">
                    <h6>Email:</h6>
                    {{form.email}}
                </div>
                <div class="form-floating mb-3">
                    <h6>Contraseña:</h6>
                    {{form.password1}}
                </div>
                <div class="form-floating mb-3">
                    <h6>Repite Contraseña:</h6>
                    {{form.password2}}
                </div>


                {% if form.errors or form.non_field_errors %}
                <div class="error-box mb-4 p-3 text-white rounded">
                    {% for field in form %}
                    <h5 class="text-white">
                        {{field.errors}}
                    </h5>
                    {% endfor %}
                    {{ form.non_field_errors }}
                </div>
                {% endif %}
               
                <div class="d-flex justify-content-center align-items-center mb-4">
                    <button class="epic-btn" type="submit">
                        <span style="position: relative; z-index: 1;">Registrarse</span>
                    </button>
                </div>
               
                <div class="d-flex justify-content-center align-items-center">
                    <a href="{% url 'login' %}" class="register-link">
                        ¿Ya tienes cuenta? ¡Accesa aquí! →
                    </a>
                </div>
            </form>
        </div>
    </div>
</div>


<script>
    // Efecto de tecleo en inputs
    document.querySelectorAll('.form-floating input').forEach(input => {
        input.addEventListener('input', function() {
            this.style.animation = 'none';
            setTimeout(() => {
                this.style.animation = '';
            }, 10);
        });
    });


    // Efecto de ripple en el botón
    document.querySelector('.epic-btn').addEventListener('click', function(e) {
        if (!this.querySelector('.ripple')) {
            const ripple = document.createElement('span');
            ripple.className = 'ripple';
            ripple.style.cssText = `
                position: absolute;
                border-radius: 50%;
                background: rgba(255,255,255,0.6);
                width: 100px;
                height: 100px;
                animation: rippleEffect 0.6s ease-out;
                pointer-events: none;
            `;
           
            const rect = this.getBoundingClientRect();
            const x = e.clientX - rect.left - 50;
            const y = e.clientY - rect.top - 50;
           
            ripple.style.left = x + 'px';
            ripple.style.top = y + 'px';
           
            this.appendChild(ripple);
           
            setTimeout(() => ripple.remove(), 600);
        }
    });


    // Animación de entrada de campos
    const formInputs = document.querySelectorAll('.form-floating');
    formInputs.forEach((input, index) => {
        input.style.animation = `cardEntrance 0.6s ease ${index * 0.1}s both`;
    });
</script>


<style>
    @keyframes rippleEffect {
        0% {
            transform: scale(0);
            opacity: 1;
        }
        100% {
            transform: scale(4);
            opacity: 0;
        }
    }
</style>


{% endblock %}


```
![Arquitectura MVT](/img/img10.png)

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
```python
{% extends 'store/base.html' %}


{% block title %}{{item.name}} | {% endblock %}


{% block content %}
<div class="container mt-4 mb-4">
    <div class="row">
        <div class="col-4">
            <img src="{{ item.image.url }}" alt=""
            class="rounded" width="100%">
        </div>
        <div class="col-8 p-4 rounded bg-light">
            <h1 class="mb-4 text-center">
                {{ item.name }}
            </h1>
            <hr>
            <h4><strong>Precio ${{ item.price }}</strong></h4>
            <h4><strong>Vendedor {{ item.created_by.username }}</strong></h4>
           
            {% if item.description %}
                <p>{{ item.description }}</p>
            {% endif %}


            <a href="" class="btn btn-dark">Contacta a el vendedor</a>
           
        </div>
    </div>
</div>
{% endblock %}

```
![Arquitectura MVT](/img/img11.png)

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
![Arquitectura MVT](/img/img12.png)

### <a id="logout-function"></a>logout_user()

Cierra la sesión del usuario actual.

#### Código:

```python
def logout_user(request):
    logout(request)
    return redirect('home')
```
![Arquitectura MVT](/img/img13.png)


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
![Arquitectura MVT](/img/img14.png)
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
