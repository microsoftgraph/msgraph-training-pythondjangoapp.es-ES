<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="6577d-101">En esta sección, agregará la capacidad de crear eventos en el calendario del usuario.</span><span class="sxs-lookup"><span data-stu-id="6577d-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="6577d-102">Agregue el método siguiente a **./tutorial/graph_helper. py** para crear un nuevo evento.</span><span class="sxs-lookup"><span data-stu-id="6577d-102">Add the following method to **./tutorial/graph_helper.py** to create a new event.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="CreateEventSnippet":::

## <a name="create-a-new-event-form"></a><span data-ttu-id="6577d-103">Crear un nuevo formulario de eventos</span><span class="sxs-lookup"><span data-stu-id="6577d-103">Create a new event form</span></span>

1. <span data-ttu-id="6577d-104">Cree un nuevo archivo en el directorio **./tutorial/templates/tutorial** denominado `newevent.html` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="6577d-104">Create a new file in the **./tutorial/templates/tutorial** directory named `newevent.html` and add the following code.</span></span>

    :::code language="html" source="../demo/graph_tutorial/tutorial/templates/tutorial/newevent.html" id="NewEventSnippet":::

1. <span data-ttu-id="6577d-105">Agregue la vista siguiente a **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="6577d-105">Add the following view to **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="NewEventViewSnippet":::

1. <span data-ttu-id="6577d-106">Abra **./tutorial/URLs.py** y agregue `path` instrucciones para la `newevent` vista.</span><span class="sxs-lookup"><span data-stu-id="6577d-106">Open **./tutorial/urls.py** and add a `path` statements for the `newevent` view.</span></span>

    ```python
    path('calendar/new', views.newevent, name='newevent'),
    ```

1. <span data-ttu-id="6577d-107">Guarde los cambios y actualice la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6577d-107">Save your changes and refresh the app.</span></span> <span data-ttu-id="6577d-108">En la página **calendario** , seleccione el botón **nuevo evento** .</span><span class="sxs-lookup"><span data-stu-id="6577d-108">On the **Calendar** page, select the **New event** button.</span></span> <span data-ttu-id="6577d-109">Rellene el formulario y seleccione **crear** para crear el evento.</span><span class="sxs-lookup"><span data-stu-id="6577d-109">Fill in the form and select **Create** to create the event.</span></span>

    ![Captura de pantalla del nuevo formulario de eventos](images/create-event-01.png)
