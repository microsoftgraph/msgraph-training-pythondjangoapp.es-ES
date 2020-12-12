<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="14e15-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="14e15-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="14e15-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="14e15-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="14e15-103">En este paso, integrará la biblioteca [de MSAL para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="14e15-103">In this step you will integrate the [MSAL for Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) library into the application.</span></span>

1. <span data-ttu-id="14e15-104">Cree un nuevo archivo en la raíz del proyecto denominado `oauth_settings.yml` y agregue el contenido siguiente.</span><span class="sxs-lookup"><span data-stu-id="14e15-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. <span data-ttu-id="14e15-105">Reemplace `YOUR_APP_ID_HERE` por el identificador de la aplicación del portal de registro de la aplicación y reemplace `YOUR_APP_SECRET_HERE` por la contraseña que ha generado.</span><span class="sxs-lookup"><span data-stu-id="14e15-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="14e15-106">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el archivo **oauth_settings. yml** del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.</span><span class="sxs-lookup"><span data-stu-id="14e15-106">If you're using source control such as git, now would be a good time to exclude the **oauth_settings.yml** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="14e15-107">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="14e15-107">Implement sign-in</span></span>

1. <span data-ttu-id="14e15-108">Cree un nuevo archivo en el directorio **./tutorial** denominado `auth_helper.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="14e15-108">Create a new file in the **./tutorial** directory named `auth_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="14e15-109">Este archivo conservará todos los métodos relacionados con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="14e15-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="14e15-110">El `get_sign_in_flow` genera una dirección URL de autorización y el `get_token_from_code` método intercambia la respuesta de autorización para un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="14e15-110">The `get_sign_in_flow` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

1. <span data-ttu-id="14e15-111">Agregue la siguiente `import` instrucción a la parte superior de **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="14e15-111">Add the following `import` statement to the top of **./tutorial/views.py**.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. <span data-ttu-id="14e15-112">Agregue una vista de inicio de sesión en el archivo **./tutorial/views.py** .</span><span class="sxs-lookup"><span data-stu-id="14e15-112">Add a sign-in view in the **./tutorial/views.py** file.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. <span data-ttu-id="14e15-113">Agregue una vista de devolución de llamada en el archivo **./tutorial/views.py** .</span><span class="sxs-lookup"><span data-stu-id="14e15-113">Add a callback view in the **./tutorial/views.py** file.</span></span>

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    <span data-ttu-id="14e15-114">Tenga en cuenta lo que hacen estas vistas:</span><span class="sxs-lookup"><span data-stu-id="14e15-114">Consider what these views do:</span></span>

    - <span data-ttu-id="14e15-115">La `signin` acción genera la dirección URL de inicio de sesión de Azure ad, guarda el flujo generado por el cliente de OAuth y, a continuación, redirige el explorador a la página de inicio de sesión de Azure ad.</span><span class="sxs-lookup"><span data-stu-id="14e15-115">The `signin` action generates the Azure AD signin URL, saves the flow generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    - <span data-ttu-id="14e15-116">La `callback` acción es donde Azure redirige después de que se complete el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="14e15-116">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="14e15-117">Esa acción usa el flujo guardado y la cadena de consulta enviada por Azure para solicitar un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="14e15-117">That action uses the saved flow and the query string sent by Azure to request an access token.</span></span> <span data-ttu-id="14e15-118">A continuación, se redirige de nuevo a la Página principal con la respuesta en el valor de error temporal.</span><span class="sxs-lookup"><span data-stu-id="14e15-118">It then redirects back to the home page with the response in the temporary error value.</span></span> <span data-ttu-id="14e15-119">Usará esto para comprobar que el inicio de sesión está funcionando antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="14e15-119">You'll use this to verify that our sign-in is working before moving on.</span></span>

1. <span data-ttu-id="14e15-120">Abra **./tutorial/URLs.py** y reemplace las `path` instrucciones existentes por `signin` lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="14e15-120">Open **./tutorial/urls.py** and replace the existing `path` statements for `signin` with the following.</span></span>

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. <span data-ttu-id="14e15-121">Agregue un nuevo `path` para la `callback` vista.</span><span class="sxs-lookup"><span data-stu-id="14e15-121">Add a new `path` for the `callback` view.</span></span>

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. <span data-ttu-id="14e15-122">Inicie el servidor y vaya a `https://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="14e15-122">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="14e15-123">Haga clic en el botón de inicio de sesión y se le redirigirá a `https://login.microsoftonline.com` .</span><span class="sxs-lookup"><span data-stu-id="14e15-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="14e15-124">Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados.</span><span class="sxs-lookup"><span data-stu-id="14e15-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="14e15-125">El explorador redirige a la aplicación, que muestra la respuesta, incluido el token de acceso.</span><span class="sxs-lookup"><span data-stu-id="14e15-125">The browser redirects to the app, showing the response, including the access token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="14e15-126">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="14e15-126">Get user details</span></span>

1. <span data-ttu-id="14e15-127">Cree un nuevo archivo en el directorio **./tutorial** denominado `graph_helper.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="14e15-127">Create a new file in the **./tutorial** directory named `graph_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="14e15-128">El `get_user` método realiza una solicitud GET al punto de conexión de Microsoft Graph `/me` para obtener el perfil del usuario, mediante el token de acceso adquirido anteriormente.</span><span class="sxs-lookup"><span data-stu-id="14e15-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

1. <span data-ttu-id="14e15-129">Actualice el `callback` método en **./tutorial/views.py** para obtener el perfil del usuario de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="14e15-129">Update the `callback` method in **./tutorial/views.py** to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="14e15-130">Agregue la siguiente `import` instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="14e15-130">Add the following `import` statement to the top of the file.</span></span>

    ```python
    from tutorial.graph_helper import *
    ```

1. <span data-ttu-id="14e15-131">Reemplace el `callback` método con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="14e15-131">Replace the `callback` method with the following code.</span></span>

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)

      #Get the user's profile
      user = get_user(result['access_token'])
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': 'User: {0}\nToken: {1}'.format(user, result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    <span data-ttu-id="14e15-132">El nuevo código llama al `get_user` método para solicitar el perfil del usuario.</span><span class="sxs-lookup"><span data-stu-id="14e15-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="14e15-133">Agrega el objeto de usuario a la salida temporal para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="14e15-133">It adds the user object to the temporary output for testing.</span></span>

1. <span data-ttu-id="14e15-134">Agregue los siguientes métodos nuevos a **./tutorial/auth_helper. py**.</span><span class="sxs-lookup"><span data-stu-id="14e15-134">Add the following new methods to **./tutorial/auth_helper.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. <span data-ttu-id="14e15-135">Actualice la `callback` función en **./tutorial/views.py** para almacenar el usuario en la sesión y volver a redirigir a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="14e15-135">Update the `callback` function in **./tutorial/views.py** to store the user in the session and redirect back to the main page.</span></span> <span data-ttu-id="14e15-136">Reemplace la `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` línea por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="14e15-136">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. <span data-ttu-id="14e15-137">Reemplace el `callback` método por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="14e15-137">Replace the `callback` method with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="14e15-138">Implementación de cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="14e15-138">Implement sign-out</span></span>

1. <span data-ttu-id="14e15-139">Agregue una nueva `sign_out` vista en **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="14e15-139">Add a new `sign_out` view in **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. <span data-ttu-id="14e15-140">Abra **./tutorial/URLs.py** y reemplace las `path` instrucciones existentes por `signout` lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="14e15-140">Open **./tutorial/urls.py** and replace the existing `path` statements for `signout` with the following.</span></span>

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. <span data-ttu-id="14e15-141">Reinicie el servidor y pase por el proceso de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="14e15-141">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="14e15-142">Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="14e15-142">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

1. <span data-ttu-id="14e15-144">Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** .</span><span class="sxs-lookup"><span data-stu-id="14e15-144">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="14e15-145">Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="14e15-145">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="14e15-147">Actualizar tokens</span><span class="sxs-lookup"><span data-stu-id="14e15-147">Refreshing tokens</span></span>

<span data-ttu-id="14e15-148">En este punto, la aplicación tiene un token de acceso, que se envía en el `Authorization` encabezado de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="14e15-148">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="14e15-149">Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.</span><span class="sxs-lookup"><span data-stu-id="14e15-149">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="14e15-150">Sin embargo, este token es de corta duración.</span><span class="sxs-lookup"><span data-stu-id="14e15-150">However, this token is short-lived.</span></span> <span data-ttu-id="14e15-151">El token expira una hora después de su emisión.</span><span class="sxs-lookup"><span data-stu-id="14e15-151">The token expires an hour after it is issued.</span></span> <span data-ttu-id="14e15-152">Aquí es donde el token de actualización se vuelve útil.</span><span class="sxs-lookup"><span data-stu-id="14e15-152">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="14e15-153">El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo.</span><span class="sxs-lookup"><span data-stu-id="14e15-153">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="14e15-154">Como en este ejemplo se usa la MSAL, no es necesario escribir ningún código específico para actualizar el token.</span><span class="sxs-lookup"><span data-stu-id="14e15-154">Because this sample uses MSAL, you do not have to write any specific code to refresh the token.</span></span> <span data-ttu-id="14e15-155">`acquire_token_silent`El método de MSAL controla la actualización del token si es necesario.</span><span class="sxs-lookup"><span data-stu-id="14e15-155">MSAL's `acquire_token_silent` method handles refreshing the token if needed.</span></span>
