---
title: Déployer des applications ASP.NET Core sur Azure App Service
author: bradygaster
description: Cet article contient des liens vers des ressources d’hébergement et de déploiement Azure.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/16/2019
uid: host-and-deploy/azure-apps/index
ms.openlocfilehash: ba9671f68a0faf99ff5232a6d5dd132d0a1d5ac5
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665143"
---
# <a name="deploy-aspnet-core-apps-to-azure-app-service"></a><span data-ttu-id="f00a6-103">Déployer des applications ASP.NET Core sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f00a6-103">Deploy ASP.NET Core apps to Azure App Service</span></span>

<span data-ttu-id="f00a6-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) est un [service de plateforme de cloud computing Microsoft](https://azure.microsoft.com/) qui permet d’héberger des applications web, notamment ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f00a6-104">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span>

## <a name="useful-resources"></a><span data-ttu-id="f00a6-105">Ressources utiles</span><span class="sxs-lookup"><span data-stu-id="f00a6-105">Useful resources</span></span>

<span data-ttu-id="f00a6-106">[App Service Documentation](/azure/app-service/) héberge la documentation, les tutoriels, les exemples, les guides pratiques et d’autres ressources Azure.</span><span class="sxs-lookup"><span data-stu-id="f00a6-106">[App Service Documentation](/azure/app-service/) is the home for Azure Apps documentation, tutorials, samples, how-to guides, and other resources.</span></span> <span data-ttu-id="f00a6-107">Voici deux didacticiels importants qui abordent l’hébergement d’applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f00a6-107">Two notable tutorials that pertain to hosting ASP.NET Core apps are:</span></span>

[<span data-ttu-id="f00a6-108">Créer une application web ASP.NET Core dans Azure</span><span class="sxs-lookup"><span data-stu-id="f00a6-108">Create an ASP.NET Core web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)  
<span data-ttu-id="f00a6-109">Utilisez Visual Studio pour créer et déployer une application web ASP.NET Core dans Azure App Service sur Windows.</span><span class="sxs-lookup"><span data-stu-id="f00a6-109">Use Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service on Windows.</span></span>

[<span data-ttu-id="f00a6-110">Créer une application ASP.NET Core dans App Service sur Linux</span><span class="sxs-lookup"><span data-stu-id="f00a6-110">Create an ASP.NET Core app in App Service on Linux</span></span>](/azure/app-service/containers/quickstart-dotnetcore)  
<span data-ttu-id="f00a6-111">Utilisez la ligne de commande pour créer et déployer une application web ASP.NET Core dans Azure App Service sur Linux.</span><span class="sxs-lookup"><span data-stu-id="f00a6-111">Use the command line to create and deploy an ASP.NET Core web app to Azure App Service on Linux.</span></span>

<span data-ttu-id="f00a6-112">Voir le [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) pour la version de ASP.NET Core disponible sur le service Azure App.</span><span class="sxs-lookup"><span data-stu-id="f00a6-112">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

<span data-ttu-id="f00a6-113">Abonnez-vous au référentiel [App Service Announcements](https://github.com/Azure/app-service-announcements/) et surveillez les problèmes.</span><span class="sxs-lookup"><span data-stu-id="f00a6-113">Subscribe to the [App Service Announcements](https://github.com/Azure/app-service-announcements/) repository and monitor the issues.</span></span> <span data-ttu-id="f00a6-114">L’équipe d’App Service publie régulièrement des annonces et des scénarios arrivant dans App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-114">The App Service team regularly posts announcements and scenarios arriving in App Service.</span></span>

<span data-ttu-id="f00a6-115">Les articles suivants sont disponibles dans la documentation d’ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="f00a6-115">The following articles are available in ASP.NET Core documentation:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="f00a6-116">Découvrez comment publier une application ASP.NET Core sur Azure App Service à l’aide de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f00a6-116">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

<xref:host-and-deploy/azure-apps/azure-continuous-deployment>  
<span data-ttu-id="f00a6-117">Découvrez comment créer une application web ASP.NET Core à l’aide de Visual Studio et comment la déployer sur Azure App Service en utilisant Git pour le déploiement continu.</span><span class="sxs-lookup"><span data-stu-id="f00a6-117">Learn how to create an ASP.NET Core web app using Visual Studio and deploy it to Azure App Service using Git for continuous deployment.</span></span>

[<span data-ttu-id="f00a6-118">Créer votre premier pipeline</span><span class="sxs-lookup"><span data-stu-id="f00a6-118">Create your first pipeline</span></span>](/azure/devops/pipelines/get-started-yaml)  
<span data-ttu-id="f00a6-119">Configurez une build CI pour une application ASP.NET Core, puis créez une version de déploiement continu sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-119">Set up a CI build for an ASP.NET Core app, then create a continuous deployment release to Azure App Service.</span></span>

[<span data-ttu-id="f00a6-120">Bac à sable Azure Web App</span><span class="sxs-lookup"><span data-stu-id="f00a6-120">Azure Web App sandbox</span></span>](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)  
<span data-ttu-id="f00a6-121">Découvrez les limitations d’exécution du runtime Azure App Service appliquées par la plateforme Azure Apps.</span><span class="sxs-lookup"><span data-stu-id="f00a6-121">Discover Azure App Service runtime execution limitations enforced by the Azure Apps platform.</span></span>

<xref:test/troubleshoot>  
<span data-ttu-id="f00a6-122">Comprenez et résolvez les avertissements et les erreurs avec les projets ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f00a6-122">Understand and troubleshoot warnings and errors with ASP.NET Core projects.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="f00a6-123">Configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="f00a6-123">Application configuration</span></span>

### <a name="platform"></a><span data-ttu-id="f00a6-124">Plateforme</span><span class="sxs-lookup"><span data-stu-id="f00a6-124">Platform</span></span>

<span data-ttu-id="f00a6-125">L’architecture de la plate-forme (x86/x64) d’une application App Services est définie dans les paramètres de l’application dans le portail Azure pour les applications hébergées sur un calcul de la série A (Basic) ou un niveau d’hébergement supérieur.</span><span class="sxs-lookup"><span data-stu-id="f00a6-125">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="f00a6-126">Confirmez que les paramètres de publication de l’application (par exemple, dans le visual Studio publiez le [profil (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondent au paramètre dans la configuration de service de l’application dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="f00a6-126">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure Portal.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f00a6-127">Des runtimes pour les applications 64 bits (x64) et 32 bits (x 86) sont présents sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-127">Runtimes for 64-bit (x64) and 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="f00a6-128">Le [SDK .NET Core](/dotnet/core/sdk) disponible sur App Service est 32 bits, mais vous pouvez déployer des applications 64 bits crées localement en utilisant la console [Kudu](https://github.com/projectkudu/kudu/wiki) ou le processus de publication de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f00a6-128">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit, but you can deploy 64-bit apps built locally using the [Kudu](https://github.com/projectkudu/kudu/wiki) console or the publish process in Visual Studio.</span></span> <span data-ttu-id="f00a6-129">Pour plus d’informations, consultez la section [Publier et déployer l’application](#publish-and-deploy-the-app).</span><span class="sxs-lookup"><span data-stu-id="f00a6-129">For more information, see the [Publish and deploy the app](#publish-and-deploy-the-app) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f00a6-130">Concernant les applications avec des dépendances natives, des runtimes pour les applications 32 bits (x86) sont présents sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-130">For apps with native dependencies, runtimes for 32-bit (x86) apps are present on Azure App Service.</span></span> <span data-ttu-id="f00a6-131">Le [SDK .NET Core](/dotnet/core/sdk) disponible sur App Service est 32 bits.</span><span class="sxs-lookup"><span data-stu-id="f00a6-131">The [.NET Core SDK](/dotnet/core/sdk) available on App Service is 32-bit.</span></span>

::: moniker-end

<span data-ttu-id="f00a6-132">Pour plus d’informations sur les composants-cadres et les méthodes de distribution .NET Core, tels que les informations sur le runtime .NET Core et le .NET Core SDK, voir [About .NET Core: Composition](/dotnet/core/about#composition).</span><span class="sxs-lookup"><span data-stu-id="f00a6-132">For more information on .NET Core framework components and distribution methods, such as information on the .NET Core runtime and the .NET Core SDK, see [About .NET Core: Composition](/dotnet/core/about#composition).</span></span>

### <a name="packages"></a><span data-ttu-id="f00a6-133">.</span><span class="sxs-lookup"><span data-stu-id="f00a6-133">Packages</span></span>

<span data-ttu-id="f00a6-134">Ajoutez les packages NuGet suivants pour fournir des fonctionnalités de journalisation automatique aux applications déployées sur Azure App Service :</span><span class="sxs-lookup"><span data-stu-id="f00a6-134">Include the following NuGet packages to provide automatic logging features for apps deployed to Azure App Service:</span></span>

* <span data-ttu-id="f00a6-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) utilise [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) pour intégrer la fonction d’éclairage ASP.NET Core dans Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-135">[Microsoft.AspNetCore.AzureAppServices.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServices.HostingStartup/) uses [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) to provide ASP.NET Core light-up integration with Azure App Service.</span></span> <span data-ttu-id="f00a6-136">Les fonctionnalités de journalisation ajoutées sont fournies par le package `Microsoft.AspNetCore.AzureAppServicesIntegration`.</span><span class="sxs-lookup"><span data-stu-id="f00a6-136">The added logging features are provided by the `Microsoft.AspNetCore.AzureAppServicesIntegration` package.</span></span>
* <span data-ttu-id="f00a6-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) exécute [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) pour ajouter des fournisseurs de journalisation de diagnostics Azure App Service dans le package `Microsoft.Extensions.Logging.AzureAppServices`.</span><span class="sxs-lookup"><span data-stu-id="f00a6-137">[Microsoft.AspNetCore.AzureAppServicesIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.AzureAppServicesIntegration/) executes [AddAzureWebAppDiagnostics](/dotnet/api/microsoft.extensions.logging.azureappservicesloggerfactoryextensions.addazurewebappdiagnostics) to add Azure App Service diagnostics logging providers in the `Microsoft.Extensions.Logging.AzureAppServices` package.</span></span>
* <span data-ttu-id="f00a6-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) fournit des implémentations de journaliseur pour prendre en charge les journaux de diagnostics et les fonctionnalités de streaming de journal Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-138">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/) provides logger implementations to support Azure App Service diagnostics logs and log streaming features.</span></span>

<span data-ttu-id="f00a6-139">Les packages précédents ne sont pas disponibles dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f00a6-139">The preceding packages aren't available from the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="f00a6-140">Les applications qui ciblent le .NET Framework ou référencent le métapackage `Microsoft.AspNetCore.App` doivent référencer explicitement les packages individuels dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="f00a6-140">Apps that target .NET Framework or reference the `Microsoft.AspNetCore.App` metapackage must explicitly reference the individual packages in the app's project file.</span></span>

## <a name="override-app-configuration-using-the-azure-portal"></a><span data-ttu-id="f00a6-141">Remplacer la configuration de l’application à l’aide du Portail Azure</span><span class="sxs-lookup"><span data-stu-id="f00a6-141">Override app configuration using the Azure Portal</span></span>

<span data-ttu-id="f00a6-142">Les paramètres d’application dans le portail Azure vous permettent de définir des variables d’environnement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="f00a6-142">App settings in the Azure Portal permit you to set environment variables for the app.</span></span> <span data-ttu-id="f00a6-143">Celles-ci peuvent être utilisées par le [Fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f00a6-143">Environment variables can be consumed by the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

<span data-ttu-id="f00a6-144">Quand un paramètre d’application est créé ou modifié dans le portail Azure et que le bouton **Enregistrer** est sélectionné, Azure App redémarre.</span><span class="sxs-lookup"><span data-stu-id="f00a6-144">When an app setting is created or modified in the Azure Portal and the **Save** button is selected, the Azure App is restarted.</span></span> <span data-ttu-id="f00a6-145">La variable d’environnement est à la disposition de l’application après le redémarrage du service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-145">The environment variable is available to the app after the service restarts.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f00a6-146">Lorsqu’une application utilise [l’hôte générique,](xref:fundamentals/host/generic-host)les variables <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> de l’environnement sont chargées dans la configuration de l’application lorsqu’elles sont appelées pour construire l’hôte.</span><span class="sxs-lookup"><span data-stu-id="f00a6-146">When an app uses the [Generic Host](xref:fundamentals/host/generic-host), environment variables are loaded into the app's configuration when <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="f00a6-147">Pour plus d’informations, voir <xref:fundamentals/host/generic-host> et [Fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f00a6-147">For more information, see <xref:fundamentals/host/generic-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f00a6-148">Lorsqu’une application utilise l’hébergeur [Web,](xref:fundamentals/host/web-host)les variables <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> de l’environnement sont chargées dans la configuration de l’application lorsqu’elles sont appelées pour construire l’hôte.</span><span class="sxs-lookup"><span data-stu-id="f00a6-148">When an app uses the [Web Host](xref:fundamentals/host/web-host), environment variables are loaded into the app's configuration when <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> is called to build the host.</span></span> <span data-ttu-id="f00a6-149">Pour plus d’informations, voir <xref:fundamentals/host/web-host> et [Fournisseur de configuration des variables d’environnement](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f00a6-149">For more information, see <xref:fundamentals/host/web-host> and the [Environment Variables Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="f00a6-150">Scénarios avec un serveur proxy et un équilibreur de charge</span><span class="sxs-lookup"><span data-stu-id="f00a6-150">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="f00a6-151">Le [middleware d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), qui configure le middleware des en-têtes transférés lors de l’hébergement [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), et le module ASP.NET Core sont configurés pour transférer le schéma (HTTP/HTTPS) et l’adresse IP distante d’où provient la requête.</span><span class="sxs-lookup"><span data-stu-id="f00a6-151">The [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components), which configures Forwarded Headers Middleware when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="f00a6-152">Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="f00a6-152">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="f00a6-153">Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="f00a6-153">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="monitoring-and-logging"></a><span data-ttu-id="f00a6-154">Surveillance et journalisation</span><span class="sxs-lookup"><span data-stu-id="f00a6-154">Monitoring and logging</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f00a6-155">Les applications ASP.NET Core déployées sur App Service reçoivent automatiquement une extension App Service, **Intégration de journalisation ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-155">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Integration**.</span></span> <span data-ttu-id="f00a6-156">L’extension permet d’intégrer la journalisation dans les applications ASP.NET Core sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-156">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f00a6-157">Les applications ASP.NET Core déployées sur App Service reçoivent automatiquement une extension App Service, **Extensions de journalisation ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-157">ASP.NET Core apps deployed to App Service automatically receive an App Service extension, **ASP.NET Core Logging Extensions**.</span></span> <span data-ttu-id="f00a6-158">L’extension permet d’intégrer la journalisation dans les applications ASP.NET Core sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-158">The extension enables logging integration for ASP.NET Core apps on Azure App Service.</span></span>

::: moniker-end

<span data-ttu-id="f00a6-159">Pour des informations de surveillance, de journalisation et de dépannage, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="f00a6-159">For monitoring, logging, and troubleshooting information, see the following articles:</span></span>

[<span data-ttu-id="f00a6-160">Surveiller les applications dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f00a6-160">Monitor apps in Azure App Service</span></span>](/azure/app-service/web-sites-monitor)  
<span data-ttu-id="f00a6-161">Découvrez comment consulter les quotas et les métriques des applications et des plans App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-161">Learn how to review quotas and metrics for apps and App Service plans.</span></span>

[<span data-ttu-id="f00a6-162">Activez la connexion diagnostique pour les applications dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f00a6-162">Enable diagnostics logging for apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)  
<span data-ttu-id="f00a6-163">Découvrez comment activer et accéder à la journalisation des diagnostics pour les codes d’état HTTP, les requêtes en échec et les activités de serveur web.</span><span class="sxs-lookup"><span data-stu-id="f00a6-163">Discover how to enable and access diagnostic logging for HTTP status codes, failed requests, and web server activity.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="f00a6-164">Découvrez les approches courantes permettant de gérer les erreurs dans les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f00a6-164">Understand common approaches to handling errors in ASP.NET Core apps.</span></span>

<xref:test/troubleshoot-azure-iis>  
<span data-ttu-id="f00a6-165">Découvrez comment diagnostiquer les problèmes de déploiements Azure App Service avec les applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f00a6-165">Learn how to diagnose issues with Azure App Service deployments with ASP.NET Core apps.</span></span>

<xref:host-and-deploy/azure-iis-errors-reference>  
<span data-ttu-id="f00a6-166">Découvrez les erreurs de configuration de déploiement courantes dans les applications hébergées par Azure App Service/IIS, ainsi que des conseils de dépannage.</span><span class="sxs-lookup"><span data-stu-id="f00a6-166">See the common deployment configuration errors for apps hosted by Azure App Service/IIS with troubleshooting advice.</span></span>

## <a name="data-protection-key-ring-and-deployment-slots"></a><span data-ttu-id="f00a6-167">Porte-clés de Protection des données et emplacements de déploiement</span><span class="sxs-lookup"><span data-stu-id="f00a6-167">Data Protection key ring and deployment slots</span></span>

<span data-ttu-id="f00a6-168">Les [clés de Protection des données](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) sont persistantes dans le dossier *%HOME%\ASP.NET\DataProtection-Keys*.</span><span class="sxs-lookup"><span data-stu-id="f00a6-168">[Data Protection keys](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) are persisted to the *%HOME%\ASP.NET\DataProtection-Keys* folder.</span></span> <span data-ttu-id="f00a6-169">Ce dossier est alimenté par le stockage réseau et synchronisé sur tous les ordinateurs hébergeant l’application.</span><span class="sxs-lookup"><span data-stu-id="f00a6-169">This folder is backed by network storage and is synchronized across all machines hosting the app.</span></span> <span data-ttu-id="f00a6-170">Les clés ne sont pas protégées au repos.</span><span class="sxs-lookup"><span data-stu-id="f00a6-170">Keys aren't protected at rest.</span></span> <span data-ttu-id="f00a6-171">Ce dossier fournit le porte-clés à toutes les instances d’une application dans un seul emplacement de déploiement.</span><span class="sxs-lookup"><span data-stu-id="f00a6-171">This folder supplies the key ring to all instances of an app in a single deployment slot.</span></span> <span data-ttu-id="f00a6-172">Les emplacements de déploiement distincts, tels que Préproduction et Production, ne partagent pas de porte-clés.</span><span class="sxs-lookup"><span data-stu-id="f00a6-172">Separate deployment slots, such as Staging and Production, don't share a key ring.</span></span>

<span data-ttu-id="f00a6-173">Lors d’une permutation entre les emplacements de déploiement, aucun système utilisant la protection des données ne peut déchiffrer les données stockées à l’aide du porte-clés au sein de l’emplacement précédent.</span><span class="sxs-lookup"><span data-stu-id="f00a6-173">When swapping between deployment slots, any system using data protection won't be able to decrypt stored data using the key ring inside the previous slot.</span></span> <span data-ttu-id="f00a6-174">L’intergiciel (middleware) ASP.NET Cookie utilise la protection des données pour protéger ses cookies.</span><span class="sxs-lookup"><span data-stu-id="f00a6-174">ASP.NET Cookie Middleware uses data protection to protect its cookies.</span></span> <span data-ttu-id="f00a6-175">Cela entraîne la déconnexion des utilisateurs des applications qui utilisent l’intergiciel ASP.NET Cookie standard.</span><span class="sxs-lookup"><span data-stu-id="f00a6-175">This leads to users being signed out of an app that uses the standard ASP.NET Cookie Middleware.</span></span> <span data-ttu-id="f00a6-176">Pour une solution de porte-clés indépendante de l’emplacement, utilisez un fournisseur de porte-clés externe, tel que :</span><span class="sxs-lookup"><span data-stu-id="f00a6-176">For a slot-independent key ring solution, use an external key ring provider, such as:</span></span>

* <span data-ttu-id="f00a6-177">Stockage Blob Azure</span><span class="sxs-lookup"><span data-stu-id="f00a6-177">Azure Blob Storage</span></span>
* <span data-ttu-id="f00a6-178">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="f00a6-178">Azure Key Vault</span></span>
* <span data-ttu-id="f00a6-179">Magasin SQL</span><span class="sxs-lookup"><span data-stu-id="f00a6-179">SQL store</span></span>
* <span data-ttu-id="f00a6-180">Le cache Redis</span><span class="sxs-lookup"><span data-stu-id="f00a6-180">Redis cache</span></span>

<span data-ttu-id="f00a6-181">Pour plus d’informations, consultez <xref:security/data-protection/implementation/key-storage-providers>.</span><span class="sxs-lookup"><span data-stu-id="f00a6-181">For more information, see <xref:security/data-protection/implementation/key-storage-providers>.</span></span>
<a name="deploy-aspnet-core-preview-release-to-azure-app-service"></a>

## <a name="deploy-an-aspnet-core-app-that-uses-a-net-core-preview"></a><span data-ttu-id="f00a6-182">Déployer une application ASP.NET Core qui utilise un aperçu .NET Core</span><span class="sxs-lookup"><span data-stu-id="f00a6-182">Deploy an ASP.NET Core app that uses a .NET Core preview</span></span>

<span data-ttu-id="f00a6-183">Pour déployer une application qui utilise une version prévisualisante de .NET Core, voir les ressources suivantes.</span><span class="sxs-lookup"><span data-stu-id="f00a6-183">To deploy an app that uses a preview release of .NET Core, see the following resources.</span></span> <span data-ttu-id="f00a6-184">Ces approches sont également utilisées lorsque l’heure d’exécution est disponible, mais le SDK n’a pas été installé sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-184">These approaches are also used when the runtime is available but the SDK hasn't been installed on Azure App Service.</span></span>

* [<span data-ttu-id="f00a6-185">Spécifier la version SDK Core .NET à l’aide de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f00a6-185">Specify the .NET Core SDK Version using Azure Pipelines</span></span>](#specify-the-net-core-sdk-version-using-azure-pipelines)
* [<span data-ttu-id="f00a6-186">Déployer une application d’aperçu autonome</span><span class="sxs-lookup"><span data-stu-id="f00a6-186">Deploy a self-contained preview app</span></span>](#deploy-a-self-contained-preview-app)
* [<span data-ttu-id="f00a6-187">Utiliser Docker avec Web Apps pour conteneurs</span><span class="sxs-lookup"><span data-stu-id="f00a6-187">Use Docker with Web Apps for containers</span></span>](#use-docker-with-web-apps-for-containers)
* [<span data-ttu-id="f00a6-188">Installer l’extension de site de version Preview</span><span class="sxs-lookup"><span data-stu-id="f00a6-188">Install the preview site extension</span></span>](#install-the-preview-site-extension)

<span data-ttu-id="f00a6-189">Voir le [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) pour la version de ASP.NET Core disponible sur le service Azure App.</span><span class="sxs-lookup"><span data-stu-id="f00a6-189">See the [ASP.NET Core on App Service Dashboard](https://aspnetcoreon.azurewebsites.net/) for the version of ASP.NET Core available on Azure App service.</span></span>

### <a name="specify-the-net-core-sdk-version-using-azure-pipelines"></a><span data-ttu-id="f00a6-190">Spécifier la version SDK Core .NET à l’aide de Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="f00a6-190">Specify the .NET Core SDK Version using Azure Pipelines</span></span>

<span data-ttu-id="f00a6-191">Utilisez les [scénarios CI/CD d’Azure App Pour](/azure/app-service/deploy-continuous-deployment) configurer une intégration continue avec Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="f00a6-191">Use [Azure App Service CI/CD scenarios](/azure/app-service/deploy-continuous-deployment) to set up a continuous integration build with Azure DevOps.</span></span> <span data-ttu-id="f00a6-192">Une fois la version Azure DevOps créée, configurez la version en option pour utiliser une version SDK spécifique.</span><span class="sxs-lookup"><span data-stu-id="f00a6-192">After the Azure DevOps build is created, optionally configure the build to use a specific SDK version.</span></span> 

#### <a name="specify-the-net-core-sdk-version"></a><span data-ttu-id="f00a6-193">Spécifier la version SDK Core .NET</span><span class="sxs-lookup"><span data-stu-id="f00a6-193">Specify the .NET Core SDK version</span></span>

<span data-ttu-id="f00a6-194">Lors de l’utilisation du centre de déploiement App Service pour créer une `Restore` `Build`version `Test`Azure DevOps, le pipeline de construction par défaut comprend des étapes pour , , , et `Publish`.</span><span class="sxs-lookup"><span data-stu-id="f00a6-194">When using the App Service deployment center to create an Azure DevOps build, the default build pipeline includes steps for `Restore`, `Build`, `Test`, and `Publish`.</span></span> <span data-ttu-id="f00a6-195">Pour spécifier la version SDK, sélectionnez le bouton **Ajouter ()** dans la liste d’emploi Agent pour ajouter une nouvelle étape.</span><span class="sxs-lookup"><span data-stu-id="f00a6-195">To specify the SDK version, select the **Add (+)** button in the Agent job list to add a new step.</span></span> <span data-ttu-id="f00a6-196">Recherchez **.NET Core SDK** dans la barre de recherche.</span><span class="sxs-lookup"><span data-stu-id="f00a6-196">Search for **.NET Core SDK** in the search bar.</span></span> 

![Ajouter l’étape SDK de base .NET](index/add-sdk-step.png)

<span data-ttu-id="f00a6-198">Déplacez l’étape dans la première position de la construction de sorte que les étapes qui la suivent utilisent la version spécifiée du SDK core .NET.</span><span class="sxs-lookup"><span data-stu-id="f00a6-198">Move the step into the first position in the build so that the steps following it use the specified version of the .NET Core SDK.</span></span> <span data-ttu-id="f00a6-199">Spécifier la version du .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="f00a6-199">Specify the version of the .NET Core SDK.</span></span> <span data-ttu-id="f00a6-200">Dans cet exemple, le SDK est configuré à `3.0.100`.</span><span class="sxs-lookup"><span data-stu-id="f00a6-200">In this example, the SDK is set to `3.0.100`.</span></span>

![Étape SDK terminée](index/sdk-step-first-place.png)

<span data-ttu-id="f00a6-202">Pour publier un [déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), `Publish` configurer SCD dans l’étape et fournir [l’identifiant Runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f00a6-202">To publish a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd), configure SCD in the `Publish` step and provide the [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

![Publier des contenus autonomes](index/self-contained.png)

### <a name="deploy-a-self-contained-preview-app"></a><span data-ttu-id="f00a6-204">Déployer une application en préversion autonome</span><span class="sxs-lookup"><span data-stu-id="f00a6-204">Deploy a self-contained preview app</span></span>

<span data-ttu-id="f00a6-205">Un [déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) qui cible une préversion runtime exécute le runtime de la préversion dans le déploiement.</span><span class="sxs-lookup"><span data-stu-id="f00a6-205">A [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) that targets a preview runtime carries the preview runtime in the deployment.</span></span>

<span data-ttu-id="f00a6-206">Pendant le déploiement d’une application autonome :</span><span class="sxs-lookup"><span data-stu-id="f00a6-206">When deploying a self-contained app:</span></span>

* <span data-ttu-id="f00a6-207">Le site dans Azure App Service ne requiert pas l’[extension du site de préversion](#install-the-preview-site-extension).</span><span class="sxs-lookup"><span data-stu-id="f00a6-207">The site in Azure App Service doesn't require the [preview site extension](#install-the-preview-site-extension).</span></span>
* <span data-ttu-id="f00a6-208">L’application doit être publiée suivant une autre approche que dans le cadre de la publication d’un [déploiement en fonction de l’infrastructure (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="f00a6-208">The app must be published following a different approach than when publishing for a [framework-dependent deployment (FDD)](/dotnet/core/deploying#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="f00a6-209">Suivez les instructions de la section [Déployer l’application autonome](#deploy-the-app-self-contained).</span><span class="sxs-lookup"><span data-stu-id="f00a6-209">Follow the guidance in the [Deploy the app self-contained](#deploy-the-app-self-contained) section.</span></span>

### <a name="use-docker-with-web-apps-for-containers"></a><span data-ttu-id="f00a6-210">Utiliser Docker avec Web Apps pour conteneurs</span><span class="sxs-lookup"><span data-stu-id="f00a6-210">Use Docker with Web Apps for containers</span></span>

<span data-ttu-id="f00a6-211">[Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contient les dernières images de Docker en préversion.</span><span class="sxs-lookup"><span data-stu-id="f00a6-211">The [Docker Hub](https://hub.docker.com/r/microsoft/aspnetcore/) contains the latest preview Docker images.</span></span> <span data-ttu-id="f00a6-212">Les images peuvent être utilisées comme images de base.</span><span class="sxs-lookup"><span data-stu-id="f00a6-212">The images can be used as a base image.</span></span> <span data-ttu-id="f00a6-213">Utilisez l’image pour effectuer un déploiement sur Web Apps pour conteneurs normalement.</span><span class="sxs-lookup"><span data-stu-id="f00a6-213">Use the image and deploy to Web Apps for Containers normally.</span></span>

### <a name="install-the-preview-site-extension"></a><span data-ttu-id="f00a6-214">Installer l’extension de site de version Preview</span><span class="sxs-lookup"><span data-stu-id="f00a6-214">Install the preview site extension</span></span>

<span data-ttu-id="f00a6-215">Si un problème se produit à l’aide de l’extension du site d’aperçu, ouvrez un [problème dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/issues).</span><span class="sxs-lookup"><span data-stu-id="f00a6-215">If a problem occurs using the preview site extension, open an [dotnet/AspNetCore issue](https://github.com/dotnet/AspNetCore/issues).</span></span>

1. <span data-ttu-id="f00a6-216">Dans le portail Azure, accédez à App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-216">From the Azure Portal, navigate to the App Service.</span></span>
1. <span data-ttu-id="f00a6-217">Sélectionnez l’application web.</span><span class="sxs-lookup"><span data-stu-id="f00a6-217">Select the web app.</span></span>
1. <span data-ttu-id="f00a6-218">Tapez « ex » dans la zone de recherche pour filtrer sur les « Extensions » ou faites défiler la liste outils de gestion.</span><span class="sxs-lookup"><span data-stu-id="f00a6-218">Type "ex" in the search box to filter for "Extensions" or scroll down the list of management tools.</span></span>
1. <span data-ttu-id="f00a6-219">Sélectionnez **Extensions**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-219">Select **Extensions**.</span></span>
1. <span data-ttu-id="f00a6-220">Sélectionnez **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-220">Select **Add**.</span></span>
1. <span data-ttu-id="f00a6-221">Sélectionnez l’extension **ASP.NET Core {X.Y} ({x64|x86}) Runtime** dans la liste, où `{X.Y}` correspond à la préversion d’ASP.NET Core et `{x64|x86}` spécifie la plateforme.</span><span class="sxs-lookup"><span data-stu-id="f00a6-221">Select the **ASP.NET Core {X.Y} ({x64|x86}) Runtime** extension from the list, where `{X.Y}` is the ASP.NET Core preview version and `{x64|x86}` specifies the platform.</span></span>
1. <span data-ttu-id="f00a6-222">Sélectionnez **OK** pour accepter les conditions légales.</span><span class="sxs-lookup"><span data-stu-id="f00a6-222">Select **OK** to accept the legal terms.</span></span>
1. <span data-ttu-id="f00a6-223">Sélectionnez **OK** pour installer l’extension.</span><span class="sxs-lookup"><span data-stu-id="f00a6-223">Select **OK** to install the extension.</span></span>

<span data-ttu-id="f00a6-224">Une fois l’opération effectuée, la dernière préversion de .NET Core est installée.</span><span class="sxs-lookup"><span data-stu-id="f00a6-224">When the operation completes, the latest .NET Core preview is installed.</span></span> <span data-ttu-id="f00a6-225">Vérifiez l’installation :</span><span class="sxs-lookup"><span data-stu-id="f00a6-225">Verify the installation:</span></span>

1. <span data-ttu-id="f00a6-226">Sélectionnez **Outils avancés**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-226">Select **Advanced Tools**.</span></span>
1. <span data-ttu-id="f00a6-227">Sélectionnez **Go** dans **Outils avancés**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-227">Select **Go** in **Advanced Tools**.</span></span>
1. <span data-ttu-id="f00a6-228">Sélectionnez l’élément de menu > **PowerShell** de la **console Debug.**</span><span class="sxs-lookup"><span data-stu-id="f00a6-228">Select the **Debug console** > **PowerShell** menu item.</span></span>
1. <span data-ttu-id="f00a6-229">À l’invite PowerShell, exécutez la commande suivante.</span><span class="sxs-lookup"><span data-stu-id="f00a6-229">At the PowerShell prompt, execute the following command.</span></span> <span data-ttu-id="f00a6-230">Remplacez `{X.Y}` par la version du runtime ASP.NET Core et `{PLATFORM}` par la plateforme dans la commande :</span><span class="sxs-lookup"><span data-stu-id="f00a6-230">Substitute the ASP.NET Core runtime version for `{X.Y}` and the platform for `{PLATFORM}` in the command:</span></span>

   ```powershell
   Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.{PLATFORM}\
   ```

   <span data-ttu-id="f00a6-231">La commande retourne `True` quand le runtime de la préversion x64 est installée.</span><span class="sxs-lookup"><span data-stu-id="f00a6-231">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="f00a6-232">L’architecture de la plate-forme (x86/x64) d’une application App Services est définie dans les paramètres de l’application dans le portail Azure pour les applications hébergées sur un calcul de la série A (Basic) ou un niveau d’hébergement supérieur.</span><span class="sxs-lookup"><span data-stu-id="f00a6-232">The platform architecture (x86/x64) of an App Services app is set in the app's settings in the Azure Portal for apps that are hosted on an A-series compute (Basic) or higher hosting tier.</span></span> <span data-ttu-id="f00a6-233">Confirmez que les paramètres de publication de l’application (par exemple, dans le visual Studio publiez le [profil (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) correspondent au paramètre dans la configuration de service de l’application dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="f00a6-233">Confirm that the app's publish settings (for example, in the Visual Studio [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles)) match the setting in the app's service configuration in the Azure portal.</span></span>
>
> <span data-ttu-id="f00a6-234">Si l’application s’exécute en mode in-process et si la plateforme est configurée pour une architecture 64 bits (x64), le module ASP.NET Core utilise le runtime de la préversion 64 bits, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="f00a6-234">If the app is run in in-process mode and the platform architecture is configured for 64-bit (x64), the ASP.NET Core Module uses the 64-bit preview runtime, if present.</span></span> <span data-ttu-id="f00a6-235">Installez **l’extension Runtime ASP.NET Core 'X.Y' (x64)** à l’aide du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="f00a6-235">Install the **ASP.NET Core {X.Y} (x64) Runtime** extension using the Azure Portal.</span></span>
>
> <span data-ttu-id="f00a6-236">Après l’installation de l’heure d’exécution x64 aperçu, exécuter la commande suivante dans la fenêtre de commande Azure Kudu PowerShell pour vérifier l’installation.</span><span class="sxs-lookup"><span data-stu-id="f00a6-236">After installing the x64 preview runtime, run the following command in the Azure Kudu PowerShell command window to verify the installation.</span></span> <span data-ttu-id="f00a6-237">Remplacez la version ASP.NET Core `{X.Y}` runtime dans la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="f00a6-237">Substitute the ASP.NET Core runtime version for `{X.Y}` in the following command:</span></span>
>
> ```powershell
> Test-Path D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64\
> ```
>
> <span data-ttu-id="f00a6-238">La commande retourne `True` quand le runtime de la préversion x64 est installée.</span><span class="sxs-lookup"><span data-stu-id="f00a6-238">The command returns `True` when the x64 preview runtime is installed.</span></span>

> [!NOTE]
> <span data-ttu-id="f00a6-239">Les **extensions ASP.NET Core** permettent d’activer des fonctionnalités supplémentaires pour ASP.NET Core sur Azure App Services, par exemple la journalisation Azure.</span><span class="sxs-lookup"><span data-stu-id="f00a6-239">**ASP.NET Core Extensions** enables additional functionality for ASP.NET Core on Azure App Services, such as enabling Azure logging.</span></span> <span data-ttu-id="f00a6-240">L’extension est installée automatiquement quand vous effectuez le déploiement à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f00a6-240">The extension is installed automatically when deploying from Visual Studio.</span></span> <span data-ttu-id="f00a6-241">Si l’extension n’est pas installée, installez-la pour l’application.</span><span class="sxs-lookup"><span data-stu-id="f00a6-241">If the extension isn't installed, install it for the app.</span></span>

<span data-ttu-id="f00a6-242">**Utiliser l’extension de site en préversion avec un modèle ARM**</span><span class="sxs-lookup"><span data-stu-id="f00a6-242">**Use the preview site extension with an ARM template**</span></span>

<span data-ttu-id="f00a6-243">Si un modèle ARM est utilisé pour créer et déployer des applications, le type de ressource `siteextensions` peut être utilisé pour ajouter l’extension de site à une application web.</span><span class="sxs-lookup"><span data-stu-id="f00a6-243">If an ARM template is used to create and deploy apps, the `siteextensions` resource type can be used to add the site extension to a web app.</span></span> <span data-ttu-id="f00a6-244">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="f00a6-244">For example:</span></span>

[!code-json[](index/sample/arm.json?highlight=2)]

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="f00a6-245">Publier et déployer l’application</span><span class="sxs-lookup"><span data-stu-id="f00a6-245">Publish and deploy the app</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f00a6-246">Pour un déploiement 64 bits :</span><span class="sxs-lookup"><span data-stu-id="f00a6-246">For a 64-bit deployment:</span></span>

* <span data-ttu-id="f00a6-247">Utilisez un SDK .NET Core 64 bits pour générer une application 64 bits.</span><span class="sxs-lookup"><span data-stu-id="f00a6-247">Use a 64-bit .NET Core SDK to build a 64-bit app.</span></span>
* <span data-ttu-id="f00a6-248">Définissez **Plateforme** sur **64 bits** dans **Configuration** > **Paramètres généraux** d’App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-248">Set the **Platform** to **64 Bit** in the App Service's **Configuration** > **General settings**.</span></span> <span data-ttu-id="f00a6-249">L’application doit utiliser un plan de service De base ou supérieur pour permettre le choix du nombre de bits de la plateforme.</span><span class="sxs-lookup"><span data-stu-id="f00a6-249">The app must use a Basic or higher service plan to enable the choice of platform bitness.</span></span>

::: moniker-end

### <a name="deploy-the-app-framework-dependent"></a><span data-ttu-id="f00a6-250">Déployer l’application en fonction du framework</span><span class="sxs-lookup"><span data-stu-id="f00a6-250">Deploy the app framework-dependent</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f00a6-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f00a6-251">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f00a6-252">Sélectionnez **Build** > Publier le nom de**l’application** à partir de la barre d’outils Visual Studio ou cliquer à droite sur le projet dans **Solution Explorer** et **sélectionnez Publier**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-252">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="f00a6-253">Dans la boîte de dialogue **Choisir une cible de publication**, confirmez qu’**App Service** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="f00a6-253">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="f00a6-254">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-254">Select **Advanced**.</span></span> <span data-ttu-id="f00a6-255">La boîte de dialogue **Publier** s’ouvre.</span><span class="sxs-lookup"><span data-stu-id="f00a6-255">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="f00a6-256">Dans la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="f00a6-256">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="f00a6-257">Confirmez que la configuration **Mise en production** est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="f00a6-257">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="f00a6-258">Ouvrez la liste déroulante **Mode de déploiement** et sélectionnez **Dépendant du framework**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-258">Open the **Deployment Mode** drop-down list and select **Framework-Dependent**.</span></span>
   * <span data-ttu-id="f00a6-259">Sélectionnez **Portable** comme **Runtime cible**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-259">Select **Portable** as the **Target Runtime**.</span></span>
   * <span data-ttu-id="f00a6-260">Si vous devez supprimer des fichiers supplémentaires lors du déploiement, ouvrez **Options de publication de fichiers** et sélectionnez la case à cocher pour supprimer des fichiers supplémentaires à la destination.</span><span class="sxs-lookup"><span data-stu-id="f00a6-260">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="f00a6-261">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-261">Select **Save**.</span></span>
1. <span data-ttu-id="f00a6-262">Créez un nouveau site ou mettez à jour un site existant en suivant les autres invites de l'Assistant de publication.</span><span class="sxs-lookup"><span data-stu-id="f00a6-262">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f00a6-263">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f00a6-263">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f00a6-264">Dans le fichier projet, ne spécifiez pas d’[Identificateur d’exécution (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f00a6-264">In the project file, don't specify a [Runtime Identifier (RID)](/dotnet/core/rid-catalog).</span></span>

1. <span data-ttu-id="f00a6-265">A partir d’un interpréteur de commandes, publiez l’application en configuration Release avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="f00a6-265">From a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f00a6-266">Dans l’exemple suivant, l’application est publiée en tant qu’application dépendante du framework :</span><span class="sxs-lookup"><span data-stu-id="f00a6-266">In the following example, the app is published as a framework-dependent app:</span></span>

   ```console
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="f00a6-267">Déplacez le contenu du répertoire *bin/Release/{TARGET FRAMEWORK}/publish* vers le site dans App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-267">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="f00a6-268">Si vous faites glisser le contenu du dossier *publish* depuis votre disque dur local ou votre partage réseau directement vers App service dans la console [Kudu](https://github.com/projectkudu/kudu/wiki), faites glisser les fichiers vers le dossier `D:\home\site\wwwroot` dans la console Kudu.</span><span class="sxs-lookup"><span data-stu-id="f00a6-268">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the [Kudu](https://github.com/projectkudu/kudu/wiki) console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

### <a name="deploy-the-app-self-contained"></a><span data-ttu-id="f00a6-269">Déployer l’application autonome</span><span class="sxs-lookup"><span data-stu-id="f00a6-269">Deploy the app self-contained</span></span>

<span data-ttu-id="f00a6-270">Utilisez Visual Studio ou le CLI Core .NET pour un [déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="f00a6-270">Use Visual Studio or the .NET Core CLI for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f00a6-271">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f00a6-271">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f00a6-272">Sélectionnez **Build** > Publier le nom de**l’application** à partir de la barre d’outils Visual Studio ou cliquer à droite sur le projet dans **Solution Explorer** et **sélectionnez Publier**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-272">Select **Build** > **Publish {Application Name}** from the Visual Studio toolbar or right-click the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="f00a6-273">Dans la boîte de dialogue **Choisir une cible de publication**, confirmez qu’**App Service** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="f00a6-273">In the **Pick a publish target** dialog, confirm that **App Service** is selected.</span></span>
1. <span data-ttu-id="f00a6-274">Sélectionnez **Avancé**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-274">Select **Advanced**.</span></span> <span data-ttu-id="f00a6-275">La boîte de dialogue **Publier** s’ouvre.</span><span class="sxs-lookup"><span data-stu-id="f00a6-275">The **Publish** dialog opens.</span></span>
1. <span data-ttu-id="f00a6-276">Dans la boîte de dialogue **Publier** :</span><span class="sxs-lookup"><span data-stu-id="f00a6-276">In the **Publish** dialog:</span></span>
   * <span data-ttu-id="f00a6-277">Confirmez que la configuration **Mise en production** est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="f00a6-277">Confirm that the **Release** configuration is selected.</span></span>
   * <span data-ttu-id="f00a6-278">Ouvrez la liste déroulante **Mode de déploiement** et sélectionnez **Autonome**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-278">Open the **Deployment Mode** drop-down list and select **Self-Contained**.</span></span>
   * <span data-ttu-id="f00a6-279">Sélectionnez le runtime cible à partir de la liste déroulante **Runtime cible**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-279">Select the target runtime from the **Target Runtime** drop-down list.</span></span> <span data-ttu-id="f00a6-280">Par défaut, il s’agit de `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="f00a6-280">The default is `win-x86`.</span></span>
   * <span data-ttu-id="f00a6-281">Si vous devez supprimer des fichiers supplémentaires lors du déploiement, ouvrez **Options de publication de fichiers** et sélectionnez la case à cocher pour supprimer des fichiers supplémentaires à la destination.</span><span class="sxs-lookup"><span data-stu-id="f00a6-281">If you need to remove additional files upon deployment, open **File Publish Options** and select the check box to remove additional files at the destination.</span></span>
   * <span data-ttu-id="f00a6-282">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="f00a6-282">Select **Save**.</span></span>
1. <span data-ttu-id="f00a6-283">Créez un nouveau site ou mettez à jour un site existant en suivant les autres invites de l'Assistant de publication.</span><span class="sxs-lookup"><span data-stu-id="f00a6-283">Create a new site or update an existing site by following the remaining prompts of the publish wizard.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f00a6-284">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="f00a6-284">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f00a6-285">Dans le fichier projet, spécifiez un ou plusieurs [identificateurs de runtime (RID)](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="f00a6-285">In the project file, specify one or more [Runtime Identifiers (RIDs)](/dotnet/core/rid-catalog).</span></span> <span data-ttu-id="f00a6-286">Utilisez `<RuntimeIdentifier>` (singulier) pour un seul RID, ou `<RuntimeIdentifiers>` (pluriel) pour fournir une liste de RID délimitée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="f00a6-286">Use `<RuntimeIdentifier>` (singular) for a single RID, or use `<RuntimeIdentifiers>` (plural) to provide a semicolon-delimited list of RIDs.</span></span> <span data-ttu-id="f00a6-287">Dans l’exemple suivant, le RID `win-x86` est spécifié :</span><span class="sxs-lookup"><span data-stu-id="f00a6-287">In the following example, the `win-x86` RID is specified:</span></span>

   ```xml
   <PropertyGroup>
     <TargetFramework>{TARGET FRAMEWORK}</TargetFramework>
     <RuntimeIdentifier>win-x86</RuntimeIdentifier>
   </PropertyGroup>
   ```

1. <span data-ttu-id="f00a6-288">A partir d'un interpréteur de commandes, publiez l'application dans la configuration Mise en production pour le runtime de l'hôte avec la commande [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="f00a6-288">From a command shell, publish the app in Release configuration for the host's runtime with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="f00a6-289">Dans l’exemple suivant, l’application est publiée pour le RID `win-x86`.</span><span class="sxs-lookup"><span data-stu-id="f00a6-289">In the following example, the app is published for the `win-x86` RID.</span></span> <span data-ttu-id="f00a6-290">Le RID fourni à l’option `--runtime` doit être fourni dans la propriété `<RuntimeIdentifier>` (ou `<RuntimeIdentifiers>`) du fichier projet.</span><span class="sxs-lookup"><span data-stu-id="f00a6-290">The RID supplied to the `--runtime` option must be provided in the `<RuntimeIdentifier>` (or `<RuntimeIdentifiers>`) property in the project file.</span></span>

   ```console
   dotnet publish --configuration Release --runtime win-x86 --self-contained
   ```

1. <span data-ttu-id="f00a6-291">Déplacez le contenu du répertoire *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* vers le site dans App Service.</span><span class="sxs-lookup"><span data-stu-id="f00a6-291">Move the contents of the *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* directory to the site in App Service.</span></span> <span data-ttu-id="f00a6-292">Si vous faites glisser le contenu du dossier *publish* depuis votre disque dur local ou votre partage réseau directement vers App service dans la console Kudu, faites glisser les fichiers vers le dossier `D:\home\site\wwwroot` dans la console Kudu.</span><span class="sxs-lookup"><span data-stu-id="f00a6-292">If dragging the *publish* folder contents from your local hard drive or network share directly to App Service in the Kudu console, drag the files to the `D:\home\site\wwwroot` folder in the Kudu console.</span></span>

---

## <a name="protocol-settings-https"></a><span data-ttu-id="f00a6-293">Paramètres de protocole (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="f00a6-293">Protocol settings (HTTPS)</span></span>

<span data-ttu-id="f00a6-294">Les liaisons de protocole sécurisées permettent de spécifier un certificat à utiliser pour répondre à des requêtes sur HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f00a6-294">Secure protocol bindings allow you specify a certificate to use when responding to requests over HTTPS.</span></span> <span data-ttu-id="f00a6-295">La liaison nécessite un certificat privé valide (*.pfx*) émis pour le nom d’hôte spécifique.</span><span class="sxs-lookup"><span data-stu-id="f00a6-295">Binding requires a valid private certificate (*.pfx*) issued for the specific hostname.</span></span> <span data-ttu-id="f00a6-296">Pour plus d’informations, voir [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="f00a6-296">For more information, see [Tutorial: Bind an existing custom SSL certificate to Azure App Service](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

## <a name="transform-webconfig"></a><span data-ttu-id="f00a6-297">Transformer web.config</span><span class="sxs-lookup"><span data-stu-id="f00a6-297">Transform web.config</span></span>

<span data-ttu-id="f00a6-298">Si vous devez transformer *web.config* lors de la publication (par exemple, définir les variables d’environnement basées sur la configuration, le profil ou l’environnement), consultez <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="f00a6-298">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f00a6-299">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="f00a6-299">Additional resources</span></span>

* [<span data-ttu-id="f00a6-300">Vue d'ensemble d'App Service</span><span class="sxs-lookup"><span data-stu-id="f00a6-300">App Service overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="f00a6-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span><span class="sxs-lookup"><span data-stu-id="f00a6-301">Azure App Service: The Best Place to Host your .NET Apps (55-minute overview video)</span></span>](https://channel9.msdn.com/events/dotnetConf/2017/T222)
* [<span data-ttu-id="f00a6-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span><span class="sxs-lookup"><span data-stu-id="f00a6-302">Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)</span></span>](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
* [<span data-ttu-id="f00a6-303">Présentation des diagnostics Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f00a6-303">Azure App Service diagnostics overview</span></span>](/azure/app-service/app-service-diagnostics)
* <xref:host-and-deploy/web-farm>

<span data-ttu-id="f00a6-304">Azure App Service sur Windows Server utilise [IIS (Internet Information Services)](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="f00a6-304">Azure App Service on Windows Server uses [Internet Information Services (IIS)](https://www.iis.net/).</span></span> <span data-ttu-id="f00a6-305">Les rubriques suivantes se rapportent à la technologie IIS sous-jacente :</span><span class="sxs-lookup"><span data-stu-id="f00a6-305">The following topics pertain to the underlying IIS technology:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>
* [<span data-ttu-id="f00a6-306">Windows Server - contenu d’administrateur informatique pour les versions actuelles et antérieures</span><span class="sxs-lookup"><span data-stu-id="f00a6-306">Windows Server - IT administrator content for current and previous releases</span></span>](/windows-server/windows-server-versions)
