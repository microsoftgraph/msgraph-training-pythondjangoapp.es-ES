<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e94e8-101">En este ejercicio, incorporará Microsoft Graph a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e94e8-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e94e8-102">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="e94e8-102">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="e94e8-103">Empiece agregando un método a **./tutorial/graph_helper. py** para obtener una vista del calendario entre dos fechas.</span><span class="sxs-lookup"><span data-stu-id="e94e8-103">Start by adding a method to **./tutorial/graph_helper.py** to fetch a view of the calendar between two dates.</span></span> <span data-ttu-id="e94e8-104">Agregue el método siguiente.</span><span class="sxs-lookup"><span data-stu-id="e94e8-104">Add the following method.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    <span data-ttu-id="e94e8-105">Tenga en cuenta lo que está haciendo este código.</span><span class="sxs-lookup"><span data-stu-id="e94e8-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="e94e8-106">La dirección URL a la que se llamará es `/v1.0/me/calendarview` .</span><span class="sxs-lookup"><span data-stu-id="e94e8-106">The URL that will be called is `/v1.0/me/calendarview`.</span></span>
        - <span data-ttu-id="e94e8-107">El `Prefer: outlook.timezone` encabezado hace que las horas de inicio y finalización de los resultados se ajusten a la zona horaria del usuario.</span><span class="sxs-lookup"><span data-stu-id="e94e8-107">The `Prefer: outlook.timezone` header causes the start and end times in the results to be adjusted to the user's time zone.</span></span>
        - <span data-ttu-id="e94e8-108">Los `startDateTime` `endDateTime` parámetros y establecen el inicio y el final de la vista.</span><span class="sxs-lookup"><span data-stu-id="e94e8-108">The `startDateTime` and `endDateTime` parameters set the start and end of the view.</span></span>
        - <span data-ttu-id="e94e8-109">El `$select` parámetro limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="e94e8-109">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
        - <span data-ttu-id="e94e8-110">El `$orderby` parámetro ordena los resultados por hora de inicio.</span><span class="sxs-lookup"><span data-stu-id="e94e8-110">The `$orderby` parameter sorts the results by start time.</span></span>
        - <span data-ttu-id="e94e8-111">El `$top` parámetro limita los resultados a 50 eventos.</span><span class="sxs-lookup"><span data-stu-id="e94e8-111">The `$top` parameter limits the results to 50 events.</span></span>

1. <span data-ttu-id="e94e8-112">Agregue el código siguiente a **./tutorial/graph_helper. py** para buscar un [identificador de zona horaria de IANA](https://www.iana.org/time-zones) basado en un nombre de zona horaria de Windows.</span><span class="sxs-lookup"><span data-stu-id="e94e8-112">Add the following code to **./tutorial/graph_helper.py** to lookup an [IANA time zone identifier](https://www.iana.org/time-zones) based on a Windows time zone name.</span></span> <span data-ttu-id="e94e8-113">Esto es necesario porque Microsoft Graph puede devolver zonas horarias como nombres de zona horaria de Windows y la biblioteca **DateTime** de Python requiere identificadores de zona horaria de IANA.</span><span class="sxs-lookup"><span data-stu-id="e94e8-113">This is necessary because Microsoft Graph can return time zones as Windows time zone names, and the Python **datetime** library requires IANA time zone identifiers.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. <span data-ttu-id="e94e8-114">Agregue la vista siguiente a **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="e94e8-114">Add the following view to **./tutorial/views.py**.</span></span>

    ```python
    def calendar(request):
      context = initialize_context(request)
      user = context['user']

      # Load the user's time zone
      # Microsoft Graph can return the user's time zone as either
      # a Windows time zone name or an IANA time zone identifier
      # Python datetime requires IANA, so convert Windows to IANA
      time_zone = get_iana_from_windows(user['timeZone'])
      tz_info = tz.gettz(time_zone)

      # Get midnight today in user's time zone
      today = datetime.now(tz_info).replace(
        hour=0,
        minute=0,
        second=0,
        microsecond=0)

      # Based on today, get the start of the week (Sunday)
      if (today.weekday() != 6):
        start = today - timedelta(days=today.isoweekday())
      else:
        start = today

      end = start + timedelta(days=7)

      token = get_token(request)

      events = get_calendar_events(
        token,
        start.isoformat(timespec='seconds'),
        end.isoformat(timespec='seconds'),
        user['timeZone'])

      context['errors'] = [
        { 'message': 'Events', 'debug': format(events)}
      ]

      return render(request, 'tutorial/home.html', context)
    ```

1. <span data-ttu-id="e94e8-115">Abra **./tutorial/URLs.py** y reemplace las `path` instrucciones existentes por `calendar` lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="e94e8-115">Open **./tutorial/urls.py** and replace the existing `path` statements for `calendar` with the following.</span></span>

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. <span data-ttu-id="e94e8-116">Inicie sesión y haga clic en el vínculo de **calendario** en la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="e94e8-116">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="e94e8-117">Si todo funciona, debería ver un volcado JSON de eventos en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="e94e8-117">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e94e8-118">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="e94e8-118">Display the results</span></span>

<span data-ttu-id="e94e8-119">Ahora puede Agregar una plantilla para mostrar los resultados de forma más fácil de uso.</span><span class="sxs-lookup"><span data-stu-id="e94e8-119">Now you can add a template to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="e94e8-120">Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `calendar.html` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="e94e8-120">Create a new file in the **./tutorial/templates/tutorial** directory named `calendar.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    <span data-ttu-id="e94e8-121">Se recorrerá en bucle una colección de eventos y se agregará una fila de tabla para cada uno.</span><span class="sxs-lookup"><span data-stu-id="e94e8-121">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="e94e8-122">Reemplace la `calendar` vista de **./tutorial/views.py** por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="e94e8-122">Replace the `calendar` view in **./tutorial/views.py** with the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. <span data-ttu-id="e94e8-123">Actualice la página y la aplicación ahora debería representar una tabla de eventos.</span><span class="sxs-lookup"><span data-stu-id="e94e8-123">Refresh the page and the app should now render a table of events.</span></span>

    ![Captura de pantalla de la tabla de eventos](./images/add-msgraph-01.png)
