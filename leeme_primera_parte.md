# Proyecto: Clínica Nutricional

**Lenguaje**: Python  
**Framework**: Django  
**Editor**: VS Code  

---

## Procedimientos

### 1. Crear carpeta del Proyecto

- Crear la carpeta: `UIII_Clinicanutricional_8313`

### 2. Abrir VS Code sobre la carpeta

- Abrir la carpeta `UIII_Clinicanutricional_8313` en VS Code.

### 3. Abrir terminal en VS Code

- Abrir la terminal integrada dentro de VS Code.

### 4. Crear entorno virtual `.venv` desde terminal

- Ejecutar el comando en terminal para crear el entorno virtual:
  ```bash
  python -m venv .venv
5. Activar el entorno virtual
En Windows:

bash
Copiar código
.\.venv\Scripts\activate
En macOS/Linux:

bash
Copiar código
source .venv/bin/activate
6. Activar el intérprete de Python
En VS Code, selecciona el intérprete de Python desde la barra inferior o desde el comando Python: Select Interpreter.

7. Instalar Django
Ejecutar el comando para instalar Django:

bash
Copiar código
pip install django
8. Crear proyecto backend_clinicanutricional sin duplicar carpeta
Ejecutar el comando:

bash
Copiar código
django-admin startproject backend_clinicanutricional
9. Ejecutar servidor en el puerto 8313
Para ejecutar el servidor en el puerto 8313:

bash
Copiar código
python manage.py runserver 8313
10. Copiar y pegar el link en el navegador
El servidor estará disponible en: http://127.0.0.1:8313

11. Crear aplicación app_clinicanutricional
Ejecutar el comando para crear la app:

bash
Copiar código
python manage.py startapp app_clinicanutricional
Modelos en models.py
python
Copiar código
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
    sexo = models.CharField(max_length=10, choices=[('Femenino', 'Femenino'), ('Masculino', 'Masculino')])
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
Procedimientos adicionales
12. Realizar migraciones (makemigrations y migrate)
Ejecutar los siguientes comandos:

bash
Copiar código
python manage.py makemigrations
python manage.py migrate
13. Trabajar con el modelo: NUTRIÓLOGOS
Crear las vistas y operaciones CRUD para Nutriólogos.

14. Vistas en views.py para operaciones CRUD en Nutriólogos
python
Copiar código
# views.py de app_clinicanutricional
from django.shortcuts import render

def inicio_clinicanutricional(request):
    return render(request, 'inicio.html')

def agregar_nutriologo(request):
    # Código para agregar nutriólogo
    pass

def actualizar_nutriologo(request):
    # Código para actualizar nutriólogo
    pass

def realizar_actualizacion_nutriologo(request):
    # Código para realizar actualización de nutriólogo
    pass

def borrar_nutriologo(request):
    # Código para borrar nutriólogo
    pass
15. Crear la carpeta "templates" dentro de app_clinicanutricional
Estructura de directorios:

css
Copiar código
app_clinicanutricional/
└── templates/
    ├── base.html
    ├── header.html
    ├── navbar.html
    ├── footer.html
    └── inicio.html
16. Agregar Bootstrap en base.html
Incluir los enlaces a Bootstrap para CSS y JS.

17. Crear archivos HTML de la interfaz
navbar.html con opciones de menú.

footer.html con derechos de autor y fecha.

inicio.html con información del sistema.

18. Subcarpeta "nutriologos" dentro de app_clinicanutricional/templates
Estructura de directorios:

markdown
Copiar código
app_clinicanutricional/
└── templates/
    └── nutriologos/
        ├── agregar_nutriologo.html
        ├── ver_nutriologos.html
        ├── actualizar_nutriologo.html
        └── borrar_nutriologo.html
19. Archivos HTML de operaciones CRUD para Nutriólogos
Crear los archivos HTML con su código correspondiente en nutriologos/:

agregar_nutriologo.html

ver_nutriologos.html (mostrar en tabla con los botones ver, editar y borrar)

actualizar_nutriologo.html

borrar_nutriologo.html

20. No utilizar forms.py
Las operaciones CRUD se realizarán sin el uso de formularios en forms.py.

21. Crear archivo urls.py en app_clinicanutricional
Incluir las rutas para acceder a las funciones de views.py para operaciones CRUD en nutriólogos.

22. Agregar app_clinicanutricional en settings.py de backend_clinicanutricional
Registrar la app en el archivo settings.py.

23. Configuración de urls.py de backend_clinicanutricional
Realizar las configuraciones correspondientes para enlazar app_clinicanutricional con el proyecto principal.

24. Registrar modelos en admin.py y realizar las migraciones nuevamente.
Registrar el modelo Nutriólogo en admin.py para poder gestionarlo desde el panel de administración.

Tareas pendientes
Modelo Pacientes y Citas: A trabajar más adelante.

Validación de entrada de datos: No se validará en este momento.

Últimos pasos
Ejecutar servidor en el puerto 8313:

bash
Copiar código
python manage.py runserver 8313
El proyecto debe ser completamente funcional.

¡Creado por: Keyla Paola Palacios Espinoza, Cbtis 128**
