<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio usará [Django](https://www.djangoproject.com/) para crear una aplicación Web. Si todavía no tiene instalado Django, puede instalarlo desde la interfaz de línea de comandos (CLI) con el siguiente comando.

```Shell
pip install Django=2.2.5
```

Abra la CLI, vaya a un directorio donde tenga derechos para crear archivos y ejecute el siguiente comando para crear una nueva aplicación de Django.

```Shell
django-admin startproject graph_tutorial
```

Django crea un nuevo directorio denominado `graph_tutorial` y scaffolding una aplicación Web de Django. Navegue a este nuevo directorio y escriba el siguiente comando para iniciar un servidor Web local.

```Shell
python manage.py runserver
```

Abra el explorador y vaya a `http://localhost:8000`. Si todo funciona, verá una página de bienvenida de Django. Si no lo ve, consulte la [Guía de introducción de Django](https://www.djangoproject.com/start/).

Ahora que ya ha comprobado el proyecto, agregue una aplicación al proyecto. Ejecute el siguiente comando en su CLI.

```Shell
python manage.py startapp tutorial
```

Se creará una nueva aplicación en `./tutorial` el directorio. Abra el `./graph_tutorial/settings.py` y agregue la nueva `tutorial` aplicación a la `INSTALLED_APPS` configuración.

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

En la CLI, ejecute el siguiente comando para inicializar la base de datos para el proyecto.

```Shell
python manage.py migrate
```

Agregue un [URLconf](https://docs.djangoproject.com/en/2.1/topics/http/urls/) para la `tutorial` aplicación. Cree un nuevo archivo en el `./tutorial` directorio denominado `urls.py` y agregue el siguiente código.

```python
from django.urls import path

from . import views

urlpatterns = [
  # /tutorial
  path('', views.home, name='home'),
]
```

Ahora, actualice el proyecto URLconf para importarlo. Abra el `./graph_tutorial/urls.py` archivo y reemplace el contenido por lo siguiente.

```python
from django.contrib import admin
from django.urls import path, include
from tutorial import views

urlpatterns = [
    path('tutorial/', include('tutorial.urls')),
    path('admin/', admin.site.urls),
]
```

Por último, agregue una vista temporal `tutorials` a la aplicación para comprobar que el enrutamiento de la dirección URL está funcionando. Abra el archivo `./tutorial/views.py` y agregue el siguiente código.

```python
from django.http import HttpResponse, HttpResponseRedirect

def home(request):
  # Temporary!
  return HttpResponse("Welcome to the tutorial.")
```

Guarde todos los cambios y reinicie el servidor. Vaya a `http://localhost:8000/tutorial`. Debe ver`Welcome to the tutorial.`

Antes de continuar, instale algunas bibliotecas adicionales que usará más adelante:

- [Requests-OAuthlib: OAuth para usuarios](https://requests-oauthlib.readthedocs.io/en/latest/) que controlan los flujos de tokens de inicio de sesión y OAuth, y para realizar llamadas a Microsoft Graph.
- [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para cargar la configuración desde un archivo de YAML.
- [Python-DateUtil](https://pypi.org/project/python-dateutil/) para analizar cadenas de fecha ISO 8601 devueltas de Microsoft Graph.

Ejecute el siguiente comando en su CLI.

```Shell
pip install requests_oauthlib==1.2.0
pip install pyyaml==5.1
pip install python-dateutil==2.8.0
```

## <a name="design-the-app"></a>Diseñar la aplicación

Empiece por crear un directorio de plantillas y definir un diseño global para la aplicación. Cree un nuevo directorio en el `./tutorial` directorio denominado `templates`. En el `templates` directorio, cree un nuevo directorio denominado `tutorial`. Por último, cree un archivo nuevo en este directorio `layout.html`denominado. La ruta de acceso relativa de la raíz del proyecto debería `./tutorial/templates/tutorial/layout.html`ser. Agregue el siguiente código en ese archivo.

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

Este código agrega un [bootstrap](http://getbootstrap.com/) para los estilos sencillos y la [fuente maravilla](https://fontawesome.com/) para algunos iconos simples. También define un diseño global con una barra de navegación.

Ahora, cree un nuevo directorio en `./tutorial` el directorio `static`denominado. En el `static` directorio, cree un nuevo directorio denominado `tutorial`. Por último, cree un archivo nuevo en este directorio `app.css`denominado. La ruta de acceso relativa de la raíz del proyecto debería `./tutorial/static/tutorial/app.css`ser. Agregue el siguiente código en ese archivo.

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

A continuación, cree una plantilla para la Página principal que use el diseño. Cree un nuevo archivo en el `./tutorial/templates/tutorial` directorio denominado `home.html` y agregue el siguiente código.

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

Actualice la `home` vista para usar esta plantilla. Abra el `./tutorial/views.py` archivo y agregue la siguiente función nueva.

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

A continuación, reemplace `home` la vista existente por lo siguiente.

```python
def home(request):
  context = initialize_context(request)

  return render(request, 'tutorial/home.html', context)
```

Guarde todos los cambios y reinicie el servidor. Ahora, la aplicación debe tener un aspecto muy diferente.

![Una captura de pantalla de la Página principal rediseñada](./images/create-app-01.png)
