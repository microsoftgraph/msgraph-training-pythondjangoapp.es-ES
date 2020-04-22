<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, ampliará la aplicación del ejercicio anterior para admitir la autenticación con Azure AD. Esto es necesario para obtener el token de acceso de OAuth necesario para llamar a Microsoft Graph. En este paso, integrará la biblioteca [requests-OAuthlib](https://requests-oauthlib.readthedocs.io/en/latest/) en la aplicación.

1. Cree un nuevo archivo en la raíz del proyecto denominado `oauth_settings.yml`y agregue el contenido siguiente.

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. Reemplace `YOUR_APP_ID_HERE` por el identificador de la aplicación del portal de registro de la `YOUR_APP_SECRET_HERE` aplicación y reemplace por la contraseña que ha generado.

> [!IMPORTANT]
> Si usa un control de código fuente como GIT, ahora sería un buen momento para excluir el archivo **oauth_settings. yml** del control de código fuente para evitar la pérdida inadvertida del identificador de la aplicación y la contraseña.

## <a name="implement-sign-in"></a>Implementar el inicio de sesión

1. Cree un nuevo archivo en el directorio **./tutorial** denominado `auth_helper.py` y agregue el siguiente código.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    Este archivo conservará todos los métodos relacionados con la autenticación. El `get_sign_in_url` genera una dirección URL de autorización y `get_token_from_code` el método intercambia la respuesta de autorización para un token de acceso.

1. Agregue las siguientes `import` instrucciones en la parte superior de **./tutorial/views.py**.

    ```python
    from django.urls import reverse
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code
    ```

1. Agregue una vista de inicio de sesión en el archivo **./tutorial/views.py** .

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. Agregue una vista de devolución de llamada en el archivo **./tutorial/views.py** .

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

    Tenga en cuenta lo que hacen estas vistas:

    - La `signin` acción genera la dirección URL de inicio de sesión de Azure `state` ad, guarda el valor generado por el cliente de OAuth y, a continuación, redirige el explorador a la página de inicio de sesión de Azure ad.

    - La `callback` acción es donde Azure redirige después de que se complete el inicio de sesión. Esa acción garantiza que el `state` valor coincida con el valor guardado y, a continuación, usa el código de autorización enviado por Azure para solicitar un token de acceso. A continuación, se redirige de nuevo a la Página principal con el token de acceso en el valor de error temporal. Usará esto para comprobar que el inicio de sesión está funcionando antes de continuar.

1. Abra **./tutorial/URLs.py** y reemplace las instrucciones `path` existentes por `signin` lo siguiente.

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. Agregue un nuevo `path` para la `callback` vista.

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. Inicie el servidor y vaya a `https://localhost:8000`. Haga clic en el botón de inicio de sesión y se le `https://login.microsoftonline.com`redirigirá a. Inicie sesión con su cuenta de Microsoft y dé su consentimiento a los permisos solicitados. El explorador redirige a la aplicación, que muestra el token.

### <a name="get-user-details"></a>Obtener detalles del usuario

1. Cree un nuevo archivo en el directorio **./tutorial** denominado `graph_helper.py` y agregue el siguiente código.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    El `get_user` método realiza una solicitud GET al punto de conexión `/me` de Microsoft Graph para obtener el perfil del usuario, mediante el token de acceso adquirido anteriormente.

1. Actualice el `callback` método en **./tutorial/views.py** para obtener el perfil del usuario de Microsoft Graph. Agregue la siguiente `import` instrucción a la parte superior del archivo.

    ```python
    from tutorial.graph_helper import get_user
    ```

1. Reemplace el `callback` método con el código siguiente.

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

1. Agregue los siguientes métodos nuevos a **./tutorial/auth_helper. py**.

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

1. Actualice la `callback` función en **./tutorial/views.py** para almacenar los tokens en la sesión y volver a redirigir a la Página principal. Reemplace la `from tutorial.auth_helper import get_sign_in_url, get_token_from_code` línea por lo siguiente.

    ```python
    from tutorial.auth_helper import get_sign_in_url, get_token_from_code, store_token, store_user, remove_user_and_token, get_token
    ```

1. Reemplace el `callback` método por lo siguiente.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>Implementación de cierre de sesión

1. Agregue una nueva `sign_out` vista en **./tutorial/views.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. Abra **./tutorial/URLs.py** y reemplace las instrucciones `path` existentes por `signout` lo siguiente.

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. Reinicie el servidor y pase por el proceso de inicio de sesión. Deberás volver a la Página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.

    ![Una captura de pantalla de la Página principal después de iniciar sesión](./images/add-aad-auth-01.png)

1. Haga clic en el avatar de usuario en la esquina superior derecha para acceder al vínculo **Cerrar sesión** . Al hacer clic en **cerrar** sesión se restablece la sesión y se vuelve a la Página principal.

    ![Captura de pantalla del menú desplegable con el vínculo cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualizar tokens

En este punto, la aplicación tiene un token de acceso, que se envía `Authorization` en el encabezado de las llamadas a la API. Este es el token que permite que la aplicación tenga acceso a Microsoft Graph en nombre del usuario.

Sin embargo, este token es de corta duración. El token expira una hora después de su emisión. Aquí es donde el token de actualización se vuelve útil. El token de actualización permite que la aplicación solicite un nuevo token de acceso sin que el usuario tenga que iniciar sesión de nuevo. Actualice el código de administración de tokens para implementar la actualización de tokens.

1. Reemplace el método `get_token` existente en **./tutorial/auth_helper. py** por lo siguiente.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="GetTokenSnippet":::

    Este método comprueba primero si el token de acceso ha expirado o está próximo a expirar. Si es así, usa el token de actualización para obtener nuevos tokens y, a continuación, actualiza la memoria caché y devuelve el nuevo token de acceso.
