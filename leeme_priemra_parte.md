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

# 📁 Estructura del Proyecto
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
                ├── agregar_cliente.html
                ├── ver_cliente.html
                ├── actualizar_cliente.html
                └── borrar_cliente.html
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

# 🧩 Crear la Aplicación Principal

    python manage.py startapp app_Lavanderia

# 🧱 Modelos (models.py)

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

    # views.py
    from django.shortcuts import render, get_object_or_404, redirect
    from django.urls import reverse
    from .models import Cliente, Empleado, Servicio
    from .forms import ClienteForm, EmpleadoForm, ServicioForm  # Crea estos forms.py
    from django.contrib import messages
    
    
    # ---------------------------
    # VISTAS CLIENTE
    # ---------------------------
    
    def lista_clientes(request):
        """Mostrar todos los clientes registrados."""
        clientes = Cliente.objects.all().order_by('-fecha_registro')
        return render(request, 'clientes/lista_clientes.html', {'clientes': clientes})
    
    
    def agregar_cliente(request):
        """Agregar un nuevo cliente."""
        if request.method == 'POST':
            form = ClienteForm(request.POST)
            if form.is_valid():
                form.save()
                messages.success(request, "Cliente agregado correctamente.")
                return redirect('lista_clientes')
        else:
            form = ClienteForm()
        return render(request, 'clientes/agregar_cliente.html', {'form': form})
    
    
    def ver_cliente(request, cliente_id):
        """Ver los detalles de un cliente específico."""
        cliente = get_object_or_404(Cliente, id=cliente_id)
        return render(request, 'clientes/ver_cliente.html', {'cliente': cliente})
    
    
    def actualizar_cliente(request, cliente_id):
        """Actualizar la información de un cliente existente."""
        cliente = get_object_or_404(Cliente, id=cliente_id)
        if request.method == 'POST':
            form = ClienteForm(request.POST, instance=cliente)
            if form.is_valid():
                form.save()
                messages.success(request, "Cliente actualizado correctamente.")
                return redirect('ver_cliente', cliente_id=cliente.id)
        else:
            form = ClienteForm(instance=cliente)
        return render(request, 'clientes/actualizar_cliente.html', {'form': form, 'cliente': cliente})
    
    
    def borrar_cliente(request, cliente_id):
        """Eliminar un cliente del sistema."""
        cliente = get_object_or_404(Cliente, id=cliente_id)
        if request.method == 'POST':
            cliente.delete()
            messages.warning(request, "Cliente eliminado correctamente.")
            return redirect('lista_clientes')
        return render(request, 'clientes/borrar_cliente.html', {'cliente': cliente})
    
    
    # ---------------------------
    # VISTAS EMPLEADO (Opcional para continuar)
    # ---------------------------
    
    def lista_empleados(request):
        empleados = Empleado.objects.all().order_by('nombre')
        return render(request, 'empleados/lista_empleados.html', {'empleados': empleados})
    
    
    def agregar_empleado(request):
        if request.method == 'POST':
            form = EmpleadoForm(request.POST)
            if form.is_valid():
                form.save()
                messages.success(request, "Empleado agregado correctamente.")
                return redirect('lista_empleados')
        else:
            form = EmpleadoForm()
        return render(request, 'empleados/agregar_empleado.html', {'form': form})
    
    
    # ---------------------------
    # VISTAS SERVICIO (Opcional para expandir)
    # ---------------------------
    
    def lista_servicios(request):
        servicios = Servicio.objects.select_related('cliente').prefetch_related('empleados')
        return render(request, 'servicios/lista_servicios.html', {'servicios': servicios})
    
    
    def agregar_servicio(request):
        if request.method == 'POST':
            form = ServicioForm(request.POST)
            if form.is_valid():
                form.save()
                messages.success(request, "Servicio registrado correctamente.")
                return redirect('lista_servicios')
        else:
            form = ServicioForm()
        return render(request, 'servicios/agregar_servicio.html', {'form': form})

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

# Agregar_cliente.html 
    <html lang="es">
    <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Agregar Cliente</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary mb-4">
    <div class="container-fluid">
    <a class="navbar-brand" href="{% url 'lista_clientes' %}">Lavandería</a>
    </div>
    </nav>
    <div class="container">
    <h1 class="h3 mb-3">Agregar Cliente</h1>
    
    
    <form method="post" novalidate>
    {% csrf_token %}
    {% for field in form %}
    <div class="mb-3">
    <label class="form-label">{{ field.label_tag }}{% if field.field.required %} <span class="text-danger">*</span>{% endif %}</label>
    {{ field }}
    {% if field.help_text %}<div class="form-text">{{ field.help_text }}</div>{% endif %}
    {% for error in field.errors %}
    <div class="text-danger small">{{ error }}</div>
    {% endfor %}
    </div>
    {% endfor %}
    
    
    <button type="submit" class="btn btn-success">Guardar</button>
    <a href="{% url 'lista_clientes' %}" class="btn btn-secondary">Cancelar</a>
    </form>
    </div>
    
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>

# ver_cliente.html 
    <html lang="es">
    <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Ver Cliente</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary mb-4">
    <div class="container-fluid">
    <a class="navbar-brand" href="{% url 'lista_clientes' %}">Lavandería</a>
    </div>
    </nav>
    <div class="container">
    <h1 class="h3 mb-3">Detalle de Cliente</h1>
    
    
    <div class="card mb-3">
    <div class="card-body">
    <h5 class="card-title">{{ cliente.nombre }}</h5>
    <p class="card-text"><strong>Email:</strong> {{ cliente.email }}</p>
    <p class="card-text"><strong>Teléfono:</strong> {{ cliente.telefono }}</p>
    <p class="card-text"><strong>Dirección:</strong> {{ cliente.direccion }}</p>
    {% if cliente.observaciones %}
    <p class="card-text"><strong>Observaciones:</strong> {{ cliente.observaciones }}</p>
    {% endif %}
    </div>
    <div class="card-footer">
    <a href="{% url 'actualizar_cliente' cliente.id %}" class="btn btn-primary">Editar</a>
    <a href="{% url 'borrar_cliente' cliente.id %}" class="btn btn-danger">Eliminar</a>
    <a href="{% url 'lista_clientes' %}" class="btn btn-secondary">Volver</a>
    </div>
    </div>
    </div>
    
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>

# actualizar_cliente.html
    <!DOCTYPE html>
    <html lang="es">
    <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Actualizar Cliente</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary mb-4">
    <div class="container-fluid">
    <a class="navbar-brand" href="{% url 'lista_clientes' %}">Lavandería</a>
    </div>
    </nav>
    <div class="container">
    <h1 class="h3 mb-3">Actualizar Cliente</h1>
    
    
    <form method="post" novalidate>
    {% csrf_token %}
    {% for field in form %}
    <div class="mb-3">
    <label class="form-label">{{ field.label_tag }}</label>
    {{ field }}
    {% for error in field.errors %}
    <div class="text-danger small">{{ error }}</div>
    {% endfor %}
    </div>
    {% endfor %}
    
    
    <button type="submit" class="btn btn-primary">Actualizar</button>
    <a href="{% url 'ver_cliente' cliente.id %}" class="btn btn-secondary">Cancelar</a>
    </form>
    </div>
    
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>

# borrar_cliente.html
    <html lang="es">
    <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Eliminar Cliente</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    </head>
    <body>
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary mb-4">
    <div class="container-fluid">
    <a class="navbar-brand" href="{% url 'lista_clientes' %}">Lavandería</a>
    </div>
    </nav>
    <div class="container">
    <h1 class="h3 mb-3 text-danger">Eliminar Cliente</h1>
    
    
    <div class="alert alert-warning">¿Estás seguro que deseas eliminar al cliente <strong>{{ cliente.nombre }}</strong>?</div>
    
    
    <form method="post">
    {% csrf_token %}
    <button type="submit" class="btn btn-danger">Sí, eliminar</button>
    <a href="{% url 'ver_cliente' cliente.id %}" class="btn btn-secondary">No, cancelar</a>
    </form>
    </div>
    
    
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>

# style.css

    :root {
        --color-primario: #0069d9;
        --color-secundario: #f8f9fa;
        --color-acento: #20c997;
        --color-peligro: #dc3545;
        --color-texto: #212529;
        --color-fondo: #f2f6fa;
    }
    
    /* ----------- RESETEO BÁSICO ----------- */
    body {
        font-family: "Poppins", Arial, sans-serif;
        background-color: var(--color-fondo);
        color: var(--color-texto);
        margin: 0;
        padding: 0;
    }
    
    a {
        text-decoration: none;
    }
    
    .container {
        max-width: 900px;
    }
    
    /* ----------- NAVBAR ----------- */
    .navbar {
        box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
    }
    
    .navbar-brand {
        font-weight: 600;
        letter-spacing: 1px;
    }
    
    .navbar-dark .navbar-brand {
        color: #fff !important;
    }
    
    /* ----------- TÍTULOS ----------- */
    h1, h2, h3 {
        font-weight: 600;
        color: var(--color-primario);
    }
    
    /* ----------- BOTONES ----------- */
    .btn {
        border-radius: 6px;
        transition: all 0.2s ease;
    }
    
    .btn:hover {
        transform: translateY(-2px);
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
    }
    
    .btn-success {
        background-color: var(--color-acento);
        border-color: var(--color-acento);
    }
    
    .btn-success:hover {
        background-color: #18a27a;
        border-color: #18a27a;
    }
    
    /* ----------- TABLAS ----------- */
    .table {
        background-color: white;
        border-radius: 10px;
        overflow: hidden;
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    }
    
    .table thead {
        background-color: var(--color-primario);
        color: white;
    }
    
    .table-hover tbody tr:hover {
        background-color: #f1f1f1;
    }
    
    /* ----------- FORMULARIOS ----------- */
    form {
        background-color: white;
        padding: 25px;
        border-radius: 10px;
        box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
    }
    
    .form-label {
        font-weight: 500;
        color: var(--color-primario);
    }
    
    .form-control:focus {
        border-color: var(--color-acento);
        box-shadow: 0 0 4px var(--color-acento);
    }
    
    /* ----------- TARJETAS ----------- */
    .card {
        border-radius: 12px;
        box-shadow: 0 3px 8px rgba(0, 0, 0, 0.1);
    }
    
    .card-title {
        color: var(--color-primario);
        font-weight: 600;
    }
    
    .card-footer {
        background-color: var(--color-secundario);
    }
    
    /* ----------- ALERTAS ----------- */
    .alert-warning {
        background-color: #fff3cd;
        border: 1px solid #ffeeba;
    }
    
    .alert-warning strong {
        color: var(--color-peligro);
    }
    
    /* ----------- PIE DE PÁGINA ----------- */
    footer {
        background-color: var(--color-primario);
        color: white;
        text-align: center;
        padding: 15px 0;
        margin-top: 40px;
        font-size: 0.9rem;
        letter-spacing: 0.5px;
    }
    
    /* ----------- RESPONSIVE ----------- */
    @media (max-width: 768px) {
        .container {
            padding: 10px;
        }
    
        form {
            padding: 15px;
        }
    
        h1 {
            font-size: 1.5rem;
        }
    }

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


# 🌐 Abrir en el navegador:

    http://127.0.0.1:8052/
