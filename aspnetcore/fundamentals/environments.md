---
title: Utiliser plusieurs environnements dans ASP.NET Core
author: rick-anderson
description: Découvrez comment contrôler le comportement de l’application dans différents environnements dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656218"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="0f2cc-103">Utiliser plusieurs environnements dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0f2cc-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f2cc-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0f2cc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0f2cc-105">ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="0f2cc-106">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0f2cc-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="0f2cc-107">Environnements</span><span class="sxs-lookup"><span data-stu-id="0f2cc-107">Environments</span></span>

<span data-ttu-id="0f2cc-108">ASP.NET Core lit la variable `ASPNETCORE_ENVIRONMENT` de l’environnement au démarrage de l’application et stocke la valeur dans [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="0f2cc-109">`ASPNETCORE_ENVIRONMENT`peut être définie à n’importe quelle valeur, mais trois valeurs sont fournies par le cadre :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="0f2cc-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (par défaut)</span><span class="sxs-lookup"><span data-stu-id="0f2cc-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="0f2cc-111">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-111">The preceding code:</span></span>

* <span data-ttu-id="0f2cc-112">Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="0f2cc-113">Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quand `ASPNETCORE_ENVIRONMENT` est définie sur l’une des valeurs :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="0f2cc-114">Le [Tag helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de la propriété `IHostingEnvironment.EnvironmentName` pour inclure ou exclure le balisage dans l’élément :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="0f2cc-115">Sur Windows et macOS, les valeurs et les variables d’environnement ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="0f2cc-116">Les valeurs et les variables d’environnement Linux **respectent la casse** par défaut.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="0f2cc-117">Développement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-117">Development</span></span>

<span data-ttu-id="0f2cc-118">L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées en production.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="0f2cc-119">Par exemple, les modèles ASP.NET Core activent la [page d’exceptions du développeur](xref:fundamentals/error-handling#developer-exception-page) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="0f2cc-120">L’environnement de développement de l’ordinateur local peut être défini dans le fichier *Properties\launchSettings.json* du projet.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="0f2cc-121">Les valeurs d’environnement définies dans *launchSettings.json* remplacent les valeurs définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="0f2cc-122">Le code JSON suivant montre trois profils à partir d’un fichier *launchSettings.json* :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="0f2cc-123">La propriété `applicationUrl` dans *launchSettings.json* peut spécifier une liste d’URL de serveur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="0f2cc-124">Utilisez un point-virgule entre les URL de la liste :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="0f2cc-125">Quand l’application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run), le premier profil avec `"commandName": "Project"` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="0f2cc-126">La valeur de `commandName` spécifie le serveur web à lancer.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="0f2cc-127">`commandName` peut avoir l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="0f2cc-128">`Project` (qui lance Kestrel)</span><span class="sxs-lookup"><span data-stu-id="0f2cc-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="0f2cc-129">Quand une application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run) :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="0f2cc-130">*launchSettings.json* est lu, s’il est disponible.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="0f2cc-131">Les paramètres `environmentVariables` dans *launchSettings.json* remplacent les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="0f2cc-132">L’environnement d’hébergement s’affiche.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="0f2cc-133">La sortie suivante montre une application démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run) :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="0f2cc-134">L’onglet **Déboguer** des propriétés de projet Visual Studio fournit une interface graphique utilisateur qui permet de modifier le fichier *launchSettings.json* :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Propriétés de projet, définition des variables d’environnement](environments/_static/project-properties-debug.png)

<span data-ttu-id="0f2cc-136">Les modifications apportées aux profils de projet peuvent ne prendre effet qu’une fois le serveur web redémarré.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="0f2cc-137">Vous devez redémarrer Kestrel pour qu’il puisse détecter les modifications apportées à son environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2cc-138">*launchSettings.json* ne doit pas stocker de secrets.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="0f2cc-139">Vous pouvez utiliser [l’outil Secret Manager](xref:security/app-secrets) afin de stocker des secrets pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="0f2cc-140">Quand vous utilisez [Visual Studio Code](https://code.visualstudio.com/), les variables d’environnement peuvent être définies dans le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="0f2cc-141">L’exemple suivant définit `Development` comme environnement :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="0f2cc-142">Un fichier *.vscode/launch.json* dans le projet n’est pas lu au démarrage de l’application avec `dotnet run` de la même façon que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="0f2cc-143">Lors du lancement d’une application en cours de développement qui n’a pas de fichier *launchSettings.json*, vous devez définir l’environnement avec une variable d’environnement ou un argument de ligne de commande pour la commande `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="0f2cc-144">Production</span><span class="sxs-lookup"><span data-stu-id="0f2cc-144">Production</span></span>

<span data-ttu-id="0f2cc-145">Vous devez configurer l’environnement de production pour optimiser la sécurité, les performances et la robustesse de l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="0f2cc-146">Voici quelques paramètres courants qui diffèrent du développement :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="0f2cc-147">Mise en cache.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-147">Caching.</span></span>
* <span data-ttu-id="0f2cc-148">Les ressources côté client sont groupées, réduites et éventuellement servies à partir d’un CDN.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="0f2cc-149">Les Pages d’erreur de diagnostic sont désactivées.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="0f2cc-150">Les pages d’erreur conviviales sont activées.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="0f2cc-151">La journalisation et la surveillance de la production sont activées.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="0f2cc-152">Par exemple, [Applications Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0f2cc-153">Définir l’environnement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-153">Set the environment</span></span>

<span data-ttu-id="0f2cc-154">Il est souvent utile de définir un environnement spécifique pour les tests avec une variable d’environnement ou un réglage de plate-forme.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="0f2cc-155">Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="0f2cc-156">La méthode de configuration de l’environnement dépend du système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="0f2cc-157">Lorsque l’hôte est construit, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="0f2cc-158">L’environnement de l’application ne peut pas être modifié pendant que l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="0f2cc-159">Variable d’environnement ou réglage de plate-forme</span><span class="sxs-lookup"><span data-stu-id="0f2cc-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="0f2cc-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0f2cc-160">Azure App Service</span></span>

<span data-ttu-id="0f2cc-161">Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="0f2cc-162">Sélectionnez l’application dans le panneau **App Services**.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="0f2cc-163">Dans le groupe **Paramètres,** sélectionnez la lame **Configuration.**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="0f2cc-164">Dans l’onglet **Paramètres d’application,** sélectionnez **nouveau paramètre d’application**.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="0f2cc-165">Dans la fenêtre de réglage `ASPNETCORE_ENVIRONMENT` de **l’application Add/Edit,** prévoir le **nom**.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="0f2cc-166">Pour **la valeur**, fournir `Staging`l’environnement (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="0f2cc-167">Sélectionnez la case à cocher **de réglage de configuration de fente de déploiement** si vous souhaitez que le paramètre de l’environnement reste avec la fente actuelle lorsque les emplacements de déploiement sont échangés.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="0f2cc-168">Pour plus d’informations, consultez [les environnements de mise en scène d’Azure App Service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="0f2cc-169">Sélectionnez **OK** pour fermer la fenêtre **de réglage d’application Add/Edit.**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="0f2cc-170">Sélectionnez **Enregistrer** en haut de la lame **configuration.**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="0f2cc-171">Azure App Service redémarre automatiquement l’application après qu’un paramètre d’application (variable d’environnement) est ajouté, changé ou supprimé dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="0f2cc-172">Windows</span><span class="sxs-lookup"><span data-stu-id="0f2cc-172">Windows</span></span>

<span data-ttu-id="0f2cc-173">Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="0f2cc-174">**Commande rapide**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0f2cc-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="0f2cc-176">Ces commandes prennent effet uniquement pour la fenêtre active.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="0f2cc-177">Quand la fenêtre est fermée, le paramètre `ASPNETCORE_ENVIRONMENT` reprend la valeur de machine ou la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="0f2cc-178">Pour définir la valeur globalement dans Windows, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="0f2cc-179">Ouvrez le **Panneau de configuration** > **Système** > **Paramètres système avancés**, puis ajoutez ou modifiez la valeur `ASPNETCORE_ENVIRONMENT` :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriétés système avancées](environments/_static/systemsetting_environment.png)

  ![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="0f2cc-182">Ouvrez une invite de commandes d’administration, puis utilisez la commande `setx`, ou ouvrez une invite de commandes PowerShell d’administration et utilisez `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="0f2cc-183">**Commande rapide**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="0f2cc-184">Le commutateur `/M` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0f2cc-185">Si le commutateur `/M` n’est pas utilisé, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="0f2cc-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="0f2cc-187">La valeur d’option `Machine` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0f2cc-188">Si la valeur d’option est remplacée par `User`, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="0f2cc-189">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie globalement, elle prend effet pour `dotnet run` dans n’importe quelle fenêtre Commande ouverte une fois la valeur définie.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="0f2cc-190">**web.config (en anglais seulement)**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-190">**web.config**</span></span>

<span data-ttu-id="0f2cc-191">Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="0f2cc-192">**Fichier projet ou profil de publication**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-192">**Project file or publish profile**</span></span>

<span data-ttu-id="0f2cc-193">**Pour les déploiements Windows IIS :** Inclure `<EnvironmentName>` la propriété dans le [profil de publication (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier de projet.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="0f2cc-194">Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="0f2cc-195">**Par pool d’applications IIS**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="0f2cc-196">Pour définir la variables d’environnement `ASPNETCORE_ENVIRONMENT` pour une application qui s’exécute dans un pool d’applications isolé (prie en charge sur IIS 10.0 ou versions ultérieures), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="0f2cc-197">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie pour un pool d’applications, sa valeur remplace un paramètre au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0f2cc-198">Lors de l’hébergement d’une application dans IIS et de l’ajout ou du changement de la variable d’environnement `ASPNETCORE_ENVIRONMENT`, utilisez l’une des approches suivantes pour que la nouvelle valeur soit récupérée par des applications :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="0f2cc-199">Exécutez la commande `net stop was /y` suivie de `net start w3svc` à partir d’une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="0f2cc-200">Redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="0f2cc-201">macOS</span><span class="sxs-lookup"><span data-stu-id="0f2cc-201">macOS</span></span>

<span data-ttu-id="0f2cc-202">Vous pouvez définir l’environnement actuel pour macOS en ligne durant l’exécution de l’application :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="0f2cc-203">Vous pouvez également définir l’environnement avec `export` avant d’exécuter l’application :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0f2cc-204">Les variables d’environnement de niveau machine sont définies dans le fichier *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="0f2cc-205">Modifiez le fichier à l’aide d’un éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-205">Edit the file using any text editor.</span></span> <span data-ttu-id="0f2cc-206">Ajoutez l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="0f2cc-207">Linux</span><span class="sxs-lookup"><span data-stu-id="0f2cc-207">Linux</span></span>

<span data-ttu-id="0f2cc-208">Pour les versions Linux, exécutez la commande `export` à une invite de commandes pour les paramètres de variable basés sur la session, et le fichier *bash_profile* pour les paramètres d’environnement de niveau machine.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="0f2cc-209">Définir l’environnement dans le code</span><span class="sxs-lookup"><span data-stu-id="0f2cc-209">Set the environment in code</span></span>

<span data-ttu-id="0f2cc-210">Appelez <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="0f2cc-211">Consultez <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="0f2cc-212">Configuration par environnement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-212">Configuration by environment</span></span>

<span data-ttu-id="0f2cc-213">Pour charger la configuration par environnement, nous vous recommandons de disposer des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="0f2cc-214">*fichiers appsettings* *(appsettings. Environnement.json*).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="0f2cc-215">Consultez <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="0f2cc-216">Variables de l’environnement (définies sur chaque système où l’application est hébergée).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="0f2cc-217">Localisez <xref:fundamentals/host/generic-host#environmentname> et <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="0f2cc-218">Secret Manager (dans l’environnement de développement uniquement).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="0f2cc-219">Consultez <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="0f2cc-220">Classe et méthodes Startup en fonction de l’environnement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="0f2cc-221">Injecter IWebHostEnvironment dans Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="0f2cc-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="0f2cc-222">Injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup.Configure`dans .</span><span class="sxs-lookup"><span data-stu-id="0f2cc-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="0f2cc-223">Cette approche est utile lorsque l’application ne nécessite d’ajuster `Startup.Configure` que pour quelques environnements avec un minimum de différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="0f2cc-224">Injecter IWebHostEnvironment dans la classe Startup</span><span class="sxs-lookup"><span data-stu-id="0f2cc-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="0f2cc-225">Injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` le constructeur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="0f2cc-226">Cette approche est utile lorsque l’application nécessite de configurer `Startup` pour seulement quelques environnements avec un minimum de différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="0f2cc-227">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-227">In the following example:</span></span>

* <span data-ttu-id="0f2cc-228">L’environnement se `_env` tient sur le terrain.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="0f2cc-229">`_env`est utilisé `ConfigureServices` `Configure` dans et pour appliquer la configuration de démarrage basée sur l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
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
### <a name="startup-class-conventions"></a><span data-ttu-id="0f2cc-230">Conventions de la classe Startup</span><span class="sxs-lookup"><span data-stu-id="0f2cc-230">Startup class conventions</span></span>

<span data-ttu-id="0f2cc-231">Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="0f2cc-232">L’application peut `Startup` définir des classes séparées pour différents environnements (par exemple, `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="0f2cc-233">La `Startup` classe appropriée est sélectionnée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="0f2cc-234">La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="0f2cc-235">Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="0f2cc-236">Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="0f2cc-237">Pour implémenter des classes `Startup` basées sur l’environnement, créez une classe `Startup{EnvironmentName}` pour chaque environnement en cours d’utilisation et une classe `Startup` de base :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="0f2cc-238">Utilisez la surcharge [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) qui accepte un nom d’assembly :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="0f2cc-239">Conventions de la méthode Startup</span><span class="sxs-lookup"><span data-stu-id="0f2cc-239">Startup method conventions</span></span>

<span data-ttu-id="0f2cc-240">[Configurer](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigurerServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en `Configure<EnvironmentName>` charge `Configure<EnvironmentName>Services`les versions spécifiques à l’environnement du formulaire et .</span><span class="sxs-lookup"><span data-stu-id="0f2cc-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="0f2cc-241">Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="0f2cc-242">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0f2cc-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0f2cc-243">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0f2cc-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0f2cc-244">ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="0f2cc-245">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0f2cc-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="0f2cc-246">Environnements</span><span class="sxs-lookup"><span data-stu-id="0f2cc-246">Environments</span></span>

<span data-ttu-id="0f2cc-247">ASP.NET Core lit la variable d’environnement `ASPNETCORE_ENVIRONMENT` au démarrage de l’application et stocke la valeur dans [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="0f2cc-248">`ASPNETCORE_ENVIRONMENT`peut être définie à n’importe quelle valeur, mais trois valeurs sont fournies par le cadre :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="0f2cc-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (par défaut)</span><span class="sxs-lookup"><span data-stu-id="0f2cc-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="0f2cc-250">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-250">The preceding code:</span></span>

* <span data-ttu-id="0f2cc-251">Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="0f2cc-252">Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quand `ASPNETCORE_ENVIRONMENT` est définie sur l’une des valeurs :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="0f2cc-253">Le [Tag helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de la propriété `IHostingEnvironment.EnvironmentName` pour inclure ou exclure le balisage dans l’élément :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="0f2cc-254">Sur Windows et macOS, les valeurs et les variables d’environnement ne respectent pas la casse.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="0f2cc-255">Les valeurs et les variables d’environnement Linux **respectent la casse** par défaut.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="0f2cc-256">Développement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-256">Development</span></span>

<span data-ttu-id="0f2cc-257">L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées en production.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="0f2cc-258">Par exemple, les modèles ASP.NET Core activent la [page d’exceptions du développeur](xref:fundamentals/error-handling#developer-exception-page) dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="0f2cc-259">L’environnement de développement de l’ordinateur local peut être défini dans le fichier *Properties\launchSettings.json* du projet.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="0f2cc-260">Les valeurs d’environnement définies dans *launchSettings.json* remplacent les valeurs définies dans l’environnement système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="0f2cc-261">Le code JSON suivant montre trois profils à partir d’un fichier *launchSettings.json* :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="0f2cc-262">La propriété `applicationUrl` dans *launchSettings.json* peut spécifier une liste d’URL de serveur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="0f2cc-263">Utilisez un point-virgule entre les URL de la liste :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-263">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="0f2cc-264">Quand l’application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run), le premier profil avec `"commandName": "Project"` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="0f2cc-265">La valeur de `commandName` spécifie le serveur web à lancer.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="0f2cc-266">`commandName` peut avoir l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="0f2cc-267">`Project` (qui lance Kestrel)</span><span class="sxs-lookup"><span data-stu-id="0f2cc-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="0f2cc-268">Quand une application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run) :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="0f2cc-269">*launchSettings.json* est lu, s’il est disponible.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="0f2cc-270">Les paramètres `environmentVariables` dans *launchSettings.json* remplacent les variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="0f2cc-271">L’environnement d’hébergement s’affiche.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="0f2cc-272">La sortie suivante montre une application démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run) :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="0f2cc-273">L’onglet **Déboguer** des propriétés de projet Visual Studio fournit une interface graphique utilisateur qui permet de modifier le fichier *launchSettings.json* :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Propriétés de projet, définition des variables d’environnement](environments/_static/project-properties-debug.png)

<span data-ttu-id="0f2cc-275">Les modifications apportées aux profils de projet peuvent ne prendre effet qu’une fois le serveur web redémarré.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="0f2cc-276">Vous devez redémarrer Kestrel pour qu’il puisse détecter les modifications apportées à son environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="0f2cc-277">*launchSettings.json* ne doit pas stocker de secrets.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="0f2cc-278">Vous pouvez utiliser [l’outil Secret Manager](xref:security/app-secrets) afin de stocker des secrets pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="0f2cc-279">Quand vous utilisez [Visual Studio Code](https://code.visualstudio.com/), les variables d’environnement peuvent être définies dans le fichier *.vscode/launch.json*.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="0f2cc-280">L’exemple suivant définit `Development` comme environnement :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-280">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="0f2cc-281">Un fichier *.vscode/launch.json* dans le projet n’est pas lu au démarrage de l’application avec `dotnet run` de la même façon que *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="0f2cc-282">Lors du lancement d’une application en cours de développement qui n’a pas de fichier *launchSettings.json*, vous devez définir l’environnement avec une variable d’environnement ou un argument de ligne de commande pour la commande `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="0f2cc-283">Production</span><span class="sxs-lookup"><span data-stu-id="0f2cc-283">Production</span></span>

<span data-ttu-id="0f2cc-284">Vous devez configurer l’environnement de production pour optimiser la sécurité, les performances et la robustesse de l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="0f2cc-285">Voici quelques paramètres courants qui diffèrent du développement :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="0f2cc-286">Mise en cache.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-286">Caching.</span></span>
* <span data-ttu-id="0f2cc-287">Les ressources côté client sont groupées, réduites et éventuellement servies à partir d’un CDN.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="0f2cc-288">Les Pages d’erreur de diagnostic sont désactivées.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="0f2cc-289">Les pages d’erreur conviviales sont activées.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="0f2cc-290">La journalisation et la surveillance de la production sont activées.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="0f2cc-291">Par exemple, [Applications Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="0f2cc-292">Définir l’environnement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-292">Set the environment</span></span>

<span data-ttu-id="0f2cc-293">Il est souvent utile de définir un environnement spécifique pour les tests avec une variable d’environnement ou un réglage de plate-forme.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="0f2cc-294">Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="0f2cc-295">La méthode de configuration de l’environnement dépend du système d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="0f2cc-296">Lorsque l’hôte est construit, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="0f2cc-297">L’environnement de l’application ne peut pas être modifié pendant que l’application est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="0f2cc-298">Variable d’environnement ou réglage de plate-forme</span><span class="sxs-lookup"><span data-stu-id="0f2cc-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="0f2cc-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0f2cc-299">Azure App Service</span></span>

<span data-ttu-id="0f2cc-300">Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="0f2cc-301">Sélectionnez l’application dans le panneau **App Services**.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="0f2cc-302">Dans le groupe **Paramètres,** sélectionnez la lame **Configuration.**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="0f2cc-303">Dans l’onglet **Paramètres d’application,** sélectionnez **nouveau paramètre d’application**.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="0f2cc-304">Dans la fenêtre de réglage `ASPNETCORE_ENVIRONMENT` de **l’application Add/Edit,** prévoir le **nom**.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="0f2cc-305">Pour **la valeur**, fournir `Staging`l’environnement (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="0f2cc-306">Sélectionnez la case à cocher **de réglage de configuration de fente de déploiement** si vous souhaitez que le paramètre de l’environnement reste avec la fente actuelle lorsque les emplacements de déploiement sont échangés.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="0f2cc-307">Pour plus d’informations, consultez [les environnements de mise en scène d’Azure App Service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="0f2cc-308">Sélectionnez **OK** pour fermer la fenêtre **de réglage d’application Add/Edit.**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="0f2cc-309">Sélectionnez **Enregistrer** en haut de la lame **configuration.**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="0f2cc-310">Azure App Service redémarre automatiquement l’application après qu’un paramètre d’application (variable d’environnement) est ajouté, changé ou supprimé dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="0f2cc-311">Windows</span><span class="sxs-lookup"><span data-stu-id="0f2cc-311">Windows</span></span>

<span data-ttu-id="0f2cc-312">Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="0f2cc-313">**Commande rapide**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0f2cc-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="0f2cc-315">Ces commandes prennent effet uniquement pour la fenêtre active.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="0f2cc-316">Quand la fenêtre est fermée, le paramètre `ASPNETCORE_ENVIRONMENT` reprend la valeur de machine ou la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="0f2cc-317">Pour définir la valeur globalement dans Windows, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="0f2cc-318">Ouvrez le **Panneau de configuration** > **Système** > **Paramètres système avancés**, puis ajoutez ou modifiez la valeur `ASPNETCORE_ENVIRONMENT` :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Propriétés système avancées](environments/_static/systemsetting_environment.png)

  ![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="0f2cc-321">Ouvrez une invite de commandes d’administration, puis utilisez la commande `setx`, ou ouvrez une invite de commandes PowerShell d’administration et utilisez `[Environment]::SetEnvironmentVariable` :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="0f2cc-322">**Commande rapide**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="0f2cc-323">Le commutateur `/M` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0f2cc-324">Si le commutateur `/M` n’est pas utilisé, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="0f2cc-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="0f2cc-326">La valeur d’option `Machine` indique de définir la variable d’environnement au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="0f2cc-327">Si la valeur d’option est remplacée par `User`, la variable d’environnement est définie pour le compte d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="0f2cc-328">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie globalement, elle prend effet pour `dotnet run` dans n’importe quelle fenêtre Commande ouverte une fois la valeur définie.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="0f2cc-329">**web.config (en anglais seulement)**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-329">**web.config**</span></span>

<span data-ttu-id="0f2cc-330">Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="0f2cc-331">**Fichier projet ou profil de publication**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-331">**Project file or publish profile**</span></span>

<span data-ttu-id="0f2cc-332">**Pour les déploiements Windows IIS :** Inclure `<EnvironmentName>` la propriété dans le [profil de publication (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier de projet.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="0f2cc-333">Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="0f2cc-334">**Par pool d’applications IIS**</span><span class="sxs-lookup"><span data-stu-id="0f2cc-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="0f2cc-335">Pour définir la variables d’environnement `ASPNETCORE_ENVIRONMENT` pour une application qui s’exécute dans un pool d’applications isolé (prie en charge sur IIS 10.0 ou versions ultérieures), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="0f2cc-336">Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie pour un pool d’applications, sa valeur remplace un paramètre au niveau du système.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0f2cc-337">Lors de l’hébergement d’une application dans IIS et de l’ajout ou du changement de la variable d’environnement `ASPNETCORE_ENVIRONMENT`, utilisez l’une des approches suivantes pour que la nouvelle valeur soit récupérée par des applications :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="0f2cc-338">Exécutez la commande `net stop was /y` suivie de `net start w3svc` à partir d’une invite de commandes.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="0f2cc-339">Redémarrez le serveur.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="0f2cc-340">macOS</span><span class="sxs-lookup"><span data-stu-id="0f2cc-340">macOS</span></span>

<span data-ttu-id="0f2cc-341">Vous pouvez définir l’environnement actuel pour macOS en ligne durant l’exécution de l’application :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="0f2cc-342">Vous pouvez également définir l’environnement avec `export` avant d’exécuter l’application :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="0f2cc-343">Les variables d’environnement de niveau machine sont définies dans le fichier *.bashrc* ou *.bash_profile*.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="0f2cc-344">Modifiez le fichier à l’aide d’un éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-344">Edit the file using any text editor.</span></span> <span data-ttu-id="0f2cc-345">Ajoutez l’instruction suivante :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="0f2cc-346">Linux</span><span class="sxs-lookup"><span data-stu-id="0f2cc-346">Linux</span></span>

<span data-ttu-id="0f2cc-347">Pour les versions Linux, exécutez la commande `export` à une invite de commandes pour les paramètres de variable basés sur la session, et le fichier *bash_profile* pour les paramètres d’environnement de niveau machine.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="0f2cc-348">Définir l’environnement dans le code</span><span class="sxs-lookup"><span data-stu-id="0f2cc-348">Set the environment in code</span></span>

<span data-ttu-id="0f2cc-349">Appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> lors de la construction de l’hôte.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="0f2cc-350">Consultez <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="0f2cc-351">Configuration par environnement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-351">Configuration by environment</span></span>

<span data-ttu-id="0f2cc-352">Pour charger la configuration par environnement, nous vous recommandons de disposer des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="0f2cc-353">*fichiers appsettings* *(appsettings. Environnement.json*).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="0f2cc-354">Consultez <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="0f2cc-355">Variables de l’environnement (définies sur chaque système où l’application est hébergée).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="0f2cc-356">Localisez <xref:fundamentals/host/web-host#environment> et <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="0f2cc-357">Secret Manager (dans l’environnement de développement uniquement).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="0f2cc-358">Consultez <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="0f2cc-359">Classe et méthodes Startup en fonction de l’environnement</span><span class="sxs-lookup"><span data-stu-id="0f2cc-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="0f2cc-360">Injecter IHostingEnvironment dans Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="0f2cc-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="0f2cc-361">Injecter <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> `Startup.Configure`dans .</span><span class="sxs-lookup"><span data-stu-id="0f2cc-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="0f2cc-362">Cette approche est utile lorsque l’application ne nécessite de configurer `Startup.Configure` que pour quelques environnements avec un minimum de différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="0f2cc-363">Injecter IHostingEnvironment dans la classe Startup</span><span class="sxs-lookup"><span data-stu-id="0f2cc-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="0f2cc-364">Injecter <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> dans `Startup` le constructeur et affecter le service `Startup` à un champ pour une utilisation dans toute la classe.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="0f2cc-365">Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour seulement quelques environnements avec un minimum de différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="0f2cc-366">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-366">In the following example:</span></span>

* <span data-ttu-id="0f2cc-367">L’environnement se `_env` tient sur le terrain.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="0f2cc-368">`_env`est utilisé `ConfigureServices` `Configure` dans et pour appliquer la configuration de démarrage basée sur l’environnement de l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="0f2cc-369">Conventions de la classe Startup</span><span class="sxs-lookup"><span data-stu-id="0f2cc-369">Startup class conventions</span></span>

<span data-ttu-id="0f2cc-370">Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="0f2cc-371">L’application peut `Startup` définir des classes séparées pour différents environnements (par exemple, `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="0f2cc-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="0f2cc-372">La `Startup` classe appropriée est sélectionnée au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="0f2cc-373">La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="0f2cc-374">Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="0f2cc-375">Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="0f2cc-376">Pour implémenter des classes `Startup` basées sur l’environnement, créez une classe `Startup{EnvironmentName}` pour chaque environnement en cours d’utilisation et une classe `Startup` de base :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="0f2cc-377">Utilisez la surcharge [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) qui accepte un nom d’assembly :</span><span class="sxs-lookup"><span data-stu-id="0f2cc-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="0f2cc-378">Conventions de la méthode Startup</span><span class="sxs-lookup"><span data-stu-id="0f2cc-378">Startup method conventions</span></span>

<span data-ttu-id="0f2cc-379">[Configurer](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigurerServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en `Configure<EnvironmentName>` charge `Configure<EnvironmentName>Services`les versions spécifiques à l’environnement du formulaire et .</span><span class="sxs-lookup"><span data-stu-id="0f2cc-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="0f2cc-380">Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.</span><span class="sxs-lookup"><span data-stu-id="0f2cc-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="0f2cc-381">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0f2cc-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
