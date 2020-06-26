---
title: Publier une SignalR application ASP.net Core sur Azure App service
author: bradygaster
description: Découvrez comment publier une application ASP.NET Core SignalR sur Azure App service.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d3f48b3171012b03fcaf7665c2089b27d37bbeca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408836"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a><span data-ttu-id="0e907-103">Publier une SignalR application ASP.net Core sur Azure App service</span><span class="sxs-lookup"><span data-stu-id="0e907-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="0e907-104">Par [Brady Gaster](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="0e907-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="0e907-105">[Azure App service](/azure/app-service/app-service-web-overview) est un service de plateforme [Microsoft Cloud Computing](https://azure.microsoft.com/) pour héberger des applications web, y compris ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="0e907-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="0e907-106">Cet article fait référence à la publication d’une SignalR application ASP.net core à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0e907-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="0e907-107">Pour plus d’informations, consultez [ SignalR service pour Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="0e907-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="0e907-108">Publier l’application</span><span class="sxs-lookup"><span data-stu-id="0e907-108">Publish the app</span></span>

<span data-ttu-id="0e907-109">Cet article traite de la publication à l’aide des outils de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0e907-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="0e907-110">Visual Studio Code utilisateurs peuvent utiliser des commandes [Azure CLI](/cli/azure) pour publier des applications sur Azure.</span><span class="sxs-lookup"><span data-stu-id="0e907-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="0e907-111">Pour plus d’informations, consultez [publier une application ASP.net Core sur Azure à l’aide des outils en ligne de commande](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="0e907-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="0e907-112">Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.</span><span class="sxs-lookup"><span data-stu-id="0e907-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="0e907-113">Confirmez que **app service** et **créer nouveau** sont sélectionnés dans la boîte de dialogue **choisir une cible de publication** .</span><span class="sxs-lookup"><span data-stu-id="0e907-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="0e907-114">Sélectionnez **créer un profil** dans la liste déroulante du bouton **publier** .</span><span class="sxs-lookup"><span data-stu-id="0e907-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="0e907-115">Entrez les informations décrites dans le tableau suivant de la boîte de dialogue **créer un app service** , puis sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="0e907-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create**.</span></span>

   | <span data-ttu-id="0e907-116">Élément</span><span class="sxs-lookup"><span data-stu-id="0e907-116">Item</span></span>               | <span data-ttu-id="0e907-117">Description</span><span class="sxs-lookup"><span data-stu-id="0e907-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="0e907-118">**Nom**</span><span class="sxs-lookup"><span data-stu-id="0e907-118">**Name**</span></span>           | <span data-ttu-id="0e907-119">Nom unique de l’application.</span><span class="sxs-lookup"><span data-stu-id="0e907-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="0e907-120">**Abonnement**</span><span class="sxs-lookup"><span data-stu-id="0e907-120">**Subscription**</span></span>   | <span data-ttu-id="0e907-121">Abonnement Azure utilisé par l’application.</span><span class="sxs-lookup"><span data-stu-id="0e907-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="0e907-122">**Groupe de ressources**</span><span class="sxs-lookup"><span data-stu-id="0e907-122">**Resource Group**</span></span> | <span data-ttu-id="0e907-123">Groupe de ressources associées auxquelles l’application appartient.</span><span class="sxs-lookup"><span data-stu-id="0e907-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="0e907-124">**Plan d’hébergement**</span><span class="sxs-lookup"><span data-stu-id="0e907-124">**Hosting Plan**</span></span>   | <span data-ttu-id="0e907-125">Plan de tarification pour l’application Web.</span><span class="sxs-lookup"><span data-stu-id="0e907-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="0e907-126">Sélectionnez le \*\* SignalR service Azure\*\* dans la liste déroulante Ajouter des **dépendances**  >  **Add** :</span><span class="sxs-lookup"><span data-stu-id="0e907-126">Select the **Azure SignalR Service** in the **Dependencies** > **Add** drop-down list:</span></span>

   <span data-ttu-id="0e907-127">![Zone dépendances présentant la sélection du SignalR service Azure dans la liste déroulante Ajouter](publish-to-azure-web-app/_static/signalr-service-dependency.png)</span><span class="sxs-lookup"><span data-stu-id="0e907-127">![Dependencies area showing the selection of Azure SignalR Service in the Add drop-down list](publish-to-azure-web-app/_static/signalr-service-dependency.png)</span></span>

1. <span data-ttu-id="0e907-128">Dans la boîte de dialogue \*\* SignalR service Azure\*\* , sélectionnez **créer une nouvelle SignalR instance de service Azure**.</span><span class="sxs-lookup"><span data-stu-id="0e907-128">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance**.</span></span>

1. <span data-ttu-id="0e907-129">Fournissez un **nom**, un **groupe de ressources**et un **emplacement**.</span><span class="sxs-lookup"><span data-stu-id="0e907-129">Provide a **Name**, **Resource Group**, and **Location**.</span></span> <span data-ttu-id="0e907-130">Revenez à la boîte de dialogue \*\* SignalR service Azure\*\* , puis sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="0e907-130">Return to the **Azure SignalR Service** dialog and select **Add**.</span></span>

<span data-ttu-id="0e907-131">Visual Studio effectue les tâches suivantes :</span><span class="sxs-lookup"><span data-stu-id="0e907-131">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="0e907-132">Crée un profil de publication contenant les paramètres de publication.</span><span class="sxs-lookup"><span data-stu-id="0e907-132">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="0e907-133">Crée une *application Web Azure* avec les détails fournis.</span><span class="sxs-lookup"><span data-stu-id="0e907-133">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="0e907-134">Publie l’application.</span><span class="sxs-lookup"><span data-stu-id="0e907-134">Publishes the app.</span></span>
* <span data-ttu-id="0e907-135">Lance un navigateur, qui charge l’application Web.</span><span class="sxs-lookup"><span data-stu-id="0e907-135">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="0e907-136">Le format de l’URL de l’application est `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="0e907-136">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="0e907-137">Par exemple, une application nommée `SignalRChatApp` a une URL de `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="0e907-137">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="0e907-138">Si une erreur de *passerelle HTTP 502,2-incorrecte* se produit lors du déploiement d’une application qui cible une version préliminaire de .net Core, consultez [déployer ASP.net core version préliminaire dans Azure App service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) pour la résoudre.</span><span class="sxs-lookup"><span data-stu-id="0e907-138">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="0e907-139">Configurer l’application dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0e907-139">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="0e907-140">*Cette section s’applique uniquement aux applications qui n’utilisent pas le SignalR service Azure.*</span><span class="sxs-lookup"><span data-stu-id="0e907-140">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="0e907-141">Si l’application utilise le SignalR service Azure, le App service ne nécessite pas la configuration de l’affinité application Request Routing (arr) et des sockets Web décrits dans cette section.</span><span class="sxs-lookup"><span data-stu-id="0e907-141">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="0e907-142">Les clients connectent leurs Sockets Web au SignalR service Azure, et non directement à l’application.</span><span class="sxs-lookup"><span data-stu-id="0e907-142">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="0e907-143">Pour les applications hébergées sans le SignalR service Azure, activez :</span><span class="sxs-lookup"><span data-stu-id="0e907-143">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="0e907-144">[Affinité arr](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) pour acheminer les demandes d’un utilisateur vers la même instance de App service.</span><span class="sxs-lookup"><span data-stu-id="0e907-144">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="0e907-145">La valeur par défaut est **on**.</span><span class="sxs-lookup"><span data-stu-id="0e907-145">The default setting is **On**.</span></span>
* <span data-ttu-id="0e907-146">[WebSockets](xref:fundamentals/websockets) pour permettre au transport Web Sockets de fonctionner.</span><span class="sxs-lookup"><span data-stu-id="0e907-146">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="0e907-147">La valeur par défaut est **off**.</span><span class="sxs-lookup"><span data-stu-id="0e907-147">The default setting is **Off**.</span></span>

1. <span data-ttu-id="0e907-148">Dans le Portail Azure, accédez à l’application Web dans **app services**.</span><span class="sxs-lookup"><span data-stu-id="0e907-148">In the Azure portal, navigate to the web app in **App Services**.</span></span>
1. <span data-ttu-id="0e907-149">Ouvrez **Configuration**  >  **paramètres généraux**de configuration.</span><span class="sxs-lookup"><span data-stu-id="0e907-149">Open **Configuration** > **General settings**.</span></span>
1. <span data-ttu-id="0e907-150">Affectez la valeur **on**à **Web Sockets** .</span><span class="sxs-lookup"><span data-stu-id="0e907-150">Set **Web sockets** to **On**.</span></span>
1. <span data-ttu-id="0e907-151">Vérifiez que l' **affinité arr** est définie sur **on**.</span><span class="sxs-lookup"><span data-stu-id="0e907-151">Verify that **ARR affinity** is set to **On**.</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="0e907-152">Limites du plan de App Service</span><span class="sxs-lookup"><span data-stu-id="0e907-152">App Service Plan limits</span></span>

<span data-ttu-id="0e907-153">Les sockets Web et les autres transports sont limités en fonction du plan de App Service sélectionné.</span><span class="sxs-lookup"><span data-stu-id="0e907-153">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="0e907-154">Pour plus d’informations, consultez les sections *limites de services Cloud Azure* et *limites de App service* de l’article [abonnement Azure et limites, quotas et contraintes du service](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="0e907-154">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e907-155">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0e907-155">Additional resources</span></span>

* <span data-ttu-id="0e907-156">[Qu’est-ce que le SignalR service Azure ?](/azure/azure-signalr/signalr-overview)</span><span class="sxs-lookup"><span data-stu-id="0e907-156">[What is Azure SignalR Service?](/azure/azure-signalr/signalr-overview)</span></span>
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="0e907-157">Publier une application ASP.NET Core sur Azure avec des outils en ligne de commande</span><span class="sxs-lookup"><span data-stu-id="0e907-157">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="0e907-158">Héberger et déployer ASP.NET Core des applications en version préliminaire sur Azure</span><span class="sxs-lookup"><span data-stu-id="0e907-158">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
