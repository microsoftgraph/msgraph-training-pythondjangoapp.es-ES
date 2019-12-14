<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a62e8-101">En este ejercicio usará [Django](https://www.djangoproject.com/) para crear una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="a62e8-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span> <span data-ttu-id="a62e8-102">Si todavía no tiene instalado Django, puede instalarlo desde la interfaz de línea de comandos (CLI) con el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="a62e8-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

```Shell
pip install Django==3.0
```

<span data-ttu-id="a62e8-103">Abra la CLI, vaya a un directorio donde tenga derechos para crear archivos y ejecute el siguiente comando para crear una nueva aplicación de Django.</span><span class="sxs-lookup"><span data-stu-id="a62e8-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

```Shell
django-admin startproject graph_tutorial
```

<span data-ttu-id="a62e8-104">Django crea un nuevo directorio denominado `graph_tutorial` y scaffolding una aplicación Web de Django.</span><span class="sxs-lookup"><span data-stu-id="a62e8-104">Django creates a new directory called `graph_tutorial` and scaffolds a Django web app.</span></span> <span data-ttu-id="a62e8-105">Navegue a este nuevo directorio y escriba el siguiente comando para iniciar un servidor Web local.</span><span class="sxs-lookup"><span data-stu-id="a62e8-105">Navigate to this new directory and enter the following command to start a local web server.</span></span>

```Shell
python manage.py runserver
```

<span data-ttu-id="a62e8-106">Abra el explorador y vaya a `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="a62e8-106">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="a62e8-107">Si todo funciona, verá una página de bienvenida de Django.</span><span class="sxs-lookup"><span data-stu-id="a62e8-107">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="a62e8-108">Si no lo ve, consulte la [Guía de introducción de Django](https://www.djangoproject.com/start/).</span><span class="sxs-lookup"><span data-stu-id="a62e8-108">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

<span data-ttu-id="a62e8-109">Ahora que ya ha comprobado el proyecto, agregue una aplicación al proyecto.</span><span class="sxs-lookup"><span data-stu-id="a62e8-109">Now that you've verified the project, add an app to the project.</span></span> <span data-ttu-id="a62e8-110">Ejecute el siguiente comando en su CLI.</span><span class="sxs-lookup"><span data-stu-id="a62e8-110">Run the following command in your CLI.</span></span>

```Shell
python manage.py startapp tutorial
```

<span data-ttu-id="a62e8-111">Se creará una nueva aplicación en `./tutorial` el directorio.</span><span class="sxs-lookup"><span data-stu-id="a62e8-111">This creates a new app in the `./tutorial` directory.</span></span> <span data-ttu-id="a62e8-112">Abra el `./graph_tutorial/settings.py` y agregue la nueva `tutorial` aplicación a la `INSTALLED_APPS` configuración.</span><span class="sxs-lookup"><span data-stu-id="a62e8-112">Open the `./graph_tutorial/settings.py` and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tutorial',
]
```

<span data-ttu-id="a62e8-113">En la CLI, ejecute el siguiente comando para inicializar la base de datos para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="a62e8-113">In your CLI, run the following command to initialize the database for the project.</span></span>

```Shell
python manage.py migrate
```

<span data-ttu-id="a62e8-114">Agregue un [URLconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/) para la `tutorial` aplicación.</span><span class="sxs-lookup"><span data-stu-id="a62e8-114">Add a [URLconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="a62e8-115">Cree un nuevo archivo en el `./tutorial` directorio denominado `urls.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="a62e8-115">Create a new file in the `./tutorial` directory named `urls.py` and add the following code.</span></span>

```python
from django.urls import path

from . import views

urlpatterns = [
  # /tutorial
  path('', views.home, name='home'),
]
```

<span data-ttu-id="a62e8-116">Ahora, actualice el proyecto URLconf para importarlo.</span><span class="sxs-lookup"><span data-stu-id="a62e8-116">Now update the project URLconf to import this one.</span></span> <span data-ttu-id="a62e8-117">Abra el `./graph_tutorial/urls.py` archivo y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="a62e8-117">Open the `./graph_tutorial/urls.py` file and replace the contents with the following.</span></span>

```python
from django.contrib import admin
from django.urls import path, include
from tutorial import views

urlpatterns = [
    path('tutorial/', include('tutorial.urls')),
    path('admin/', admin.site.urls),
]
```

<span data-ttu-id="a62e8-118">Por último, agregue una vista temporal `tutorials` a la aplicación para comprobar que el enrutamiento de la dirección URL está funcionando.</span><span class="sxs-lookup"><span data-stu-id="a62e8-118">Finally add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="a62e8-119">Abra el archivo `./tutorial/views.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="a62e8-119">Open the `./tutorial/views.py` file and add the following code.</span></span>

```python
from django.shortcuts import render
from django.http import HttpResponse, HttpResponseRedirect

def home(request):
  # Temporary!
  return HttpResponse("Welcome to the tutorial.")
```

<span data-ttu-id="a62e8-120">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="a62e8-120">Save all of your changes and restart the server.</span></span> <span data-ttu-id="a62e8-121">Vaya a `http://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a62e8-121">Browse to `http://localhost:8000/tutorial`.</span></span> <span data-ttu-id="a62e8-122">Debe ver`Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="a62e8-122">You should see `Welcome to the tutorial.`</span></span>

<span data-ttu-id="a62e8-123">Antes de continuar, instale algunas bibliotecas adicionales que usará más adelante:</span><span class="sxs-lookup"><span data-stu-id="a62e8-123">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="a62e8-124">[Requests-OAuthlib: OAuth para usuarios](https://requests-oauthlib.readthedocs.io/en/latest/) que controlan los flujos de tokens de inicio de sesión y OAuth, y para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a62e8-124">[Requests-OAuthlib: OAuth for Humans](https://requests-oauthlib.readthedocs.io/en/latest/) for handling sign-in and OAuth token flows, and for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="a62e8-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para cargar la configuración desde un archivo de YAML.</span><span class="sxs-lookup"><span data-stu-id="a62e8-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="a62e8-126">[Python-DateUtil](https://pypi.org/project/python-dateutil/) para analizar cadenas de fecha ISO 8601 devueltas de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="a62e8-126">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

<span data-ttu-id="a62e8-127">Ejecute el siguiente comando en su CLI.</span><span class="sxs-lookup"><span data-stu-id="a62e8-127">Run the following command in your CLI.</span></span>

```Shell
pip install requests_oauthlib==1.3.0
pip install pyyaml==5.2
pip install python-dateutil==2.8.1
```

## <a name="design-the-app"></a><span data-ttu-id="a62e8-128">Diseñar la aplicación</span><span class="sxs-lookup"><span data-stu-id="a62e8-128">Design the app</span></span>

<span data-ttu-id="a62e8-129">Empiece por crear un directorio de plantillas y definir un diseño global para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a62e8-129">Start by creating a templates directory and defining a global layout for the app.</span></span> <span data-ttu-id="a62e8-130">Cree un nuevo directorio en el `./tutorial` directorio denominado `templates`.</span><span class="sxs-lookup"><span data-stu-id="a62e8-130">Create a new directory in the `./tutorial` directory named `templates`.</span></span> <span data-ttu-id="a62e8-131">En el `templates` directorio, cree un nuevo directorio denominado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a62e8-131">In the `templates` directory, create a new directory named `tutorial`.</span></span> <span data-ttu-id="a62e8-132">Por último, cree un archivo nuevo en este directorio `layout.html`denominado.</span><span class="sxs-lookup"><span data-stu-id="a62e8-132">Finally, create a new file in this directory named `layout.html`.</span></span> <span data-ttu-id="a62e8-133">La ruta de acceso relativa de la raíz del proyecto debería `./tutorial/templates/tutorial/layout.html`ser.</span><span class="sxs-lookup"><span data-stu-id="a62e8-133">The relative path from the root of your project should be `./tutorial/templates/tutorial/layout.html`.</span></span> <span data-ttu-id="a62e8-134">Agregue el siguiente código en ese archivo.</span><span class="sxs-lookup"><span data-stu-id="a62e8-134">Add the following code in that file.</span></span>

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Python Graph Tutorial</title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
      integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
      integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    {% load static %}
    <link rel="stylesheet" href="{% static "tutorial/app.css" %}">
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
      integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
      integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <a href="{% url 'home' %}" class="navbar-brand">Python Graph Tutorial</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
          aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a href="{% url 'home' %}" class="nav-link{% if request.resolver_match.view_name == 'home' %} active{% endif %}">Home</a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item" data-turbolinks="false">
                <a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>
              </li>
            {% endif %}
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            {% if user.is_authenticated %}
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                  {% if user.avatar %}
                    <img src="{{ user.avatar }}" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  {% else %}
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  {% endif %}
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0">{{ user.name }}</h5>
                  <p class="dropdown-item-text text-muted mb-0">{{ user.email }}</p>
                  <div class="dropdown-divider"></div>
                  <a href="#" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            {% else %}
              <li class="nav-item">
                <a href="#" class="nav-link">Sign In</a>
              </li>
            {% endif %}
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      {% if errors %}
        {% for error in errors %}
          <div class="alert alert-danger" role="alert">
            <p class="mb-3">{{ error.message }}</p>
            {% if error.debug %}
              <pre class="alert-pre border bg-light p-2"><code>{{ error.debug }}</code></pre>
            {% endif %}
          </div>
        {% endfor %}
      {% endif %}
      {% block content %}{% endblock %}
    </main>
  </body>
</html>
```

<span data-ttu-id="a62e8-135">Este código agrega un [bootstrap](http://getbootstrap.com/) para los estilos sencillos y la [fuente maravilla](https://fontawesome.com/) para algunos iconos simples.</span><span class="sxs-lookup"><span data-stu-id="a62e8-135">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="a62e8-136">También define un diseño global con una barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="a62e8-136">It also defines a global layout with a nav bar.</span></span>

<span data-ttu-id="a62e8-137">Ahora, cree un nuevo directorio en `./tutorial` el directorio `static`denominado.</span><span class="sxs-lookup"><span data-stu-id="a62e8-137">Now create a new directory in the `./tutorial` directory named `static`.</span></span> <span data-ttu-id="a62e8-138">En el `static` directorio, cree un nuevo directorio denominado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="a62e8-138">In the `static` directory, create a new directory named `tutorial`.</span></span> <span data-ttu-id="a62e8-139">Por último, cree un archivo nuevo en este directorio `app.css`denominado.</span><span class="sxs-lookup"><span data-stu-id="a62e8-139">Finally, create a new file in this directory named `app.css`.</span></span> <span data-ttu-id="a62e8-140">La ruta de acceso relativa de la raíz del proyecto debería `./tutorial/static/tutorial/app.css`ser.</span><span class="sxs-lookup"><span data-stu-id="a62e8-140">The relative path from the root of your project should be `./tutorial/static/tutorial/app.css`.</span></span> <span data-ttu-id="a62e8-141">Agregue el siguiente código en ese archivo.</span><span class="sxs-lookup"><span data-stu-id="a62e8-141">Add the following code in that file.</span></span>

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

<span data-ttu-id="a62e8-142">A continuación, cree una plantilla para la Página principal que use el diseño.</span><span class="sxs-lookup"><span data-stu-id="a62e8-142">Next, create a template for the home page that uses the layout.</span></span> <span data-ttu-id="a62e8-143">Cree un nuevo archivo en el `./tutorial/templates/tutorial` directorio denominado `home.html` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="a62e8-143">Create a new file in the `./tutorial/templates/tutorial` directory named `home.html` and add the following code.</span></span>

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<div class="jumbotron">
  <h1>Python Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from Python</p>
  {% if user.is_authenticated %}
    <h4>Welcome {{ user.name }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  {% else %}
    <a href="#" class="btn btn-primary btn-large">Click here to sign in</a>
  {% endif %}
</div>
{% endblock %}
```

<span data-ttu-id="a62e8-144">Actualice la `home` vista para usar esta plantilla.</span><span class="sxs-lookup"><span data-stu-id="a62e8-144">Update the `home` view to use this template.</span></span> <span data-ttu-id="a62e8-145">Abra el `./tutorial/views.py` archivo y agregue la siguiente función nueva.</span><span class="sxs-lookup"><span data-stu-id="a62e8-145">Open the `./tutorial/views.py` file and add the following new function.</span></span>

```python
def initialize_context(request):
  context = {}

  # Check for any errors in the session
  error = request.session.pop('flash_error', None)

  if error != None:
    context['errors'] = []
    context['errors'].append(error)

  # Check for user in the session
  context['user'] = request.session.get('user', {'is_authenticated': False})
  return context
```

<span data-ttu-id="a62e8-146">A continuación, reemplace `home` la vista existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="a62e8-146">Then replace the existing `home` view with the following.</span></span>

```python
def home(request):
  context = initialize_context(request)

  return render(request, 'tutorial/home.html', context)
```

<span data-ttu-id="a62e8-147">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="a62e8-147">Save all of your changes and restart the server.</span></span> <span data-ttu-id="a62e8-148">Ahora, la aplicación debe tener un aspecto muy diferente.</span><span class="sxs-lookup"><span data-stu-id="a62e8-148">Now, the app should look very different.</span></span>

![Una captura de pantalla de la Página principal rediseñada](./images/create-app-01.png)
