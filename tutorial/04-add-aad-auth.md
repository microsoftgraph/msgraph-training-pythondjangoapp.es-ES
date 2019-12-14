<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0c1db-101">En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD.</span><span class="sxs-lookup"><span data-stu-id="0c1db-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="0c1db-102">Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0c1db-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="0c1db-103">En este paso, integrará la biblioteca [requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0c1db-103">In this step you will integrate the [Requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) library into the application.</span></span>

<span data-ttu-id="0c1db-104">Cree un nuevo archivo en la raíz del proyecto denominado `oauth_settings.yml`y agregue el contenido siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-104">Create a new file in the root of the project named `oauth_settings.yml`, and add the following content.</span></span>

```text
app_id: "YOUR_APP_ID_HERE"
app_secret: "YOUR_APP_PASSWORD_HERE"
redirect: "http://localhost:8000/tutorial/callback"
scopes: "openid profile offline_access user.read calendars.read"
authority: "https://login.microsoftonline.com/common"
authorize_endpoint: "/oauth2/v2.0/authorize"
token_endpoint: "/oauth2/v2.0/token"
```

<span data-ttu-id="0c1db-105">Reemplace `YOUR_APP_ID_HERE` por el identificador de la aplicación del portal de registro de la `YOUR_APP_SECRET_HERE` aplicación y reemplace por la contraseña que ha generado.</span><span class="sxs-lookup"><span data-stu-id="0c1db-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0c1db-106">Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `oauth_settings.yml` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.</span><span class="sxs-lookup"><span data-stu-id="0c1db-106">If you're using source control such as git, now would be a good time to exclude the `oauth_settings.yml` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="0c1db-107">Implementar el inicio de sesión</span><span class="sxs-lookup"><span data-stu-id="0c1db-107">Implement sign-in</span></span>

<span data-ttu-id="0c1db-108">Cree un nuevo archivo en el `./tutorial` directorio denominado `auth_helper.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="0c1db-108">Create a new file in the `./tutorial` directory named `auth_helper.py` and add the following code.</span></span>

```python
import yaml
from requests_oauthlib import OAuth2Session
import os
import time

# This is necessary for testing with non-HTTPS localhost
# Remove this if deploying to production
os.environ['OAUTHLIB_INSECURE_TRANSPORT'] = '1'

# This is necessary because Azure does not guarantee
# to return scopes in the same case and order as requested
os.environ['OAUTHLIB_RELAX_TOKEN_SCOPE'] = '1'
os.environ['OAUTHLIB_IGNORE_SCOPE_CHANGE'] = '1'

# Load the oauth_settings.yml file
stream = open('oauth_settings.yml', 'r')
settings = yaml.load(stream, yaml.SafeLoader)
authorize_url = '{0}{1}'.format(settings['authority'], settings['authorize_endpoint'])
token_url = '{0}{1}'.format(settings['authority'], settings['token_endpoint'])

# Method to generate a sign-in url
def get_sign_in_url():
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  sign_in_url, state = aad_auth.authorization_url(authorize_url, prompt='login')

  return sign_in_url, state

# Method to exchange auth code for access token
def get_token_from_code(callback_url, expected_state):
  # Initialize the OAuth client
  aad_auth = OAuth2Session(settings['app_id'],
    state=expected_state,
    scope=settings['scopes'],
    redirect_uri=settings['redirect'])

  token = aad_auth.fetch_token(token_url,
    client_secret = settings['app_secret'],
    authorization_response=callback_url)

  return token
```

<span data-ttu-id="0c1db-109">Este archivo conservará todos los métodos relacionados con la autenticación.</span><span class="sxs-lookup"><span data-stu-id="0c1db-109">This file will hold all of your authentication-related methods.</span></span> <span data-ttu-id="0c1db-110">El `get_sign_in_url` genera una dirección URL de autorización y `get_token_from_code` el método intercambia la respuesta de autorización para un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="0c1db-110">The `get_sign_in_url` generates an authorization URL, and the `get_token_from_code` method exchanges the authorization response for an access token.</span></span>

<span data-ttu-id="0c1db-111">Agregue las siguientes `import` instrucciones a la parte superior `./tutorial/views.py`de.</span><span class="sxs-lookup"><span data-stu-id="0c1db-111">Add the following `import` statements to the top of `./tutorial/views.py`.</span></span>

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

<span data-ttu-id="0c1db-112">Ahora, agregue un par de vistas nuevas en `./tutorial/views.py` el archivo.</span><span class="sxs-lookup"><span data-stu-id="0c1db-112">Now add a couple of new views in the `./tutorial/views.py` file.</span></span>

```python
def sign_in(request):
  # Get the sign-in URL
  sign_in_url, state = get_sign_in_url()
  # Save the expected state so we can validate in the callback
  request.session['auth_state'] = state
  # Redirect to the Azure sign-in page
  return HttpResponseRedirect(sign_in_url)

def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)
  # Temporary! Save the response in an error so it's displayed
  request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(token) }
  return HttpResponseRedirect(reverse('home'))
```

<span data-ttu-id="0c1db-113">Define dos vistas nuevas: `signin` y. `callback`</span><span class="sxs-lookup"><span data-stu-id="0c1db-113">This defines two new views: `signin` and `callback`.</span></span>

<span data-ttu-id="0c1db-114">La `signin` acción genera la dirección URL de inicio de sesión de Azure `state` ad, guarda el valor generado por el cliente de OAuth y, a continuación, redirige el explorador a la página de inicio de sesión de Azure ad.</span><span class="sxs-lookup"><span data-stu-id="0c1db-114">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

<span data-ttu-id="0c1db-115">La `callback` acción es donde Azure redirige después de que se complete el inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="0c1db-115">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="0c1db-116">Esa acción garantiza que el `state` valor coincida con el valor guardado y, a continuación, usa el código de autorización enviado por Azure para solicitar un token de acceso.</span><span class="sxs-lookup"><span data-stu-id="0c1db-116">That action makes sure the `state` value matches the saved value, then uses the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="0c1db-117">A continuación, se redirige de nuevo a la Página principal con el token de acceso en el valor de error temporal.</span><span class="sxs-lookup"><span data-stu-id="0c1db-117">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="0c1db-118">Usará esto para comprobar que el inicio de sesión está funcionando antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="0c1db-118">You'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="0c1db-119">Antes de realizar la prueba, debe agregar las vistas a `./tutorial/urls.py`.</span><span class="sxs-lookup"><span data-stu-id="0c1db-119">Before you test, you need to add the views to `./tutorial/urls.py`.</span></span>

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

<span data-ttu-id="0c1db-120">Reemplace la `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` línea `./tutorial/templates/tutorial/home.html` con lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-120">Replace the `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` line in `./tutorial/templates/tutorial/home.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

<span data-ttu-id="0c1db-121">Reemplace la `<a href="#" class="nav-link">Sign In</a>` línea `./tutorial/templates/tutorial/layout.html` con lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-121">Replace the `<a href="#" class="nav-link">Sign In</a>` line in `./tutorial/templates/tutorial/layout.html` with the following.</span></span>

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

<span data-ttu-id="0c1db-122">Inicie el servidor y vaya a `https://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="0c1db-122">Start the server and browse to `https://localhost:8000/tutorial`.</span></span> <span data-ttu-id="0c1db-123">Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a.</span><span class="sxs-lookup"><span data-stu-id="0c1db-123">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="0c1db-124">Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados.</span><span class="sxs-lookup"><span data-stu-id="0c1db-124">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="0c1db-125">El explorador redirige a la aplicación, que muestra el token.</span><span class="sxs-lookup"><span data-stu-id="0c1db-125">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="0c1db-126">Obtener detalles del usuario</span><span class="sxs-lookup"><span data-stu-id="0c1db-126">Get user details</span></span>

<span data-ttu-id="0c1db-127">Cree un nuevo archivo en el `./tutorial` directorio denominado `graph_helper.py` y agregue el siguiente código.</span><span class="sxs-lookup"><span data-stu-id="0c1db-127">Create a new file in the `./tutorial` directory named `graph_helper.py` and add the following code.</span></span>

```python
from requests_oauthlib import OAuth2Session

graph_url = 'https://graph.microsoft.com/v1.0'

def get_user(token):
  graph_client = OAuth2Session(token=token)
  # Send GET to /me
  user = graph_client.get('{0}/me'.format(graph_url))
  # Return the JSON result
  return user.json()
```

<span data-ttu-id="0c1db-128">El `get_user` método realiza una solicitud GET al punto de conexión `/me` de Microsoft Graph para obtener el perfil del usuario, mediante el token de acceso adquirido anteriormente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-128">The `get_user` method makes a GET request to the Microsoft Graph `/me` endpoint to get the user's profile, using the access token you acquired previously.</span></span>

<span data-ttu-id="0c1db-129">Actualice el `callback` método en `./tutorial/views.py` para obtener el perfil del usuario de Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0c1db-129">Update the `callback` method in `./tutorial/views.py` to get the user's profile from Microsoft Graph.</span></span>

<span data-ttu-id="0c1db-130">En primer lugar, agregue `import` la siguiente instrucción a la parte superior del archivo.</span><span class="sxs-lookup"><span data-stu-id="0c1db-130">First, add the following `import` statement to the top of the file.</span></span>

```python
from tutorial.graph_helper import get_user
```

<span data-ttu-id="0c1db-131">Reemplace el `callback` método con el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-131">Replace the `callback` method with the following code.</span></span>

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

<span data-ttu-id="0c1db-132">El nuevo código llama al `get_user` método para solicitar el perfil del usuario.</span><span class="sxs-lookup"><span data-stu-id="0c1db-132">The new code calls the `get_user` method to request the user's profile.</span></span> <span data-ttu-id="0c1db-133">Agrega el objeto de usuario a la salida temporal para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="0c1db-133">It adds the user object to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="0c1db-134">Almacenamiento de tokens</span><span class="sxs-lookup"><span data-stu-id="0c1db-134">Storing the tokens</span></span>

<span data-ttu-id="0c1db-135">Ahora que puede obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0c1db-135">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="0c1db-136">Dado que se trata de una aplicación de ejemplo, por razones de simplicidad, se almacenará en la sesión.</span><span class="sxs-lookup"><span data-stu-id="0c1db-136">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="0c1db-137">Una aplicación real usaría una solución de almacenamiento seguro más confiable, como una base de datos.</span><span class="sxs-lookup"><span data-stu-id="0c1db-137">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="0c1db-138">Agregue los siguientes métodos nuevos a `./tutorial/auth_helper.py`.</span><span class="sxs-lookup"><span data-stu-id="0c1db-138">Add the following new methods to `./tutorial/auth_helper.py`.</span></span>

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

<span data-ttu-id="0c1db-139">A continuación, actualice `callback` la función `./tutorial/views.py` en para almacenar los tokens en la sesión y vuelva a redirigir a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="0c1db-139">Then, update the `callback` function in `./tutorial/views.py` to store the tokens in the session and redirect back to the main page.</span></span> <span data-ttu-id="0c1db-140">Reemplace la `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` línea por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-140">Replace the `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` line with the following.</span></span>

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

<span data-ttu-id="0c1db-141">Reemplace el `callback` método por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-141">Replace the `callback` method with the following.</span></span>

```python
def callback(request):
  # Get the state saved in session
  expected_state = request.session.pop('auth_state', '')
  # Make the token request
  token = get_token_from_code(request.get_full_path(), expected_state)

  # Get the user's profile
  user = get_user(token)

  # Save token and user
  store_token(request, token)
  store_user(request, user)

  return HttpResponseRedirect(reverse('home'))
```

## <a name="implement-sign-out"></a><span data-ttu-id="0c1db-142">Implementación de cierre de sesión</span><span class="sxs-lookup"><span data-stu-id="0c1db-142">Implement sign-out</span></span>

<span data-ttu-id="0c1db-143">Antes de probar esta nueva característica, agregue una forma de cerrar sesión. Agregue una nueva `sign_out` vista en `./tutorial/views.py`.</span><span class="sxs-lookup"><span data-stu-id="0c1db-143">Before you test this new feature, add a way to sign out. Add a new `sign_out` view in `./tutorial/views.py`.</span></span>

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

<span data-ttu-id="0c1db-144">Ahora, agregue esta vista `./tutorial/urls.py`a.</span><span class="sxs-lookup"><span data-stu-id="0c1db-144">Now add this view to `./tutorial/urls.py`.</span></span>

```python
path('signout', views.sign_out, name='signout'),
```

<span data-ttu-id="0c1db-145">Actualice el vínculo **Cerrar sesión** en `./tutorial/templates/tutorial/layout.html` para usar esta nueva vista.</span><span class="sxs-lookup"><span data-stu-id="0c1db-145">Update the **Sign Out** link in `./tutorial/templates/tutorial/layout.html` to use this new view.</span></span> <span data-ttu-id="0c1db-146">Reemplace la `<a href="#" class="dropdown-item">Sign Out</a>` línea por lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-146">Replace the `<a href="#" class="dropdown-item">Sign Out</a>` line with the following.</span></span>

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

<span data-ttu-id="0c1db-147">Reinicie el servidor y pase por el proceso de inicio de sesión.</span><span class="sxs-lookup"><span data-stu-id="0c1db-147">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="0c1db-148">Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.</span><span class="sxs-lookup"><span data-stu-id="0c1db-148">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

<span data-ttu-id="0c1db-150">Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** .</span><span class="sxs-lookup"><span data-stu-id="0c1db-150">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="0c1db-151">Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.</span><span class="sxs-lookup"><span data-stu-id="0c1db-151">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="0c1db-153">Actualizar tokens</span><span class="sxs-lookup"><span data-stu-id="0c1db-153">Refreshing tokens</span></span>

<span data-ttu-id="0c1db-154">En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API.</span><span class="sxs-lookup"><span data-stu-id="0c1db-154">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="0c1db-155">Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.</span><span class="sxs-lookup"><span data-stu-id="0c1db-155">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="0c1db-156">Sin embargo, este token es de corta duración.</span><span class="sxs-lookup"><span data-stu-id="0c1db-156">However, this token is short-lived.</span></span> <span data-ttu-id="0c1db-157">El token expira una hora después de su emisión.</span><span class="sxs-lookup"><span data-stu-id="0c1db-157">The token expires an hour after it is issued.</span></span> <span data-ttu-id="0c1db-158">Aquí es donde el token de actualización se vuelve útil.</span><span class="sxs-lookup"><span data-stu-id="0c1db-158">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="0c1db-159">El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo.</span><span class="sxs-lookup"><span data-stu-id="0c1db-159">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="0c1db-160">Actualice el código de administración de tokens para implementar la actualización de tokens.</span><span class="sxs-lookup"><span data-stu-id="0c1db-160">Update the token management code to implement token refresh.</span></span>

<span data-ttu-id="0c1db-161">Reemplace el método `get_token` existente `./tutorial/auth_helper.py` con lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="0c1db-161">Replace the existing `get_token` method in `./tutorial/auth_helper.py` with the following.</span></span>

```python
def get_token(request):
  token = request.session['oauth_token']
  if token != None:
    # Check expiration
    now = time.time()
    # Subtract 5 minutes from expiration to account for clock skew
    expire_time = token['expires_at'] - 300
    if now >= expire_time:
      # Refresh the token
      aad_auth = OAuth2Session(settings['app_id'],
        token = token,
        scope=settings['scopes'],
        redirect_uri=settings['redirect'])

      refresh_params = {
        'client_id': settings['app_id'],
        'client_secret': settings['app_secret'],
      }
      new_token = aad_auth.refresh_token(token_url, **refresh_params)

      # Save new token
      store_token(request, new_token)

      # Return new access token
      return new_token

    else:
      # Token still valid, just return it
      return token
```

<span data-ttu-id="0c1db-162">Este método comprueba primero si el token de acceso ha expirado o está próximo a expirar.</span><span class="sxs-lookup"><span data-stu-id="0c1db-162">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="0c1db-163">Si es así, usa el token de actualización para obtener nuevos tokens y, a continuación, actualiza la memoria caché y devuelve el nuevo token de acceso.</span><span class="sxs-lookup"><span data-stu-id="0c1db-163">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
