<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="28039-101">Este tutorial le enseña a crear una aplicación Web de Python Django que use la API de Microsoft Graph para recuperar la información de calendario de un usuario.</span><span class="sxs-lookup"><span data-stu-id="28039-101">This tutorial teaches you how to build a Python Django web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="28039-102">Si prefiere descargar solo el tutorial completo, puede descargarlo de dos maneras.</span><span class="sxs-lookup"><span data-stu-id="28039-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="28039-103">Descargue el [Inicio rápido de Python](https://developer.microsoft.com/graph/quick-start?platform=option-Python) para obtener el código de trabajo en minutos.</span><span class="sxs-lookup"><span data-stu-id="28039-103">Download the [Python quick start](https://developer.microsoft.com/graph/quick-start?platform=option-Python) to get working code in minutes.</span></span>
> - <span data-ttu-id="28039-104">Descargue o Clone el [repositorio de github](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span><span class="sxs-lookup"><span data-stu-id="28039-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="28039-105">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="28039-105">Prerequisites</span></span>

<span data-ttu-id="28039-106">Antes de iniciar este tutorial, debe tener [Python](https://www.python.org/) (con [PIP](https://pypi.org/project/pip/)) instalado en el equipo de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="28039-106">Before you start this tutorial, you should have [Python](https://www.python.org/) (with [pip](https://pypi.org/project/pip/)) installed on your development machine.</span></span> <span data-ttu-id="28039-107">Si no tiene Python, visite el vínculo anterior para las opciones de descarga.</span><span class="sxs-lookup"><span data-stu-id="28039-107">If you do not have Python, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="28039-108">Este tutorial se ha escrito con Python versión 3.7.0 y Django versión 2,1.</span><span class="sxs-lookup"><span data-stu-id="28039-108">This tutorial was written with Python version 3.7.0 and Django version 2.1.</span></span> <span data-ttu-id="28039-109">Los pasos de esta guía pueden funcionar con otras versiones, pero no se han probado.</span><span class="sxs-lookup"><span data-stu-id="28039-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="28039-110">Comentarios</span><span class="sxs-lookup"><span data-stu-id="28039-110">Feedback</span></span>

<span data-ttu-id="28039-111">Envíe sus comentarios sobre este tutorial en el [repositorio de github](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span><span class="sxs-lookup"><span data-stu-id="28039-111">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-pythondjangoapp).</span></span>