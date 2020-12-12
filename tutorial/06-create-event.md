<!-- markdownlint-disable MD002 MD041 -->

En esta sección, agregará la capacidad de crear eventos en el calendario del usuario.

1. Agregue el método siguiente a **./tutorial/graph_helper. py** para crear un nuevo evento.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="CreateEventSnippet":::

## <a name="create-a-new-event-form"></a>Crear un nuevo formulario de eventos

1. Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `newevent.html` y agregue el siguiente código.

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/newevent.html" id="NewEventSnippet":::

1. Agregue la vista siguiente a **./tutorial/views.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="NewEventViewSnippet":::

1. Abra **./tutorial/URLs.py** y agregue `path` instrucciones para la `newevent` vista.

    ```python
    path('calendar/new', views.newevent, name='newevent'),
    ```

1. Guarde los cambios y actualice la aplicación. En la página **calendario** , seleccione el botón **nuevo evento** . Rellene el formulario y seleccione **crear** para crear el evento.

    ![Captura de pantalla del nuevo formulario de eventos](images/create-event-01.png)
