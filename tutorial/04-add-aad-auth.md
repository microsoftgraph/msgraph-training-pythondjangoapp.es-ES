<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD. Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph. En este paso, integrará la biblioteca requests [-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) en la aplicación.

Cree un nuevo archivo en la raíz del proyecto denominado `oauth_settings.yml`y agregue el contenido siguiente.

```text
app_id: YOUR_APP_ID_HERE
app_secret: YOUR_APP_PASSWORD_HERE
redirect: http://localhost:8000/tutorial/callback
scopes: openid profile offline_access user.read calendars.read
authority: https://login.microsoftonline.com/common
authorize_endpoint: /oauth2/v2.0/authorize
token_endpoint: /oauth2/v2.0/token
```

Reemplace `YOUR_APP_ID_HERE` por el identificador de la aplicación del portal de registro de la `YOUR_APP_SECRET_HERE` aplicación y reemplace por la contraseña que ha generado.

> [!IMPORTANT]
> Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el `oauth_settings.yml` archivo del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.

## <a name="implement-sign-in"></a>Implementar el inicio de sesión

Cree un nuevo archivo en el `./tutorial` directorio denominado `auth_helper.py` y agregue el siguiente código.

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

Este archivo conservará todos los métodos relacionados con la autenticación. El `get_sign_in_url` genera una dirección URL de autorización y `get_token_from_code` el método intercambia la respuesta de autorización para un token de acceso.

Agregue las siguientes `import` instrucciones a la parte superior `./tutorial/views.py`de.

```python
from django.urls import reverse
from tutorial.auth_helper import get_sign_in_url, get_token_from_code
```

Ahora, agregue un par de vistas nuevas en `./tutorial/views.py` el archivo.

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

Define dos vistas nuevas: `signin` y. `callback`

La `signin` acción genera la dirección URL de inicio de sesión de Azure `state` ad, guarda el valor generado por el cliente de OAuth y, a continuación, redirige el explorador a la página de inicio de sesión de Azure ad.

La `callback` acción es donde Azure redirige después de que se complete el inicio de sesión. Esa acción garantiza que el `state` valor coincida con el valor guardado y, a continuación, usa el código de autorización enviado por Azure para solicitar un token de acceso. A continuación, se redirige de nuevo a la Página principal con el token de acceso en el valor de error temporal. Usará esto para comprobar que el inicio de sesión está funcionando antes de continuar. Antes de realizar la prueba, debe agregar las vistas a `./tutorial/urls.py`.

```python
path('signin', views.sign_in, name='signin'),
path('callback', views.callback, name='callback'),
```

Reemplace la `<a href="#" class="btn btn-primary btn-large">Click here to sign in</a>` línea `./tutorial/templates/tutorial/home.html` con lo siguiente.

```html
<a href="{% url 'signin' %}" class="btn btn-primary btn-large">Click here to sign in</a>
```

Reemplace la `<a href="#" class="nav-link">Sign In</a>` línea `./tutorial/templates/tutorial/layout.html` con lo siguiente.

```html
<a href="{% url 'signin' %}" class="nav-link">Sign In</a>
```

Inicie el servidor y vaya a `https://localhost:8000/tutorial`. Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a. Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados. El explorador redirige a la aplicación, que muestra el token.

### <a name="get-user-details"></a>Obtener detalles del usuario

Cree un nuevo archivo en el `./tutorial` directorio denominado `graph_helper.py` y agregue el siguiente código.

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

El `get_user` método realiza una solicitud GET al punto de conexión `/me` de Microsoft Graph para obtener el perfil del usuario, mediante el token de acceso adquirido anteriormente.

Actualice el `callback` método en `./tutorial/views.py` para obtener el perfil del usuario de Microsoft Graph.

En primer lugar, agregue `import` la siguiente instrucción a la parte superior del archivo.

```python
from tutorial.graph_helper import get_user
```

Reemplace el `callback` método con el código siguiente.

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

El nuevo código llama al `get_user` método para solicitar el perfil del usuario. Agrega el objeto de usuario a la salida temporal para las pruebas.

## <a name="storing-the-tokens"></a>Almacenamiento de tokens

Ahora que puede obtener tokens, es el momento de implementar una forma de almacenarlos en la aplicación. Dado que se trata de una aplicación de ejemplo, por razones de simplicidad, se almacenará en la sesión. Una aplicación real usaría una solución de almacenamiento seguro más confiable, como una base de datos.

Agregue los siguientes métodos nuevos a `./tutorial/auth_helper.py`.

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

A continuación, actualice `callback` la función `./tutorial/views.py` en para almacenar los tokens en la sesión y vuelva a redirigir a la Página principal. Reemplace la `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` línea por lo siguiente.

```python
from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
```

Reemplace el `callback` método por lo siguiente.

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

## <a name="implement-sign-out"></a>Implementación de cierre de sesión

Antes de probar esta nueva característica, agregue una forma de cerrar sesión. Agregue una nueva `sign_out` vista en `./tutorial/views.py`.

```python
def sign_out(request):
  # Clear out the user and token
  remove_user_and_token(request)

  return HttpResponseRedirect(reverse('home'))
```

Ahora, agregue esta vista `./tutorial/urls.py`a.

```python
path('signout', views.sign_out, name='signout'),
```

Actualice el vínculo **Cerrar sesión** en `./tutorial/templates/tutorial/layout.html` para usar esta nueva vista. Reemplace la `<a href="#" class="dropdown-item">Sign Out</a>` línea por lo siguiente.

```html
<a href="{% url 'signout' %}" class="dropdown-item">Sign Out</a>
```

Reinicie el servidor y pase por el proceso de inicio de sesión. Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.

![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** . Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.

![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualizar tokens

En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API. Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.

Sin embargo, este token es de corta duración. El token expira una hora después de su emisión. Aquí es donde el token de actualización se vuelve útil. El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo. Actualice el código de administración de tokens para implementar la actualización de tokens.

Reemplace el método `get_token` existente `./tutorial/auth_helper.py` con lo siguiente.

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

Este método comprueba primero si el token de acceso ha expirado o está próximo a expirar. Si es así, usa el token de actualización para obtener nuevos tokens y, a continuación, actualiza la memoria caché y devuelve el nuevo token de acceso.
