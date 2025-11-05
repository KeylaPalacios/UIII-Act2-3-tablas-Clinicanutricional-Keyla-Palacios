## Proyecto: Clinicanutricional

# Lenguaje: Python

# Framework: Django

# Editor: Visual Studio Code

---

# 1 Crear carpeta del proyecto

```bash
mkdir UIII_Clinicanutricional_8313
cd UIII_Clinicanutricional_8313
```

# 2 Abrir VS Code en la carpeta

```bash
code .
```

# 3 Abrir la terminal de VS Code

Menú superior → Terminal → New Terminal
O atajo: Ctrl + ñ

# 4 Crear el entorno virtual “.venv”

```bash
python -m venv .venv
```

# 5 Activar entorno virtual

```bash
.venv\Scripts\activate
```

# 6 Seleccionar el intérprete de Python

Presiona Ctrl + Shift + P → “Python: Select Interpreter” → elige el que termina en .venv

# 7 Instalar Django

```bash
pip install django
```

# 8 Crear el proyecto principal

```bash
django-admin startproject backend_clinicanutricional .
```

# 9 Probar el servidor

```bash
python manage.py runserver 8313
```

Abrir en el navegador 👉 [http://127.0.0.1:8313/](http://127.0.0.1:8313/)

# 10 Crear aplicación

```bash
python manage.py startapp app_clinicanutricional
```

# 11 Registrar la app en settings.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_clinicanutricional',
]
```

# 12 Archivo models.py

```python
from django.db import models

# ==========================================
# MODELO: NUTRIÓLOGOS
# ==========================================
class Nutriologo(models.Model):
    nombre = models.CharField(max_length=50)
    apellido = models.CharField(max_length=50)
    correo = models.EmailField(unique=True)
    direccion = models.CharField(max_length=100)
    telefono = models.CharField(max_length=10)
    experiencia = models.PositiveIntegerField(help_text="Años de experiencia")
    especialidad = models.CharField(max_length=100)

    def __str__(self):
        return f"{self.nombre} {self.apellido}"

    class Meta:
        verbose_name = "Nutriólogo"
        verbose_name_plural = "Nutriólogos"


# ==========================================
# MODELO: PACIENTES
# ==========================================
class Paciente(models.Model):
    nombre = models.CharField(max_length=50)
    apellido = models.CharField(max_length=50)
    telefono = models.CharField(max_length=10)
    fechnacimiento = models.DateField()
    correo = models.EmailField(unique=True)
    direccion = models.CharField(max_length=100)
    sexo = models.CharField(max_length=10, choices=[
        ('Femenino', 'Femenino'),
        ('Masculino', 'Masculino')
    ])
    id_nut = models.ForeignKey(Nutriologo, on_delete=models.CASCADE, related_name='pacientes')

    def __str__(self):
        return f"{self.nombre} {self.apellido}"

    class Meta:
        verbose_name = "Paciente"
        verbose_name_plural = "Pacientes"


# ==========================================
# MODELO: CITAS
# ==========================================
class Cita(models.Model):
    fecha = models.DateField()
    horario = models.TimeField()
    motivo = models.CharField(max_length=200)
    estado = models.CharField(max_length=50, choices=[
        ('pendiente', 'Pendiente'),
        ('confirmada', 'Confirmada'),
        ('cancelada', 'Cancelada'),
        ('finalizada', 'Finalizada'),
    ])
    observaciones = models.TextField(blank=True, null=True)
    duracion = models.PositiveIntegerField(help_text="Duración en minutos")
    id_nut = models.ForeignKey(Nutriologo, on_delete=models.CASCADE, related_name='citas')

    def __str__(self):
        return f"Cita con {self.id_nut.nombre} el {self.fecha} a las {self.horario}"

    class Meta:
        verbose_name = "Cita"
        verbose_name_plural = "Citas"
```

# 13 Migrar base de datos

```bash
python manage.py makemigrations
python manage.py migrate
```

# 14 Crear superusuario

```bash
python manage.py createsuperuser
```

# 15 Registrar modelo en admin.py

```python
from django.contrib import admin
from .models import Nutriologo, Paciente, Cita

admin.site.register(Nutriologo)
admin.site.register(Paciente)
admin.site.register(Cita)
```

# 16 Probar en el panel de administración

```bash
python manage.py runserver 8313
```

👉 [http://127.0.0.1:8313/admin/](http://127.0.0.1:8313/admin/)

# 17 Crear archivo urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio, name='inicio'),
    path('nutriologos/', views.lista_nutriologos, name='lista_nutriologos'),
    path('crear_nutriologo/', views.crear_nutriologo, name='crear_nutriologo'),
    path('editar_nutriologo/<int:id>/', views.editar_nutriologo, name='editar_nutriologo'),
    path('eliminar_nutriologo/<int:id>/', views.eliminar_nutriologo, name='eliminar_nutriologo'),
]
```

# 18 Enlazar las rutas al proyecto principal

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_clinicanutricional.urls')),
]
```

# 19 Crear carpeta templates

📁 app_clinicanutricional → 📁 templates → 📁 app_clinicanutricional

# 20 Configurar templates en settings.py

```python
import os
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'app_clinicanutricional', 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

# 21 Crear vistas en views.py
    
    ```python
    from django.shortcuts import render, redirect, get_object_or_404
    from .models import Nutriologo
    
    # ============================================================
    # 🥕 VISTA DE INICIO
    # ============================================================
    def inicio(request):
        return render(request, 'app_clinicanutricional/inicio.html')
    
    
    # ============================================================
    # 🌿 LISTAR NUTRIÓLOGOS
    # ============================================================
    def lista_nutriologos(request):
        nutriologos = Nutriologo.objects.all()
        return render(request, 'app_clinicanutricional/lista_nutriologos.html', {'nutriologos': nutriologos})
    
    
    # ============================================================
    # 🧡 CREAR NUTRIÓLOGO
    # ============================================================
    def crear_nutriologo(request):
        if request.method == 'POST':
            nombre = request.POST['nombre']
            apellido = request.POST['apellido']
            correo = request.POST['correo']
            direccion = request.POST['direccion']
            telefono = request.POST['telefono']
            experiencia = request.POST['experiencia']
            especialidad = request.POST['especialidad']
    
            Nutriologo.objects.create(
                nombre=nombre,
                apellido=apellido,
                correo=correo,
                direccion=direccion,
                telefono=telefono,
                experiencia=experiencia,
                especialidad=especialidad
            )
            return redirect('lista_nutriologos')
        return render(request, 'app_clinicanutricional/crear_nutriologo.html')
    
    
    # ============================================================
    # 🌸 EDITAR NUTRIÓLOGO
    # ============================================================
    def editar_nutriologo(request, id):
        nutriologo = get_object_or_404(Nutriologo, id=id)
        if request.method == 'POST':
            nutriologo.nombre = request.POST['nombre']
            nutriologo.apellido = request.POST['apellido']
            nutriologo.correo = request.POST['correo']
            nutriologo.direccion = request.POST['direccion']
            nutriologo.telefono = request.POST['telefono']
            nutriologo.experiencia = request.POST['experiencia']
            nutriologo.especialidad = request.POST['especialidad']
            nutriologo.save()
            return redirect('lista_nutriologos')
        return render(request, 'app_clinicanutricional/editar_nutriologo.html', {'nutriologo': nutriologo})
    
    
    # ============================================================
    # ❌ ELIMINAR NUTRIÓLOGO
    # ============================================================
    def eliminar_nutriologo(request, id):
        nutriologo = get_object_or_404(Nutriologo, id=id)
        if request.method == 'POST':
            nutriologo.delete()
            return redirect('lista_nutriologos')
        return render(request, 'app_clinicanutricional/eliminar_nutriologo.html', {'nutriologo': nutriologo})

# 22 Crear los archivos HTML

Ubicación: `app_clinicanutricional/templates/app_clinicanutricional/`

---

## 📄 inicio.html

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Inicio - Baby Carrots</title>
    <style>
        body { background-color: #F6BBB7; text-align: center; font-family: Arial; }
        h1 { color: #E5645E; }
        a { color: #5b8740; text-decoration: none; font-weight: bold; }
    </style>
</head>
<body>
    <h1>Bienvenida a Baby Carrots 🥕</h1>
    <a href="{% url 'lista_nutriologos' %}">Ver Nutriólogos</a>
</body>
</html>
```

---

## 📄 lista_nutriologos.html

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Lista de Nutriólogos</title>
    <style>
        body { background-color: #F6BBB7; font-family: Arial; text-align: center; }
        h1 { color: #E5645E; }
        table { margin: auto; border-collapse: collapse; width: 80%; }
        th, td { padding: 10px; border: 1px solid #5b8740; }
        a, button { background-color: #5b8740; color: white; padding: 8px 15px; border: none; border-radius: 8px; text-decoration: none; margin: 5px; }
        a:hover, button:hover { background-color: #E5645E; }
    </style>
</head>
<body>
    <h1>Lista de Nutriólogos 🩷</h1>
    <a href="{% url 'crear_nutriologo' %}">Agregar Nutriólogo</a>
    <table>
        <tr>
            <th>Nombre</th><th>Correo</th><th>Teléfono</th><th>Acciones</th>
        </tr>
        {% for nut in nutriologos %}
        <tr>
            <td>{{ nut.nombre }} {{ nut.apellido }}</td>
            <td>{{ nut.correo }}</td>
            <td>{{ nut.telefono }}</td>
            <td>
                <a href="{% url 'editar_nutriologo' nut.id %}">Editar</a>
                <a href="{% url 'eliminar_nutriologo' nut.id %}">Eliminar</a>
            </td>
        </tr>
        {% endfor %}
    </table>
</body>
</html>
```

---

## 📄 crear_nutriologo.html

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Crear Nutriólogo</title>
    <style>
        body { background-color: #F6BBB7; text-align: center; font-family: Arial; }
        form { background-color: white; padding: 20px; margin: 20px auto; width: 50%; border-radius: 15px; }
        input, button { margin: 5px; padding: 10px; width: 90%; border-radius: 8px; border: 1px solid #ccc; }
        button { background-color: #5b8740; color: white; }
        button:hover { background-color: #E5645E; }
    </style>
</head>
<body>
    <h1>Agregar Nutriólogo 🥕</h1>
    <form method="POST">
        {% csrf_token %}
        <input type="text" name="nombre" placeholder="Nombre" required>
        <input type="text" name="apellido" placeholder="Apellido" required>
        <input type="email" name="correo" placeholder="Correo" required>
        <input type="text" name="direccion" placeholder="Dirección" required>
        <input type="text" name="telefono" placeholder="Teléfono" required>
        <input type="number" name="experiencia" placeholder="Años de experiencia" required>
        <input type="text" name="especialidad" placeholder="Especialidad" required>
        <button type="submit">Guardar</button>
    </form>
    <a href="{% url 'lista_nutriologos' %}">Volver</a>
</body>
</html>
```

---

## 📄 editar_nutriologo.html

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Editar Nutriólogo</title>
    <style>
        body { background-color: #F6BBB7; text-align: center; font-family: Arial; }
        form { background-color: white; padding: 20px; margin: 20px auto; width: 50%; border-radius: 15px; }
        input, button { margin: 5px; padding: 10px; width: 90%; border-radius: 8px; border: 1px solid #ccc; }
        button { background-color: #5b8740; color: white; }
        button:hover { background-color: #E5645E; }
    </style>
</head>
<body>
    <h1>Editar Nutriólogo 🌿</h1>
    <form method="POST">
        {% csrf_token %}
        <input type="text" name="nombre" value="{{ nutriologo.nombre }}" required>
        <input type="text" name="apellido" value="{{ nutriologo.apellido }}" required>
        <input type="email" name="correo" value="{{ nutriologo.correo }}" required>
        <input type="text" name="direccion" value="{{ nutriologo.direccion }}" required>
        <input type="text" name="telefono" value="{{ nutriologo.telefono }}" required>
        <input type="number" name="experiencia" value="{{ nutriologo.experiencia }}" required>
        <input type="text" name="especialidad" value="{{ nutriologo.especialidad }}" required>
        <button type="submit">Actualizar</button>
    </form>
    <a href="{% url 'lista_nutriologos' %}">Volver</a>
</body>
</html>
```

---

## 📄 eliminar_nutriologo.html

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>Eliminar Nutriólogo</title>
    <style>
        body { background-color: #F6BBB7; text-align: center; font-family: Arial; }
        button { background-color: #E5645E; color: white; padding: 10px 20px; border: none; border-radius: 10px; margin: 10px; }
        a { background-color: #5b8740; color: white; padding: 10px 20px; border-radius: 10px; text-decoration: none; }
        a:hover, button:hover { opacity: 0.8; }
    </style>
</head>
<body>
    <h1>Eliminar Nutriólogo ❌</h1>
    <p>¿Seguro que deseas eliminar a <b>{{ nutriologo.nombre }} {{ nutriologo.apellido }}</b>?</p>
    <form method="POST">
        {% csrf_token %}
        <button type="submit">Eliminar</button>
        <a href="{% url 'lista_nutriologos' %}">Cancelar</a>
    </form>
</body>
</html>
```

---

# 23 Probar CRUD completo

```bash
python manage.py runserver 8313
```

Rutas:

* `/` → Inicio
* `/nutriologos/` → Lista
* `/crear_nutriologo/` → Crear
* `/editar_nutriologo/id` → Editar
* `/eliminar_nutriologo/id` → Eliminar

---

# 24 Crear carpeta static

📁 app_clinicanutricional/static/app_clinicanutricional/css

# 25 Configurar carpeta static

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'app_clinicanutricional/static')]
```

# 26 Confirmar diseño Baby Carrots

🍑 Fondo: #F6BBB7
🥕 Encabezado: #E5645E
🌿 Botones: #5b8740

# 27 Agregar app “Pacientes” (en pausa)

# 28 Agregar app “Citas” (en pausa)

# 29 Verificar panel admin

👉 [http://127.0.0.1:8313/admin/](http://127.0.0.1:8313/admin/)

# 30 Guardar en GitHub

```bash
git init
git add .
git commit -m "Proyecto Clinicanutricional con CRUD Nutriologos completo"
```

# 31 Proyecto Finalizado ✅
    UIII_Clinicanutricional_8313/
    │
    ├── backend_clinicanutricional/
    │   ├── __init__.py
    │   ├── asgi.py
    │   ├── settings.py          ← Configuraciones del proyecto
    │   ├── urls.py              ← Enlaces globales (admin + app principal)
    │   └── wsgi.py
    │
    ├── app_clinicanutricional/
    │   ├── __init__.py
    │   ├── admin.py             ← Modelos registrados (Nutriólogos, Pacientes, Citas)
    │   ├── apps.py
    │   ├── models.py            ← Los tres modelos principales
    │   ├── views.py             ← CRUD completo de Nutriólogos
    │   ├── urls.py              ← Rutas internas de la app
    │   ├── migrations/
    │   │   └── 0001_initial.py  ← Migraciones automáticas
    │   │
    │   ├── templates/
    │   │   └── app_clinicanutricional/
    │   │       ├── inicio.html                ← Página de bienvenida
    │   │       ├── lista_nutriologos.html     ← Tabla con todos los nutriólogos
    │   │       ├── crear_nutriologo.html      ← Formulario para agregar nuevo
    │   │       ├── editar_nutriologo.html     ← Formulario para editar
    │   │       └── eliminar_nutriologo.html   ← Confirmación para borrar
    │   │
    │   └── static/
    │       └── app_clinicanutricional/
    │           └── css/
    │               └── estilos.css            ← Colores y diseño “Baby Carrots”
    │
    ├── db.sqlite3               ← Base de datos SQLite
    ├── manage.py
    └── .venv/                   ← Entorno virtual (bibliotecas de Django)

🎉 CRUD completo de Nutriólogos funcional
🩷 Modelos de Pacientes y Citas listos para continuar
🌿 Colores suaves y estilo “Baby Carrots”

