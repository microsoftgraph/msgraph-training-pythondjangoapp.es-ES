<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="75660-101">En este ejercicio usará [Django](https://www.djangoproject.com/) para crear una aplicación Web.</span><span class="sxs-lookup"><span data-stu-id="75660-101">In this exercise you will use [Django](https://www.djangoproject.com/) to build a web app.</span></span>

1. <span data-ttu-id="75660-102">Si todavía no tiene instalado Django, puede instalarlo desde la interfaz de línea de comandos (CLI) con el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="75660-102">If you don't already have Django installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

    ```Shell
    pip install Django==3.0.4
    ```

1. <span data-ttu-id="75660-103">Abra la CLI, vaya a un directorio donde tenga derechos para crear archivos y ejecute el siguiente comando para crear una nueva aplicación de Django.</span><span class="sxs-lookup"><span data-stu-id="75660-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Django app.</span></span>

    ```Shell
    django-admin startproject graph_tutorial
    ```

1. <span data-ttu-id="75660-104">Navegue hasta el directorio **graph_tutorial** y escriba el siguiente comando para iniciar un servidor Web local.</span><span class="sxs-lookup"><span data-stu-id="75660-104">Navigate to the **graph_tutorial** directory and enter the following command to start a local web server.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="75660-105">Abra el explorador y vaya a `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="75660-105">Open your browser and navigate to `http://localhost:8000`.</span></span> <span data-ttu-id="75660-106">Si todo funciona, verá una página de bienvenida de Django.</span><span class="sxs-lookup"><span data-stu-id="75660-106">If everything is working, you will see a Django welcome page.</span></span> <span data-ttu-id="75660-107">Si no lo ve, consulte la [Guía de introducción de Django](https://www.djangoproject.com/start/).</span><span class="sxs-lookup"><span data-stu-id="75660-107">If you don't see that, check the [Django getting started guide](https://www.djangoproject.com/start/).</span></span>

1. <span data-ttu-id="75660-108">Agregue una aplicación al proyecto.</span><span class="sxs-lookup"><span data-stu-id="75660-108">Add an app to the project.</span></span> <span data-ttu-id="75660-109">Ejecute el siguiente comando en su CLI.</span><span class="sxs-lookup"><span data-stu-id="75660-109">Run the following command in your CLI.</span></span>

    ```Shell
    python manage.py startapp tutorial
    ```

1. <span data-ttu-id="75660-110">Abra **./graph_tutorial/Settings.py** y agregue la nueva `tutorial` aplicación a la `INSTALLED_APPS` configuración.</span><span class="sxs-lookup"><span data-stu-id="75660-110">Open **./graph_tutorial/settings.py** and add the new `tutorial` app to the `INSTALLED_APPS` setting.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/settings.py" id="InstalledAppsSnippet" highlight="8":::

1. <span data-ttu-id="75660-111">En la CLI, ejecute el siguiente comando para inicializar la base de datos para el proyecto.</span><span class="sxs-lookup"><span data-stu-id="75660-111">In your CLI, run the following command to initialize the database for the project.</span></span>

    ```Shell
    python manage.py migrate
    ```

1. <span data-ttu-id="75660-112">Agregue un [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) para la `tutorial` aplicación.</span><span class="sxs-lookup"><span data-stu-id="75660-112">Add a [URLconf](https://docs.djangoproject.com/en/3.0/topics/http/urls/) for the `tutorial` app.</span></span> <span data-ttu-id="75660-113">Cree un nuevo archivo en el directorio **./tutorial** denominado `urls.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="75660-113">Create a new file in the **./tutorial** directory named `urls.py` and add the following code.</span></span>

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

1. <span data-ttu-id="75660-114">Actualice el proyecto URLconf para importarlo.</span><span class="sxs-lookup"><span data-stu-id="75660-114">Update the project URLconf to import this one.</span></span> <span data-ttu-id="75660-115">Abra **./graph_tutorial/URLs.py** y reemplace el contenido por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="75660-115">Open **./graph_tutorial/urls.py** and replace the contents with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/graph_tutorial/urls.py" id="UrlConfSnippet":::

1. <span data-ttu-id="75660-116">Agregue una vista temporal a la `tutorials` aplicación para comprobar que el enrutamiento de la dirección URL está funcionando.</span><span class="sxs-lookup"><span data-stu-id="75660-116">Add a temporary view to the `tutorials` app to verify that URL routing is working.</span></span> <span data-ttu-id="75660-117">Abra el **./tutorial/views.py** y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="75660-117">Open **./tutorial/views.py** and add the following code.</span></span>

    ```python
    from django.shortcuts import render
    from django.http import HttpResponse, HttpResponseRedirect

    def home(request):
      # Temporary!
      return HttpResponse("Welcome to the tutorial.")
    ```

1. <span data-ttu-id="75660-118">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="75660-118">Save all of your changes and restart the server.</span></span> <span data-ttu-id="75660-119">Vaya a `http://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="75660-119">Browse to `http://localhost:8000`.</span></span> <span data-ttu-id="75660-120">Debe ver`Welcome to the tutorial.`</span><span class="sxs-lookup"><span data-stu-id="75660-120">You should see `Welcome to the tutorial.`</span></span>

## <a name="install-libraries"></a><span data-ttu-id="75660-121">Instalar bibliotecas</span><span class="sxs-lookup"><span data-stu-id="75660-121">Install libraries</span></span>

<span data-ttu-id="75660-122">Antes de continuar, instale algunas bibliotecas adicionales que usará más adelante:</span><span class="sxs-lookup"><span data-stu-id="75660-122">Before moving on, install some additional libraries that you will use later:</span></span>

- <span data-ttu-id="75660-123">[Requests-OAuthlib: OAuth para usuarios](https://requests-oauthlib.readthedocs.io/en/latest/) que controlan los flujos de tokens de inicio de sesión y OAuth, y para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="75660-123">[Requests-OAuthlib: OAuth for Humans](https://requests-oauthlib.readthedocs.io/en/latest/) for handling sign-in and OAuth token flows, and for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="75660-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) para cargar la configuración desde un archivo de YAML.</span><span class="sxs-lookup"><span data-stu-id="75660-124">[PyYAML](https://pyyaml.org/wiki/PyYAMLDocumentation) for loading configuration from a YAML file.</span></span>
- <span data-ttu-id="75660-125">[Python-DateUtil](https://pypi.org/project/python-dateutil/) para analizar cadenas de fecha ISO 8601 devueltas de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="75660-125">[python-dateutil](https://pypi.org/project/python-dateutil/) for parsing ISO 8601 date strings returned from Microsoft Graph.</span></span>

1. <span data-ttu-id="75660-126">Ejecute el siguiente comando en su CLI.</span><span class="sxs-lookup"><span data-stu-id="75660-126">Run the following command in your CLI.</span></span>

    ```Shell
    pip install requests_oauthlib==1.3.0
    pip install pyyaml==5.3.1
    pip install python-dateutil==2.8.1
    ```

## <a name="design-the-app"></a><span data-ttu-id="75660-127">Diseñar la aplicación</span><span class="sxs-lookup"><span data-stu-id="75660-127">Design the app</span></span>

1. <span data-ttu-id="75660-128">Cree un nuevo directorio en el directorio **./tutorial** denominado `templates`.</span><span class="sxs-lookup"><span data-stu-id="75660-128">Create a new directory in the **./tutorial** directory named `templates`.</span></span>

1. <span data-ttu-id="75660-129">En el directorio **./tutorial/templates** , cree un nuevo directorio denominado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="75660-129">In the **./tutorial/templates** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="75660-130">En el directorio **./tutorial/templates/tutorial** , cree un nuevo archivo denominado `layout.html`.</span><span class="sxs-lookup"><span data-stu-id="75660-130">In the **./tutorial/templates/tutorial** directory, create a new file named `layout.html`.</span></span> <span data-ttu-id="75660-131">Agregue el siguiente código en ese archivo.</span><span class="sxs-lookup"><span data-stu-id="75660-131">Add the following code in that file.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/layout.html" id="LayoutSnippet":::

    <span data-ttu-id="75660-132">Este código agrega un [bootstrap](http://getbootstrap.com/) para los estilos sencillos y la [fuente maravilla](https://fontawesome.com/) para algunos iconos simples.</span><span class="sxs-lookup"><span data-stu-id="75660-132">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="75660-133">También define un diseño global con una barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="75660-133">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="75660-134">Cree un nuevo directorio en el directorio **./tutorial** denominado `static`.</span><span class="sxs-lookup"><span data-stu-id="75660-134">Create a new directory in the **./tutorial** directory named `static`.</span></span>

1. <span data-ttu-id="75660-135">En el directorio **./tutorial/static** , cree un nuevo directorio denominado `tutorial`.</span><span class="sxs-lookup"><span data-stu-id="75660-135">In the **./tutorial/static** directory, create a new directory named `tutorial`.</span></span>

1. <span data-ttu-id="75660-136">En el directorio **./tutorial/static/tutorial** , cree un nuevo archivo denominado `app.css`.</span><span class="sxs-lookup"><span data-stu-id="75660-136">In the **./tutorial/static/tutorial** directory, create a new file named `app.css`.</span></span> <span data-ttu-id="75660-137">Agregue el siguiente código en ese archivo.</span><span class="sxs-lookup"><span data-stu-id="75660-137">Add the following code in that file.</span></span>

    :::code language="css" source="../demo/graph_tutorial/tutorial/static/tutorial/app.css":::

1. <span data-ttu-id="75660-138">Cree una plantilla para la Página principal que use el diseño.</span><span class="sxs-lookup"><span data-stu-id="75660-138">Create a template for the home page that uses the layout.</span></span> <span data-ttu-id="75660-139">Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `home.html` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="75660-139">Create a new file in the **./tutorial/templates/tutorial** directory named `home.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/home.html" id="HomeSnippet":::

1. <span data-ttu-id="75660-140">Abra el `./tutorial/views.py` archivo y agregue la siguiente función nueva.</span><span class="sxs-lookup"><span data-stu-id="75660-140">Open the `./tutorial/views.py` file and add the following new function.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="InitializeContextSnippet":::

1. <span data-ttu-id="75660-141">Reemplace la vista `home` existente por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="75660-141">Replace the existing `home` view with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="HomeViewSnippet":::

1. <span data-ttu-id="75660-142">Guarde todos los cambios y reinicie el servidor.</span><span class="sxs-lookup"><span data-stu-id="75660-142">Save all of your changes and restart the server.</span></span> <span data-ttu-id="75660-143">Ahora, la aplicación debe tener un aspecto muy diferente.</span><span class="sxs-lookup"><span data-stu-id="75660-143">Now, the app should look very different.</span></span>

    ![Una captura de pantalla de la Página principal rediseñada](./images/create-app-01.png)
