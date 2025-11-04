Este proyecto se llama UIII_Lavanderia_0452 e incluye un proyecto de backend (backend_Lavanderia) y una aplicación (app_Lavanderia), enfocándonos inicialmente en el CRUD del modelo Cliente.

📂 Estructura del Proyecto
A continuación, se muestra la estructura de carpetas y archivos, lista para ser replicada en tu entorno de desarrollo (UIII_Lavanderia_0452):

UIII_Lavanderia_0452/
├── .venv/ (Entorno Virtual)
├── manage.py
├── backend_Lavanderia/ (Proyecto Principal)
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── app_Lavanderia/ (Aplicación)
    ├── __init__.py
    ├── admin.py
    ├── migrations/
    ├── models.py
    ├── tests.py
    ├── urls.py  <-- Creado según la instrucción
    ├── views.py
    └── templates/  <-- Creado según la instrucción
        ├── base.html
        ├── footer.html
        ├── header.html
        ├── inicio.html
        ├── navbar.html
        └── clientes/ <-- Creado según la instrucción
            ├── agregar_cliente.html
            ├── actualizar_cliente.html
            ├── borrar_cliente.html
            └── ver_cliente.html
💻 Archivos Clave del Código
Aquí tienes el código para los archivos más importantes, basado en el PDF:

1. app_Lavanderia/models.py
Contiene los modelos Cliente, Empleado, y Servicio.

Python

from django.db import models

# MODELO CLIENTE
class Cliente (models.Model):
    nombre = models.CharField(max_length=100) # Se corrige la sintaxis de la línea 116 del PDF.
    apellido = models.CharField(max_length=100) # Se corrige la sintaxis de la línea 116 del PDF.
    telefono = models.CharField(max_length=15)
    correo = models.EmailField() # Se corrige la sintaxis de la línea 116 del PDF.
    direccion = models.CharField(max_length=200) # Se corrige la sintaxis de la línea 117 del PDF.
    fecha_registro = models.DateField(auto_now_add=True)
    notas = models.TextField(blank=True, null=True)

    def __str__(self): # Se corrige __str__ y la sintaxis de retorno de la línea 119 del PDF.
        return f"{self.nombre} {self.apellido}"

# MODELO EMPLEADO
class Empleado (models.Model):
    nombre = models.CharField(max_length=100) # Se corrige la sintaxis de la línea 122 del PDF.
    apellido = models.CharField(max_length=100) # Se corrige la sintaxis de la línea 123 del PDF.
    puesto = models.CharField(max_length=100) # Se corrige la sintaxis de la línea 124 del PDF.
    telefono = models.CharField(max_length=15)
    correo = models.EmailField() # Se corrige la sintaxis de la línea 124 del PDF.
    fecha_contratacion = models.DateField() # Se corrige la sintaxis de la línea 125 del PDF.
    salario = models.DecimalField(max_digits=8, decimal_places=2) # Se corrige la sintaxis de la línea 126 del PDF.

    def __str__(self): # Se corrige __str__ y la sintaxis de retorno de la línea 129 del PDF.
        return f"{self.nombre} ({self.puesto})"

# MODELO SERVICIO
class Servicio (models.Model):
    # Relación 1:N con Cliente
    cliente = models.ForeignKey(Cliente, on_delete=models.CASCADE, related_name='servicios')
    # Relación N:M con Empleado
    empleados = models.ManyToManyField(Empleado, related_name='servicios')

    TIPO_SERVICIO_CHOICES = [
        ('Lavado', 'Lavado'),
        ('Planchado', 'Planchado'), # Ej: Lavado, Planchado, Tintoreria
        ('Tintoreria', 'Tintoreria'),
    ]
    tipo_servicio = models.CharField(max_length=100, choices=TIPO_SERVICIO_CHOICES)

    estado_choices = [ # Opciones de estado
        ('Pendiente', 'Pendiente'),
        ('En proceso', 'En proceso'),
        ('Completado', 'Completado'), # Se corrige el cierre del paréntesis en línea 147 del PDF.
    ]

    fecha_servicio = models.DateField() #
    hora_entrega = models.TimeField() # Nuevo campo: hora estimada de entrega
    descripcion = models.TextField() #
    precio = models.DecimalField(max_digits=8, decimal_places=2) #
    estado = models.CharField(max_length=50, choices=estado_choices, default='Pendiente') #
    forma_pago = models.CharField(max_length=50, default='Efectivo') # Nuevo campo: método de pago

    def __str__(self): # Se corrige __str__ y la sintaxis de retorno de la línea 154 del PDF.
        return f"{self.tipo_servicio} ({self.cliente.nombre})"
2. app_Lavanderia/views.py
Implementación de las funciones CRUD para Cliente. No se utiliza forms.py.

Python

from django.shortcuts import render, redirect, get_object_or_404
from .models import Cliente # Importamos el modelo Cliente

# Función de inicio
def inicio_lavanderia(request):
    # Esto renderizará app_Lavanderia/templates/inicio.html
    return render(request, 'inicio.html', {'titulo': 'Sistema de Administración Lavandería'})

# CREATE: Mostrar formulario y procesar la adición de un nuevo cliente
def agregar_cliente(request):
    if request.method == 'POST':
        # No se valida la entrada de datos según la instrucción
        Cliente.objects.create(
            nombre=request.POST.get('nombre'),
            apellido=request.POST.get('apellido'),
            telefono=request.POST.get('telefono'),
            correo=request.POST.get('correo'),
            direccion=request.POST.get('direccion'),
            notas=request.POST.get('notas', '') # Notas puede ser opcional
        )
        return redirect('ver_clientes') # Redirigir a la lista de clientes
    
    # Esto renderizará app_Lavanderia/templates/clientes/agregar_cliente.html
    return render(request, 'clientes/agregar_cliente.html')

# READ: Ver todos los clientes en una tabla
def ver_clientes(request):
    clientes = Cliente.objects.all() # Obtener todos los clientes
    # Esto renderizará app_Lavanderia/templates/clientes/ver_cliente.html
    return render(request, 'clientes/ver_cliente.html', {'clientes': clientes})

# UPDATE: Mostrar formulario con datos actuales
def actualizar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    # Esto renderizará app_Lavanderia/templates/clientes/actualizar_cliente.html
    return render(request, 'clientes/actualizar_cliente.html', {'cliente': cliente})

# UPDATE: Procesar la actualización del cliente
def realizar_actualizacion_cliente(request, cliente_id):
    if request.method == 'POST':
        cliente = get_object_or_404(Cliente, pk=cliente_id)
        # Actualizar los campos
        cliente.nombre = request.POST.get('nombre')
        cliente.apellido = request.POST.get('apellido')
        cliente.telefono = request.POST.get('telefono')
        cliente.correo = request.POST.get('correo')
        cliente.direccion = request.POST.get('direccion')
        cliente.notas = request.POST.get('notas', '')
        cliente.save()
        return redirect('ver_clientes')
    # Si no es POST, redirigir al formulario de actualización
    return redirect('actualizar_cliente', cliente_id=cliente_id)

# DELETE: Borrar un cliente
def borrar_cliente(request, cliente_id):
    cliente = get_object_or_404(Cliente, pk=cliente_id)
    if request.method == 'POST':
        cliente.delete()
        return redirect('ver_clientes')
        
    # Mostrar la confirmación de borrado
    # Esto renderizará app_Lavanderia/templates/clientes/borrar_cliente.html
    return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
3. app_Lavanderia/urls.py
Define las rutas para las operaciones CRUD del cliente.

Python

from django.urls import path
from . import views

urlpatterns = [
    path('', views.inicio_lavanderia, name='inicio_lavanderia'), # Ruta principal

    # RUTAS CRUD CLIENTES
    path('clientes/agregar/', views.agregar_cliente, name='agregar_cliente'), #
    path('clientes/ver/', views.ver_clientes, name='ver_clientes'), #
    path('clientes/actualizar/<int:cliente_id>/', views.actualizar_cliente, name='actualizar_cliente'), #
    path('clientes/actualizar/realizar/<int:cliente_id>/', views.realizar_actualizacion_cliente, name='realizar_actualizacion_cliente'), #
    path('clientes/borrar/<int:cliente_id>/', views.borrar_cliente, name='borrar_cliente'), #
]
4. backend_Lavanderia/settings.py
Solo se muestra la parte de configuración necesaria, asumiendo que el resto del archivo tiene la configuración por defecto.

Python

# ... (otras configuraciones)

# Incluir la aplicación 'app_Lavanderia'
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'app_Lavanderia', # La aplicación de la lavandería
]

# ... (otras configuraciones)

# Puerto de ejecución, aunque se usa en el comando, es buena práctica mantener la configuración de Hosts.
# ALLOWED_HOSTS = ['127.0.0.1', 'localhost', '[::1]'] 

# ... (otras configuraciones)
5. backend_Lavanderia/urls.py
Enlaza las rutas principales con las de la aplicación app_Lavanderia.

Python

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    # Enlazar las URLs de la aplicación
    path('', include('app_Lavanderia.urls')),
]
6. app_Lavanderia/admin.py
Registro del modelo Cliente en el panel de administración.

Python

from django.contrib import admin
from .models import Cliente
# from .models import Empleado, Servicio # Se dejan comentados según la instrucción

# Registrar el modelo Cliente
admin.site.register(Cliente)
# admin.site.register(Empleado)
# admin.site.register(Servicio)
🎨 Plantillas (Templates)
Para mantener el código conciso, proporciono solo la estructura de base.html y un ejemplo de un template de cliente.

7. app_Lavanderia/templates/base.html
Incluye Bootstrap para estilos y estructura.

HTML

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Lavandería{% endblock %}</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        /* Colores suaves, atractivos y modernos */
        :root {
            --color-primary: #a3dae9; /* Azul cielo suave */
            --color-secondary: #f0f8ff; /* Blanco azulado */
            --color-text: #333;
        }
        body {
            background-color: var(--color-secondary);
            color: var(--color-text);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
        }
        .footer-fixed {
            position: sticky; /* Mantener fijo al final */
            top: 100vh;
        }
    </style>
</head>
<body>
    {% include 'navbar.html' %} 

    <main class="container my-4 flex-grow-1">
        {% block content %}
        {% endblock %}
    </main>

    {% include 'footer.html' %} 

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
8. app_Lavanderia/templates/navbar.html
Barra de navegación con submenús.

HTML

<nav class="navbar navbar-expand-lg navbar-dark" style="background-color: var(--color-primary);">
    <div class="container-fluid">
        <a class="navbar-brand" href="{% url 'inicio_lavanderia' %}">
            <i class="bi bi-house-door-fill"></i> Sistema de Administración Lavandería </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNavDropdown">
            <ul class="navbar-nav">
                <li class="nav-item">
                    <a class="nav-link" href="{% url 'inicio_lavanderia' %}"><i class="bi bi-house-fill"></i> Inicio</a>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="clienteDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="bi bi-person-circle"></i> Cliente </a>
                    <ul class="dropdown-menu" aria-labelledby="clienteDropdown">
                        <li><a class="dropdown-item" href="{% url 'agregar_cliente' %}">Agregar Cliente</a></li>
                        <li><a class="dropdown-item" href="{% url 'ver_clientes' %}">Ver Cliente</a></li>
                        </ul>
                </li>
                
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="empleadoDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="bi bi-people-fill"></i> Empleado </a>
                    <ul class="dropdown-menu" aria-labelledby="empleadoDropdown">
                        <li><a class="dropdown-item" href="#">Agregar Empleado (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Ver Empleado (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Empleado (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Empleado (Pendiente)</a></li>
                    </ul>
                </li>

                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" id="servicioDropdown" role="button" data-bs-toggle="dropdown" aria-expanded="false">
                        <i class="bi bi-hand-thumbs-up-fill"></i> Servicio </a>
                    <ul class="dropdown-menu" aria-labelledby="servicioDropdown">
                        <li><a class="dropdown-item" href="#">Agregar Servicio (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Ver Servicio (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Actualizar Servicio (Pendiente)</a></li>
                        <li><a class="dropdown-item" href="#">Borrar Servicio (Pendiente)</a></li>
                    </ul>
                </li>
            </ul>
        </div>
    </div>
</nav>
9. app_Lavanderia/templates/footer.html
Pie de página fijo.

HTML

<footer class="footer-fixed mt-auto py-3 bg-light text-center">
    <div class="container">
        <span class="text-muted">
            &copy; Derechos de autor - Sistema Lavandería - {{ "now"|date:"Y" }} | 
            Creado por **Jesus Campoya**, Cbtis 128.
        </span>
    </div>
</footer>
10. app_Lavanderia/templates/inicio.html
Página de inicio.

HTML

{% extends 'base.html' %}

{% block title %}Inicio - Lavandería{% endblock %}

{% block content %}
    <div class="p-5 mb-4 bg-light rounded-3 text-center">
        <div class="container-fluid py-5">
            <h1 class="display-5 fw-bold">Bienvenido al {{ titulo }}</h1>
            <p class="col-md-8 fs-4 mx-auto">
                Este sistema le permitirá administrar los **clientes**, **empleados** y **servicios** de su lavandería de manera eficiente. 
                Utilizando Django y la metodología CRUD.
            </p>
        </div>
    </div>
    
    <div class="row justify-content-center">
        <div class="col-md-8 text-center">
            <img src="https://via.placeholder.com/800x400?text=Imagen+de+Lavanderia+Moderna" class="img-fluid rounded" alt="Imagen de una lavandería moderna"> 
        </div>
    </div>
{% endblock %}
11. app_Lavanderia/templates/clientes/ver_cliente.html
Muestra la lista de clientes con botones de acción.

HTML

{% extends 'base.html' %}

{% block title %}Ver Clientes{% endblock %}

{% block content %}
    <h2 class="mb-4">📋 Listado de Clientes</h2>

    {% if clientes %}
    <div class="table-responsive">
        <table class="table table-striped table-hover">
            <thead class="table-primary">
                <tr>
                    <th>Nombre Completo</th>
                    <th>Teléfono</th>
                    <th>Correo</th>
                    <th>Dirección</th>
                    <th>Fecha de Registro</th>
                    <th class="text-center">Acciones</th> </tr>
            </thead>
            <tbody>
                {% for cliente in clientes %}
                <tr>
                    <td>{{ cliente.nombre }} {{ cliente.apellido }}</td>
                    <td>{{ cliente.telefono }}</td>
                    <td>{{ cliente.correo }}</td>
                    <td>{{ cliente.direccion }}</td>
                    <td>{{ cliente.fecha_registro|date:"d/m/Y" }}</td>
                    <td class="text-center">
                        <button type="button" class="btn btn-sm btn-info" data-bs-toggle="modal" data-bs-target="#notasModal{{ cliente.id }}">
                            <i class="bi bi-eye-fill"></i> Ver
                        </button>
                        <a href="{% url 'actualizar_cliente' cliente.id %}" class="btn btn-sm btn-warning"><i class="bi bi-pencil-fill"></i> Editar</a>
                        <a href="{% url 'borrar_cliente' cliente.id %}" class="btn btn-sm btn-danger"><i class="bi bi-trash-fill"></i> Borrar</a>
                    </td>
                </tr>
                <div class="modal fade" id="notasModal{{ cliente.id }}" tabindex="-1" aria-labelledby="notasModalLabel{{ cliente.id }}" aria-hidden="true">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h5 class="modal-title" id="notasModalLabel{{ cliente.id }}">Detalle de {{ cliente.nombre }}</h5>
                                <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                            </div>
                            <div class="modal-body">
                                <strong>Notas:</strong>
                                <p>{{ cliente.notas|default:"(Sin notas)" }}</p>
                            </div>
                            <div class="modal-footer">
                                <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cerrar</button>
                            </div>
                        </div>
                    </div>
                </div>
                {% endfor %}
            </tbody>
        </table>
    </div>
    {% else %}
    <div class="alert alert-info">No hay clientes registrados.</div>
    {% endif %}
    
    <div class="mt-4">
        <a href="{% url 'agregar_cliente' %}" class="btn btn-primary"><i class="bi bi-plus-circle-fill"></i> Agregar Nuevo Cliente</a>
    </div>

{% endblock %}
Pasos a Seguir para el Proyecto Funcional
Para que el proyecto esté totalmente funcional, debes ejecutar los siguientes comandos en la terminal de VS Code, después de crear la estructura de carpetas y pegar el código:

Crear y activar el entorno virtual:

Bash

python -m venv .venv
# En Windows: .venv\Scripts\activate
# En macOS/Linux: source .venv/bin/activate 
Instalar Django:

Bash

pip install Django
Realizar las migraciones iniciales:

Bash

python manage.py makemigrations app_Lavanderia
python manage.py migrate
Crear un superusuario (para acceder al admin):

Bash

python manage.py createsuperuser
Ejecutar el servidor en el puerto 8052:

Bash

python manage.py runserver 8052
Una vez que el servidor esté corriendo, puedes acceder a la aplicación desde tu navegador en el link: http://127.0.0.1:8052/.
