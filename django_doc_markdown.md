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
![][image1]
## <a id="comandos"></a>3. Explicación de los comandos vistos en clase
![][image1]
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

---
[image1]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAbgAAAFJCAIAAABmZq9YAAArXUlEQVR4Xu2dCXgUVbq/m7CFQGKAgOwQ9iXIvkYIyL4jhC2CEEQRZAmgIiASQRZZwg4RBsFRGHFGNq8CKuQqjIyKXFFR/8Mfvc64DCCighAikPt1PjgW54R0qrq6lu7f+/yePFWnqqtPn1Pfm+pOp9uTDQAAIE88cgMAAIBbgSgBAMAHECUAAPgAogQAAB9AlAAA4AOIEgAAfABRAgCADyBKAADwAUQJAAA+gCgBAMAHECUAAPgAogQAAB9AlAAA4AOIEgAAfABRAgCADyBKAADwAUQJAAA+gCgBAMAHECUAAPgAogQAAB9AlAAA4AOIEgAAfABRAgCADyBKAADwAUQJAAA+gCgBAMAHECUAAPgAogQAAB9AlAAA4AOIEgAAfABRAgCADyBKAADwgT2i/Oijjx555JHWrVt7dFKnTp2kpKTVq1fLRwQAgIBhkSiPHDkyaNAgrfIiSkTXbda5e9KMR1ceWrj92/SD2Xkkbc/5mc8dG/XEn5smJMaUj9Uep1WrVi+++KJ8fwAAYB6BFeXKlStLlCghzNh7ZKoqQX8y76VTVWo3E9IcO3bshQsX5E4AAIB/mC/Ks2fPCnOtfP2iareApk/yXL7r3r17yz0DAABDmCnKtLQ0ltTwaRtVhVmZmekfRUaXoZ507NhR7iUAAOjEHFGePn2aFTl+/muqtuzKsl3nioYX917Yrlwp9xgAAPKNv6Ls1asXmajEHTGqp5yTVl2Gs8fl3gMAQD7wyx1PPPEE2Wfh9n+rbnJgIiJLxsTEyI8BAAB8YVyUzZp5/9ys+sjJKRIegetKAIBeDFqDdNN75BzVRM7Pgpf/RZ3ftGmT/JAAAOA2GBFlenp6g5Y9VAe5JSnLDuC6EgCQf4z4wnXPuNXUbtzhzTfflB8YAEHBeZAb8jDpQbcoyZIdB0xS1eO64KISBCW//fYbv8cDSMgjpQfdN/a4/3KSQw/k4MGD8sMDwOWwKFO2ZiMi1Zv2hSgNhh7I3Llz5YcHgMuBKNVAlMZTuGixwYMHyw8PAJcDUaqBKI2HHsigQYPkhweAy4Eo1dgjyqYJiap3XBd6ILiiBMEHRKnGHlEGwUVl58HTIEoQlECUauwRZfM+T7Ttnqzaxy1ZutP7iZnlaraGKEHwAVGqsUeUdMf086E5f1Ud5IpQ52u1TCxXsxVeowTBB0SpxjZRpuS40uO25+ARJaKpzw8/d476HyKizMzMXLBgAU8WyA9xcXFXrlyRx9E9QJRq7BQlpVK9BI97XFkhNk7b+aAX5cWLF7X1X7hIeHzPB2as/1AdGWTaind7DJ+lHS7i8OHD8pi6AYhSjc2ipJQsX5ta6jXrop58zsnI6Vv41Nf2PIhFqVVkZHQZsoA6JsjtsvL1P0avcuXK169fl8fX2UCUauwXJaf/9H18Yk1e8pZ65tmV5a/9Eh1TkXrVqMsjap+D8o85P/30E09E4aLFFv/tP+qYILrCg0lkZWXJY+1UIEo1ThElp0bz/nxWzdrwP+o5Z2VW77scW68V9aRoRPRD68+oXU0JRlEmJyfz+KcsO6COCWIsA8cu4VF9+eWX5RF3JBClGmeJktNn6i4+sUqWqbTqjd/UMy+gGTR+Od970eIl1b5pE2Si3LfvxkX9M1u/UocF8TM8thUrVpTH3XlAlGqcKEptarYcyGcYUb1Bm6Qp6eop6E/mb/u6SbsB4i5Klq/d//G9ajdyTdC8Rnn16lV++LP/9Ik6RIhZGT5tA4+zPAEOA6JU43RRisQPWVA8uvwNn+VAgus/ZsHkxW+qZ+TtMn/b/w6ZuKp11/vpWlV7qLs6jxvx7GfqneadoBElD0K5KnXVEUPMzaTF+3m05TlwEhClGteIUpvhiz5tl7S0VMX6NzynkxKlKtKF6sBZB9Uj60pwiDIlJYXGJLZeK7WqkUCkaLESfB7KM+EYIEo1rhSlQxIEr1HGxMTQdMx76ZRaz0jgkjzjRRr2cePGyfPhDCBKNRCl8bhdlDt27OBLG7WSkUCneoM2NPI//fSTPCsOAKJUA1Eaj9tFyZZc8foFtYwRC8LjL8+KA4Ao1UCUxuNqUfK/b6vVi1iWJzced6YrIUo1EKXxuFqUNAstO9+nVi9iZUrc4X2NWJ4bu4Eo1UCUxuNeUeJy0jmhifjkk0/kGbIViFINRGk87hVlzhM+iNIR4f8Ek2fIViBKNRCl8bhUlJMmTaIpeGDWNrVoEVtC03Ho0CF5nuwDolQDURqPS0WJy0mnhWdEnif7gCjVQJTG415RNmzdSy1XxK5AlM4PRGk87hWlcz6lvEBYGGvCk3ORu2rvJf6yjZp3tWvQsju3hxUstOL1C9o9mSETV9NNaKtomfP8CbFcu3GH9JsOKliocPrNz6TolDiFfhaPKq12xq7wR6O/9dZb8lTZBESpBqI0HjeK8vz58x6HPe9ml0VGl9G2aDfd+9CztDzisU28yptSt3wR16qndreY8tVpefLiN3l19b7LvDU8IpJ+DktZp725WHBCqKvUn+rVq8uzZRMQpRqI0njcKEr+S45aqzZmwcv/YoWl7TlPq4te+e7Zv/3Am7g9V1HO3vSpJEqxiZfZj+KbPqV9lr/2Cy84JNw3ebZsAqJUA1EajytESaPdvn177aqQhXPCvSpbqRYt31mpttSuinLk9C28M2fIxFViE0l23LwbH/xMq9ExFXiflp3v40Zi6c4fxW0dEu6YmKZy5cppVy0moKKcsPmSmAhabdDhAV6esPkyrQ6d+wEtD3n6Hyk3v6V1SOqRB9f+UL1pH95ftBP3Lbzxr03qvZgeiNJ43CJKIiwsTKyWr1pfLVR7c+PEz1Fbn1FPS+2SKKemZcT3HKMVpdiTLjPrNu0kVsc+/eqwyWvVeyESxy3T3tz2cK94jjp37syr58+f/2MiLSSgokzRmE672qTHFF4tU7Wxtp1EKVbVm9dqmUgLtdsMle7C9ECUxuM0UZ45c2bTpk1zbkWcVcRTTz1FPxvf3V8tVHsj/ghTv3lXbTs3qleUaronzeCtM9M/EjdUd16265zY9OTGj9Xj2BXuUrdu3UT3iOnTp0uzqbJ27Vr5PPCbQIuyac+p/AB5NSqmqljtOnaz2I0bWZS9U16V2nn/YlFlaGHiC1ekuzA9EKXxOEqUZcp4z5j84Mx/8ea+9UmeqzbeTpT8mqa0My+37X7jW9LE1ibtB/LCxGdvfDVQy05J2pvbm/CISO6VYY4cOSKfE0YJtChTbsruofVn7l/yhVgdNPvd8BKlpX3urN6CF6R24o6y1elnt3F/Vo9veiBK43GIKMUXzBLx8fHSFYfYRLz99tv08642fdRCtT39HpjvUS4Audu3E6W0f9FiJWrExfPy+gPXaWtkybJia8mylbsNm87L/E6jkY9v1t7c3vBDmz/fOwiC/FxRii/OJDp16iSfHIawQJQFCxWhu6hUr0OpivVSbrqvRKlKfabuEvtwI19Rtuz/pNTOPUy4fyUtxFS5S70LcwNRGo9DRMknjdx6E95K15titUJsnFqoTojnVvFJ76MsWKgwr2rR7k9y1K7Wb9FNu0ppmuB9PavniNn0s+vQx6Wt9oYfDs+R0KWu1yjvueceusm2bdvkDfqxQJSTX7zGj/GhdadptVL9jryq3YdbxGuUUrvYmZcHPXVI2s3cQJTG4wRRfv755zSeW7dulTfchLbOnj1bu+pRLtwQ28PzIqaJ/54jVvMJv/wit+rHAlGm3BQcLz+y6aJ2VbuDEOWELZmtB6aKdrEzLw9f+Il0fHMDURqPE0SZkJCga/5mzpzpgSidF652ebb0Qwc5fPiw3KoTa0TZeczGWq0GiVW6R/H3btHiufWv3kPnvi/auYdDUt8TywENRGk8ThAlnyVy6+05d877Z1+1UBEbs+L1CzQpsbGx8mzph47TtGlTuVUn1ohSypg136mNzglEaTxOEGWBAgX0zh/tP/O5Y2q5Inal5/AnPSb9rzcdp0iRInKrTmwRpcMDURqPe0XZLGGQWq6IXSlcJFzvJN4OTw5yq04gSjUQpfG4V5QePPt2UkyxG8Pnw6pVq+QNeXLu3Lnt27eLVYhSDURpPC4V5ejRo+kmExa+rlYsYktoOj744AN5ngzx73//24B2+Sbh4eG8ClGqgSiNx6WivH7d+2ZsDy4qnRH+lEx5kvygSZMmdMAKFSrIG24Pnw9EvXr1siHK3AJRGo9LRUmkpqZClA4JTcTJkyflGfIPNp1hpkzxfraxesKHciBK43GvKLNzZqHzoKlq3SJWJqZ8rLHp8wm5sm3btlr95Z+FCxd6gqVIzYrrRSn++WnCZu/HRBN9puzkTbzaJnHu6JX/Swvla7VRb+5PXC3KFStWeHBRaWue2foVn6Ly3FgOd4P5/PPP8dRbjetFWb/dSJ5gWo6p3FAsp2hEqV1Vj2A4rhZl9s3yEF+ZgFgcHn95VuyAe1K3bl1e1StKvrmgaqPu3N7loee17eElSjfr9aj2hp0f/BO1V27QiX7WT0iWjlawUBHtau3Wg2k1rKD3X/6LRtxBy3Edx9Ay/6xQ525qGfHsZ2J/wX0Lj9OmAgVu+c6l0pXjJmy+pO1M3gkqUfKC+ARQXpVE2bLfLPUgxuJ2Ue7Zs4fHRK1hJNBpFN+PRv7XX3+VZ8UOTpw4of00Db2ijCrjfQGBb0LlRgsly9fhTdxeqGiEWBZHTnzyv2m5etM+tNzv0f/SbuLl+u1HpeR8bpB2U4q37lqleD8p3fsMkp5Q0vL4Td5/beKtWiFQihSLjB+ykBa6PHjj06doefJLN/6emf8PsgweUTbpPpl+Js7KEJu4XRIl/WJRD2IsbhclERUVRTdf/OpptZKRwGXykrdo2KdNmybPhzPwR5QPrv1BLKfcKsrarYeom4SqeHXSn3+n5bLVmqp7dnrgOV6d+EIW/WzcbZJHU853lK3OC5IoKZXqJaTcKkpxzPghC8RueSd4REnLw545qo6FJEqx1f8EgSiJBQsWeHK+HlatZyQQKVb8Do/mTYsOxB9RRkR7v+1HbOJ2FiVv4q0NO40Vy9o9C4eX0K4OSX2v16S/tuh74+PrqV18StD9i72fmyXaRbRC0HowV1HSpaX2tnkkqESZcuvj52VJlI26PKIexFiCQ5TZN1+iKlOxplrViLmZsPANHm15DpyEYVHeO30/XwwmzT/Gm7idRMlPyfnZNKVinXa8SRxEfD+79oYRUWUpYvXh586JK0fRyIxe8TU3CiHQk0vxammKRpQTtmSWqliPFia/eE1s9ZngFKV2GX/M8cm1azc+RXX62vfV2kbMSt/keR7vs8UC8gQ4DMOi5FVeHrnsn2K5YGHvP7MTRYpF8j61Ww/W3kTsKVrEap+pu2mVrjRpuepd3dolLRE3GbfB+yX1guGLPk1RhCAiREk2z3WHvON6UdZo5n1RnI/Jny8vfudwO4uSFsrGNlNv7k+CRpTM7t27ecRmpB9VixzxMzy2zZs3l8fdeZgiyvK124pluqIcufT/8TI/pXvk+RtviRcH4dXOYzbyqpAgr4o/v4j9xUUrXSHyJt6qipKev6fc+tS7brz3u4tJ32Ifn3G9KG1MkImS6NWrF59Mo2dtVUsdMZauQx7jUd2/f7884o7EFFGSmMRyrn/1pgsXWr73iTe1t9IeVmqhZe0H/XZ6YAN/jYTYM7pcrZTcRMnLub5Gmbz8lNgt70CUxhN8oiR++eUXPoeIOc+fUMse0RUxmNevX5fH2qnoFSV/ZyzfhN/VWLnBPbyJ28MKFkrRvK2S/2wtttJC8ejytNBr8t+0h21172ztZ55rb+g9Wo746MJTe5yUW59i3rfwuFim5+za3fgbHPP/HhiI0niCUpRMZmZmWM5XFTJ9k+epCkBuF373D1OlShUXKZLRK8pQCERpPEEsSkb8kUfQuHHjt99+m7ee+TX7u/O+8/XZ7E//nf3hV3/k/VPZ//Vx9u5jueTPh2XpuCVjn3618d39peE6fvz4rSPqDiBKNRCl8QS9KBm6IFq0aNGtBgB5kZCQIA+iq4Ao1UCUxhMionQso0aNSktLk1uB30CUaiBK44Eo7YUv306dOiVvAP4BUaqBKI0HorSR+vXrsyhD8+EHFIhSDURpPBClXURERAhLEo0aNZL3AH4AUaqBKI0HorSF9evXay3JfPzxx/J+wCgQpRqI0nggSlv4/vvvb5Wkl99//13eDxgFolQDURoPRGkvrEi5FfgNRKkGojQeiNJeIMoAAVGqgSiNB6K0F4gyQECUaiBK44Eo7QWiDBAQpRqI0nggSnuBKAMERKkGojQeiNJeIMoAAVGqgSiNB6K0F4gyQECUaiBK44Eo7QWiDBAQpRqI0nggSnuBKAMERKkGojQeiNJeIMoAAVGqgSiNB6K0F4gyQECUaiBK44Eo7QWiDBAQpRqI0nggSnuBKAMERKkGojQeiNJeIMoAAVGqgSiNB6K0F4gyQECUaiBK44Eo7QWiDBAQpRqI0nggSnuBKAMERKkGojQeiNJeIMoAAVGqgSiNB6K0F4gyQECUaiBK44Eo7QWiDBAQpRqI0nggSnuBKAMERKkGojQeiNJeIMoAAVGqgSiNJ6Zyw0GDBskPz1ogSrkV+A1EqQaiNJ6oMtXGjx8vPzxrgSjlVuA3EKUae0Q5bsN5tSuuCz2QI0eOyA/PWiBKuRX4DUSpxgZRlipVqmLd9mpXXBc/B84UIEq5FfgNixKoyCOlB903/vbbbz3u/32VvPxU69at5cdmORCl3Ar8JjMzs4MjiYuLi4qKklstRB4pPRg5U1944YXytduq9nFLhs370CElClHKrSBIGTp0KM/4zp075W1uwOCZSg+4cbdJqoOcn4fWnabOb968WX5IdgBRyq0gSOHpZrKysuTNjsfgmfrrr7/SAy4b20w1kZPT77HXqdvr1q2TH49NQJRyKwhGBg8erPGkK+fdrx7v37+fHnPy8lOqkhyYwkWLd+zYUX4MtgJRyq0g6Dh16pRWkYzt/+uhF3/P1IULF9LDLhYZo4rJOanffhR1sn79+nLv7QailFtB8OLqGTen340aNaIh6DhqrSope3P/ki95en7++We50w4AopRbQfDi6hk3s9+bN2/msWg/PE11lpUZknqkYOFw6kmPHj3kXjoJiFJuBcGLq2fc/H6fOXOGR4QYOPOAarHAZcya7/gt+B7HK5KBKOVWELy4esYD2O85c+bw0BCdx2xUvWZW+j76WqV6HcR9nT17Vu6KU4Eo5VYQvLh6xi3q93vvvTds2DDhMiK8RKmqDbu26Ddz8FOHH1zzvao/bR5+7tywZ452HbulZosBkaWraI/TunXr7du3y/fnEiBKuRUEL66ecXv6ffTo0fHjx7dq1Ur4Lp/UqVMnKSlp9erV8hHdCUQpt4LgxdUz7sR+b9u2rXHjxnJrMBKaopw/f754WYYW0tLS5D1AMAJRmklWVhYPqHvHNP+EoCh37twp5lfw3XffyfuBoIPnWm51CY7rt7Z+fvjhB3lzcBGCoiSqVaumnWXX/ZMGMAZPt9zqEpzVb37juhZ5j+AiNEWZfeuvQ3kbCFJcPd0O6vf333+vrR8mMTFR3i+ICFlRZt8sm4sXL8obQJDCMy63ugQn9tvVA6oLW0R59erVrKysy3azd+/eAwcOyK2WQ0Nx7do1eYxAAHB1XTux364eUF1YJsoZM2bwqIK8WbNmzZUrV+ThA2bAIyy3ugQn9tvVA6qLgIry/fffF/VP1G3WOXnGi+kHs5FcM3nJW5VrNdGO2Ny5c+UxBX7Aoyq3ugQn9tvVA6qLAInyyJEjotob391/1d5LqheQPFKtbgsxgD/++KM8vsAQPJ5yq0twYr9dPaC6CIQon376aR7AKrWaqgpA8p97H1zEIxkVFSWPMtCPq+vaif129YDqwlxR1qhRg4du+Wu/qGWPGMuc50/wqA4dOlQecaAHV9e1E/vt6gHVhYminDRpEo+bWuqI/+GxpfmSxx3kG1fXtRP77eoB1YVZomzRwvuaWsXqDdUKR8xKeEQkDfLs2bPl0Qf5w9V17cR+u3pAdWGKKHm4mt8zVK1txNwseuW70Dk5TcfVQ+fEfrt6QHXhvygTEhLoCG26jVKrGglEKsTGhc75aS6uHjcn9tvVA6oLP0W5Y8cOunntxh3UekYCl+JRpWnYf/nlF3k+QJ64uq6d2G9XD6gu/BHlo48+ygOlVjIS6HRKnEIjf+zYMXlWwO1xdV07sd+uHlBd+CNKWNLehM5ZahauHjEn9tvVA6oLw6IcMWIELGlv0vacD50T1RRcPVxO7LerB1QXhkVJt+qbPE+tXsTKVKx+l7HpC01cXddO7LerB1QXxkSZlJTkweWkM0IT8dprr8kzBHLD1XXtxH67ekB1YUCUv//+O92kcNFiatEi1ufBp17RO4Mhi6vr2on9dvWA6sKAKLt37043mbbiXbVoEVtC07F161Z5noCCq+vaif129YDqwoAoeXDUckXsSuicrn7i6oFyYr9dPaC6MCbKtt2T1XJF7Ar/D/jVq1flqQIa+JnQ+vXr5Q0uQV+VWgNEmQe0//xtX6vlakp45CWovUBYmNQ4ZOJqam/YupdoEQdZs/+KaNQuM50HTeXdokreyS1V6zRXe8KdEctxrXrecpQcasTF824S4lZVajejVX5zeLs+Y9W7MCWJ45bR8bdv3y5PFdDAUyO3ugcndt3tY5p/9Iry7NmzWhGYm8lL3uKD8/gTi189zS0jHtvELbxn6pYvyFy8XKz4Hbzp2b9+zy3NOgzmlnHzdqXf9Ajf9vHV79FCwUKFaXnuiydFu5reI+doN3luXkfzTe596FkSH+/ALWJn8RFKw1LWUWPK0rfFEaS7MDF08MjISHm2wE3OnDlDQzRo0CB5g3vQUaWWwee93BqM6BXl6NGjA1fwje++lxdumufGHZFAJVHO3vSpECVdD0r7J894kVdVUYqDkyXzFiVvGvjwUl4dPm2Dtp1EScsPzNomWrTHeer5z9T2iBLR2uObG74jebZADmvXrqXBqVu3rrzBVThxdkPntNMrStq5aLESaqGamxuC0ahHK8qR07eUrVRLbBKfpsNb+z0wXxwhD1Gue+tqHqKkJ8upL3yZ61ZuZFFqW4iVr18sXa6aaG/Z+T6xaenOH6XjmBu+FzFNYTmvVGjmLXTp16+fNDguxYkPIDhGNj8YEGXlWk3UQjU3PP6e3EQ5NS0jvucYrSjvrFS7bQ/vdS7vX7JMJXEEVZQL/vINLcSUr56e51Pvmg3vFgd59m8/aDdxY66inLDw9bCChdSdGW276eG74DmqUKECr54/f/7W2Qs5Zs6cyUNx8uRJeZvb0FGllqE97YIbA6Js0m6AWqjmhsffk5so1Z2jYyqIm7AlxaokSjrIM1u/EjfMQ5R9kudSmt8zVN2BW3IVpXocCg2X2OHJjR+rO5gSPr5QJBMfH58QekRFRWkH4cCBA/JJ7E50VKll8BDLrcGIAVHSM0q1UM2NOMtFiyrKtD3neYFfCqjRoC1tLRAWpj2CekWpjSrK+s27evcfnyZaeAe6DpVa8hZlpRqN6GeT9gN5deKz+3iHlp2SxD7mht8hBLRMmDDh+vXr8hnsWnRUqWXwQMutwYgBUTZs3UstVHMjznXRoopSLPMCf5TOyOlbtEfQK8r+YxbSz8JFwkUL73BH6fJSS96ibNf7IfpZsmxl0cJvbxr5+GbRYm64A4cOHRKd8eT8D3hG6PGf//xHPmuDAh1VahnNm3v/kHr06FF5Q9BhQJTlqtZTC9XEzP7TJ6LURWPBQoVFo3YrSY0W2vYYnZ7zvkjeuWbDu3kHuthcs/9KWMFCvFq3aSftHUVGlxGHYh5b/XdeSJqSzvuITWOe2k6rdZt15lXta5FiH8GczZ+LTY3v7t9zxGyP5t1LgQjfL8/R3/9+41HgNcpgQkeVWsacOXPoPOvVq5e8IegwIEqPcmmG2B6tKIkFCxbomlbgfBw6ndKZF6zoFSVXoFqoiL0JkdM1lHHo7I4cOZLOvGvXrskbggu9orxw4QJE6bQs23WOJiUuLk6eLRBE6KhSi6GTr1SpUnJrcKFXlNk5w/LoykNquSJ2hf+X/IMPPpCnCgQR+qrUStat8/6v7oABA+QNQYQxUTZo2V0tV8Su4Hl3KODoCa5cuXJwn4XGROnBs28nhaajSJEi8jyB4EJflVpPz543Pl/rueeek7e5HwOi3LhxI91kxKN/UisWsSU0HSdOnJDnCQQX+qrUFt555x12pSfn38KGDx8+e/bsOYFk165d33zzjdyPAGBAlNm4qHRSOvR/xMAMAtfhmjm+cOFCcnKyMKZlxMTEZGZmyr0xCWOi3LFjhweidEBW7b1EE3H69Gl5hkDQobtKQ4cNGzYIXQbovyyMiTI756KyWt0WaukiVobPDXluQDCCafbBkiVLuB6++OILeZvfGBZlRkYG3VB8MgVifcbN2wVRhg6Y5nzBJREfHy9v8A/Dosy+2aXFf/uPWsOIBYElQwrMdL64fv16IArDH1GePCl/+g5iWcpVrUcjn5WVJc8KCFIMVmkIcuLECY/Z71LyR5RE+/bt6eYTF+1VKxkJXNp0G0XD/sorr8jzAYIX41UagvDrlSZ+HKmfoiTeeOMNOkJkybJqPSOBCF/Fx8bGyjMBghq/qjQEoSJ55pln5Faj+C/K7JsvVhYNL65WNWJuugx+lIY6PDxcngMQ7PhbpaEG1Unx4sXlVqOYIsrsm67sMXyWWtuIWeGvuyhYsKA8+iAEMKFKQwpWktxqFLNESZw75/2wL2Lg2CVqkSP+ZPmen3lshw4dKo87CA3MqdLQwbGiJPbv38/d69D/EbXaEWOJrdeKR/Vf//qXPOIgZDCtSkMEJ4syO+dtTGE5X6RF9Lr/KbXskfwn9YUveSTNnSPgRnAG6MPcsjFdlIIWLVqIIuevgUXyGf4yMmbChAnyyIKQJCBVGsRw/citRgmcKBlR8EyNGjWWLl0qfb9o3rz5dsbuvRl/fe2PvLInY8PLGenbcsmyzRnzN2Q8k5PU9RlPrM6YmuaCTFl2sM+op8WXRzLVq1e/dOmSPKAgVAlglQYlXEVyq1ECLUrm448/1ioA5M3u3bvlEQQhT8CrNMjgWpJbjWKNKJ1JbGxsv3795FYAHEmIVqlhIEqz4JE8ePCgvAEA5xGiVWoYiNIUoqOjeSRD8+ED14HTVB/m1nZoilKM4eXLl2mhdOnS8h4AOIyQq1I/gSj9JDU1lR5yZGQkr27fvp1Wjx49euteADiL0KpS/4Eo/eTKlSsRERHZmpGkQZB3AsBhhFaV+g9ECUAIgirVB0QJQAiCKtUHRGkW5o4kAAEFZ6o+zC1viFJuBcCR4EzVh7nlHcqiBMBFoEr1AVECEIKgSvUBUZqFuSMJQEDBmaoPc8s7lEUJgItAleoDogQgBEGV6gOiNAtzRxKAgIIzVR/mljdEKbcC4EhwpurD3PIOZVEC4CJQpfqAKM3C3JEEIKDgTNWHueUNUcqtADgSnKn6MLe8Q1mUALgIVKk+IEoAQhBUaX7p0qVLQkICi5IWunXrdvXqVXmnfJOQgzgaIe8R7Jj7KweAgIIzNV+8/PLLXNhali5dKu+Xb+RjhZ4yQvNRA5eCMzW/rF+/Xuu1NWvWyHvooWbNmtqjVatWTd4DAOAYIEodaO0mb9OPxpMmHM11hOwDB24EZ6o+uLz9vJxkfv31Vz4aLcjbQgCIErgId5ypv/322zfffPPFF18cPnz4XVvZt2/fwYMH5VZroUE4fvw4DcjFixflkQIABADnivLKlStVq1bl6w6QN5UqVfrkk0/kEQQAmIQTRTl69GhJBPVbdOubPG/4tA0pyw5MTcsI8dAgjJ61lQakfvOu0kDR0F27dk0eUEfCHZZbAXAkDjpTV6xYIQqeLJB+MBvRlT7Jc8UA0mDK4wsAMIpTRNmuXTuu8FZdhqsKQPKf1Be+FLqURxkAYAj7a+nixYtc1VVqNVXLHjGWqnWa86g69g8+UDlwETafqYmJiVwwaqkj/ofH1pk+cmzHAFCx80wtV64clUr95l3VCkfMSp3GHT05fxaXRx8AkG9sE2WxYsX4mkKtbcTcJI5bRuNMAy7PAQAgf9gjyoYNG1LpTlq8X61qJBCZuGiv057qOq0/AOSBDWfqhg0bqEKSUtar9YwELgPGLqZh37hxozwfAABfWC3KrKwsPOO2K3WbdqKRpymQZwUAkCdWixKWtDc8/r///rs8MZbDPZFbAXAklp6p/JbJ2Zs+VQsYsSYz1n/oEEM5pBsA5AdLz1QqjKhS5dTqRaxMiTtiYCgAdGFdwTz88MMePOl2RmgiaDrkGbIWXFECF2HdmcqFoRYtYn2GpayzXVIQJXARFp2pR44coarocd9MtWgRW0LT8cEHH8jzBADIDYtEictJp8X2C7p9+/bJTQA4FYtKhWqyTMWaarkidqV0uWo0KdevX5enyhJ+/PFHuvevv/5a3gCAI7FOlCMe26SWqzXhqyfpklZt7HHfTI/3AzFHeG795ODwiEixc8PWvVKWHRCb7n/8ebFJQO0PzNomVodNXqu93+HTvP+Y1ClxCv0sHlVau8nK3Df1OerAjh075KmyCo+t17MA6MKKk5XfPqnWqmWpERfPzho0fjm3pO05L0TGLYUKF6Hl5a/9QsvzXvr/2k1zXzypXY0q5f3Qo9QXvuRV3lQkPIJX2/cdxwsx5WO17ZyylWppD1WtbkvtVovDPZFnK/AMHDjQritZAIxhRZ1kZGQINdiSqnWaFwgL0xqKP3xMtExN8/ZQ20lenfvnf6YropR25mUW4tKdZ8URchUl7yxuy162K9wTebYCzDvvvGPL/QLgD1acr4MHDxZqsCUVYuNGTt/C9bn+wHVqadNtJK9yx8pVrSeWObzasE3vdEWUYuviV0+LZRLirA3/o90nV1G27Hwf708s3fmjdpP14W6Iadq2zftygWbeAkWFChXeeOMNuRUAB2NFYXBBqoVqWchZ6Te9EFEiulPiFLHKHdMuc3i1cJHw9NuLcvi0DWKZhdigZXftnYp2bfqOfoZvwkhbrQx3gOcoIiKClgsXLnzr1JmJ9u4AcBdWnLhUHqXvrKoWqmWJjqlAP3sOf5JrtXKtJum3yrF6gzZimcOr9Zp1Sb+9KPmJOS+rQrydKCnLdp3jWxFPbvxY3cGalCxTiTrwz3/+k3vSt2/fy5cvZ2Rk8KzRwj/+8Q9ayLUxI4fbNfJNuPH+++/35PjxL3/5Cy8A4DqsOHGpPKrUbqYWqmUpWqwEL7ARFm7/Vix7cvQ3fe37Ylm7Z+qWL9IVUU5bfuNVNu2eWiEmTUlPz02Uq/dlNmk/kJcnPruPb9iyU5LYweJUqdWU+8DQTH311Ve8wLNWrVq12zWKm+TayDdR9wTApVhxBlOdlLqzilqolsWjkZowFxew2DRh4eu0PPiRFbR8z8DJtFys+B28SRKldlmsisM2aTeAF7SinLbiXb4J/SwQFsY70FU2ra7Zf0UcyuKULFuZOnDp0iV+CFWqVJFnDgCQg0WiZE3Ykq5DHqN7L1y0GC1Hlbxz4qK9tEAXldwrbce6Dn2cVtt2T6af/ccsFO2R0WXEzg3b9Na+j1L7fklBHu2UpgneL57sOWI2/aR7FIeyPtwrnqO4uDjtKgBAixWF0aRJE6EJxDmRzFi8eHEPPv8cgNywQpSHDh2CKB0YXEICkE+sqJPMzEyI0oGBKAHIJxbVCRXktBXvqrWK2BX+/6JJkybJUwUAULBOlPzmbcQhweUkAPnHolLhslTLFbErECUA+ceiUpk50/sJZqNnbVUrFrElNB2pqanyPAEAcsMiUWbjotJJ4feKyjMEALgN1lXLggULIEqHhCZixYoV8gwBAG6DdaLMzrmoTOg3Xq1bxMq06/0QLicB0IWlBZOVlUUlOmTiKrV6EWuSOD6NpiA6OlqeGwDA7bFUlNk3X6lcve+yWsOIBeHxl2cFAJAnVtfMxo0buVbVGkYCnXHzdtHIv/TSS/KsAADyxGpREseOHaNyrXVXe7WSkcClZsO7adiPHz8uzwcAwBc2iJIYMcL7lbDdk2ao9YwEIvxZc3jSDYAxbKscrtsO/SeoVY2YHh7tq1evytMAAMgHtomS6NevH1Vvuar11MJGzAp/weSAAQPk0QcA5Bs7RUls3ryZL3bUCkf8D4+tB8+4AfAP+0uoffv2XMzLdv+kljpiLGl7zvOotmvXTh5xAIBO7Bclk5bmfSM0UblmY7XskfxnzFPbeSQLFCggjzIAwBBOESVx4sQJrnAivucDq/ZeUi2A5JG2PUaLAfzss8/k8QUAGMVBomS+/faP70ckGjWPX7Dq5Q+/ytaV905mv/lp9u5jf2TXR16ViNUth2TLuDRzNn9eo0Fb7Yg9/PDD8pgCAPzDcaLUMmfOHK0CwO2YO3duZmamPHwAAJNwtCi1ZGVlXbp06eeffz4f8tAgXLx4Ed8rC4BluEaUAABgFxAlAAD4AKIEAAAfQJQAAOADiBIAAHwAUQIAgA8gSgAA8AFECQAAPoAoAQDABxAlAAD4AKIEAAAfQJQAAOADiBIAAHwAUQIAgA8gSgAA8AFECQAAPoAoAQDABxAlAAD4AKIEAAAfQJQAAOADiBIAAHwAUQIAgA8gSgAA8AFECQAAPoAoAQDABxAlAAD4AKIEAAAfQJQAAOADiBIAAHwAUQIAgA8gSgAA8AFECQAAPoAoAQDABxAlAAD4AKIEAAAfQJQAAOADiBIAAHwAUQIAgA8gSgAA8AFECQAAPoAoAQDABxAlAAD4AKIEAAAf/B+wZ5pm0j8/9gAAAABJRU5ErkJggg==>
