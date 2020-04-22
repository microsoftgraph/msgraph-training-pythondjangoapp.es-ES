<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, incorporará Microsoft Graph a la aplicación. Para esta aplicación, usará la biblioteca [requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) para realizar llamadas a Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Obtener eventos de calendario de Outlook

1. Empiece agregando un método a **./tutorial/graph_helper. py** para capturar los eventos del calendario. Agregue el método siguiente.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="GetCalendarSnippet":::

    Tenga en cuenta lo que está haciendo este código.

    - La dirección URL a la que se `/v1.0/me/events`llamará es.
    - El `$select` parámetro limita los campos devueltos para cada evento a solo aquellos que la vista usará realmente.
    - El `$orderby` parámetro ordena los resultados por la fecha y hora en que se crearon, con el elemento más reciente en primer lugar.

1. En **./tutorial/views.py**, cambie la `from tutorial.graph_helper import get_user` línea por la siguiente.

    ```python
    from tutorial.graph_helper import get_user, get_calendar_events
    ```

1. Agregue la vista siguiente a **./tutorial/views.py**.

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

1. Abra **./tutorial/URLs.py** y reemplace las instrucciones `path` existentes por `calendar` lo siguiente.

    ```python
    path('calendar', views.calendar, name='calendar'),
    ```

1. Inicie sesión y haga clic en el vínculo de **calendario** en la barra de navegación. Si todo funciona, debería ver un volcado JSON de eventos en el calendario del usuario.

## <a name="display-the-results"></a>Mostrar los resultados

Ahora puede Agregar una plantilla para mostrar los resultados de forma más fácil de uso.

1. Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `calendar.html` y agregue el siguiente código.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/calendar.html" id="CalendarSnippet":::

    Se recorrerá en bucle una colección de eventos y se agregará una fila de tabla para cada uno.

1. Agregue la siguiente `import` instrucción a la parte superior del archivo **./tutorials/views.py** .

    ```python
    import dateutil.parser
    ```

1. Reemplace la `calendar` vista de **./tutorial/views.py** por el siguiente código.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CalendarViewSnippet":::

1. Actualice la página y la aplicación ahora debería representar una tabla de eventos.

    ![Captura de pantalla de la tabla de eventos](./images/add-msgraph-01.png)
