<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, [usarás Django](https://www.djangoproject.com/) para crear una aplicación web.

1. Si aún no tiene instalado Django, puede instalarlo desde la interfaz de línea de comandos (CLI) con el siguiente comando.

    ```Shell
    pip install Django==3.2
    ```

1. Abre la CLI, navega a un directorio donde tienes derechos para crear archivos y ejecuta el siguiente comando para crear una nueva aplicación de Django.

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. Vaya al directorio **graph_tutorial** y escriba el siguiente comando para iniciar un servidor web local.

    ```Shell
    python manage.py runserver
    ```

1. Abra el explorador y vaya a `http://localhost:8000`. Si todo funciona, verá una página de bienvenida de Django. Si no lo ve, consulte la guía de introducción [a Django](https://www.djangoproject.com/start/).

1. Agregar una aplicación al proyecto. Ejecute el siguiente comando en la CLI.

    ```Shell
    python manage.py startapp tutorial
    ```

1. Abre **./graph_tutorial/settings.py** y agrega la `tutorial` nueva aplicación a la `INSTALLED_APPS` configuración.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. En la CLI, ejecute el siguiente comando para inicializar la base de datos del proyecto.

    ```Shell
    python manage.py migrate
    ```

1. Agrega un [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para la `tutorial` aplicación. Cree un nuevo archivo en el **directorio ./tutorial** denominado `urls.py` y agregue el siguiente código.

    ```python
    from django.urls import path

    from . import views

    urlpatterns = [
      # /
      path('', views.home, name='home'),
      # TEMPORARY
      path('signin', views.home, name='signin'),
      path('signout', views.home, name='signout'),
      path('calendar', views.home, name='calendar'),
    ]
    ```

1. Actualice el proyecto URLconf para importar este. Abra **./graph_tutorial/urls.py** y reemplace el contenido por lo siguiente.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. Agrega una vista temporal a la `tutorials` aplicación para comprobar que el enrutamiento de direcciones URL funciona. Abra **./tutorial/views.py** y reemplace todo su contenido por el siguiente código.

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. Guarde todos los cambios y reinicie el servidor. Vaya a `http://localhost:8000` . Debería ver `Welcome to the tutorial.`

## <a name="install-libraries"></a>Instalar bibliotecas

Antes de seguir adelante, instale algunas bibliotecas adicionales que usará más adelante:

- [Biblioteca de autenticación de Microsoft (MSAL) para Python para](https://github.com/AzureAD/microsoft-authentication-library-for-python) controlar los flujos de tokens de inicio de sesión y OAuth.
- [PyYAML para](https://pyyaml.org/wiki/PyYAMLDocumentation) cargar la configuración desde un archivo YAML.
- [python-dateutil](https://pypi.org/project/python-dateutil/) para analizar las cadenas de fecha ISO 8601 devueltas de Microsoft Graph.

1. Ejecute el siguiente comando en la CLI.

    ```Shell
    pip install msal==1.10.0
    pip install pyyaml==5.4.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>Diseñar la aplicación

1. Cree un nuevo directorio en el **directorio ./tutorial** denominado `templates` .

1. En el **directorio ./tutorial/templates,** cree un nuevo directorio denominado `tutorial` .

1. En el **directorio ./tutorial/templates/tutorial,** cree un nuevo archivo denominado `layout.html` . Agregue el siguiente código en ese archivo.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    Este código agrega [Bootstrap para](http://getbootstrap.com/) un estilo sencillo y [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) para algunos iconos sencillos. También define un diseño global con una barra de navegación.

1. Cree un nuevo directorio en el **directorio ./tutorial** denominado `static` .

1. En el **directorio ./tutorial/static,** cree un nuevo directorio denominado `tutorial` .

1. En el **directorio ./tutorial/static/tutorial,** cree un nuevo archivo denominado `app.css` . Agregue el siguiente código en ese archivo.

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. Cree una plantilla para la página principal que use el diseño. Cree un nuevo archivo en el **directorio ./tutorial/templates/tutorial** denominado `home.html` y agregue el siguiente código.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. Abra el `./tutorial/views.py` archivo y agregue la siguiente función nueva.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. Reemplace la vista `home` existente por lo siguiente.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. Agregue un archivo PNG denominado **no-profile-photo.png** **en el directorio ./tutorial/static/tutorial.**

1. Guarde todos los cambios y reinicie el servidor. Ahora, la aplicación debe tener un aspecto muy diferente.

    ![Una captura de pantalla de la página de inicio rediseñada](./images/create-app-01.png)
