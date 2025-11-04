# Proyecto: Clinicanutricional

## Lenguaje: Python
## Framework: Django
## Editor: VS Code

Índice

Requisitos previos

Estructura de carpetas recomendada

Paso a paso — Preparar el proyecto (crear carpeta, abrir VS Code, terminal, venv, instalar Django)

Crear proyecto y aplicación (sin duplicar carpetas)

Código de models.py (Nutriólogos, Pacientes, Citas)

Migraciones

Views para Nutriólogos (CRUD) — views.py (app)

Templates (estructura y ejemplos: base.html, navbar.html, footer.html, inicio.html)

Templates para nutriologos/ (agregar, ver, actualizar, borrar)

urls.py de la app y del proyecto

Registrar modelos en admin.py

Ejecutar servidor en el puerto 8313

Notas de estilo y consejos

## 1) Requisitos previos

Python 3.8+ instalado

VS Code instalado

Git (opcional)

## 2) Estructura de carpetas recomendada (al inicio)
UIII_Clinicanutricional_8313/
├─ .venv/                 # entorno virtual
├─ backend_clinicanutricional/  # carpeta del proyecto django (contendrá settings)
│  ├─ backend_clinicanutricional/
│  ├─ manage.py
├─ app_clinicanutricional/
│  ├─ migrations/
│  ├─ templates/
│  │  ├─ base.html
│  │  ├─ header.html
│  │  ├─ navbar.html
│  │  ├─ footer.html
│  │  ├─ inicio.html
│  │  └─ nutriologos/
│  │     ├─ agregar_nutriologo.html
│  │     ├─ ver_nutriologos.html
│  │     ├─ actualizar_nutriologo.html
│  │     └─ borrar_nutriologo.html
│  ├─ views.py
│  ├─ models.py
│  ├─ urls.py
│  ├─ admin.py
│  └─ __init__.py
├─ README.md

## 3) Procedimiento (comandos) — paso a paso

Ejecuta los comandos desde la terminal de VS Code (Ctrl+` o Terminal → Nueva terminal).

1. Crear carpeta del proyecto
# desde la ubicación donde quieras crear el proyecto
mkdir UIII_Clinicanutricional_8313
cd UIII_Clinicanutricional_8313

2. Abrir VS Code sobre la carpeta
code .

3. Abrir terminal en VS Code

Menú: Terminal → New Terminal

O atajo: Ctrl+`

4. Crear entorno virtual .venv

Windows (PowerShell):

python -m venv .venv


Linux / macOS:

python3 -m venv .venv

5. Activar entorno virtual

Windows (PowerShell):

.venv\Scripts\Activate.ps1


Windows (cmd):

.venv\Scripts\activate


Linux / macOS:

source .venv/bin/activate

6. Activar intérprete de Python (VS Code)

Ctrl+Shift+P → Python: Select Interpreter → elegir .venv (aparecerá en la lista).

7. Instalar Django
pip install django

8. Crear proyecto backend_clinicanutricional sin duplicar carpeta

Importante: crear el proyecto dentro de la carpeta actual sin crear una subcarpeta extra usando el punto al final:

django-admin startproject backend_clinicanutricional .


(Este comando crea manage.py y la carpeta backend_clinicanutricional/ en la carpeta actual).

9. Ejecutar servidor en puerto 8313
python manage.py runserver 8313

10. Copiar y pegar el link en el navegador

Cuando ejecutes runserver 8313 verás algo como http://127.0.0.1:8313/. Pégalo en el navegador.

11. Crear la aplicación app_clinicanutricional
python manage.py startapp app_clinicanutricional

## 4) models.py (cópialo a app_clinicanutricional/models.py)
from django.db import models

# MODELO: NUTRIÓLOGOS
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


# MODELO: PACIENTES
class Paciente(models.Model):
    nombre = models.CharField(max_length=50)
    apellido = models.CharField(max_length=50)
    telefono = models.CharField(max_length=10)
    fechnacimiento = models.DateField()
    correo = models.EmailField(unique=True)
    direccion = models.CharField(max_length=100)
    sexo = models.CharField(max_length=10, choices=[('Femenino', 'Femenino'), ('Masculino', 'Masculino')])
    id_nut = models.ForeignKey(Nutriologo, on_delete=models.CASCADE, related_name='pacientes')

    def __str__(self):
        return f"{self.nombre} {self.apellido}"

    class Meta:
        verbose_name = "Paciente"
        verbose_name_plural = "Pacientes"


# MODELO: CITAS
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


Nota: Por ahora trabajarás solo con el modelo Nutriologo. Deja Pacientes y Citas como pendientes si así lo deseas.

## 5) Migraciones (12.5 Procedimiento)
# Crear migraciones
python manage.py makemigrations

# Aplicar migraciones
python manage.py migrate

## 6) views.py (CRUD para Nutriólogos) — app_clinicanutricional/views.py
from django.shortcuts import render, redirect, get_object_or_404
from .models import Nutriologo

# Inicio (dashboard)
def inicio_clinicanutricional(request):
    return render(request, 'inicio.html')

# Agregar nutriólogo (GET muestra formulario, POST guarda)
def agregar_nutriologo(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        apellido = request.POST.get('apellido')
        correo = request.POST.get('correo')
        direccion = request.POST.get('direccion')
        telefono = request.POST.get('telefono')
        experiencia = request.POST.get('experiencia') or 0
        especialidad = request.POST.get('especialidad')

        Nutriologo.objects.create(
            nombre=nombre,
            apellido=apellido,
            correo=correo,
            direccion=direccion,
            telefono=telefono,
            experiencia=int(experiencia),
            especialidad=especialidad
        )
        return redirect('ver_nutriologos')
    return render(request, 'nutriologos/agregar_nutriologo.html')

# Ver todos
def ver_nutriologos(request):
    lista = Nutriologo.objects.all()
    return render(request, 'nutriologos/ver_nutriologos.html', {'nutriologos': lista})

# Página para actualizar (GET muestra datos)
def actualizar_nutriologo(request, pk):
    nut = get_object_or_404(Nutriologo, pk=pk)
    if request.method == 'POST':
        nut.nombre = request.POST.get('nombre')
        nut.apellido = request.POST.get('apellido')
        nut.correo = request.POST.get('correo')
        nut.direccion = request.POST.get('direccion')
        nut.telefono = request.POST.get('telefono')
        nut.experiencia = int(request.POST.get('experiencia') or 0)
        nut.especialidad = request.POST.get('especialidad')
        nut.save()
        return redirect('ver_nutriologos')
    return render(request, 'nutriologos/actualizar_nutriologo.html', {'nutriologo': nut})

# Borrar (confirmación y borrado)
def borrar_nutriologo(request, pk):
    nut = get_object_or_404(Nutriologo, pk=pk)
    if request.method == 'POST':
        nut.delete()
        return redirect('ver_nutriologos')
    return render(request, 'nutriologos/borrar_nutriologo.html', {'nutriologo': nut})

## 7) Templates principales
templates/base.html
<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width,initial-scale=1">
  <title>{% block title %}Clinica Nutricional{% endblock %}</title>

  <!-- Bootstrap CDN (ejemplo) -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
  {% block extra_head %}{% endblock %}
  <style>
    body { background-color: #F6F6F6; }
    .footer { position: fixed; bottom: 0; width: 100%; }
  </style>
</head>
<body>
  {% include 'navbar.html' %}
  <div class="container my-4">
    {% block content %}{% endblock %}
  </div>
  {% include 'footer.html' %}

  <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
  {% block extra_js %}{% endblock %}
</body>
</html>

templates/navbar.html
<nav class="navbar navbar-expand-lg navbar-light" style="background:#c0534d;">
  <div class="container-fluid">
    <a class="navbar-brand text-white" href="{% url 'inicio' %}">Sistema de Administración Clínica Nutricional</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarMain">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarMain">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link text-white" href="{% url 'inicio' %}">Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle text-white" href="#" role="button" data-bs-toggle="dropdown">Nutriólogos</a>
          <ul class="dropdown-menu dropdown-menu-end">
            <li><a class="dropdown-item" href="{% url 'agregar_nutriologo' %}">Agregar Nutriólogo</a></li>
            <li><a class="dropdown-item" href="{% url 'ver_nutriologos' %}">Ver Nutriólogos</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">Pacientes</a>
          <ul class="dropdown-menu dropdown-menu-end">
            <li><a class="dropdown-item" href="#">Agregar Paciente</a></li>
            <li><a class="dropdown-item" href="#">Ver Pacientes</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle text-white" href="#" data-bs-toggle="dropdown">Citas</a>
          <ul class="dropdown-menu dropdown-menu-end">
            <li><a class="dropdown-item" href="#">Agregar Cita</a></li>
            <li><a class="dropdown-item" href="#">Ver Citas</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>

templates/footer.html
<footer class="footer bg-light text-center py-2">
  <div class="container">
    <small>© {{ now|date:"Y" }} Derechos reservados. Creado por Ing. Eliseo Nava, Cbtis 128</small>
  </div>
</footer>


Para usar now en template, asegúrate de tener 'django.template.context_processors.request' y django.template.context_processors.tz activos o pasar la fecha desde la view. Alternativamente usa from django.utils import timezone en la view y pásalo en el contexto.

templates/inicio.html
{% extends 'base.html' %}
{% block title %}Inicio | Clínica Nutricional{% endblock %}
{% block content %}
  <div class="row">
    <div class="col-md-8">
      <h1>Bienvenido al Sistema de Administración Clínica Nutricional</h1>
      <p>Información del sistema y descripción aquí.</p>
    </div>
    <div class="col-md-4">
      <img src="https://via.placeholder.com/350x200?text=Clínica+Nutricional" class="img-fluid" alt="Clínica">
    </div>
  </div>
{% endblock %}

## 8) Templates para nutriologos/
nutriologos/agregar_nutriologo.html
{% extends 'base.html' %}
{% block content %}
<h2>Agregar Nutriólogo</h2>
<form method="post">
  {% csrf_token %}
  <div class="mb-3">
    <label class="form-label">Nombre</label>
    <input name="nombre" class="form-control" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Apellido</label>
    <input name="apellido" class="form-control" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Correo</label>
    <input name="correo" type="email" class="form-control" required>
  </div>
  <div class="mb-3">
    <label class="form-label">Dirección</label>
    <input name="direccion" class="form-control">
  </div>
  <div class="mb-3">
    <label class="form-label">Teléfono</label>
    <input name="telefono" class="form-control">
  </div>
  <div class="mb-3">
    <label class="form-label">Años de experiencia</label>
    <input name="experiencia" type="number" class="form-control" min="0">
  </div>
  <div class="mb-3">
    <label class="form-label">Especialidad</label>
    <input name="especialidad" class="form-control">
  </div>
  <button class="btn btn-primary">Guardar</button>
</form>
{% endblock %}

nutriologos/ver_nutriologos.html
{% extends 'base.html' %}
{% block content %}
<h2>Nutriólogos</h2>
<table class="table">
  <thead>
    <tr>
      <th>Nombre</th><th>Correo</th><th>Tel</th><th>Experiencia</th><th>Especialidad</th><th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {% for n in nutriologos %}
    <tr>
      <td>{{ n.nombre }} {{ n.apellido }}</td>
      <td>{{ n.correo }}</td>
      <td>{{ n.telefono }}</td>
      <td>{{ n.experiencia }}</td>
      <td>{{ n.especialidad }}</td>
      <td>
        <a class="btn btn-sm btn-info" href="{% url 'actualizar_nutriologo' n.pk %}">Editar</a>
        <a class="btn btn-sm btn-danger" href="{% url 'borrar_nutriologo' n.pk %}">Borrar</a>
      </td>
    </tr>
    {% empty %}
    <tr><td colspan="6">No hay nutriólogos registrados.</td></tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}

nutriologos/actualizar_nutriologo.html
{% extends 'base.html' %}
{% block content %}
<h2>Actualizar Nutriólogo</h2>
<form method="post">
  {% csrf_token %}
  <input name="nombre" value="{{ nutriologo.nombre }}" class="form-control mb-2">
  <input name="apellido" value="{{ nutriologo.apellido }}" class="form-control mb-2">
  <input name="correo" value="{{ nutriologo.correo }}" class="form-control mb-2">
  <input name="direccion" value="{{ nutriologo.direccion }}" class="form-control mb-2">
  <input name="telefono" value="{{ nutriologo.telefono }}" class="form-control mb-2">
  <input name="experiencia" value="{{ nutriologo.experiencia }}" class="form-control mb-2">
  <input name="especialidad" value="{{ nutriologo.especialidad }}" class="form-control mb-2">
  <button class="btn btn-primary">Guardar cambios</button>
</form>
{% endblock %}

nutriologos/borrar_nutriologo.html
{% extends 'base.html' %}
{% block content %}
<h2>Eliminar Nutriólogo</h2>
<p>¿Seguro que quieres borrar a <strong>{{ nutriologo.nombre }} {{ nutriologo.apellido }}</strong>?</p>
<form method="post">
  {% csrf_token %}
  <button class="btn btn-danger">Sí, borrar</button>
  <a class="btn btn-secondary" href="{% url 'ver_nutriologos' %}">Cancelar</a>
</form>
{% endblock %}

## 9) urls.py
app_clinicanutricional/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_clinicanutricional, name='inicio'),
    # Nutriologos
    path('nutriologos/agregar/', views.agregar_nutriologo, name='agregar_nutriologo'),
    path('nutriologos/', views.ver_nutriologos, name='ver_nutriologos'),
    path('nutriologos/actualizar/<int:pk>/', views.actualizar_nutriologo, name='actualizar_nutriologo'),
    path('nutriologos/borrar/<int:pk>/', views.borrar_nutriologo, name='borrar_nutriologo'),
]

backend_clinicanutricional/urls.py (archivo del proyecto)
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_clinicanutricional.urls')),  # enrutar la app en la raíz
]

## 10) Agregar app en settings.py

En backend_clinicanutricional/settings.py → INSTALLED_APPS añade:

INSTALLED_APPS = [
    # ...
    'app_clinicanutricional',
    # ...
]


Asegúrate también de tener configuradas TEMPLATES y STATICFILES_DIRS si usarás archivos estáticos locales.

## 11) Registrar modelos en admin.py (app_clinicanutricional/admin.py)
from django.contrib import admin
from .models import Nutriologo, Paciente, Cita

@admin.register(Nutriologo)
class NutriologoAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'apellido', 'correo', 'telefono', 'experiencia', 'especialidad')

@admin.register(Paciente)
class PacienteAdmin(admin.ModelAdmin):
    list_display = ('nombre', 'apellido', 'correo', 'telefono', 'fechnacimiento', 'id_nut')

@admin.register(Cita)
class CitaAdmin(admin.ModelAdmin):
    list_display = ('fecha', 'horario', 'motivo', 'estado', 'duracion', 'id_nut')


Después de registrar, vuelve a ejecutar:

python manage.py makemigrations
python manage.py migrate

## 12) Ejecutar servidor (último paso)
python manage.py runserver 8313


Abre en tu navegador http://127.0.0.1:8313/.

## 13) Notas finales / recomendaciones

No usar forms.py: aquí usamos formularios HTML simples y request.POST como pediste.

Validación: indicaste no validar; aun así, considera al menos required en inputs HTML para UX.

Estilos: usé colores suaves y ejemplo de Bootstrap. Cambia los códigos (#c0534d, #5b8740) en CSS si quieres más personalización.

Estructura al inicio: crea primero la estructura de carpetas y archivos vacíos para que Git no ignore carpetas vacías (puedes poner .gitkeep dentro).

Subir a GitHub: crea .gitignore con .venv/, __pycache__/, db.sqlite3 antes de commitear.
Ejemplo .gitignore:

.venv/
__pycache__/
db.sqlite3
*.pyc
.vscode/
