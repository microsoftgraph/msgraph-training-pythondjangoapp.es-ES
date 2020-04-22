<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="56fc8-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="56fc8-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="56fc8-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="56fc8-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="56fc8-103">En este paso, integrará la biblioteca [requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56fc8-103">In this step you will integrate the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library into the application.</span></span>

1. <span data-ttu-id="56fc8-104">Cree un nuevo archivo en la raíz del proyecto denominado `oauth_settings.yml`y agregue el contenido siguiente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. <span data-ttu-id="56fc8-105">Reemplace `YOUR_APP_ID_HERE` por el identificador de la aplicación del portal de registro de la `YOUR_APP_SECRET_HERE` aplicación y reemplace por la contraseña que ha generado.</span><span class="sxs-lookup"><span data-stu-id="56fc8-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="56fc8-106">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el archivo **oauth_settings. yml** del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.</span><span class="sxs-lookup"><span data-stu-id="56fc8-106">If you're using source control such as git, now would be a good time to exclude the **oauth_settings.yml** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="56fc8-107">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="56fc8-107">Implement sign-in</span></span>

1. <span data-ttu-id="56fc8-108">Cree un nuevo archivo en el directorio **./tutorial** denominado `auth_helper.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="56fc8-108">Create a new file in the **./tutorial** directory named `auth_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="56fc8-109">Este archivo conservará todos los métodos relacionados con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="56fc8-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="56fc8-110">El `get_sign_in_url` genera una dirección URL de autorización y `get_token_from_code` el método intercambia la respuesta de autorización para un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="56fc8-110">The `get_sign_in_url` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

1. <span data-ttu-id="56fc8-111">Agregue las siguientes `import` instrucciones en la parte superior de **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="56fc8-111">Add the following `import` statements to the top of **./tutorial/views.py**.</span></span>

    ```python
    from django.urls import reverse
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code
    ```

1. <span data-ttu-id="56fc8-112">Agregue una vista de inicio de sesión en el archivo **./tutorial/views.py** .</span><span class="sxs-lookup"><span data-stu-id="56fc8-112">Add a sign-in view in the **./tutorial/views.py** file.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. <span data-ttu-id="56fc8-113">Agregue una vista de devolución de llamada en el archivo **./tutorial/views.py** .</span><span class="sxs-lookup"><span data-stu-id="56fc8-113">Add a callback view in the **./tutorial/views.py** file.</span></span>

    ```python
    def callback(request):
      # Get the state saved in session
      expected_state = request.session.pop('auth_state', '')
      # Make the token request
      token = get_token_from_code(request.get_full_path(), expected_state)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
      return HttpResponseRedirect(reverse('home'))
    ```

    <span data-ttu-id="56fc8-114">Tenga en cuenta lo que hacen estas vistas:</span><span class="sxs-lookup"><span data-stu-id="56fc8-114">Consider what these views do:</span></span>

    - <span data-ttu-id="56fc8-115">La `signin` acción genera la dirección URL de inicio de sesión de Azure `state` ad, guarda el valor generado por el cliente de OAuth y, a continuación, redirige el explorador a la página de inicio de sesión de Azure ad.</span><span class="sxs-lookup"><span data-stu-id="56fc8-115">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    - <span data-ttu-id="56fc8-116">La `callback` acción es donde Azure redirige después de que se complete el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="56fc8-116">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="56fc8-117">Esa acción garantiza que el `state` valor coincida con el valor guardado y, a continuación, usa el código de autorización enviado por Azure para solicitar un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="56fc8-117">That action makes sure the `state` value matches the saved value, then uses the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="56fc8-118">A continuación, se redirige de nuevo a la Página principal con el token de acceso en el valor de error temporal.</span><span class="sxs-lookup"><span data-stu-id="56fc8-118">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="56fc8-119">Usará esto para comprobar que el inicio de sesión está funcionando antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="56fc8-119">You'll use this to verify that our sign-in is working before moving on.</span></span>

1. <span data-ttu-id="56fc8-120">Abra **./tutorial/URLs.py** y reemplace las instrucciones `path` existentes por `signin` lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-120">Open **./tutorial/urls.py** and replace the existing `path` statements for `signin` with the following.</span></span>

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. <span data-ttu-id="56fc8-121">Agregue un nuevo `path` para la `callback` vista.</span><span class="sxs-lookup"><span data-stu-id="56fc8-121">Add a new `path` for the `callback` view.</span></span>

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. <span data-ttu-id="56fc8-122">Inicie el servidor y vaya a `https://localhost:8000`.</span><span class="sxs-lookup"><span data-stu-id="56fc8-122">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="56fc8-123">Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a.</span><span class="sxs-lookup"><span data-stu-id="56fc8-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="56fc8-124">Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados.</span><span class="sxs-lookup"><span data-stu-id="56fc8-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="56fc8-125">El explorador redirige a la aplicación, que muestra el token.</span><span class="sxs-lookup"><span data-stu-id="56fc8-125">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="56fc8-126">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="56fc8-126">Get user details</span></span>

1. <span data-ttu-id="56fc8-127">Cree un nuevo archivo en el directorio **./tutorial** denominado `graph_helper.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="56fc8-127">Create a new file in the **./tutorial** directory named `graph_helper.py` and add the following code.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    <span data-ttu-id="56fc8-128">El `get_user` método realiza una solicitud GET al punto de conexión `/me` de Microsoft Graph para obtener el perfil del usuario, mediante el token de acceso adquirido anteriormente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

1. <span data-ttu-id="56fc8-129">Actualice el `callback` método en **./tutorial/views.py** para obtener el perfil del usuario de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="56fc8-129">Update the `callback` method in **./tutorial/views.py** to get the user's profile from Microsoft Graph.</span></span> <span data-ttu-id="56fc8-130">Agregue la siguiente `import` instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="56fc8-130">Add the following `import` statement to the top of the file.</span></span>

    ```python
    from tutorial.graph_helper import get_user
    ```

1. <span data-ttu-id="56fc8-131">Reemplace el `callback` método con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-131">Replace the `callback` method with the following code.</span></span>

    ```python
    def callback(request):
      # Get the state saved in session
      expected_state = request.session.pop('auth_state', '')
      # Make the token request
      token = get_token_from_code(request.get_full_path(), expected_state)

      # Get the user's profile
      user = get_user(token)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved',
        'debug': 'User: {0}\nToken: {1}'.format(user, token) }
      return HttpResponseRedirect(reverse('home'))
    ```

<span data-ttu-id="56fc8-132">El nuevo código llama al `get_user` método para solicitar el perfil del usuario.</span><span class="sxs-lookup"><span data-stu-id="56fc8-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="56fc8-133">Agrega el objeto de usuario a la salida temporal para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="56fc8-133">It adds the user object to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="56fc8-134">Almacenamiento de tokens</span><span class="sxs-lookup"><span data-stu-id="56fc8-134">Storing the tokens</span></span>

<span data-ttu-id="56fc8-135">Ahora que puede obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56fc8-135">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="56fc8-136">Dado que se trata de una aplicación de ejemplo, por razones de simplicidad, se almacenará en la sesión.</span><span class="sxs-lookup"><span data-stu-id="56fc8-136">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="56fc8-137">Una aplicación real usaría una solución de almacenamiento seguro más confiable, como una base de datos.</span><span class="sxs-lookup"><span data-stu-id="56fc8-137">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="56fc8-138">Agregue los siguientes métodos nuevos a **./tutorial/auth_helper. py**.</span><span class="sxs-lookup"><span data-stu-id="56fc8-138">Add the following new methods to **./tutorial/auth_helper.py**.</span></span>

    ```python
    def store_token(request, token):
      request.session['oauth_token'] = token

    def store_user(request, user):
      request.session['user'] = {
        'is_authenticated': True,
        'name': user['displayName'],
        'email': user['mail'] if (user['mail'] != None) else user['userPrincipalName']
      }

    def get_token(request):
      token = request.session['oauth_token']
      return token

    def remove_user_and_token(request):
      if 'oauth_token' in request.session:
        del request.session['oauth_token']

      if 'user' in request.session:
        del request.session['user']
    ```

1. <span data-ttu-id="56fc8-139">Actualice la `callback` función en **./tutorial/views.py** para almacenar los tokens en la sesión y volver a redirigir a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="56fc8-139">Update the `callback` function in **./tutorial/views.py** to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="56fc8-140">Reemplace la `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` línea por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-140">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
    ```

1. <span data-ttu-id="56fc8-141">Reemplace el `callback` método por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-141">Replace the `callback` method with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="56fc8-142">Implementación de cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="56fc8-142">Implement sign-out</span></span>

1. <span data-ttu-id="56fc8-143">Agregue una nueva `sign_out` vista en **./tutorial/views.py**.</span><span class="sxs-lookup"><span data-stu-id="56fc8-143">Add a new `sign_out` view in **./tutorial/views.py**.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. <span data-ttu-id="56fc8-144">Abra **./tutorial/URLs.py** y reemplace las instrucciones `path` existentes por `signout` lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-144">Open **./tutorial/urls.py** and replace the existing `path` statements for `signout` with the following.</span></span>

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. <span data-ttu-id="56fc8-145">Reinicie el servidor y pase por el proceso de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="56fc8-145">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="56fc8-146">Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="56fc8-146">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

1. <span data-ttu-id="56fc8-148">Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** .</span><span class="sxs-lookup"><span data-stu-id="56fc8-148">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="56fc8-149">Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="56fc8-149">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="56fc8-151">Actualizar tokens</span><span class="sxs-lookup"><span data-stu-id="56fc8-151">Refreshing tokens</span></span>

<span data-ttu-id="56fc8-152">En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="56fc8-152">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="56fc8-153">Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.</span><span class="sxs-lookup"><span data-stu-id="56fc8-153">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="56fc8-154">Sin embargo, este token es de corta duración.</span><span class="sxs-lookup"><span data-stu-id="56fc8-154">However, this token is short-lived.</span></span> <span data-ttu-id="56fc8-155">El token expira una hora después de su emisión.</span><span class="sxs-lookup"><span data-stu-id="56fc8-155">The token expires an hour after it is issued.</span></span> <span data-ttu-id="56fc8-156">Aquí es donde el token de actualización se vuelve útil.</span><span class="sxs-lookup"><span data-stu-id="56fc8-156">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="56fc8-157">El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo.</span><span class="sxs-lookup"><span data-stu-id="56fc8-157">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="56fc8-158">Actualice el código de administración de tokens para implementar la actualización de tokens.</span><span class="sxs-lookup"><span data-stu-id="56fc8-158">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="56fc8-159">Reemplace el método `get_token` existente en **./tutorial/auth_helper. py** por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="56fc8-159">Replace the existing `get_token` method in **./tutorial/auth_helper.py** with the following.</span></span>

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="GetTokenSnippet":::

    <span data-ttu-id="56fc8-160">Este método comprueba primero si el token de acceso ha expirado o está próximo a expirar.</span><span class="sxs-lookup"><span data-stu-id="56fc8-160">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="56fc8-161">Si es así, usa el token de actualización para obtener nuevos tokens y, a continuación, actualiza la memoria caché y devuelve el nuevo token de acceso.</span><span class="sxs-lookup"><span data-stu-id="56fc8-161">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
