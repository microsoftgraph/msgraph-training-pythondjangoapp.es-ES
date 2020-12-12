<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fa0af-101">En este ejercicio usará [Django](https://www.djangoproject.com/) para crear una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="fa0af-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="fa0af-102">Si todavía no tiene instalado Django, puede instalarlo desde la interfaz de línea de comandos (CLI) con el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="fa0af-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install --user Django==3.1.4
    ```

1. <span data-ttu-id="fa0af-103">Abra la CLI, vaya a un directorio donde tenga derechos para crear archivos y ejecute el siguiente comando para crear una nueva aplicación de Django.</span><span class="sxs-lookup"><span data-stu-id="fa0af-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="fa0af-104">Navegue hasta el directorio **graph_tutorial** y escriba el siguiente comando para iniciar un servidor Web local.</span><span class="sxs-lookup"><span data-stu-id="fa0af-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="fa0af-105">Abra el explorador y vaya a `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="fa0af-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="fa0af-106">Si todo funciona, verá una página de bienvenida de Django.</span><span class="sxs-lookup"><span data-stu-id="fa0af-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="fa0af-107">Si no lo ve, consulte la [Guía de introducción de Django](https://www.djangoproject.com/start/).</span><span class="sxs-lookup"><span data-stu-id="fa0af-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="fa0af-108">Agregue una aplicación al proyecto.</span><span class="sxs-lookup"><span data-stu-id="fa0af-108">Add an app to the project.</span></span> <span data-ttu-id="fa0af-109">Ejecute el siguiente comando en su CLI.</span><span class="sxs-lookup"><span data-stu-id="fa0af-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="fa0af-110">Abra **./graph_tutorial/Settings.py** y agregue la nueva `tutorial` aplicación a la `INSTALLED_APPS` configuración.</span><span class="sxs-lookup"><span data-stu-id="fa0af-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="fa0af-111">En la CLI, ejecute el siguiente comando para inicializar la base de datos para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="fa0af-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="fa0af-112">Agregue un [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para la `tutorial` aplicación.</span><span class="sxs-lookup"><span data-stu-id="fa0af-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="fa0af-113">Cree un nuevo archivo en el directorio **./tutorial** denominado `urls.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="fa0af-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="fa0af-114">Actualice el proyecto URLconf para importarlo.</span><span class="sxs-lookup"><span data-stu-id="fa0af-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="fa0af-115">Abra **./graph_tutorial/URLs.py** y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="fa0af-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="fa0af-116">Agregue una vista temporal a la `tutorials` aplicación para comprobar que el enrutamiento de la dirección URL está funcionando.</span><span class="sxs-lookup"><span data-stu-id="fa0af-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="fa0af-117">Abra **./tutorial/views.py** y reemplace todo el contenido por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="fa0af-117">Open **./tutorial/views.py** and replace its entire contents with the following code.</span></span>

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

1. <span data-ttu-id="fa0af-118">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="fa0af-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="fa0af-119">Vaya a `http://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="fa0af-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="fa0af-120">Debe ver `Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="fa0af-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="fa0af-121">Instalar bibliotecas</span><span class="sxs-lookup"><span data-stu-id="fa0af-121">Install libraries</span></span>

<span data-ttu-id="fa0af-122">Antes de continuar, instale algunas bibliotecas adicionales que usará más adelante:</span><span class="sxs-lookup"><span data-stu-id="fa0af-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="fa0af-123">[Biblioteca de autenticación de Microsoft (MSAL) para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) para la administración de los flujos de tokens de inicio de sesión y OAuth.</span><span class="sxs-lookup"><span data-stu-id="fa0af-123">[Microsoft Authentication Library (MSAL) for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) for handling sign-in and OAuth token flows.</span></span>
- <span data-ttu-id="fa0af-124">[Solicitudes: http para personas](https://requests.readthedocs.io/en/master/) que realizan llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="fa0af-124">[Requests: HTTP for Humans](https://requests.readthedocs.io/en/master/) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="fa0af-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para cargar la configuración desde un archivo de YAML.</span><span class="sxs-lookup"><span data-stu-id="fa0af-125">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="fa0af-126">[Python-DateUtil](https://pypi.org/project/python-dateutil/) para analizar cadenas de fecha ISO 8601 devueltas de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="fa0af-126">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="fa0af-127">Ejecute el siguiente comando en su CLI.</span><span class="sxs-lookup"><span data-stu-id="fa0af-127">Run the following command in your CLI.</span></span>

    ```Shell
    pip install msal==1.7.0
    pip install requests==2.25.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="fa0af-128">Diseñar la aplicación</span><span class="sxs-lookup"><span data-stu-id="fa0af-128">Design the app</span></span>

1. <span data-ttu-id="fa0af-129">Cree un nuevo directorio en el directorio **./tutorial** denominado `templates` .</span><span class="sxs-lookup"><span data-stu-id="fa0af-129">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="fa0af-130">En el directorio **./tutorial/templates** , cree un nuevo directorio denominado `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="fa0af-130">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="fa0af-131">En el directorio **./tutorial/templates/tutorial** , cree un nuevo archivo denominado `layout.html` .</span><span class="sxs-lookup"><span data-stu-id="fa0af-131">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="fa0af-132">Agregue el siguiente código en ese archivo.</span><span class="sxs-lookup"><span data-stu-id="fa0af-132">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="fa0af-133">Este código agrega [bootstrap](http://getbootstrap.com/) para los estilos sencillos y [fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) para algunos iconos sencillos.</span><span class="sxs-lookup"><span data-stu-id="fa0af-133">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Fabric Core](https://developer.microsoft.com/fluentui#/get-started#fabric-core) for some simple icons.</span></span> <span data-ttu-id="fa0af-134">También define un diseño global con una barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="fa0af-134">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="fa0af-135">Cree un nuevo directorio en el directorio **./tutorial** denominado `static` .</span><span class="sxs-lookup"><span data-stu-id="fa0af-135">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="fa0af-136">En el directorio **./tutorial/static** , cree un nuevo directorio denominado `tutorial` .</span><span class="sxs-lookup"><span data-stu-id="fa0af-136">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="fa0af-137">En el directorio **./tutorial/static/tutorial** , cree un nuevo archivo denominado `app.css` .</span><span class="sxs-lookup"><span data-stu-id="fa0af-137">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="fa0af-138">Agregue el siguiente código en ese archivo.</span><span class="sxs-lookup"><span data-stu-id="fa0af-138">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="fa0af-139">Cree una plantilla para la Página principal que use el diseño.</span><span class="sxs-lookup"><span data-stu-id="fa0af-139">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="fa0af-140">Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `home.html` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="fa0af-140">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="fa0af-141">Abra el `./tutorial/views.py` archivo y agregue la siguiente función nueva.</span><span class="sxs-lookup"><span data-stu-id="fa0af-141">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="fa0af-142">Reemplace la `home` vista existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="fa0af-142">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="fa0af-143">Agregue un archivo PNG denominado **no-profile-photo.png** en el directorio **./tutorial/static/tutorial** .</span><span class="sxs-lookup"><span data-stu-id="fa0af-143">Add a PNG file named **no-profile-photo.png** in the **./tutorial/static/tutorial** directory.</span></span>

1. <span data-ttu-id="fa0af-144">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="fa0af-144">Save all of your changes and restart the server.</span></span> <span data-ttu-id="fa0af-145">Ahora, la aplicación debe tener un aspecto muy diferente.</span><span class="sxs-lookup"><span data-stu-id="fa0af-145">Now, the app should look very different.</span></span>

    ![Una captura de pantalla de la Página principal rediseñada](./images/create-app-01.png)
