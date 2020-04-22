<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="91d43-101">En este ejercicio, incorporará Microsoft Graph a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="91d43-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="91d43-102">Para esta aplicación, usará la biblioteca [requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="91d43-102">For this application, you will use the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="91d43-103">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="91d43-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="91d43-104">Empiece agregando un método a **./tutorial/graph_helper. py** para capturar los eventos del calendario.</span><span class="sxs-lookup"><span data-stu-id="91d43-104">Start by adding a method to **./tutorial/graph_helper.py** to fetch the calendar events.</span></span> <span data-ttu-id="91d43-105">Agregue el método siguiente.</span><span class="sxs-lookup"><span data-stu-id="91d43-105">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="91d43-106">Tenga en cuenta lo que está haciendo este código.</span><span class="sxs-lookup"><span data-stu-id="91d43-106">Consider what this code is doing.</span></span>

    - <span data-ttu-id="91d43-107">La dirección URL a la que se `/v1.0/me/events`llamará es.</span><span class="sxs-lookup"><span data-stu-id="91d43-107">The URL that will be called is `/v1.0/me/events`.</span></span>
    - <span data-ttu-id="91d43-108">El `$select` parámetro limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="91d43-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="91d43-109">El `$orderby` parámetro ordena los resultados por la fecha y hora en que se crearon, con el elemento más reciente en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="91d43-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="91d43-110">En **./tutorial/views.py**, cambie la `from tutorial.graph_helper import get_user` línea por la siguiente.</span><span class="sxs-lookup"><span data-stu-id="91d43-110">In **./tutorial/views.py**, change the `from tutorial.graph_helper import get_user` line to the following.</span></span>

    ```python
    from tutorial.graph_helper import get_user, get_calendar_events
    ```

1. <span data-ttu-id="91d43-111">Agregue la vista siguiente a **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="91d43-111">Add the following view to **./tutorial/views.py**.</span></span>

    ```python
    def calendar(request):
      context = initialize_context(request)

      token = get_token(request)

      events = get_calendar_events(token)

      context['errors'] = [
        { 'message': 'Events', 'debug': format(events)}
      ]

      return render(request, 'tutorial/home.html', context)
    ```

1. <span data-ttu-id="91d43-112">Abra **./tutorial/URLs.py** y reemplace las instrucciones `path` existentes por `calendar` lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="91d43-112">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="91d43-113">Inicie sesión y haga clic en el vínculo de **calendario** en la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="91d43-113">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="91d43-114">Si todo funciona, debería ver un volcado JSON de eventos en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="91d43-114">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="91d43-115">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="91d43-115">Display the results</span></span>

<span data-ttu-id="91d43-116">Ahora puede Agregar una plantilla para mostrar los resultados de forma más fácil de uso.</span><span class="sxs-lookup"><span data-stu-id="91d43-116">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="91d43-117">Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `calendar.html` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="91d43-117">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="91d43-118">Se recorrerá en bucle una colección de eventos y se agregará una fila de tabla para cada uno.</span><span class="sxs-lookup"><span data-stu-id="91d43-118">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="91d43-119">Agregue la siguiente `import` instrucción a la parte superior del archivo **./tutorials/views.py** .</span><span class="sxs-lookup"><span data-stu-id="91d43-119">Add the following `import` statement to the top of the **./tutorials/views.py** file.</span></span>

    ```python
    import dateutil.parser
    ```

1. <span data-ttu-id="91d43-120">Reemplace la `calendar` vista de **./tutorial/views.py** por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="91d43-120">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="91d43-121">Actualice la página y la aplicación ahora debería representar una tabla de eventos.</span><span class="sxs-lookup"><span data-stu-id="91d43-121">Refresh the page and the app should now render a table of events.</span></span>

    ![Captura de pantalla de la tabla de eventos](./images/add-msgraph-01.png)
