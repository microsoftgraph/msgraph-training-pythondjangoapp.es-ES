<!-- markdownlint-disable MD002 MD041 -->

En este ejercicio, extenderá la aplicación desde el ejercicio anterior para admitir la autenticación con Azure AD. Esto es necesario para obtener el token de acceso OAuth necesario para llamar a Microsoft Graph. En este paso, integrará la [biblioteca MSAL para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) en la aplicación.

1. Cree un nuevo archivo en la raíz del proyecto denominado `oauth_settings.yml` y agregue el siguiente contenido.

    :::code language="ini" source="../demo/graph_tutorial/oauth_settings.yml.example":::

1. Reemplace por el identificador de aplicación del Portal de `YOUR_APP_ID_HERE` registro de aplicaciones y reemplace por la contraseña que `YOUR_APP_SECRET_HERE` generó.

> [!IMPORTANT]
> Si usas el control de código fuente como git, ahora sería un buen momento para excluir el **archivo oauth_settings.yml** del control de código fuente para evitar la pérdida involuntaria del identificador y la contraseña de la aplicación.

## <a name="implement-sign-in"></a>Implementar el inicio de sesión

1. Cree un nuevo archivo en el **directorio ./tutorial** denominado `auth_helper.py` y agregue el siguiente código.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="FirstCodeSnippet":::

    Este archivo contiene todos los métodos relacionados con la autenticación. Genera `get_sign_in_flow` una dirección URL de autorización y el método intercambia la respuesta de autorización para un token de `get_token_from_code` acceso.

1. Agregue las siguientes `import` instrucciones a la parte superior de **./tutorial/views.py**.

    ```python
    from dateutil import tz, parser
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code
    ```

1. Agregue una vista de inicio de sesión en el **archivo ./tutorial/views.py.**

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignInViewSnippet":::

1. Agregue una vista de devolución de llamada en el **archivo ./tutorial/views.py.**

    ```python
    def callback(request):
      # Make the token request
      result = get_token_from_code(request)
      # Temporary! Save the response in an error so it's displayed
      request.session['flash_error'] = { 'message': 'Token retrieved', 'debug': format(result) }
      return HttpResponseRedirect(reverse('home'))
    ```

    Tenga en cuenta lo que hacen estas vistas:

    - La acción genera la dirección URL de inicio de sesión de Azure AD, guarda el flujo generado por el cliente de OAuth y, a continuación, redirige el explorador a la página de inicio `signin` de sesión de Azure AD.

    - La acción es donde Azure redirige una vez completado `callback` el inicio de sesión. Esa acción usa el flujo guardado y la cadena de consulta enviada por Azure para solicitar un token de acceso. A continuación, redirige de nuevo a la página principal con la respuesta en el valor de error temporal. Lo usarás para comprobar que nuestro inicio de sesión funciona antes de seguir adelante.

1. Abra **./tutorial/urls.py** y reemplace las instrucciones `path` existentes `signin` por las siguientes.

    ```python
    path('signin', views.sign_in, name='signin'),
    ```

1. Agregue una nueva `path` para la `callback` vista.

    ```python
    path('callback', views.callback, name='callback'),
    ```

1. Inicie el servidor y vaya a `https://localhost:8000` . Haga clic en el botón de inicio de sesión y se le redirigirá a `https://login.microsoftonline.com` . Inicie sesión con su cuenta de Microsoft y consiente los permisos solicitados. El explorador redirige a la aplicación y muestra la respuesta, incluido el token de acceso.

### <a name="get-user-details"></a>Obtener detalles del usuario

1. Cree un nuevo archivo en el **directorio ./tutorial** denominado `graph_helper.py` y agregue el siguiente código.

    :::code language="python" source="../demo/graph_tutorial/tutorial/graph_helper.py" id="FirstCodeSnippet":::

    El método realiza una solicitud GET al punto de conexión de Microsoft Graph para obtener el perfil del usuario, mediante el token de acceso `get_user` `/me` que adquirió anteriormente.

1. Actualice el `callback` método **en ./tutorial/views.py** para obtener el perfil del usuario de Microsoft Graph. Agregue la siguiente instrucción `import` a la parte superior del archivo.

    ```python
    from tutorial.graph_helper import *
    ```

1. Reemplace el método `callback` con el siguiente código:

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

    El nuevo código llama al `get_user` método para solicitar el perfil del usuario. Agrega el objeto de usuario al resultado temporal para las pruebas.

1. Agregue los métodos nuevos **siguientes a ./tutorial/auth_helper.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/auth_helper.py" id="SecondCodeSnippet":::

1. Actualice la `callback` función **en ./tutorial/views.py** para almacenar el usuario en la sesión y redirigirlo de nuevo a la página principal. Reemplace la `from tutorial.auth_helper import get_sign_in_flow, get_token_from_code` línea por lo siguiente.

    ```python
    from tutorial.auth_helper import get_sign_in_flow, get_token_from_code, store_user, remove_user_and_token, get_token
    ```

1. Reemplace el `callback` método por lo siguiente.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="CallbackViewSnippet":::

## <a name="implement-sign-out"></a>Implementar el inicio de sesión

1. Agregue una nueva `sign_out` vista **en ./tutorial/views.py**.

    :::code language="python" source="../demo/graph_tutorial/tutorial/views.py" id="SignOutViewSnippet":::

1. Abra **./tutorial/urls.py** y reemplace las instrucciones `path` existentes `signout` por las siguientes.

    ```python
    path('signout', views.sign_out, name='signout'),
    ```

1. Reinicie el servidor y pase por el proceso de inicio de sesión. Debes volver a la página principal, pero la interfaz de usuario debe cambiar para indicar que has iniciado sesión.

    ![Una captura de pantalla de la página de inicio después de iniciar sesión](./images/add-aad-auth-01.png)

1. Haga clic en el avatar del usuario en la esquina superior derecha para obtener acceso al vínculo **Cerrar sesión.** Al **hacer clic en** Cerrar sesión, se restablece la sesión y se devuelve a la página principal.

    ![Una captura de pantalla del menú desplegable con el vínculo Cerrar sesión](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Actualización de tokens

En este momento, la aplicación tiene un token de acceso, que se envía en el `Authorization` encabezado de las llamadas API. Este es el token que permite a la aplicación tener acceso a Microsoft Graph en nombre del usuario.

Sin embargo, este token es de corta duración. El token expira una hora después de su emisión. Aquí es donde el token de actualización resulta útil. El token de actualización permite a la aplicación solicitar un nuevo token de acceso sin requerir que el usuario vuelva a iniciar sesión.

Dado que este ejemplo usa MSAL, no es necesario escribir ningún código específico para actualizar el token. El método de MSAL `acquire_token_silent` controla la actualización del token si es necesario.
