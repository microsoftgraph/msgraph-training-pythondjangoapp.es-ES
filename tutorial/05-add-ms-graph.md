<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, incorporará Microsoft Graph a la aplicación.

## <a name="get-calendar-events-from-outlook"></a>Obtener eventos de calendario de Outlook

1. Empiece agregando un método a **./tutorial/graph_helper. py** para obtener una vista del calendario entre dos fechas. Agregue el método siguiente.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    Tenga en cuenta lo que está haciendo este código.

    - La dirección URL a la que se llamará es `/v1.0/me/calendarview` .
        - El `Prefer: outlook.timezone` encabezado hace que las horas de inicio y finalización de los resultados se ajusten a la zona horaria del usuario.
        - Los `startDateTime` `endDateTime` parámetros y establecen el inicio y el final de la vista.
        - El `$select` parámetro limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.
        - El `$orderby` parámetro ordena los resultados por hora de inicio.
        - El `$top` parámetro limita los resultados a 50 eventos.

1. Agregue el código siguiente a **./tutorial/graph_helper. py** para buscar un [identificador de zona horaria de IANA](https://www.iana.org/time-zones) basado en un nombre de zona horaria de Windows. Esto es necesario porque Microsoft Graph puede devolver zonas horarias como nombres de zona horaria de Windows y la biblioteca **DateTime** de Python requiere identificadores de zona horaria de IANA.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="ZoneMappingsSnippet":::

1. Agregue la vista siguiente a **./tutorial/views.py**.

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

1. Abra **./tutorial/URLs.py** y reemplace las `path` instrucciones existentes por `calendar` lo siguiente.

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. Inicie sesión y haga clic en el vínculo de **calendario** en la barra de navegación. Si todo funciona, debería ver un volcado JSON de eventos en el calendario del usuario.

## <a name="display-the-results"></a>Mostrar los resultados

Ahora puede Agregar una plantilla para mostrar los resultados de forma más fácil de uso.

1. Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `calendar.html` y agregue el siguiente código.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    Se recorrerá en bucle una colección de eventos y se agregará una fila de tabla para cada uno.

1. Reemplace la `calendar` vista de **./tutorial/views.py** por el siguiente código.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. Actualice la página y la aplicación ahora debería representar una tabla de eventos.

    ![Captura de pantalla de la tabla de eventos](./images/add-msgraph-01.png)
