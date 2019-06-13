<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="4bba6-101">En este ejercicio, incorporará Microsoft Graph a la aplicación.</span><span class="sxs-lookup"><span data-stu-id="4bba6-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="4bba6-102">Para esta aplicación, usará la biblioteca requests [-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) para realizar llamadas a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="4bba6-102">For this application, you will use the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="4bba6-103">Obtener eventos de calendario de Outlook</span><span class="sxs-lookup"><span data-stu-id="4bba6-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="4bba6-104">Empiece agregando un método a `./tutorial/graph_helper.py` para capturar los eventos del calendario.</span><span class="sxs-lookup"><span data-stu-id="4bba6-104">Start by adding a method to `./tutorial/graph_helper.py` to fetch the calendar events.</span></span> <span data-ttu-id="4bba6-105">Agregue el método siguiente.</span><span class="sxs-lookup"><span data-stu-id="4bba6-105">Add the following method.</span></span>

```python
def get_calendar_events(token):
  graph_client = OAuth2Session(token=token)

  # Configure query parameters to
  # modify the results
  query_params = {
    '$select': 'subject,organizer,start,end',
    '$orderby': 'createdDateTime DESC'
  }

  # Send GET to /me/events
  events = graph_client.get('{0}/me/events'.format(graph_url), params=query_params)
  # Return the JSON result
  return events.json()
```

<span data-ttu-id="4bba6-106">Tenga en cuenta lo que está haciendo este código.</span><span class="sxs-lookup"><span data-stu-id="4bba6-106">Consider what this code is doing.</span></span>

- <span data-ttu-id="4bba6-107">La dirección URL a la que se `/v1.0/me/events`llamará es.</span><span class="sxs-lookup"><span data-stu-id="4bba6-107">The URL that will be called is `/v1.0/me/events`.</span></span>
- <span data-ttu-id="4bba6-108">El `$select` parámetro limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.</span><span class="sxs-lookup"><span data-stu-id="4bba6-108">The `$select` parameter limits the fields returned for each events to just those the view will actually use.</span></span>
- <span data-ttu-id="4bba6-109">El `$orderby` parámetro ordena los resultados por la fecha y hora en que se crearon, con el elemento más reciente en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="4bba6-109">The `$orderby` parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

<span data-ttu-id="4bba6-110">Ahora, cree una vista de calendario.</span><span class="sxs-lookup"><span data-stu-id="4bba6-110">Now create a calendar view.</span></span> <span data-ttu-id="4bba6-111">En `./tutorial/views.py`, primero cambie la `from tutorial.graph_helper import get_user` línea por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="4bba6-111">In `./tutorial/views.py`, first change the `from tutorial.graph_helper import get_user` line to the following.</span></span>

```python
from tutorial.graph_helper import get_user, get_calendar_events
```

<span data-ttu-id="4bba6-112">A continuación, agregue la vista siguiente `./tutorial/views.py`a.</span><span class="sxs-lookup"><span data-stu-id="4bba6-112">Then, add the following view to `./tutorial/views.py`.</span></span>

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

<span data-ttu-id="4bba6-113">Actualice `./tutorial/urls.py` para agregar esta nueva vista.</span><span class="sxs-lookup"><span data-stu-id="4bba6-113">Update `./tutorial/urls.py` to add this new view.</span></span>

```python
path('calendar', views.calendar, name='calendar'),
```

<span data-ttu-id="4bba6-114">Por último, actualice \*\*\*\* el vínculo de `./tutorial/templates/tutorial/layout.html` calendario en para vincular a esta vista.</span><span class="sxs-lookup"><span data-stu-id="4bba6-114">Finally, update  the **Calendar** link in `./tutorial/templates/tutorial/layout.html` to link to this view.</span></span> <span data-ttu-id="4bba6-115">Reemplace la `<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>` línea por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="4bba6-115">Replace the `<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="#">Calendar</a>` line with the following.</span></span>

```html
<a class="nav-link{% if request.resolver_match.view_name == 'calendar' %} active{% endif %}" href="{% url 'calendar' %}">Calendar</a>
```

<span data-ttu-id="4bba6-116">Ahora puede probar esto.</span><span class="sxs-lookup"><span data-stu-id="4bba6-116">Now you can test this.</span></span> <span data-ttu-id="4bba6-117">Inicie sesión y haga clic en el vínculo de **calendario** en la barra de navegación.</span><span class="sxs-lookup"><span data-stu-id="4bba6-117">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="4bba6-118">Si todo funciona, debería ver un volcado JSON de eventos en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="4bba6-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="4bba6-119">Mostrar los resultados</span><span class="sxs-lookup"><span data-stu-id="4bba6-119">Display the results</span></span>

<span data-ttu-id="4bba6-120">Ahora puede Agregar una plantilla para mostrar los resultados de forma más fácil de uso.</span><span class="sxs-lookup"><span data-stu-id="4bba6-120">Now you can add a template to display the results in a more user-friendly manner.</span></span> <span data-ttu-id="4bba6-121">Cree un nuevo archivo en el `./tutorial/templates/tutorial` directorio denominado `calendar.html` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="4bba6-121">Create a new file in the `./tutorial/templates/tutorial` directory named `calendar.html` and add the following code.</span></span>

```html
{% extends "tutorial/layout.html" %}
{% block content %}
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    {% if events %}
      {% for event in events %}
        <tr>
          <td>{{ event.organizer.emailAddress.name }}</td>
          <td>{{ event.subject }}</td>
          <td>{{ event.start.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
          <td>{{ event.end.dateTime|date:'SHORT_DATETIME_FORMAT' }}</td>
        </tr>
      {% endfor %}
    {% endif %}
  </tbody>
</table>
{% endblock %}
```

<span data-ttu-id="4bba6-122">Se recorrerá en bucle una colección de eventos y se agregará una fila de tabla para cada uno.</span><span class="sxs-lookup"><span data-stu-id="4bba6-122">That will loop through a collection of events and add a table row for each one.</span></span> <span data-ttu-id="4bba6-123">Agregue la siguiente `import` instrucción a la parte superior del `./tutorials/views.py` archivo.</span><span class="sxs-lookup"><span data-stu-id="4bba6-123">Add the following `import` statement to the top of the `./tutorials/views.py` file.</span></span>

```python
import dateutil.parser
```

<span data-ttu-id="4bba6-124">Reemplace la `calendar` vista en `./tutorial/views.py` por el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="4bba6-124">Replace the `calendar` view in `./tutorial/views.py` with the following code.</span></span>

```python
def calendar(request):
  context = initialize_context(request)

  token = get_token(request)

  events = get_calendar_events(token)

  if events:
    # Convert the ISO 8601 date times to a datetime object
    # This allows the Django template to format the value nicely
    for event in events['value']:
      event['start']['dateTime'] = dateutil.parser.parse(event['start']['dateTime'])
      event['end']['dateTime'] = dateutil.parser.parse(event['end']['dateTime'])

    context['events'] = events['value']

  return render(request, 'tutorial/calendar.html', context)
```

<span data-ttu-id="4bba6-125">Actualice la página y la aplicación ahora debería representar una tabla de eventos.</span><span class="sxs-lookup"><span data-stu-id="4bba6-125">Refresh the page and the app should now render a table of events.</span></span>

![Captura de pantalla de la tabla de eventos](./images/add-msgraph-01.png)
