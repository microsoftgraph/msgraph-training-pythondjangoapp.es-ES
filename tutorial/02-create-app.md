<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio usará [Django](https://www.djangoproject.com/) para crear una aplicación Web.

1. Si todavía no tiene instalado Django, puede instalarlo desde la interfaz de línea de comandos (CLI) con el siguiente comando.

    ```Shell
    pip install --user Django==3.1.4
    ```

1. Abra la CLI, vaya a un directorio donde tenga derechos para crear archivos y ejecute el siguiente comando para crear una nueva aplicación de Django.

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. Navegue hasta el directorio **graph_tutorial** y escriba el siguiente comando para iniciar un servidor Web local.

    ```Shell
    python manage.py runserver
    ```

1. Abra el explorador y vaya a `http://localhost:8000`. Si todo funciona, verá una página de bienvenida de Django. Si no lo ve, consulte la [Guía de introducción de Django](https://www.djangoproject.com/start/).

1. Agregue una aplicación al proyecto. Ejecute el siguiente comando en su CLI.

    ```Shell
    python manage.py startapp tutorial
    ```

1. Abra **./graph_tutorial/Settings.py** y agregue la nueva `tutorial` aplicación a la `INSTALLED_APPS` configuración.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. En la CLI, ejecute el siguiente comando para inicializar la base de datos para el proyecto.

    ```Shell
    python manage.py migrate
    ```

1. Agregue un [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para la `tutorial` aplicación. Cree un nuevo archivo en el directorio **./tutorial** denominado `urls.py` y agregue el siguiente código.

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

1. Actualice el proyecto URLconf para importarlo. Abra **./graph_tutorial/URLs.py** y reemplace el contenido por lo siguiente.

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. Agregue una vista temporal a la `tutorials` aplicación para comprobar que el enrutamiento de la dirección URL está funcionando. Abra **./tutorial/views.py** y reemplace todo el contenido por el código siguiente.

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect
    from django.urls import reverse
    from datetime import datetime, timedelta
    from dateutil import tz, parser

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. Guarde todos los cambios y reinicie el servidor. Vaya a `http://localhost:8000` . Debe ver `Welcome to the tutorial.`

## <a name="install-libraries"></a>Instalar bibliotecas

Antes de continuar, instale algunas bibliotecas adicionales que usará más adelante:

- [Biblioteca de autenticación de Microsoft (MSAL) para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) para la administración de los flujos de tokens de inicio de sesión y OAuth.
- [Solicitudes: http para personas](https://requests.readthedocs.io/en/master/) que realizan llamadas a Microsoft Graph.
- [PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para cargar la configuración desde un archivo de YAML.
- [Python-DateUtil](https://pypi.org/project/python-dateutil/) para analizar cadenas de fecha ISO 8601 devueltas de Microsoft Graph.

1. Ejecute el siguiente comando en su CLI.

    ```Shell
    pip install msal==1.7.0
    pip install requests==2.25.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a>Diseñar la aplicación

1. Cree un nuevo directorio en el directorio **./tutorial** denominado `templates` .

1. En el directorio **./tutorial/templates** , cree un nuevo directorio denominado `tutorial` .

1. En el directorio **./tutorial/templates/tutorial** , cree un nuevo archivo denominado `layout.html` . Agregue el siguiente código en ese archivo.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    Este código agrega [bootstrap](http://getbootstrap.com/) para los estilos sencillos y [fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) para algunos iconos sencillos. También define un diseño global con una barra de navegación.

1. Cree un nuevo directorio en el directorio **./tutorial** denominado `static` .

1. En el directorio **./tutorial/static** , cree un nuevo directorio denominado `tutorial` .

1. En el directorio **./tutorial/static/tutorial** , cree un nuevo archivo denominado `app.css` . Agregue el siguiente código en ese archivo.

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. Cree una plantilla para la Página principal que use el diseño. Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `home.html` y agregue el siguiente código.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. Abra el `./tutorial/views.py` archivo y agregue la siguiente función nueva.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. Reemplace la `home` vista existente por lo siguiente.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. Agregue un archivo PNG denominado **no-profile-photo.png** en el directorio **./tutorial/static/tutorial** .

1. Guarde todos los cambios y reinicie el servidor. Ahora, la aplicación debe tener un aspecto muy diferente.

    ![Una captura de pantalla de la Página principal rediseñada](./images/create-app-01.png)
