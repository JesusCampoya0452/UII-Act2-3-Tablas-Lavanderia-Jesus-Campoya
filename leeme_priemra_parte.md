🧺 Proyecto Django: Sistema de Administración de Lavandería

Autor: Jesús Campoya
Institución: CBTIS 128
Lenguaje: Python
Framework: Django
Editor: Visual Studio Code
Puerto del servidor: 8052

🚀 Descripción del Proyecto

Este proyecto tiene como objetivo desarrollar un sistema de administración para una lavandería utilizando Django, que permita gestionar clientes, empleados y servicios de forma sencilla, moderna y funcional.

El sistema inicia con el módulo de Cliente, dejando los módulos Empleado y Servicio preparados para su futura implementación.

📁 Estructura del Proyecto
UIII_Lavanderia_0452/
│
├── backend_Lavanderia/
│   ├── backend_Lavanderia/
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   ├── asgi.py
│   │   └── wsgi.py
│   ├── manage.py
│   └── db.sqlite3
│
└── app_Lavanderia/
    ├── migrations/
    ├── templates/
    │   ├── base.html
    │   ├── header.html
    │   ├── navbar.html
    │   ├── footer.html
    │   ├── inicio.html
    │   └── clientes/
    │       ├── agregar_cliente.html
    │       ├── ver_cliente.html
    │       ├── actualizar_cliente.html
    │       └── borrar_cliente.html
    ├── models.py
    ├── views.py
    ├── urls.py
    └── admin.py

⚙️ Procedimiento Paso a Paso
1️⃣ Crear la carpeta del proyecto
mkdir UIII_Lavanderia_0452
cd UIII_Lavanderia_0452

2️⃣ Abrir la carpeta en Visual Studio Code

Clic derecho sobre la carpeta → "Abrir con Code"

O desde terminal:

code .

3️⃣ Abrir la terminal integrada en VS Code

Menú: Ver → Terminal

4️⃣ Crear el entorno virtual
python -m venv .venv

5️⃣ Activar el entorno virtual
# En Windows
.venv\Scripts\activate

# En Linux/Mac
source .venv/bin/activate

6️⃣ Seleccionar el intérprete de Python

Presiona Ctrl + Shift + P

Escribe: "Seleccionar intérprete de Python"

Elige el que corresponde a .venv

7️⃣ Instalar Django
pip install django

8️⃣ Crear el proyecto principal (sin duplicar carpetas)
django-admin startproject backend_Lavanderia .

9️⃣ Ejecutar el servidor en el puerto 8052
python manage.py runserver 8052

🔗 Abrir el enlace en el navegador
http://127.0.0.1:8052/

🧩 Crear la aplicación principal
python manage.py startapp app_Lavanderia

🧱 Modelos (models.py)
from django.db import models

# ==========================
# MODELO CLIENTE
# ==========================
class Cliente(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    telefono = models.CharField(max_length=15)
    correo = models.EmailField()
    direccion = models.CharField(max_length=200)
    fecha_registro = models.DateField(auto_now_add=True)
    notas = models.TextField(blank=True, null=True)

    def __str__(self):
        return f"{self.nombre} {self.apellido}"


# ==========================
# MODELO EMPLEADO
# ==========================
class Empleado(models.Model):
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    puesto = models.CharField(max_length=100)
    telefono = models.CharField(max_length=15)
    correo = models.EmailField()
    fecha_contratacion = models.DateField()
    salario = models.DecimalField(max_digits=8, decimal_places=2)

    def __str__(self):
        return f"{self.nombre} ({self.puesto})"


# ==========================
# MODELO SERVICIO
# ==========================
class Servicio(models.Model):
    cliente = models.ForeignKey(Cliente, on_delete=models.CASCADE, related_name='servicios')
    empleados = models.ManyToManyField(Empleado, related_name='servicios')
    tipo_servicio = models.CharField(max_length=100)
    fecha_servicio = models.DateField()
    hora_entrega = models.TimeField()
    descripcion = models.TextField()
    precio = models.DecimalField(max_digits=8, decimal_places=2)
    estado = models.CharField(
        max_length=50,
        choices=[
            ('Pendiente', 'Pendiente'),
            ('En proceso', 'En proceso'),
            ('Completado', 'Completado')
        ],
        default='Pendiente'
    )
    forma_pago = models.CharField(max_length=50, default='Efectivo')

    def __str__(self):
        return f"{self.tipo_servicio} - {self.cliente.nombre}"

🧮 Migraciones
python manage.py makemigrations
python manage.py migrate

🧠 Vistas (views.py)

Crear las siguientes funciones:

inicio_lavanderia

agregar_cliente

actualizar_cliente

realizar_actualizacion_cliente

borrar_cliente

🧰 Archivos HTML
📂 Estructura
app_Lavanderia/
└── templates/
    ├── base.html
    ├── header.html
    ├── navbar.html
    ├── footer.html
    ├── inicio.html
    └── clientes/
        ├── agregar_cliente.html
        ├── ver_cliente.html
        ├── actualizar_cliente.html
        └── borrar_cliente.html

🎨 Instrucciones de diseño

Usar Bootstrap para CSS y JS.

Colores suaves, atractivos y modernos.

No validar entrada de datos.

Código sencillo y funcional.

Mantener el footer fijo con derechos de autor:

© Creado por Jesús Campoya — CBTIS 128

🗂️ Configuración de URLs

Crear urls.py en app_Lavanderia para las rutas CRUD.

Agregar app_Lavanderia en INSTALLED_APPS dentro de settings.py.

Enlazar urls.py del proyecto con el de la aplicación.

🔑 Panel de Administración

Registrar los modelos en admin.py y volver a ejecutar migraciones:

python manage.py makemigrations
python manage.py migrate

✅ Recomendaciones Finales

Trabajar primero con el modelo Cliente.

Dejar pendientes Empleado y Servicio.

Crear la estructura completa de carpetas desde el inicio.

Asegurarse de que el proyecto sea totalmente funcional.

🖥️ Ejecutar el Servidor
python manage.py runserver 8052


Abrir en el navegador:
👉 http://127.0.0.1:8052/ http://127.0.0.1:8052/.
