## Proyecto: Clinicanutricional

# Lenguaje: Python
# Framework: Django
# Editor: Visual Studio Code

# 1 Crear carpeta del proyecto
mkdir UIII_Clinicanutricional_8313
cd UIII_Clinicanutricional_8313

# 2 Abrir VS Code en la carpeta
code .

# 3 Abrir la terminal de VS Code

Menú superior → Terminal → New Terminal
O atajo: Ctrl + ñ

# 4 Crear el entorno virtual “.venv”
python -m venv .venv

# 5 Activar entorno virtual
.venv\Scripts\activate

# 6 Seleccionar el intérprete de Python

Presiona Ctrl + Shift + P → “Python: Select Interpreter” → elige el que termina en .venv

# 7 Instalar Django
pip install django

# 8 Crear el proyecto principal
django-admin startproject backend_clinicanutricional .

# 9 Probar el servidor
python manage.py runserver 8313


Abrir en el navegador 👉 http://127.0.0.1:8313/

# 10 Crear aplicación
python manage.py startapp app_clinicanutricional

# 11 Registrar la app en settings.py

Dentro de backend_clinicanutricional/settings.py agrega:

    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'app_clinicanutricional',
    ]

# 12 Archivo models.py
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

# 13 Migrar base de datos
python manage.py makemigrations
python manage.py migrate

# 14 Crear superusuario
python manage.py createsuperuser

# 15 Registrar modelo en el admin

Ubicación: app_clinicanutricional/admin.py

from django.contrib import admin
from .models import Nutriologo

admin.site.register(Nutriologo)

#1 6 Probar en el panel de administración
python manage.py runserver 8313


👉 Ir a http://127.0.0.1:8313/admin/

Inicia sesión con tu usuario de superadmin y prueba registrar nutriólogos.

# 17 Crear archivo urls.py dentro de la app

    Ubicación: app_clinicanutricional/urls.py
    
    from django.urls import path
    from . import views
    
    urlpatterns = [
        path('', views.inicio, name='inicio'),
        path('nutriologos/', views.lista_nutriologos, name='lista_nutriologos'),
        path('crear_nutriologo/', views.crear_nutriologo, name='crear_nutriologo'),
        path('editar_nutriologo/<int:id>/', views.editar_nutriologo, name='editar_nutriologo'),
        path('eliminar_nutriologo/<int:id>/', views.eliminar_nutriologo, name='eliminar_nutriologo'),
    ]

# 18 Enlazar las rutas de la app al urls.py del proyecto

    Ubicación: backend_clinicanutricional/urls.py
    
    from django.contrib import admin
    from django.urls import path, include
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('app_clinicanutricional.urls')),
    ]

#1 9 Crear carpeta templates

    Dentro de la app:
    📁 app_clinicanutricional
      📁 templates
        📁 app_clinicanutricional

# 20 Configurar templates en settings.py

Agrega:

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

#21 Crear vistas en views.py

Ubicación: app_clinicanutricional/views.py

        from django.shortcuts import render, redirect, get_object_or_404
        from .models import Nutriologo
    
    # Página de inicio
            def inicio(request):
                return render(request, 'app_clinicanutricional/inicio.html')
    
    # Listar nutriólogos
        def lista_nutriologos(request):
            nutriologos = Nutriologo.objects.all()
            return render(request, 'app_clinicanutricional/lista_nutriologos.html', {'nutriologos': nutriologos})
    
    # Crear nutriólogo
        def crear_nutriologo(request):
            if request.method == 'POST':
                Nutriologo.objects.create(
                    nombre=request.POST['nombre'],
                    apellido=request.POST['apellido'],
                    correo=request.POST['correo'],
                    direccion=request.POST['direccion'],
                    telefono=request.POST['telefono'],
                    experiencia=request.POST['experiencia'],
                    especialidad=request.POST['especialidad']
                )
                return redirect('lista_nutriologos')
            return render(request, 'app_clinicanutricional/crear_nutriologo.html')
    
    # Editar nutriólogo
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
    
    # Eliminar nutriólogo
        def eliminar_nutriologo(request, id):
            nutriologo = get_object_or_404(Nutriologo, id=id)
            if request.method == 'POST':
                nutriologo.delete()
                return redirect('lista_nutriologos')
            return render(request, 'app_clinicanutricional/eliminar_nutriologo.html', {'nutriologo': nutriologo})

# 22 Crear los archivos HTML
        📄 inicio.html
            <!DOCTYPE html>
            <html lang="es">
            <head>
                <meta charset="UTF-8">
                <title>Inicio - Baby Carrots</title>
                <style>
                    body { background-color: #F6BBB7; color: #5b8740; font-family: 'Segoe UI'; text-align: center; }
                    h1 { color: #E5645E; }
                    a { background: #E5645E; color: white; padding: 10px 15px; border-radius: 10px; text-decoration: none; }
                    a:hover { background: #5b8740; }
                </style>
            </head>
            <body>
                <h1>🥕 Bienvenida a Baby Carrots</h1>
                <p>Tu clínica nutricional con amor y bienestar.</p>
                <a href="{% url 'lista_nutriologos' %}">Ver Nutriólogos</a>
            </body>
            </html>

📄 lista_nutriologos.html
            <!DOCTYPE html>
            <html lang="es">
            <head>
                <meta charset="UTF-8">
                <title>Nutriólogos - Baby Carrots</title>
                <style>
                    body { background: #F6BBB7; font-family: 'Segoe UI'; color: #5b8740; text-align: center; }
                    table { margin: 20px auto; border-collapse: collapse; width: 80%; background: white; }
                    th, td { border: 1px solid #E5645E; padding: 10px; }
                    a { color: white; background: #E5645E; padding: 5px 10px; border-radius: 5px; text-decoration: none; }
                    a:hover { background: #5b8740; }
                </style>
            </head>
            <body>
                <h1>Lista de Nutriólogos</h1>
                <a href="{% url 'crear_nutriologo' %}">➕ Agregar Nutriólogo</a>
                <table>
                    <tr>
                        <th>Nombre</th><th>Apellido</th><th>Correo</th><th>Teléfono</th><th>Especialidad</th><th>Acciones</th>
                    </tr>
                    {% for n in nutriologos %}
                    <tr>
                        <td>{{ n.nombre }}</td>
                        <td>{{ n.apellido }}</td>
                        <td>{{ n.correo }}</td>
                        <td>{{ n.telefono }}</td>
                        <td>{{ n.especialidad }}</td>
                        <td>
                            <a href="{% url 'editar_nutriologo' n.id %}">✏️ Editar</a>
                            <a href="{% url 'eliminar_nutriologo' n.id %}">❌ Eliminar</a>
                        </td>
                    </tr>
                    {% endfor %}
                </table>
            </body>
            </html>

📄 crear_nutriologo.html
        <!DOCTYPE html>
        <html lang="es">
        <head>
            <meta charset="UTF-8">
            <title>Crear Nutriólogo</title>
            <style>
                body { background-color: #F6BBB7; font-family: 'Segoe UI'; color: #5b8740; text-align: center; }
                input { margin: 5px; padding: 8px; border-radius: 8px; border: 1px solid #E5645E; }
                button { background: #E5645E; color: white; border: none; padding: 8px 15px; border-radius: 8px; cursor: pointer; }
                button:hover { background: #5b8740; }
            </style>
        </head>
        <body>
            <h1>Agregar Nutriólogo</h1>
            <form method="post">{% csrf_token %}
                <input type="text" name="nombre" placeholder="Nombre" required><br>
                <input type="text" name="apellido" placeholder="Apellido" required><br>
                <input type="email" name="correo" placeholder="Correo" required><br>
                <input type="text" name="direccion" placeholder="Dirección" required><br>
                <input type="text" name="telefono" placeholder="Teléfono" required><br>
                <input type="text" name="experiencia" placeholder="Experiencia" required><br>
                <input type="text" name="especialidad" placeholder="Especialidad" required><br>
                <button type="submit">Guardar</button>
            </form>
        </body>
        </html>

📄 editar_nutriologo.html
        <!DOCTYPE html>
        <html lang="es">
        <head>
            <meta charset="UTF-8">
            <title>Editar Nutriólogo</title>
            <style>
                body { background-color: #F6BBB7; font-family: 'Segoe UI'; color: #5b8740; text-align: center; }
                input { margin: 5px; padding: 8px; border-radius: 8px; border: 1px solid #E5645E; }
                button { background: #E5645E; color: white; border: none; padding: 8px 15px; border-radius: 8px; cursor: pointer; }
                button:hover { background: #5b8740; }
            </style>
        </head>
        <body>
            <h1>Editar Nutriólogo</h1>
            <form method="post">{% csrf_token %}
                <input type="text" name="nombre" value="{{ nutriologo.nombre }}"><br>
                <input type="text" name="apellido" value="{{ nutriologo.apellido }}"><br>
                <input type="email" name="correo" value="{{ nutriologo.correo }}"><br>
                <input type="text" name="direccion" value="{{ nutriologo.direccion }}"><br>
                <input type="text" name="telefono" value="{{ nutriologo.telefono }}"><br>
                <input type="text" name="experiencia" value="{{ nutriologo.experiencia }}"><br>
                <input type="text" name="especialidad" value="{{ nutriologo.especialidad }}"><br>
                <button type="submit">Actualizar</button>
            </form>
        </body>
        </html>

📄 eliminar_nutriologo.html
        <!DOCTYPE html>
        <html lang="es">
        <head>
            <meta charset="UTF-8">
            <title>Eliminar Nutriólogo</title>
            <style>
                body { background-color: #F6BBB7; font-family: 'Segoe UI'; color: #5b8740; text-align: center; }
                .card { background: white; border: 2px solid #E5645E; padding: 20px; border-radius: 15px; display: inline-block; margin-top: 50px; }
                button, a { margin: 5px; padding: 10px 20px; border-radius: 8px; text-decoration: none; }
                button { background: #E5645E; color: white; border: none; }
                button:hover { background: #5b8740; }
                a { background: #5b8740; color: white; }
                a:hover { background: #E5645E; }
            </style>
        </head>
        <body>
            <div class="card">
                <h2>¿Eliminar a {{ nutriologo.nombre }} {{ nutriologo.apellido }}?</h2>
                <p>Esta acción no se puede deshacer.</p>
                <form method="post">{% csrf_token %}
                    <button type="submit">Sí, eliminar</button>
                    <a href="{% url 'lista_nutriologos' %}">Cancelar</a>
                </form>
            </div>
        </body>
        </html>

# 23 Probar CRUD completo

    Ejecuta:
    
    python manage.py runserver 8313
    
    
    Navega por las vistas:
    
    / → Inicio
    
    /nutriologos/ → Lista
    
    /crear_nutriologo/ → Crear
    
    /editar_nutriologo/id → Editar
    
    /eliminar_nutriologo/id → Confirmar eliminación

# 24 Crear carpeta static (para estilos globales)

📁 app_clinicanutricional/static/app_clinicanutricional/css

# 25 Configurar carpeta static

Agregar en settings.py:

STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'app_clinicanutricional/static')]

# 26 Confirmar diseño Baby Carrots

Colores oficiales:
🍑 Fondo: #F6BBB7
🥕 Encabezado/Logo: #E5645E
🌿 Barra y botones: #5b8740

# 27 Agregar app “Pacientes” (en pausa)
# 28 Agregar app “Citas” (en pausa)
# 29 Verificar panel admin

http://127.0.0.1:8313/admin/ → gestionar nutriólogos.

# 30 Guardar cambios en GitHub
git init
git add .
git commit -m "Proyecto Clinicanutricional con CRUD Nutriologos completo"

# 31 Proyecto Finalizado ✅

🎉 CRUD completo para Nutriólogos (crear, editar, eliminar)
🩷 Pacientes y Citas listos para implementarse después
🌿 Diseño con colores oficiales de Baby Carrots
