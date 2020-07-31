---
title: Utiliser plusieurs environnements dans ASP.NET Core
author: rick-anderson
description: Découvrez comment contrôler le comportement de l’application dans différents environnements dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330622"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="36319-103">Utiliser plusieurs environnements dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36319-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36319-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="36319-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="36319-105">ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="36319-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36319-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="36319-107">Environnements</span><span class="sxs-lookup"><span data-stu-id="36319-107">Environments</span></span>

<span data-ttu-id="36319-108">Pour déterminer l’environnement d’exécution, ASP.NET Core lit les variables d’environnement suivantes :</span><span class="sxs-lookup"><span data-stu-id="36319-108">To determine the runtime environment, ASP.NET Core reads from the following environment variables:</span></span>

1. [<span data-ttu-id="36319-109">DOTNET_ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="36319-109">DOTNET_ENVIRONMENT</span></span>](xref:fundamentals/configuration/index#default-host-configuration)
1. <span data-ttu-id="36319-110">`ASPNETCORE_ENVIRONMENT`Lorsque <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> est appelé.</span><span class="sxs-lookup"><span data-stu-id="36319-110">`ASPNETCORE_ENVIRONMENT` when <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> is called.</span></span> <span data-ttu-id="36319-111">L’appel des modèles d’application Web par défaut ASP.NET Core `ConfigureWebHostDefaults` .</span><span class="sxs-lookup"><span data-stu-id="36319-111">The default ASP.NET Core web app templates call `ConfigureWebHostDefaults`.</span></span> <span data-ttu-id="36319-112">La `ASPNETCORE_ENVIRONMENT` valeur se substitue à `DOTNET_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="36319-112">The `ASPNETCORE_ENVIRONMENT` value overrides `DOTNET_ENVIRONMENT`.</span></span>

<span data-ttu-id="36319-113">`IHostEnvironment.EnvironmentName`peut être défini sur n’importe quelle valeur, mais les valeurs suivantes sont fournies par le Framework :</span><span class="sxs-lookup"><span data-stu-id="36319-113">`IHostEnvironment.EnvironmentName` can be set to any value, but the following values are provided by the framework:</span></span>

* <span data-ttu-id="36319-114"><xref:Microsoft.Extensions.Hosting.Environments.Development>: L' [launchSettings.jssur](#lsj) les jeux de fichiers sur `ASPNETCORE_ENVIRONMENT` `Development` sur l’ordinateur local.</span><span class="sxs-lookup"><span data-stu-id="36319-114"><xref:Microsoft.Extensions.Hosting.Environments.Development> : The [launchSettings.json](#lsj) file sets `ASPNETCORE_ENVIRONMENT` to `Development` on the local machine.</span></span>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="36319-115"><xref:Microsoft.Extensions.Hosting.Environments.Production>: Valeur par défaut si `DOTNET_ENVIRONMENT` et `ASPNETCORE_ENVIRONMENT` n’ont pas été définis.</span><span class="sxs-lookup"><span data-stu-id="36319-115"><xref:Microsoft.Extensions.Hosting.Environments.Production> : The default if `DOTNET_ENVIRONMENT` and `ASPNETCORE_ENVIRONMENT` have not been set.</span></span>

<span data-ttu-id="36319-116">Le code suivant :</span><span class="sxs-lookup"><span data-stu-id="36319-116">The following code:</span></span>

* <span data-ttu-id="36319-117">Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.</span><span class="sxs-lookup"><span data-stu-id="36319-117">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="36319-118">Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) lorsque la valeur de `ASPNETCORE_ENVIRONMENT` est définie sur `Staging` , `Production` ou `Staging_2` .</span><span class="sxs-lookup"><span data-stu-id="36319-118">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set to `Staging`, `Production`, or  `Staging_2`.</span></span>
* <span data-ttu-id="36319-119">Injecte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36319-119">Injects <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="36319-120">Cette approche est utile lorsque l’application doit uniquement être ajustée `Startup.Configure` pour quelques environnements avec des différences de code minimales par environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-120">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>
* <span data-ttu-id="36319-121">Est semblable au code généré par les modèles ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="36319-121">Is similar to the code generated by the ASP.NET Core templates.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="36319-122">Le [tag Helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) pour inclure ou exclure le balisage dans l’élément :</span><span class="sxs-lookup"><span data-stu-id="36319-122">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="36319-123">La [page about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) de l' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) comprend le balisage précédent et affiche la valeur de `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="36319-123">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) includes the preceding markup and displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

<span data-ttu-id="36319-124">Sur Windows et macOS, les variables d’environnement et les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="36319-124">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="36319-125">Les valeurs et les variables d’environnement Linux respectent la **casse** par défaut.</span><span class="sxs-lookup"><span data-stu-id="36319-125">Linux environment variables and values are **case-sensitive** by default.</span></span>

### <a name="create-environmentssample"></a><span data-ttu-id="36319-126">Créer EnvironmentsSample</span><span class="sxs-lookup"><span data-stu-id="36319-126">Create EnvironmentsSample</span></span>

<span data-ttu-id="36319-127">L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) utilisé dans ce document est basé sur un Razor projet de pages nommé *EnvironmentsSample*.</span><span class="sxs-lookup"><span data-stu-id="36319-127">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) used in this document is based on a Razor Pages project named *EnvironmentsSample*.</span></span>

<span data-ttu-id="36319-128">Le code suivant crée et exécute une application Web nommée *EnvironmentsSample*:</span><span class="sxs-lookup"><span data-stu-id="36319-128">The following code creates and runs a web app named *EnvironmentsSample*:</span></span>

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

<span data-ttu-id="36319-129">Lorsque l’application s’exécute, elle affiche une partie des résultats suivants :</span><span class="sxs-lookup"><span data-stu-id="36319-129">When the app runs, it displays some of the following output:</span></span>

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a><span data-ttu-id="36319-130">Développement et launchSettings.jssur</span><span class="sxs-lookup"><span data-stu-id="36319-130">Development and launchSettings.json</span></span>

<span data-ttu-id="36319-131">L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées en production.</span><span class="sxs-lookup"><span data-stu-id="36319-131">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="36319-132">Par exemple, les modèles ASP.NET Core activent la [page d’exceptions du développeur](xref:fundamentals/error-handling#developer-exception-page) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="36319-132">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="36319-133">L’environnement de développement de l’ordinateur local peut être défini dans le fichier *Properties\launchSettings.json* du projet.</span><span class="sxs-lookup"><span data-stu-id="36319-133">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="36319-134">Les valeurs d’environnement définies dans *launchSettings.json* remplacent les valeurs définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="36319-134">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="36319-135">Le *launchSettings.jssur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="36319-135">The *launchSettings.json* file:</span></span>

* <span data-ttu-id="36319-136">Est utilisé uniquement sur l’ordinateur de développement local.</span><span class="sxs-lookup"><span data-stu-id="36319-136">Is only used on the local development machine.</span></span>
* <span data-ttu-id="36319-137">N’est pas déployé.</span><span class="sxs-lookup"><span data-stu-id="36319-137">Is not deployed.</span></span>
* <span data-ttu-id="36319-138">contient des paramètres de profil.</span><span class="sxs-lookup"><span data-stu-id="36319-138">contains profile settings.</span></span>

<span data-ttu-id="36319-139">Le code JSON suivant montre l' *launchSettings.jssur* un fichier pour un ASP.net Core Web projeté nommé *EnvironmentsSample* créé avec Visual Studio ou `dotnet new` :</span><span class="sxs-lookup"><span data-stu-id="36319-139">The following JSON shows the *launchSettings.json* file for an ASP.NET Core web projected named *EnvironmentsSample* created with Visual Studio or `dotnet new`:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

<span data-ttu-id="36319-140">Le balisage précédent contient deux profils :</span><span class="sxs-lookup"><span data-stu-id="36319-140">The preceding markup contains two profiles:</span></span>

* <span data-ttu-id="36319-141">`IIS Express`: Profil par défaut utilisé lors du lancement de l’application à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="36319-141">`IIS Express`: The default profile used when launching the app from Visual Studio.</span></span> <span data-ttu-id="36319-142">La `"commandName"` clé a la valeur `"IISExpress"` , par conséquent, [iisexpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) est le serveur Web.</span><span class="sxs-lookup"><span data-stu-id="36319-142">The `"commandName"` key has the value `"IISExpress"`, therefore, [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) is the web server.</span></span> <span data-ttu-id="36319-143">Vous pouvez définir le profil de lancement sur le projet ou tout autre profil inclus.</span><span class="sxs-lookup"><span data-stu-id="36319-143">You can set the launch profile to the project or any other profile included.</span></span> <span data-ttu-id="36319-144">Par exemple, dans l’image ci-dessous, sélectionnez le nom du projet pour lancer le [serveur Web Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="36319-144">For example, in the image below, selecting the project name launches the [Kestrel web server](xref:fundamentals/servers/kestrel).</span></span>

  ![IIS Express lancer dans le menu](environments/_static/iisx2.png)
* <span data-ttu-id="36319-146">`EnvironmentsSample`: Le nom du profil est le nom du projet.</span><span class="sxs-lookup"><span data-stu-id="36319-146">`EnvironmentsSample`: The profile name is the project name.</span></span> <span data-ttu-id="36319-147">Ce profil est utilisé par défaut lors du lancement de l’application avec `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="36319-147">This profile is used by default when launching the app with `dotnet run`.</span></span>  <span data-ttu-id="36319-148">La `"commandName"` clé a la valeur `"Project"` , par conséquent, le [serveur Web Kestrel](xref:fundamentals/servers/kestrel) est lancé.</span><span class="sxs-lookup"><span data-stu-id="36319-148">The `"commandName"` key has the value `"Project"`, therefore, the [Kestrel web server](xref:fundamentals/servers/kestrel) is launched.</span></span>

<span data-ttu-id="36319-149">La valeur de `commandName` peut spécifier le serveur Web à lancer.</span><span class="sxs-lookup"><span data-stu-id="36319-149">The value of `commandName` can specify the web server to launch.</span></span> <span data-ttu-id="36319-150">`commandName` peut avoir l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="36319-150">`commandName` can be any one of the following:</span></span>

* <span data-ttu-id="36319-151">`IISExpress`: Lance IIS Express.</span><span class="sxs-lookup"><span data-stu-id="36319-151">`IISExpress` : Launches IIS Express.</span></span>
* <span data-ttu-id="36319-152">`IIS`: Aucun serveur Web n’a été lancé.</span><span class="sxs-lookup"><span data-stu-id="36319-152">`IIS` : No web server launched.</span></span> <span data-ttu-id="36319-153">Les services Internet (IIS) sont censés être disponibles.</span><span class="sxs-lookup"><span data-stu-id="36319-153">IIS is expected to be available.</span></span>
* <span data-ttu-id="36319-154">`Project`: Lance Kestrel.</span><span class="sxs-lookup"><span data-stu-id="36319-154">`Project` : Launches Kestrel.</span></span>

<span data-ttu-id="36319-155">L’onglet **Déboguer** des propriétés de projet Visual Studio fournit une interface utilisateur graphique pour modifier les *launchSettings.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="36319-155">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file.</span></span> <span data-ttu-id="36319-156">Les modifications apportées aux profils de projet peuvent ne prendre effet qu’une fois le serveur web redémarré.</span><span class="sxs-lookup"><span data-stu-id="36319-156">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="36319-157">Vous devez redémarrer Kestrel pour qu’il puisse détecter les modifications apportées à son environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-157">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

![Propriétés de projet, définition des variables d’environnement](environments/_static/project-properties-debug.png)

<span data-ttu-id="36319-159">Le *launchSettings.jssuivant sur* le fichier contient plusieurs profils :</span><span class="sxs-lookup"><span data-stu-id="36319-159">The following *launchSettings.json* file contains multiple profiles:</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

<span data-ttu-id="36319-160">Les profils peuvent être sélectionnés :</span><span class="sxs-lookup"><span data-stu-id="36319-160">Profiles can be selected:</span></span>

* <span data-ttu-id="36319-161">À partir de l’interface utilisateur de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="36319-161">From the Visual Studio UI.</span></span>
* <span data-ttu-id="36319-162">À l’aide [`dotnet run`](/dotnet/core/tools/dotnet-run) de la commande dans une interface de commande avec l' `--launch-profile` option définie sur le nom du profil.</span><span class="sxs-lookup"><span data-stu-id="36319-162">Using the [`dotnet run`](/dotnet/core/tools/dotnet-run) command in a command shell with the `--launch-profile` option set to the profile's name.</span></span> <span data-ttu-id="36319-163">*Cette approche ne prend en charge que les profils Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="36319-163">*This approach only supports Kestrel profiles.*</span></span>

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> <span data-ttu-id="36319-164">*launchSettings.json* ne doit pas stocker de secrets.</span><span class="sxs-lookup"><span data-stu-id="36319-164">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="36319-165">Vous pouvez utiliser [l’outil Secret Manager](xref:security/app-secrets) afin de stocker des secrets pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="36319-165">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="36319-166">Quand vous utilisez [Visual Studio Code](https://code.visualstudio.com/), les variables d’environnement peuvent être définies dans le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="36319-166">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="36319-167">L’exemple suivant définit plusieurs [valeurs de configuration d’hôte variables d’environnement](xref:fundamentals/host/web-host#host-configuration-values):</span><span class="sxs-lookup"><span data-stu-id="36319-167">The following example sets several [Host configuration values environment variables](xref:fundamentals/host/web-host#host-configuration-values):</span></span>

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

<span data-ttu-id="36319-168">Le fichier *. vscode/launch.js* n’est utilisé que par Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="36319-168">The *.vscode/launch.json* file is only used by Visual Studio Code.</span></span>

### <a name="production"></a><span data-ttu-id="36319-169">Production</span><span class="sxs-lookup"><span data-stu-id="36319-169">Production</span></span>

<span data-ttu-id="36319-170">L’environnement de production doit être configuré pour optimiser la sécurité, les [performances](xref:performance/performance-best-practices)et la robustesse des applications.</span><span class="sxs-lookup"><span data-stu-id="36319-170">The production environment should be configured to maximize security, [performance](xref:performance/performance-best-practices), and application robustness.</span></span> <span data-ttu-id="36319-171">Voici quelques paramètres courants qui diffèrent du développement :</span><span class="sxs-lookup"><span data-stu-id="36319-171">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="36319-172">[Mise en cache](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="36319-172">[Caching](xref:performance/caching/memory).</span></span>
* <span data-ttu-id="36319-173">Les ressources côté client sont groupées, réduites et éventuellement servies à partir d’un CDN.</span><span class="sxs-lookup"><span data-stu-id="36319-173">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="36319-174">Les Pages d’erreur de diagnostic sont désactivées.</span><span class="sxs-lookup"><span data-stu-id="36319-174">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="36319-175">Les pages d’erreur conviviales sont activées.</span><span class="sxs-lookup"><span data-stu-id="36319-175">Friendly error pages enabled.</span></span>
* <span data-ttu-id="36319-176">[Journalisation](xref:fundamentals/logging/index) et surveillance de la production activée.</span><span class="sxs-lookup"><span data-stu-id="36319-176">Production [logging](xref:fundamentals/logging/index) and monitoring enabled.</span></span> <span data-ttu-id="36319-177">Par exemple, à l’aide de [application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="36319-177">For example, using [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="36319-178">Définir l’environnement</span><span class="sxs-lookup"><span data-stu-id="36319-178">Set the environment</span></span>

<span data-ttu-id="36319-179">Il est souvent utile de définir un environnement spécifique pour le test avec une variable d’environnement ou un paramètre de plateforme.</span><span class="sxs-lookup"><span data-stu-id="36319-179">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="36319-180">Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage.</span><span class="sxs-lookup"><span data-stu-id="36319-180">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="36319-181">La méthode de configuration de l’environnement dépend du système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="36319-181">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="36319-182">Lorsque l’hôte est généré, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="36319-182">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="36319-183">L’environnement de l’application ne peut pas être modifié tant que l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="36319-183">The app's environment can't be changed while the app is running.</span></span>

<span data-ttu-id="36319-184">La [page about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) de l' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) affiche la valeur de `IWebHostEnvironment.EnvironmentName` .</span><span class="sxs-lookup"><span data-stu-id="36319-184">The [About page](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) from the [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) displays the value of `IWebHostEnvironment.EnvironmentName`.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="36319-185">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="36319-185">Azure App Service</span></span>

<span data-ttu-id="36319-186">Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="36319-186">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="36319-187">Sélectionnez l’application dans le panneau **App Services**.</span><span class="sxs-lookup"><span data-stu-id="36319-187">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="36319-188">Dans le groupe **paramètres** , sélectionnez le panneau **configuration** .</span><span class="sxs-lookup"><span data-stu-id="36319-188">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="36319-189">Dans l’onglet Paramètres de l' **application** , sélectionnez **nouveau paramètre d’application**.</span><span class="sxs-lookup"><span data-stu-id="36319-189">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="36319-190">Dans la fenêtre **Ajouter/modifier un paramètre d’application** , indiquez `ASPNETCORE_ENVIRONMENT` le **nom**.</span><span class="sxs-lookup"><span data-stu-id="36319-190">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="36319-191">Pour **valeur**, fournissez l’environnement (par exemple, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="36319-191">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="36319-192">Activez la case à cocher **paramètre d’emplacement de déploiement** si vous souhaitez que le paramètre d’environnement reste avec l’emplacement actuel lorsque les emplacements de déploiement sont permutés.</span><span class="sxs-lookup"><span data-stu-id="36319-192">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="36319-193">Pour plus d’informations, consultez [configurer des environnements intermédiaires dans Azure App service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="36319-193">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="36319-194">Sélectionnez **OK** pour fermer la fenêtre **Ajouter/modifier un paramètre d’application** .</span><span class="sxs-lookup"><span data-stu-id="36319-194">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="36319-195">Sélectionnez **Enregistrer** en haut du panneau de **configuration** .</span><span class="sxs-lookup"><span data-stu-id="36319-195">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="36319-196">Azure App Service redémarre automatiquement l’application après l’ajout, la modification ou la suppression d’un paramètre d’application dans le Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="36319-196">Azure App Service automatically restarts the app after an app setting is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="36319-197">Windows</span><span class="sxs-lookup"><span data-stu-id="36319-197">Windows</span></span>

<span data-ttu-id="36319-198">Valeurs d’environnement danslaunchSettings.jsen cas *de remplacement des* valeurs définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="36319-198">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="36319-199">Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="36319-199">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="36319-200">**Invite de commandes**</span><span class="sxs-lookup"><span data-stu-id="36319-200">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

<span data-ttu-id="36319-201">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="36319-201">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

<span data-ttu-id="36319-202">La commande précédente définit `ASPNETCORE_ENVIRONMENT` uniquement pour les processus lancés à partir de cette fenêtre de commande.</span><span class="sxs-lookup"><span data-stu-id="36319-202">The preceding command sets `ASPNETCORE_ENVIRONMENT` only for processes launched from that command window.</span></span>

<span data-ttu-id="36319-203">Pour définir la valeur globalement dans Windows, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="36319-203">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="36319-204">Ouvrez le **Panneau de configuration** > **Système** > **Paramètres système avancés**, puis ajoutez ou modifiez la valeur `ASPNETCORE_ENVIRONMENT` :</span><span class="sxs-lookup"><span data-stu-id="36319-204">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriétés système avancées](environments/_static/systemsetting_environment.png)

  ![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="36319-207">Ouvrez une invite de commandes d’administration, puis utilisez la commande `setx`, ou ouvrez une invite de commandes PowerShell d’administration et utilisez `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="36319-207">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="36319-208">**Invite de commandes**</span><span class="sxs-lookup"><span data-stu-id="36319-208">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  <span data-ttu-id="36319-209">Le commutateur `/M` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="36319-209">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="36319-210">Si le commutateur `/M` n’est pas utilisé, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36319-210">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="36319-211">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="36319-211">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  <span data-ttu-id="36319-212">La valeur d’option `Machine` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="36319-212">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="36319-213">Si la valeur d’option est remplacée par `User`, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36319-213">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="36319-214">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie globalement, elle prend effet pour `dotnet run` dans n’importe quelle fenêtre Commande ouverte une fois la valeur définie.</span><span class="sxs-lookup"><span data-stu-id="36319-214">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span> <span data-ttu-id="36319-215">Valeurs d’environnement danslaunchSettings.jsen cas *de remplacement des* valeurs définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="36319-215">Environment values in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="36319-216">**web.config**</span><span class="sxs-lookup"><span data-stu-id="36319-216">**web.config**</span></span>

<span data-ttu-id="36319-217">Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="36319-217">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="36319-218">**Fichier projet ou profil de publication**</span><span class="sxs-lookup"><span data-stu-id="36319-218">**Project file or publish profile**</span></span>

<span data-ttu-id="36319-219">**Pour les déploiements de Windows IIS :** Incluez la `<EnvironmentName>` propriété dans le [profil de publication (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="36319-219">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="36319-220">Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :</span><span class="sxs-lookup"><span data-stu-id="36319-220">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="36319-221">**Par pool d’applications IIS**</span><span class="sxs-lookup"><span data-stu-id="36319-221">**Per IIS Application Pool**</span></span>

<span data-ttu-id="36319-222">Pour définir la variables d’environnement `ASPNETCORE_ENVIRONMENT` pour une application qui s’exécute dans un pool d’applications isolé (prie en charge sur IIS 10.0 ou versions ultérieures), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="36319-222">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="36319-223">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie pour un pool d’applications, sa valeur remplace un paramètre au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="36319-223">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

<span data-ttu-id="36319-224">Lors de l’hébergement d’une application dans IIS et de l’ajout ou du changement de la variable d’environnement `ASPNETCORE_ENVIRONMENT`, utilisez l’une des approches suivantes pour que la nouvelle valeur soit récupérée par des applications :</span><span class="sxs-lookup"><span data-stu-id="36319-224">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>

* <span data-ttu-id="36319-225">Exécutez la commande `net stop was /y` suivie de `net start w3svc` à partir d’une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="36319-225">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
* <span data-ttu-id="36319-226">Redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="36319-226">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="36319-227">macOS</span><span class="sxs-lookup"><span data-stu-id="36319-227">macOS</span></span>

<span data-ttu-id="36319-228">Vous pouvez définir l’environnement actuel pour macOS en ligne durant l’exécution de l’application :</span><span class="sxs-lookup"><span data-stu-id="36319-228">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

<span data-ttu-id="36319-229">Vous pouvez également définir l’environnement avec `export` avant d’exécuter l’application :</span><span class="sxs-lookup"><span data-stu-id="36319-229">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

<span data-ttu-id="36319-230">Les variables d’environnement de niveau machine sont définies dans le fichier *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="36319-230">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="36319-231">Modifiez le fichier à l’aide d’un éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="36319-231">Edit the file using any text editor.</span></span> <span data-ttu-id="36319-232">Ajoutez l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="36319-232">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a><span data-ttu-id="36319-233">Linux</span><span class="sxs-lookup"><span data-stu-id="36319-233">Linux</span></span>

<span data-ttu-id="36319-234">Pour les distributions Linux, utilisez la `export` commande à partir d’une invite de commandes pour les paramètres de variable basés sur session et *bash_profile* fichier pour les paramètres d’environnement au niveau de l’ordinateur.</span><span class="sxs-lookup"><span data-stu-id="36319-234">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="36319-235">Définir l’environnement dans le code</span><span class="sxs-lookup"><span data-stu-id="36319-235">Set the environment in code</span></span>

<span data-ttu-id="36319-236">Appelez <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> lors de la génération de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="36319-236">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="36319-237">Consultez <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="36319-237">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="36319-238">Configuration par environnement</span><span class="sxs-lookup"><span data-stu-id="36319-238">Configuration by environment</span></span>

<span data-ttu-id="36319-239">Pour charger la configuration par environnement, consultez <xref:fundamentals/configuration/index#json-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="36319-239">To load configuration by environment, see <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="36319-240">Classe et méthodes Startup en fonction de l’environnement</span><span class="sxs-lookup"><span data-stu-id="36319-240">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="36319-241">Injecter IWebHostEnvironment dans la classe Startup</span><span class="sxs-lookup"><span data-stu-id="36319-241">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="36319-242">Injectez <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans le `Startup` constructeur.</span><span class="sxs-lookup"><span data-stu-id="36319-242">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="36319-243">Cette approche est utile lorsque l’application ne nécessite la configuration `Startup` que pour quelques environnements avec des différences de code minimales par environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-243">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="36319-244">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="36319-244">In the following example:</span></span>

* <span data-ttu-id="36319-245">L’environnement est conservé dans le `_env` champ.</span><span class="sxs-lookup"><span data-stu-id="36319-245">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="36319-246">`_env`est utilisé dans `ConfigureServices` et `Configure` pour appliquer la configuration de démarrage en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="36319-246">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a><span data-ttu-id="36319-247">Conventions de la classe Startup</span><span class="sxs-lookup"><span data-stu-id="36319-247">Startup class conventions</span></span>

<span data-ttu-id="36319-248">Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="36319-248">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="36319-249">L’application peut définir plusieurs `Startup` classes pour différents environnements.</span><span class="sxs-lookup"><span data-stu-id="36319-249">The app can define multiple `Startup` classes for different environments.</span></span> <span data-ttu-id="36319-250">La `Startup` classe appropriée est sélectionnée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="36319-250">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="36319-251">La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="36319-251">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="36319-252">Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="36319-252">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="36319-253">Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-253">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span> <span data-ttu-id="36319-254">Les applications classiques n’ont pas besoin de cette approche.</span><span class="sxs-lookup"><span data-stu-id="36319-254">Typical apps will not need this approach.</span></span>

<span data-ttu-id="36319-255">Pour implémenter des classes basées sur l’environnement `Startup` , créez `Startup{EnvironmentName}` des classes et une classe de secours `Startup` :</span><span class="sxs-lookup"><span data-stu-id="36319-255">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` classes and a fallback `Startup` class:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

<span data-ttu-id="36319-256">Utilisez la surcharge [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) qui accepte un nom d’assembly :</span><span class="sxs-lookup"><span data-stu-id="36319-256">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a><span data-ttu-id="36319-257">Conventions de la méthode Startup</span><span class="sxs-lookup"><span data-stu-id="36319-257">Startup method conventions</span></span>

<span data-ttu-id="36319-258">[Configurez](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en charge des versions spécifiques à l’environnement du formulaire `Configure<EnvironmentName>` et `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="36319-258">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="36319-259">Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement :</span><span class="sxs-lookup"><span data-stu-id="36319-259">This approach is useful when the app requires configuring startup for several environments with many code differences per environment:</span></span>

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="36319-260">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="36319-260">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36319-261">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="36319-261">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="36319-262">ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-262">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="36319-263">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36319-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="36319-264">Environnements</span><span class="sxs-lookup"><span data-stu-id="36319-264">Environments</span></span>

<span data-ttu-id="36319-265">ASP.NET Core lit la variable d’environnement `ASPNETCORE_ENVIRONMENT` au démarrage de l’application et stocke la valeur dans [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="36319-265">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="36319-266">`ASPNETCORE_ENVIRONMENT`peut être défini sur n’importe quelle valeur, mais trois valeurs sont fournies par l’infrastructure :</span><span class="sxs-lookup"><span data-stu-id="36319-266">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="36319-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (par défaut)</span><span class="sxs-lookup"><span data-stu-id="36319-267"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="36319-268">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="36319-268">The preceding code:</span></span>

* <span data-ttu-id="36319-269">Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.</span><span class="sxs-lookup"><span data-stu-id="36319-269">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="36319-270">Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quand `ASPNETCORE_ENVIRONMENT` est définie sur l’une des valeurs :</span><span class="sxs-lookup"><span data-stu-id="36319-270">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="36319-271">Le [Tag helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de la propriété `IHostingEnvironment.EnvironmentName` pour inclure ou exclure le balisage dans l’élément :</span><span class="sxs-lookup"><span data-stu-id="36319-271">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="36319-272">Sur Windows et macOS, les variables d’environnement et les valeurs ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="36319-272">On Windows and macOS, environment variables and values aren't case-sensitive.</span></span> <span data-ttu-id="36319-273">Les valeurs et les variables d’environnement Linux respectent la casse par défaut.</span><span class="sxs-lookup"><span data-stu-id="36319-273">Linux environment variables and values are case-sensitive by default.</span></span>

### <a name="development"></a><span data-ttu-id="36319-274">Développement</span><span class="sxs-lookup"><span data-stu-id="36319-274">Development</span></span>

<span data-ttu-id="36319-275">L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées en production.</span><span class="sxs-lookup"><span data-stu-id="36319-275">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="36319-276">Par exemple, les modèles ASP.NET Core activent la [page d’exceptions du développeur](xref:fundamentals/error-handling#developer-exception-page) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="36319-276">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="36319-277">L’environnement de développement de l’ordinateur local peut être défini dans le fichier *Properties\launchSettings.json* du projet.</span><span class="sxs-lookup"><span data-stu-id="36319-277">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="36319-278">Les valeurs d’environnement définies dans *launchSettings.json* remplacent les valeurs définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="36319-278">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="36319-279">Le code JSON suivant montre trois profils à partir d’un fichier *launchSettings.json* :</span><span class="sxs-lookup"><span data-stu-id="36319-279">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="36319-280">La propriété `applicationUrl` dans *launchSettings.json* peut spécifier une liste d’URL de serveur.</span><span class="sxs-lookup"><span data-stu-id="36319-280">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="36319-281">Utilisez un point-virgule entre les URL de la liste :</span><span class="sxs-lookup"><span data-stu-id="36319-281">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="36319-282">Quand l’application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run), le premier profil avec `"commandName": "Project"` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="36319-282">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="36319-283">La valeur de `commandName` spécifie le serveur web à lancer.</span><span class="sxs-lookup"><span data-stu-id="36319-283">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="36319-284">`commandName` peut avoir l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="36319-284">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="36319-285">`Project` (qui lance Kestrel)</span><span class="sxs-lookup"><span data-stu-id="36319-285">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="36319-286">Quand une application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run) :</span><span class="sxs-lookup"><span data-stu-id="36319-286">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="36319-287">*launchSettings.json* est lu, s’il est disponible.</span><span class="sxs-lookup"><span data-stu-id="36319-287">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="36319-288">Les paramètres `environmentVariables` dans *launchSettings.json* remplacent les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-288">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="36319-289">L’environnement d’hébergement s’affiche.</span><span class="sxs-lookup"><span data-stu-id="36319-289">The hosting environment is displayed.</span></span>

<span data-ttu-id="36319-290">La sortie suivante montre une application démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run) :</span><span class="sxs-lookup"><span data-stu-id="36319-290">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="36319-291">L’onglet **Déboguer** des propriétés de projet Visual Studio fournit une interface graphique utilisateur qui permet de modifier le fichier *launchSettings.json* :</span><span class="sxs-lookup"><span data-stu-id="36319-291">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Propriétés de projet, définition des variables d’environnement](environments/_static/project-properties-debug.png)

<span data-ttu-id="36319-293">Les modifications apportées aux profils de projet peuvent ne prendre effet qu’une fois le serveur web redémarré.</span><span class="sxs-lookup"><span data-stu-id="36319-293">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="36319-294">Vous devez redémarrer Kestrel pour qu’il puisse détecter les modifications apportées à son environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-294">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="36319-295">*launchSettings.json* ne doit pas stocker de secrets.</span><span class="sxs-lookup"><span data-stu-id="36319-295">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="36319-296">Vous pouvez utiliser [l’outil Secret Manager](xref:security/app-secrets) afin de stocker des secrets pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="36319-296">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="36319-297">Quand vous utilisez [Visual Studio Code](https://code.visualstudio.com/), les variables d’environnement peuvent être définies dans le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="36319-297">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="36319-298">L’exemple suivant définit `Development` comme environnement :</span><span class="sxs-lookup"><span data-stu-id="36319-298">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="36319-299">Un fichier *.vscode/launch.json* dans le projet n’est pas lu au démarrage de l’application avec `dotnet run` de la même façon que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="36319-299">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="36319-300">Lors du lancement d’une application en cours de développement qui n’a pas de fichier *launchSettings.json*, vous devez définir l’environnement avec une variable d’environnement ou un argument de ligne de commande pour la commande `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="36319-300">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="36319-301">Production</span><span class="sxs-lookup"><span data-stu-id="36319-301">Production</span></span>

<span data-ttu-id="36319-302">Vous devez configurer l’environnement de production pour optimiser la sécurité, les performances et la robustesse de l’application.</span><span class="sxs-lookup"><span data-stu-id="36319-302">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="36319-303">Voici quelques paramètres courants qui diffèrent du développement :</span><span class="sxs-lookup"><span data-stu-id="36319-303">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="36319-304">Mise en cache.</span><span class="sxs-lookup"><span data-stu-id="36319-304">Caching.</span></span>
* <span data-ttu-id="36319-305">Les ressources côté client sont groupées, réduites et éventuellement servies à partir d’un CDN.</span><span class="sxs-lookup"><span data-stu-id="36319-305">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="36319-306">Les Pages d’erreur de diagnostic sont désactivées.</span><span class="sxs-lookup"><span data-stu-id="36319-306">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="36319-307">Les pages d’erreur conviviales sont activées.</span><span class="sxs-lookup"><span data-stu-id="36319-307">Friendly error pages enabled.</span></span>
* <span data-ttu-id="36319-308">La journalisation et la surveillance de la production sont activées.</span><span class="sxs-lookup"><span data-stu-id="36319-308">Production logging and monitoring enabled.</span></span> <span data-ttu-id="36319-309">Par exemple, [application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="36319-309">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="36319-310">Définir l’environnement</span><span class="sxs-lookup"><span data-stu-id="36319-310">Set the environment</span></span>

<span data-ttu-id="36319-311">Il est souvent utile de définir un environnement spécifique pour le test avec une variable d’environnement ou un paramètre de plateforme.</span><span class="sxs-lookup"><span data-stu-id="36319-311">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="36319-312">Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage.</span><span class="sxs-lookup"><span data-stu-id="36319-312">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="36319-313">La méthode de configuration de l’environnement dépend du système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="36319-313">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="36319-314">Lorsque l’hôte est généré, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="36319-314">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="36319-315">L’environnement de l’application ne peut pas être modifié tant que l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="36319-315">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="36319-316">Variable d’environnement ou paramètre de plateforme</span><span class="sxs-lookup"><span data-stu-id="36319-316">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="36319-317">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="36319-317">Azure App Service</span></span>

<span data-ttu-id="36319-318">Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="36319-318">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="36319-319">Sélectionnez l’application dans le panneau **App Services**.</span><span class="sxs-lookup"><span data-stu-id="36319-319">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="36319-320">Dans le groupe **paramètres** , sélectionnez le panneau **configuration** .</span><span class="sxs-lookup"><span data-stu-id="36319-320">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="36319-321">Dans l’onglet Paramètres de l' **application** , sélectionnez **nouveau paramètre d’application**.</span><span class="sxs-lookup"><span data-stu-id="36319-321">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="36319-322">Dans la fenêtre **Ajouter/modifier un paramètre d’application** , indiquez `ASPNETCORE_ENVIRONMENT` le **nom**.</span><span class="sxs-lookup"><span data-stu-id="36319-322">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="36319-323">Pour **valeur**, fournissez l’environnement (par exemple, `Staging` ).</span><span class="sxs-lookup"><span data-stu-id="36319-323">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="36319-324">Activez la case à cocher **paramètre d’emplacement de déploiement** si vous souhaitez que le paramètre d’environnement reste avec l’emplacement actuel lorsque les emplacements de déploiement sont permutés.</span><span class="sxs-lookup"><span data-stu-id="36319-324">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="36319-325">Pour plus d’informations, consultez [configurer des environnements intermédiaires dans Azure App service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="36319-325">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="36319-326">Sélectionnez **OK** pour fermer la fenêtre **Ajouter/modifier un paramètre d’application** .</span><span class="sxs-lookup"><span data-stu-id="36319-326">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="36319-327">Sélectionnez **Enregistrer** en haut du panneau de **configuration** .</span><span class="sxs-lookup"><span data-stu-id="36319-327">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="36319-328">Azure App Service redémarre automatiquement l’application après qu’un paramètre d’application (variable d’environnement) est ajouté, changé ou supprimé dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="36319-328">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="36319-329">Windows</span><span class="sxs-lookup"><span data-stu-id="36319-329">Windows</span></span>

<span data-ttu-id="36319-330">Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="36319-330">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="36319-331">**Invite de commandes**</span><span class="sxs-lookup"><span data-stu-id="36319-331">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="36319-332">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="36319-332">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="36319-333">Ces commandes prennent effet uniquement pour la fenêtre active.</span><span class="sxs-lookup"><span data-stu-id="36319-333">These commands only take effect for the current window.</span></span> <span data-ttu-id="36319-334">Quand la fenêtre est fermée, le paramètre `ASPNETCORE_ENVIRONMENT` reprend la valeur de machine ou la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="36319-334">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="36319-335">Pour définir la valeur globalement dans Windows, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="36319-335">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="36319-336">Ouvrez le **Panneau de configuration** > **Système** > **Paramètres système avancés**, puis ajoutez ou modifiez la valeur `ASPNETCORE_ENVIRONMENT` :</span><span class="sxs-lookup"><span data-stu-id="36319-336">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriétés système avancées](environments/_static/systemsetting_environment.png)

  ![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="36319-339">Ouvrez une invite de commandes d’administration, puis utilisez la commande `setx`, ou ouvrez une invite de commandes PowerShell d’administration et utilisez `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="36319-339">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="36319-340">**Invite de commandes**</span><span class="sxs-lookup"><span data-stu-id="36319-340">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="36319-341">Le commutateur `/M` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="36319-341">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="36319-342">Si le commutateur `/M` n’est pas utilisé, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36319-342">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="36319-343">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="36319-343">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="36319-344">La valeur d’option `Machine` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="36319-344">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="36319-345">Si la valeur d’option est remplacée par `User`, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="36319-345">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="36319-346">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie globalement, elle prend effet pour `dotnet run` dans n’importe quelle fenêtre Commande ouverte une fois la valeur définie.</span><span class="sxs-lookup"><span data-stu-id="36319-346">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="36319-347">**web.config**</span><span class="sxs-lookup"><span data-stu-id="36319-347">**web.config**</span></span>

<span data-ttu-id="36319-348">Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="36319-348">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="36319-349">**Fichier projet ou profil de publication**</span><span class="sxs-lookup"><span data-stu-id="36319-349">**Project file or publish profile**</span></span>

<span data-ttu-id="36319-350">**Pour les déploiements de Windows IIS :** Incluez la `<EnvironmentName>` propriété dans le [profil de publication (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="36319-350">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="36319-351">Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :</span><span class="sxs-lookup"><span data-stu-id="36319-351">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="36319-352">**Par pool d’applications IIS**</span><span class="sxs-lookup"><span data-stu-id="36319-352">**Per IIS Application Pool**</span></span>

<span data-ttu-id="36319-353">Pour définir la variables d’environnement `ASPNETCORE_ENVIRONMENT` pour une application qui s’exécute dans un pool d’applications isolé (prie en charge sur IIS 10.0 ou versions ultérieures), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="36319-353">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="36319-354">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie pour un pool d’applications, sa valeur remplace un paramètre au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="36319-354">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="36319-355">Lors de l’hébergement d’une application dans IIS et de l’ajout ou du changement de la variable d’environnement `ASPNETCORE_ENVIRONMENT`, utilisez l’une des approches suivantes pour que la nouvelle valeur soit récupérée par des applications :</span><span class="sxs-lookup"><span data-stu-id="36319-355">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="36319-356">Exécutez la commande `net stop was /y` suivie de `net start w3svc` à partir d’une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="36319-356">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="36319-357">Redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="36319-357">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="36319-358">macOS</span><span class="sxs-lookup"><span data-stu-id="36319-358">macOS</span></span>

<span data-ttu-id="36319-359">Vous pouvez définir l’environnement actuel pour macOS en ligne durant l’exécution de l’application :</span><span class="sxs-lookup"><span data-stu-id="36319-359">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="36319-360">Vous pouvez également définir l’environnement avec `export` avant d’exécuter l’application :</span><span class="sxs-lookup"><span data-stu-id="36319-360">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="36319-361">Les variables d’environnement de niveau machine sont définies dans le fichier *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="36319-361">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="36319-362">Modifiez le fichier à l’aide d’un éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="36319-362">Edit the file using any text editor.</span></span> <span data-ttu-id="36319-363">Ajoutez l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="36319-363">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="36319-364">Linux</span><span class="sxs-lookup"><span data-stu-id="36319-364">Linux</span></span>

<span data-ttu-id="36319-365">Pour les distributions Linux, utilisez la `export` commande à partir d’une invite de commandes pour les paramètres de variable basés sur session et *bash_profile* fichier pour les paramètres d’environnement au niveau de l’ordinateur.</span><span class="sxs-lookup"><span data-stu-id="36319-365">For Linux distributions, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="36319-366">Définir l’environnement dans le code</span><span class="sxs-lookup"><span data-stu-id="36319-366">Set the environment in code</span></span>

<span data-ttu-id="36319-367">Appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> lors de la génération de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="36319-367">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="36319-368">Consultez <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="36319-368">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="36319-369">Configuration par environnement</span><span class="sxs-lookup"><span data-stu-id="36319-369">Configuration by environment</span></span>

<span data-ttu-id="36319-370">Pour charger la configuration par environnement, nous vous recommandons de disposer des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="36319-370">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="36319-371">fichiers *appSettings* (*appSettings. { Environment}. JSON*).</span><span class="sxs-lookup"><span data-stu-id="36319-371">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="36319-372">Consultez <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="36319-372">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="36319-373">Variables d’environnement (définies sur chaque système sur lequel l’application est hébergée).</span><span class="sxs-lookup"><span data-stu-id="36319-373">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="36319-374">Localisez <xref:fundamentals/host/web-host#environment> et <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="36319-374">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="36319-375">Secret Manager (dans l’environnement de développement uniquement).</span><span class="sxs-lookup"><span data-stu-id="36319-375">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="36319-376">Consultez <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="36319-376">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="36319-377">Classe et méthodes Startup en fonction de l’environnement</span><span class="sxs-lookup"><span data-stu-id="36319-377">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="36319-378">Injecter IHostingEnvironment dans Startup.Configurer</span><span class="sxs-lookup"><span data-stu-id="36319-378">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="36319-379">Injectez <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> dans `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36319-379">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="36319-380">Cette approche est utile lorsque l’application ne nécessite que la configuration `Startup.Configure` de quelques environnements avec des différences de code minimales par environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-380">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="36319-381">Injecter IHostingEnvironment dans la classe Startup</span><span class="sxs-lookup"><span data-stu-id="36319-381">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="36319-382">Injectez <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> dans le `Startup` constructeur et assignez le service à un champ à utiliser dans toute la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="36319-382">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="36319-383">Cette approche est utile lorsque l’application requiert la configuration du démarrage pour quelques environnements avec des différences de code minimales par environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-383">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="36319-384">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="36319-384">In the following example:</span></span>

* <span data-ttu-id="36319-385">L’environnement est conservé dans le `_env` champ.</span><span class="sxs-lookup"><span data-stu-id="36319-385">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="36319-386">`_env`est utilisé dans `ConfigureServices` et `Configure` pour appliquer la configuration de démarrage en fonction de l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="36319-386">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="36319-387">Conventions de la classe Startup</span><span class="sxs-lookup"><span data-stu-id="36319-387">Startup class conventions</span></span>

<span data-ttu-id="36319-388">Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="36319-388">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="36319-389">L’application peut définir `Startup` des classes distinctes pour différents environnements (par exemple, `StartupDevelopment` ).</span><span class="sxs-lookup"><span data-stu-id="36319-389">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="36319-390">La `Startup` classe appropriée est sélectionnée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="36319-390">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="36319-391">La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="36319-391">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="36319-392">Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="36319-392">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="36319-393">Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-393">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="36319-394">Pour implémenter des classes `Startup` basées sur l’environnement, créez une classe `Startup{EnvironmentName}` pour chaque environnement en cours d’utilisation et une classe `Startup` de base :</span><span class="sxs-lookup"><span data-stu-id="36319-394">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="36319-395">Utilisez la surcharge [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) qui accepte un nom d’assembly :</span><span class="sxs-lookup"><span data-stu-id="36319-395">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="36319-396">Conventions de la méthode Startup</span><span class="sxs-lookup"><span data-stu-id="36319-396">Startup method conventions</span></span>

<span data-ttu-id="36319-397">[Configurez](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en charge des versions spécifiques à l’environnement du formulaire `Configure<EnvironmentName>` et `Configure<EnvironmentName>Services` .</span><span class="sxs-lookup"><span data-stu-id="36319-397">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="36319-398">Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="36319-398">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="36319-399">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="36319-399">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
