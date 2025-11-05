# 🧺 Sistema de Administración de Lavandería

Autor: Jesús Campoya
Institución: CBTIS 128
Lenguaje: Python
Framework: Django
Editor: Visual Studio Code
Puerto del servidor: 8052

# 🚀 Descripción del Proyecto

Este proyecto tiene como objetivo desarrollar un sistema de administración para una lavandería, utilizando el framework Django.
Permite gestionar clientes, empleados y servicios de forma sencilla, moderna y funcional.

# 💡 En la versión actual se implementa el módulo Cliente, dejando preparados los módulos Empleado y Servicio para futuras expansiones.

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

# ⚙️ Procedimiento Paso a Paso
1️⃣ Crear carpeta del proyecto
mkdir UIII_Lavanderia_0452
cd UIII_Lavanderia_0452

2️⃣ Abrir en Visual Studio Code

Clic derecho → Abrir con Code

O desde terminal:

code .

3️⃣ Abrir la terminal integrada

Menú: Ver → Terminal

4️⃣ Crear entorno virtual
python -m venv .venv

5️⃣ Activar el entorno virtual

Windows:

.venv\Scripts\activate


Linux/Mac:

source .venv/bin/activate

6️⃣ Seleccionar intérprete de Python

Presiona Ctrl + Shift + P → “Seleccionar intérprete de Python” → elige .venv

7️⃣ Instalar Django
pip install django

8️⃣ Crear el proyecto principal
django-admin startproject backend_Lavanderia .

9️⃣ Ejecutar el servidor
python manage.py runserver 8052


# 📎 Abrir en el navegador:
http://127.0.0.1:8052/

# 🧩 Crear la Aplicación Principal
python manage.py startapp app_Lavanderia

# 🧱 Modelos (models.py)
👤 Cliente
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

👨‍🔧 Empleado
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

🧼 Servicio
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

# 🧮 Migraciones
python manage.py makemigrations
python manage.py migrate

🧠 Vistas (views.py)

Funciones principales:

inicio_lavanderia

agregar_cliente

actualizar_cliente

realizar_actualizacion_cliente

borrar_cliente

# 🧰 Archivos HTML
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

# 🎨 Diseño

Framework Bootstrap (CSS y JS)

Colores suaves, atractivos y modernos

Código sencillo y funcional

No validar datos del usuario

Footer fijo:

© Creado por Jesús Campoya — CBTIS 128

# 🗂️ Configuración de URLs

Crear urls.py en app_Lavanderia para rutas CRUD.

Agregar app_Lavanderia en INSTALLED_APPS dentro de settings.py.

Enlazar urls.py del proyecto con el de la aplicación.

# 🔑 Panel de Administración

Registrar los modelos en admin.py y aplicar migraciones:

python manage.py makemigrations
python manage.py migrate

# ✅ Recomendaciones Finales

Comienza con el modelo Cliente.

Deja preparados los módulos Empleado y Servicio.

Crea la estructura completa desde el inicio.

Asegúrate de que el proyecto sea totalmente funcional.

# 🖥️ Ejecución del Servidor
python manage.py runserver 8052


🌐 Abrir en el navegador:
👉 http://127.0.0.1:8052/
