# Proyecto: Clinicanutricional

## Lenguaje: Python
## Framework: Django
## Editor: VS Code

# 1) Procedimiento para crear carpeta del Proyecto: UIII_Clinicanutricional_8313

Abrir tu terminal (PowerShell, CMD o Terminal de VS Code) y ejecutar:

mkdir UIII_Clinicanutricional_8313
cd UIII_Clinicanutricional_8313

# 2) Procedimiento para abrir VS Code sobre la carpeta UIII_Clinicanutricional_8313
code .


(O abrir VS Code y seleccionar File > Open Folder...)

# 3) Procedimiento para abrir terminal en VS Code

Menú superior: Terminal → New Terminal
Atajo: Ctrl + `

# 4) Procedimiento para crear carpeta de entorno virtual .venv desde terminal de VS Code

Windows:

python -m venv .venv


Linux / macOS:

python3 -m venv .venv

# 5) Procedimiento para activar el entorno virtual

Windows PowerShell:

.venv\Scripts\Activate.ps1


Windows CMD:

.venv\Scripts\activate


Linux / macOS:

source .venv/bin/activate

# 6) Procedimiento para activar intérprete de Python en VS Code

Ctrl + Shift + P → buscar:
Python: Select Interpreter
Elegir el que diga .venv.

# 7) Procedimiento para instalar Django
pip install django

# 8) Procedimiento para crear proyecto backend_clinicanutricional sin duplicar carpeta

Usar el punto final para evitar crear otra carpeta:

django-admin startproject backend_clinicanutricional .

# 9) Procedimiento para ejecutar servidor en el puerto 8313
python manage.py runserver 8313

# 10) Procedimiento para copiar y pegar el link en el navegador

Abrir http://127.0.0.1:8313/

Debe aparecer la página inicial de Django.
Para detener el servidor: Ctrl + C

# 11) Procedimiento para crear aplicación app_clinicanutricional
python manage.py startapp app_clinicanutricional

# 12) Aquí el modelo models.py

Copia el siguiente código en app_clinicanutricional/models.py
(Los modelos Pacientes y Citas quedan en pausa).

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
# MODELO: PACIENTES (EN PAUSA)
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
# MODELO: CITAS (EN PAUSA)
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

# 12.5) Procedimiento para realizar las migraciones
python manage.py makemigrations
python manage.py migrate

# 13) Trabajar con el modelo NUTRIÓLOGOS

Por ahora solo se hará CRUD de Nutriólogos.
(Pacientes y Citas quedarán en pausa).

# 14) En views.py crear funciones CRUD para Nutriólogos

Archivo: app_clinicanutricional/views.py

    from django.shortcuts import render, redirect, get_object_or_404
    from .models import Nutriologo
    
    def inicio_clinicanutricional(request):
        return render(request, 'inicio.html')
    
    def agregar_nutriologo(request):
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
            return redirect('ver_nutriologos')
        return render(request, 'nutriologos/agregar_nutriologo.html')
    
    def ver_nutriologos(request):
        nutriologos = Nutriologo.objects.all()
        return render(request, 'nutriologos/ver_nutriologos.html', {'nutriologos': nutriologos})
    
    def actualizar_nutriologo(request, id):
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
            return redirect('ver_nutriologos')
        return render(request, 'nutriologos/actualizar_nutriologo.html', {'nutriologo': nutriologo})
    
    def borrar_nutriologo(request, id):
        nutriologo = get_object_or_404(Nutriologo, id=id)
        if request.method == 'POST':
            nutriologo.delete()
            return redirect('ver_nutriologos')
        return render(request, 'nutriologos/borrar_nutriologo.html', {'nutriologo': nutriologo})

# 15) Crear carpeta templates dentro de app_clinicanutricional

Ruta:

app_clinicanutricional/
└── templates/

# 16) En la carpeta templates crear:
base.html  
navbar.html  
footer.html  
inicio.html

# 17) En base.html agregar Bootstrap
    <!DOCTYPE html>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <title>Clínica Nutricional</title>
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
    </head>
    <body style="background-color:#F6BBB7;">
        {% include 'navbar.html' %}
        <div class="container mt-4 mb-5">
            {% block content %}{% endblock %}
        </div>
        {% include 'footer.html' %}
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>

# 18) En navbar.html incluir las opciones principales
    <nav class="navbar navbar-expand-lg" style="background-color:#E5645E;">
      <div class="container-fluid">
        <a class="navbar-brand text-white" href="#">💚 Sistema Clínica Nutricional</a>
        <div class="collapse navbar-collapse">
          <ul class="navbar-nav ms-auto">
            <li class="nav-item"><a class="nav-link text-white" href="{% url 'inicio_clinicanutricional' %}">Inicio</a></li>
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">Nutriólogos</a>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="{% url 'agregar_nutriologo' %}">Agregar Nutriólogo</a></li>
                <li><a class="dropdown-item" href="{% url 'ver_nutriologos' %}">Ver Nutriólogos</a></li>
              </ul>
            </li>
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">Pacientes</a>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="#">En pausa</a></li>
              </ul>
            </li>
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">Citas</a>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="#">En pausa</a></li>
              </ul>
            </li>
          </ul>
        </div>
      </div>
    </nav>

# 19) En footer.html
    <footer class="text-center py-3 text-white" style="background-color:#5b8740; position:fixed; bottom:0; width:100%;">
        © {{ now|date:"Y" }} Creado por Keyla Palacios | CBTis 128
    </footer>

# 20) En inicio.html
    {% extends 'base.html' %}
    {% block content %}
    <div class="text-center">
      <h1 style="color:#E5645E;">Bienvenida a la Clínica Nutricional "Baby Carrots"</h1>
      <p class="mt-3">Sistema para administrar Nutriólogos, Pacientes y Citas.</p>
    </div>
    {% endblock %}

# 21) Crear subcarpeta nutriologos dentro de app_clinicanutricional/templates
app_clinicanutricional/templates/nutriologos/

# 22) Crear los siguientes archivos HTML en nutriologos/:

agregar_nutriologo.html

ver_nutriologos.html

actualizar_nutriologo.html

borrar_nutriologo.html

(Aquí puedes colocar los formularios simples sin forms.py)

# 23) No utilizar forms.py.
# 24) Crear urls.py dentro de app_clinicanutricional
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_clinicanutricional, name='inicio_clinicanutricional'),
    path('agregar/', views.agregar_nutriologo, name='agregar_nutriologo'),
    path('ver/', views.ver_nutriologos, name='ver_nutriologos'),
    path('actualizar/<int:id>/', views.actualizar_nutriologo, name='actualizar_nutriologo'),
    path('borrar/<int:id>/', views.borrar_nutriologo, name='borrar_nutriologo'),
]

# 25) Agregar app_clinicanutricional en settings.py del proyecto
INSTALLED_APPS = [
    # ...
    'app_clinicanutricional',
]

# 26) Configurar urls.py del proyecto principal (backend_clinicanutricional/urls.py)
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_clinicanutricional.urls')),
]

# 27) Registrar los modelos en admin.py
from django.contrib import admin
from .models import Nutriologo, Paciente, Cita

admin.site.register(Nutriologo)
admin.site.register(Paciente)
admin.site.register(Cita)

# 28) Realizar nuevamente las migraciones
python manage.py makemigrations
python manage.py migrate

# 29) Ejecutar el servidor en el puerto 8313
python manage.py runserver 8313

# 30) Verificar en el navegador

Abrir: http://127.0.0.1:8313/

Debe mostrarse la página de inicio con el título y la imagen de la clínica.

# 31) Proyecto totalmente funcional
    UIII_Clinicanutricional_8313/
    │
    ├── .venv/                              ← Entorno virtual
    │
    ├── backend_clinicanutricional/         ← Carpeta del proyecto Django
    │   ├── __init__.py
    │   ├── asgi.py
    │   ├── settings.py
    │   ├── urls.py
    │   └── wsgi.py
    │
    ├── app_clinicanutricional/             ← Aplicación principal
    │   ├── __init__.py
    │   ├── admin.py
    │   ├── apps.py
    │   ├── models.py                       ← Modelos: Nutriólogo, Paciente, Cita
    │   ├── views.py                        ← CRUD de Nutriólogos
    │   ├── urls.py                         ← Rutas de la app
    │   └── templates/
    │       ├── base.html
    │       ├── navbar.html
    │       ├── footer.html
    │       ├── inicio.html
    │       └── nutriologos/
    │           ├── agregar_nutriologo.html
    │           ├── ver_nutriologos.html
    │           ├── actualizar_nutriologo.html
    │           └── borrar_nutriologo.html
    │
    ├── db.sqlite3                          ← Base de datos
    │
    └── manage.py
✅ CRUD completo de Nutriólogos
🕓 Pacientes y Citas en pausa
🎨 Colores suaves, diseño moderno y funcional
💚 Creado por Keyla Palacios | CBTis 128
