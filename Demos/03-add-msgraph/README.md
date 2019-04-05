# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="6c768-101">Cómo ejecutar el proyecto completado</span><span class="sxs-lookup"><span data-stu-id="6c768-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6c768-102">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="6c768-102">Prerequisites</span></span>

<span data-ttu-id="6c768-103">Para ejecutar el proyecto completado en esta carpeta, necesita lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="6c768-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="6c768-104">[Python](https://www.python.org/) (con [PIP](https://pypi.org/project/pip/)) instalado en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="6c768-104">[Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="6c768-105">Si no tiene Python, visite el vínculo anterior para las opciones de descarga.</span><span class="sxs-lookup"><span data-stu-id="6c768-105">If you do not have Python, visit the previous link for download options.</span></span> <span data-ttu-id="6c768-106">(**Nota:** este tutorial se ha escrito con Python versión 3.7.0 y Django versión 2,1.</span><span class="sxs-lookup"><span data-stu-id="6c768-106">(**Note:** This tutorial was written with Python version 3.7.0 and Django version 2.1.</span></span> <span data-ttu-id="6c768-107">Los pasos de esta guía pueden funcionar con otras versiones, pero no se han probado.</span><span class="sxs-lookup"><span data-stu-id="6c768-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="6c768-108">Una cuenta de Microsoft personal con un buzón de correo en Outlook.com o una cuenta profesional o educativa de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6c768-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="6c768-109">Si no tiene una cuenta de Microsoft, hay un par de opciones para obtener una cuenta gratuita:</span><span class="sxs-lookup"><span data-stu-id="6c768-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="6c768-110">Puede [registrarse para obtener una nueva cuenta Microsoft personal](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="6c768-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="6c768-111">Puede [registrarse para el programa de desarrolladores de office 365](https://developer.microsoft.com/office/dev-program) para obtener una suscripción gratuita a Office 365.</span><span class="sxs-lookup"><span data-stu-id="6c768-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="6c768-112">Registro de una aplicación web con el centro de administración de Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="6c768-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="6c768-113">Abra un explorador y vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="6c768-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="6c768-114">Inicie sesión con una **cuenta personal** (también conocido como Microsoft Account) o una **cuenta profesional o educativa**.</span><span class="sxs-lookup"><span data-stu-id="6c768-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="6c768-115">Seleccione **Azure Active Directory** en el panel de navegación de la izquierda y, después, seleccione **registros de aplicaciones (vista previa)** en **administrar**.</span><span class="sxs-lookup"><span data-stu-id="6c768-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations (Preview)** under **Manage**.</span></span>

    ![<span data-ttu-id="6c768-116">Una captura de pantalla de los registros de la aplicación</span><span class="sxs-lookup"><span data-stu-id="6c768-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="6c768-117">Seleccione **registro nuevo**.</span><span class="sxs-lookup"><span data-stu-id="6c768-117">Select **New registration**.</span></span> <span data-ttu-id="6c768-118">En la página **registrar una aplicación** , establezca los valores de la siguiente manera.</span><span class="sxs-lookup"><span data-stu-id="6c768-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="6c768-119">Establezca **el nombre** en `Python Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="6c768-119">Set **Name** to `Python Graph Tutorial`.</span></span>
    - <span data-ttu-id="6c768-120">Establezca **tipos de cuenta compatibles** en **cuentas de cualquier directorio de la organización y cuentas personales de Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="6c768-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="6c768-121">En **URI**de redireccionamiento, establezca la primera lista desplegable para `Web` y establezca el `http://localhost:8000/tutorial/callback`valor en.</span><span class="sxs-lookup"><span data-stu-id="6c768-121">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:8000/tutorial/callback`.</span></span>

    ![Captura de pantalla de la página registrar una aplicación](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="6c768-123">Elija **registrar**.</span><span class="sxs-lookup"><span data-stu-id="6c768-123">Choose **Register**.</span></span> <span data-ttu-id="6c768-124">En la página **tutorial de Python Graph** , copie el valor del **identificador de la aplicación (cliente)** y guárdelo, lo necesitará en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="6c768-124">On the **Python Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Captura de pantalla del identificador de la aplicación del nuevo registro de la aplicación](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="6c768-126">Seleccione **certificados & secretos** en **administrar**.</span><span class="sxs-lookup"><span data-stu-id="6c768-126">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="6c768-127">Seleccione el botón **nuevo secreto de cliente** .</span><span class="sxs-lookup"><span data-stu-id="6c768-127">Select the **New client secret** button.</span></span> <span data-ttu-id="6c768-128">Escriba un valor en **Descripción** y seleccione una de las opciones para **Expires** y elija **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="6c768-128">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Captura de pantalla del cuadro de diálogo Agregar un secreto de cliente](/tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="6c768-130">Copie el valor de secreto de cliente antes de salir de esta página.</span><span class="sxs-lookup"><span data-stu-id="6c768-130">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="6c768-131">Lo necesitará en el paso siguiente.</span><span class="sxs-lookup"><span data-stu-id="6c768-131">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6c768-132">Este secreto de cliente no se vuelve a mostrar nunca, así que asegúrese de copiarlo ahora.</span><span class="sxs-lookup"><span data-stu-id="6c768-132">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Captura de pantalla del secreto de cliente recién agregado](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="6c768-134">Configuración del ejemplo</span><span class="sxs-lookup"><span data-stu-id="6c768-134">Configure the sample</span></span>

1. <span data-ttu-id="6c768-135">Cambie el nombre `oauth_settings.yml.example` del archivo `oauth_settings.yml`a.</span><span class="sxs-lookup"><span data-stu-id="6c768-135">Rename the `oauth_settings.yml.example` file to `oauth_settings.yml`.</span></span>
1. <span data-ttu-id="6c768-136">Edite `oauth_settings.yml` el archivo y realice los cambios siguientes.</span><span class="sxs-lookup"><span data-stu-id="6c768-136">Edit the `oauth_settings.yml` file and make the following changes.</span></span>
    1. <span data-ttu-id="6c768-137">Reemplace `YOUR_APP_ID_HERE` por el **identificador de aplicación** que obtuvo desde el portal de registro de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="6c768-137">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="6c768-138">Reemplace `YOUR_APP_PASSWORD_HERE` por la contraseña que obtuvo en el portal de registro de aplicaciones.</span><span class="sxs-lookup"><span data-stu-id="6c768-138">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="6c768-139">En la interfaz de línea de comandos (CLI), navegue a este directorio y ejecute el siguiente comando para instalar los requisitos.</span><span class="sxs-lookup"><span data-stu-id="6c768-139">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    pip install -r requirements.txt
    ```

1. <span data-ttu-id="6c768-140">En la CLI, ejecute el siguiente comando para inicializar la base de datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6c768-140">In your CLI, run the following command to initialize the app's database.</span></span>

    ```Shell
    python manage.py migrate
    ```

## <a name="run-the-sample"></a><span data-ttu-id="6c768-141">Ejecutar el ejemplo</span><span class="sxs-lookup"><span data-stu-id="6c768-141">Run the sample</span></span>

1. <span data-ttu-id="6c768-142">Ejecute el siguiente comando en su CLI para iniciar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="6c768-142">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    python manage.py runserver
    ```

1. <span data-ttu-id="6c768-143">Abra un explorador y vaya a `http://localhost:8000/tutorial`.</span><span class="sxs-lookup"><span data-stu-id="6c768-143">Open a browser and browse to `http://localhost:8000/tutorial`.</span></span>