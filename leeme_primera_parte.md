# PROYECTO: Clinicanutricional

**Lenguaje:** Python

**Framework:** Django

**Editor:** Visual Studio Code

---

## PARTE 1 — PROCEDIMIENTO COMPLETO (UIII_Clinicanutricional_8313)

> A continuación tienes el procedimiento paso a paso, con los comandos y todos los archivos que debes crear. Puedes copiar/pegar directamente en VS Code.

### 1) Crear la carpeta del proyecto

1. Abre el Explorador de archivos de tu sistema.
2. Crea una nueva carpeta con el nombre:

```
UIII_Clinicanutricional_8313
```

Guárdala en Documentos o Escritorio (o donde prefieras).

### 2) Abrir VS Code sobre la carpeta

1. Abre Visual Studio Code.
2. Menú **Archivo → Abrir carpeta** y selecciona `UIII_Clinicanutricional_8313`.

### 3) Abrir la terminal en VS Code

En la barra superior: **Terminal → Nueva terminal**. Se abrirá una terminal en la parte inferior apuntando a la carpeta del proyecto.

### 4) Crear el entorno virtual `.venv` desde la terminal

En la terminal ejecuta:

```bash
python -m venv .venv
```

Eso crea la carpeta oculta `.venv` dentro del proyecto.

### 5) Activar el entorno virtual

**Windows (PowerShell o CMD)**:

```bash
.venv\Scripts\activate
```

Verás `(.venv)` al inicio del prompt si se activó correctamente.

### 6) Activar intérprete de Python en VS Code

Presiona `Ctrl + Shift + P` → escribe `Python: Select Interpreter` → elige el intérprete:

```
.venv\Scripts\python.exe
```

### 7) Instalar Django

Con el entorno activado ejecuta:

```bash
pip install django
```

Verifica:

```bash
django-admin --version
```

### 8) Crear el proyecto principal `backend_clinicanutricional` sin duplicar carpeta

Desde la terminal, estando en la raíz `UIII_Clinicanutricional_8313`, ejecuta:

```bash
django-admin startproject backend_clinicanutricional .
```

El punto `.` evita crear una carpeta extra.

### 9) Ejecutar servidor en el puerto 8313

Inicia el servidor con:

```bash
python manage.py runserver 8313
```

### 10) Copiar y pegar el enlace en el navegador

Abre en tu navegador: `http://127.0.0.1:8313/` — deberías ver la página de bienvenida de Django.

### 11) Crear la aplicación `app_clinicanutricional`

En la terminal ejecuta:

```bash
python manage.py startapp app_clinicanutricional
```

---

## 12) Archivo `app_clinicanutricional/models.py`

Pega el siguiente código (este es el modelo que nos proporcionaste, formateado correctamente):

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
# MODELO: PACIENTES (pendiente de usar por ahora)
# ==========================================
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

# ==========================================
# MODELO: CITAS (pendiente de usar por ahora)
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

---

## 12.5) Realizar las migraciones

Después de guardar `models.py`, crea y aplica migraciones:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 13) Empezamos trabajando con el MODELO: NUTRIÓLOGOS

Vamos a crear las vistas y plantillas para el CRUD de Nutriólogos (sin `forms.py`, usando POST directo en templates).

## 14) `app_clinicanutricional/views.py` (añade estas funciones)

```python
from django.shortcuts import render, redirect, get_object_or_404
from .models import Nutriologo

# Página de inicio
def inicio_clinicanutricional(request):
    return render(request, 'inicio.html')

# Agregar nutriólogo
def agregar_nutriologo(request):
    if request.method == 'POST':
        nombre = request.POST.get('nombre')
        apellido = request.POST.get('apellido')
        correo = request.POST.get('correo')
        direccion = request.POST.get('direccion')
        telefono = request.POST.get('telefono')
        experiencia = request.POST.get('experiencia')
        especialidad = request.POST.get('especialidad')

        nuevo = Nutriologo(
            nombre=nombre,
            apellido=apellido,
            correo=correo,
            direccion=direccion,
            telefono=telefono,
            experiencia=experiencia,
            especialidad=especialidad
        )
        nuevo.save()
        return redirect('ver_nutriologos')

    return render(request, 'nutriologos/agregar_nutriologo.html')

# Ver nutriólogos
def ver_nutriologos(request):
    nutris = Nutriologo.objects.all()
    return render(request, 'nutriologos/ver_nutriologos.html', {'nutris': nutris})

# Actualizar nutriólogo (formulario de edición)
def actualizar_nutriologo(request, id):
    nutri = get_object_or_404(Nutriologo, pk=id)
    return render(request, 'nutriologos/actualizar_nutriologo.html', {'nutri': nutri})

# Realizar actualización
def realizar_actualizacion_nutriologo(request, id):
    nutri = get_object_or_404(Nutriologo, pk=id)
    if request.method == 'POST':
        nutri.nombre = request.POST.get('nombre')
        nutri.apellido = request.POST.get('apellido')
        nutri.correo = request.POST.get('correo')
        nutri.direccion = request.POST.get('direccion')
        nutri.telefono = request.POST.get('telefono')
        nutri.experiencia = request.POST.get('experiencia')
        nutri.especialidad = request.POST.get('especialidad')
        nutri.save()
        return redirect('ver_nutriologos')
    return redirect('ver_nutriologos')

# Borrar nutriólogo
def borrar_nutriologo(request, id):
    nutri = get_object_or_404(Nutriologo, pk=id)
    if request.method == 'POST':
        nutri.delete()
        return redirect('ver_nutriologos')
    return render(request, 'nutriologos/borrar_nutriologo.html', {'nutri': nutri})
```

---

## 15) Crear la carpeta `templates` dentro de `app_clinicanutricional`

Ruta:

```
app_clinicanutricional/templates/
```

---

## 16) Archivos HTML dentro de `templates`

Crea los archivos: `base.html`, `header.html`, `navbar.html`, `footer.html`, `inicio.html`.

### 17) `base.html` (con Bootstrap CSS y JS)

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Clínica Nutricional</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="bg-light">
    {% include 'header.html' %}
    {% include 'navbar.html' %}

    <main class="container mt-4">
        {% block content %}{% endblock %}
    </main>

    {% include 'footer.html' %}

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

### 18) `navbar.html` (con iconos en opciones principales)

```html
<nav class="navbar navbar-expand-lg navbar-dark" style="background-color:#5b8740;">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">🥕 Sistema de Administración Clínica Nutricional</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navMenu">
      <ul class="navbar-nav ms-auto">
        <li class="nav-item"><a class="nav-link" href="/">🏠 Inicio</a></li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">👩‍⚕️ Nutriólogos</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="/agregar_nutriologo/">Agregar Nutriólogo</a></li>
            <li><a class="dropdown-item" href="/ver_nutriologos/">Ver Nutriólogos</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Nutriólogo</a></li>
            <li><a class="dropdown-item" href="#">Borrar Nutriólogo</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">👥 Pacientes</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Paciente</a></li>
            <li><a class="dropdown-item" href="#">Ver Pacientes</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Paciente</a></li>
            <li><a class="dropdown-item" href="#">Borrar Paciente</a></li>
          </ul>
        </li>

        <li class="nav-item dropdown">
          <a class="nav-link dropdown-toggle" href="#" data-bs-toggle="dropdown">📅 Citas</a>
          <ul class="dropdown-menu">
            <li><a class="dropdown-item" href="#">Agregar Cita</a></li>
            <li><a class="dropdown-item" href="#">Ver Citas</a></li>
            <li><a class="dropdown-item" href="#">Actualizar Cita</a></li>
            <li><a class="dropdown-item" href="#">Borrar Cita</a></li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</nav>
```

> Nota: los iconos sólo están en las opciones principales (Nutriólogos, Pacientes, Citas) como solicitaste.

### 19) `footer.html` (fijo al final, con derechos y autor)

```html
<footer class="bg-dark text-white text-center py-3 fixed-bottom">
    <p>© <span id="anio"></span> Creado por Keyla Palacios, Cbtis 128</p>
</footer>
<script>
    document.getElementById("anio").innerText = new Date().getFullYear();
</script>
```

### 20) `inicio.html` (info del sistema + imagen desde la web)

```django
{% extends 'base.html' %}
{% block content %}
<div class="text-center">
    <h1 class="mb-3">Bienvenido al Sistema de Administración — Clínica Nutricional</h1>
    <p class="lead">Gestiona Nutriólogos, Pacientes y Citas de forma sencilla.</p>
    <img src="https://images.unsplash.com/photo-1600180758890-7a5d7f5b5b7f?q=80&w=800&auto=format&fit=crop&ixlib=rb-4.0.3&s=placeholder" alt="Clínica Nutricional" class="img-fluid" style="max-width:500px;">
</div>
{% endblock %}
```

---

## 21) Crear subcarpeta `nutriologos` dentro de `templates`

Ruta:

```
app_clinicanutricional/templates/nutriologos/
```

## 22) Archivos dentro de `templates/nutriologos/`

Crea los siguientes archivos con este código:

### `agregar_nutriologo.html`

```django
{% extends 'base.html' %}
{% block content %}
<h2>Agregar Nutriólogo</h2>
<form method="POST">
  {% csrf_token %}
  <input type="text" name="nombre" placeholder="Nombre" class="form-control mb-2">
  <input type="text" name="apellido" placeholder="Apellido" class="form-control mb-2">
  <input type="email" name="correo" placeholder="Correo" class="form-control mb-2">
  <input type="text" name="direccion" placeholder="Dirección" class="form-control mb-2">
  <input type="text" name="telefono" placeholder="Teléfono" class="form-control mb-2">
  <input type="number" name="experiencia" placeholder="Años de experiencia" class="form-control mb-2">
  <input type="text" name="especialidad" placeholder="Especialidad" class="form-control mb-2">
  <button type="submit" class="btn btn-success">Guardar</button>
</form>
{% endblock %}
```

### `ver_nutriologos.html`

```django
{% extends 'base.html' %}
{% block content %}
<h2>Lista de Nutriólogos</h2>
<table class="table table-bordered">
  <thead>
    <tr>
      <th>ID</th><th>Nombre</th><th>Apellido</th><th>Correo</th><th>Teléfono</th><th>Acciones</th>
    </tr>
  </thead>
  <tbody>
    {% for n in nutris %}
    <tr>
      <td>{{ n.id }}</td>
      <td>{{ n.nombre }}</td>
      <td>{{ n.apellido }}</td>
      <td>{{ n.correo }}</td>
      <td>{{ n.telefono }}</td>
      <td>
        <a href="/actualizar_nutriologo/{{ n.id }}/" class="btn btn-warning btn-sm">Editar</a>
        <a href="/borrar_nutriologo/{{ n.id }}/" class="btn btn-danger btn-sm">Borrar</a>
      </td>
    </tr>
    {% endfor %}
  </tbody>
</table>
{% endblock %}
```

### `actualizar_nutriologo.html`

```django
{% extends 'base.html' %}
{% block content %}
<h2>Actualizar Nutriólogo</h2>
<form method="POST" action="/realizar_actualizacion_nutriologo/{{ nutri.id }}/">
  {% csrf_token %}
  <input type="text" name="nombre" value="{{ nutri.nombre }}" class="form-control mb-2">
  <input type="text" name="apellido" value="{{ nutri.apellido }}" class="form-control mb-2">
  <input type="email" name="correo" value="{{ nutri.correo }}" class="form-control mb-2">
  <input type="text" name="direccion" value="{{ nutri.direccion }}" class="form-control mb-2">
  <input type="text" name="telefono" value="{{ nutri.telefono }}" class="form-control mb-2">
  <input type="number" name="experiencia" value="{{ nutri.experiencia }}" class="form-control mb-2">
  <input type="text" name="especialidad" value="{{ nutri.especialidad }}" class="form-control mb-2">
  <button type="submit" class="btn btn-primary">Actualizar</button>
</form>
{% endblock %}
```

### `borrar_nutriologo.html`

```django
{% extends 'base.html' %}
{% block content %}
<h2>Confirmar borrado</h2>
<p>¿Deseas eliminar al nutriólogo <strong>{{ nutri.nombre }} {{ nutri.apellido }}</strong>?</p>
<form method="POST">
  {% csrf_token %}
  <button type="submit" class="btn btn-danger">Sí, borrar</button>
  <a href="/ver_nutriologos/" class="btn btn-secondary">Cancelar</a>
</form>
{% endblock %}
```

> Nota: no estamos usando `forms.py` por tu indicación.

---

## 23) No utilizar `forms.py` (confirmado)

Todas las plantillas usan formularios HTML normales y `request.POST` en las vistas.

## 24) `app_clinicanutricional/urls.py` (crear)

Crea `urls.py` dentro de la app con este contenido:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_clinicanutricional, name='inicio_clinicanutricional'),
    path('agregar_nutriologo/', views.agregar_nutriologo, name='agregar_nutriologo'),
    path('ver_nutriologos/', views.ver_nutriologos, name='ver_nutriologos'),
    path('actualizar_nutriologo/<int:id>/', views.actualizar_nutriologo, name='actualizar_nutriologo'),
    path('realizar_actualizacion_nutriologo/<int:id>/', views.realizar_actualizacion_nutriologo, name='realizar_actualizacion_nutriologo'),
    path('borrar_nutriologo/<int:id>/', views.borrar_nutriologo, name='borrar_nutriologo'),
]
```

---

## 25) Agregar `app_clinicanutricional` en `backend_clinicanutricional/settings.py`

En `INSTALLED_APPS` agrega:

```python
INSTALLED_APPS = [
    # ...
    'app_clinicanutricional',
]
```

---

## 26) Configurar `backend_clinicanutricional/urls.py` para enlazar la app

Edita `backend_clinicanutricional/urls.py` y deja algo así:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('app_clinicanutricional.urls')),
]
```

---

## 27) Registrar modelos en `app_clinicanutricional/admin.py` y volver a migrar

Edita `admin.py`:

```python
from django.contrib import admin
from .models import Nutriologo, Paciente, Cita

admin.site.register(Nutriologo)
admin.site.register(Paciente)
admin.site.register(Cita)
```

Luego vuelve a ejecutar (si hiciste cambios en modelos):

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## 27 (bis) — Por ahora trabajar solo con NUTRIÓLOGOS

Como solicitaste, dejamos los modelos de Pacientes y Citas definidos pero **pendientes** para implementar su CRUD después.

---

## 28) Estilos y colores

Usamos Bootstrap y una paleta suave: encabezados y barras con verde `#5b8740` (ya puesto en `navbar.html`) — el resto lo maneja Bootstrap para mantener un diseño limpio y moderno.

> No validamos entradas (tal como pediste).

---

## 29) Estructura inicial de carpetas y archivos (crear al inicio)

```
UIII_Clinicanutricional_8313/
│
├── .venv/
│
├── backend_clinicanutricional/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
│
├── app_clinicanutricional/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── urls.py
│   ├── views.py
│   ├── migrations/
│   │    └── __init__.py
│   └── templates/
│        ├── base.html
│        ├── header.html
│        ├── navbar.html
│        ├── footer.html
│        ├── inicio.html
│        └── nutriologos/
│             ├── agregar_nutriologo.html
│             ├── ver_nutriologos.html
│             ├── actualizar_nutriologo.html
│             └── borrar_nutriologo.html
│
├── db.sqlite3
└── manage.py
```

---

## 30) Proyecto totalmente funcional (con Nutriólogos CRUD)

Sigue los pasos anteriores en orden y deberías tener el CRUD de Nutriólogos listo y funcionando.

## 31) Ejecutar servidor final en puerto 8313

Para iniciar el sitio ya con los cambios ejecuta:

```bash
python manage.py runserver 8313
```

Visita:

```
http://127.0.0.1:8313/
```

---

### Notas finales

* No se validan entradas (tal como pediste). Ten en cuenta que `correo` está definido como único en el modelo; si intentas insertar dos registros con el mismo correo, Django lanzará un error de integridad.
* Si quieres que implemente también los CRUD para `Pacientes` y `Citas` (vistas, templates y urls), dímelo y los agrego.
* Si quieres que cambie colores, logotipo o textos (por ejemplo tu nombre en el footer), lo actualizo.

¡Listo! El proyecto está descrito paso a paso y con todos los archivos necesarios para tener el CRUD de **Nutriólogos** funcionando en Django.

--
*Creado para Keyla Palacios — Clinicanutricional (UIII_Clinicanutricional_8313)*
