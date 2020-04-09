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
# <a name="use-multiple-environments-in-aspnet-core"></a>Utiliser plusieurs environnements dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Environnements

ASP.NET Core lit la variable `ASPNETCORE_ENVIRONMENT` de l’environnement au démarrage de l’application et stocke la valeur dans [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`peut être définie à n’importe quelle valeur, mais trois valeurs sont fournies par le cadre :

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production> (par défaut)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Le code précédent :

* Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.
* Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quand `ASPNETCORE_ENVIRONMENT` est définie sur l’une des valeurs :

  * `Staging`
  * `Production`
  * `Staging_2`

Le [Tag helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de la propriété `IHostingEnvironment.EnvironmentName` pour inclure ou exclure le balisage dans l’élément :

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Sur Windows et macOS, les valeurs et les variables d’environnement ne respectent pas la casse. Les valeurs et les variables d’environnement Linux **respectent la casse** par défaut.

### <a name="development"></a>Développement

L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées en production. Par exemple, les modèles ASP.NET Core activent la [page d’exceptions du développeur](xref:fundamentals/error-handling#developer-exception-page) dans l’environnement de développement.

L’environnement de développement de l’ordinateur local peut être défini dans le fichier *Properties\launchSettings.json* du projet. Les valeurs d’environnement définies dans *launchSettings.json* remplacent les valeurs définies dans l’environnement système.

Le code JSON suivant montre trois profils à partir d’un fichier *launchSettings.json* :

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
> La propriété `applicationUrl` dans *launchSettings.json* peut spécifier une liste d’URL de serveur. Utilisez un point-virgule entre les URL de la liste :
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

Quand l’application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run), le premier profil avec `"commandName": "Project"` est utilisé. La valeur de `commandName` spécifie le serveur web à lancer. `commandName` peut avoir l’une des valeurs suivantes :

* `IISExpress`
* `IIS`
* `Project` (qui lance Kestrel)

Quand une application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run) :

* *launchSettings.json* est lu, s’il est disponible. Les paramètres `environmentVariables` dans *launchSettings.json* remplacent les variables d’environnement.
* L’environnement d’hébergement s’affiche.

La sortie suivante montre une application démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run) :

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

L’onglet **Déboguer** des propriétés de projet Visual Studio fournit une interface graphique utilisateur qui permet de modifier le fichier *launchSettings.json* :

![Propriétés de projet, définition des variables d’environnement](environments/_static/project-properties-debug.png)

Les modifications apportées aux profils de projet peuvent ne prendre effet qu’une fois le serveur web redémarré. Vous devez redémarrer Kestrel pour qu’il puisse détecter les modifications apportées à son environnement.

> [!WARNING]
> *launchSettings.json* ne doit pas stocker de secrets. Vous pouvez utiliser [l’outil Secret Manager](xref:security/app-secrets) afin de stocker des secrets pour le développement local.

Quand vous utilisez [Visual Studio Code](https://code.visualstudio.com/), les variables d’environnement peuvent être définies dans le fichier *.vscode/launch.json*. L’exemple suivant définit `Development` comme environnement :

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

Un fichier *.vscode/launch.json* dans le projet n’est pas lu au démarrage de l’application avec `dotnet run` de la même façon que *Properties/launchSettings.json*. Lors du lancement d’une application en cours de développement qui n’a pas de fichier *launchSettings.json*, vous devez définir l’environnement avec une variable d’environnement ou un argument de ligne de commande pour la commande `dotnet run`.

### <a name="production"></a>Production

Vous devez configurer l’environnement de production pour optimiser la sécurité, les performances et la robustesse de l’application. Voici quelques paramètres courants qui diffèrent du développement :

* Mise en cache.
* Les ressources côté client sont groupées, réduites et éventuellement servies à partir d’un CDN.
* Les Pages d’erreur de diagnostic sont désactivées.
* Les pages d’erreur conviviales sont activées.
* La journalisation et la surveillance de la production sont activées. Par exemple, [Applications Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Définir l’environnement

Il est souvent utile de définir un environnement spécifique pour les tests avec une variable d’environnement ou un réglage de plate-forme. Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage. La méthode de configuration de l’environnement dépend du système d’exploitation.

Lorsque l’hôte est construit, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application. L’environnement de l’application ne peut pas être modifié pendant que l’application est en cours d’exécution.

### <a name="environment-variable-or-platform-setting"></a>Variable d’environnement ou réglage de plate-forme

#### <a name="azure-app-service"></a>Azure App Service

Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :

1. Sélectionnez l’application dans le panneau **App Services**.
1. Dans le groupe **Paramètres,** sélectionnez la lame **Configuration.**
1. Dans l’onglet **Paramètres d’application,** sélectionnez **nouveau paramètre d’application**.
1. Dans la fenêtre de réglage `ASPNETCORE_ENVIRONMENT` de **l’application Add/Edit,** prévoir le **nom**. Pour **la valeur**, fournir `Staging`l’environnement (par exemple, ).
1. Sélectionnez la case à cocher **de réglage de configuration de fente de déploiement** si vous souhaitez que le paramètre de l’environnement reste avec la fente actuelle lorsque les emplacements de déploiement sont échangés. Pour plus d’informations, consultez [les environnements de mise en scène d’Azure App Service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.
1. Sélectionnez **OK** pour fermer la fenêtre **de réglage d’application Add/Edit.**
1. Sélectionnez **Enregistrer** en haut de la lame **configuration.**

Azure App Service redémarre automatiquement l’application après qu’un paramètre d’application (variable d’environnement) est ajouté, changé ou supprimé dans le portail Azure.

#### <a name="windows"></a>Windows

Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :

**Commande rapide**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Ces commandes prennent effet uniquement pour la fenêtre active. Quand la fenêtre est fermée, le paramètre `ASPNETCORE_ENVIRONMENT` reprend la valeur de machine ou la valeur par défaut.

Pour définir la valeur globalement dans Windows, utilisez l’une des approches suivantes :

* Ouvrez le **Panneau de configuration** > **Système** > **Paramètres système avancés**, puis ajoutez ou modifiez la valeur `ASPNETCORE_ENVIRONMENT` :

  ![Propriétés système avancées](environments/_static/systemsetting_environment.png)

  ![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Ouvrez une invite de commandes d’administration, puis utilisez la commande `setx`, ou ouvrez une invite de commandes PowerShell d’administration et utilisez `[Environment]::SetEnvironmentVariable` :

  **Commande rapide**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Le commutateur `/M` indique de définir la variable d’environnement au niveau du système. Si le commutateur `/M` n’est pas utilisé, la variable d’environnement est définie pour le compte d’utilisateur.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  La valeur d’option `Machine` indique de définir la variable d’environnement au niveau du système. Si la valeur d’option est remplacée par `User`, la variable d’environnement est définie pour le compte d’utilisateur.

Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie globalement, elle prend effet pour `dotnet run` dans n’importe quelle fenêtre Commande ouverte une fois la valeur définie.

**web.config (en anglais seulement)**

Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Fichier projet ou profil de publication**

**Pour les déploiements Windows IIS :** Inclure `<EnvironmentName>` la propriété dans le [profil de publication (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier de projet. Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Par pool d’applications IIS**

Pour définir la variables d’environnement `ASPNETCORE_ENVIRONMENT` pour une application qui s’exécute dans un pool d’applications isolé (prie en charge sur IIS 10.0 ou versions ultérieures), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie pour un pool d’applications, sa valeur remplace un paramètre au niveau du système.

> [!IMPORTANT]
> Lors de l’hébergement d’une application dans IIS et de l’ajout ou du changement de la variable d’environnement `ASPNETCORE_ENVIRONMENT`, utilisez l’une des approches suivantes pour que la nouvelle valeur soit récupérée par des applications :
>
> * Exécutez la commande `net stop was /y` suivie de `net start w3svc` à partir d’une invite de commandes.
> * Redémarrez le serveur.

#### <a name="macos"></a>macOS

Vous pouvez définir l’environnement actuel pour macOS en ligne durant l’exécution de l’application :

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Vous pouvez également définir l’environnement avec `export` avant d’exécuter l’application :

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Les variables d’environnement de niveau machine sont définies dans le fichier *.bashrc* ou *.bash_profile*. Modifiez le fichier à l’aide d’un éditeur de texte. Ajoutez l’instruction suivante :

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Pour les versions Linux, exécutez la commande `export` à une invite de commandes pour les paramètres de variable basés sur la session, et le fichier *bash_profile* pour les paramètres d’environnement de niveau machine.

### <a name="set-the-environment-in-code"></a>Définir l’environnement dans le code

Appelez <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> lors de la construction de l’hôte. Consultez <xref:fundamentals/host/generic-host#environmentname>.


### <a name="configuration-by-environment"></a>Configuration par environnement

Pour charger la configuration par environnement, nous vous recommandons de disposer des éléments suivants :

* *fichiers appsettings* *(appsettings. Environnement.json*). Consultez <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variables de l’environnement (définies sur chaque système où l’application est hébergée). Localisez <xref:fundamentals/host/generic-host#environmentname> et <xref:security/app-secrets#environment-variables>.
* Secret Manager (dans l’environnement de développement uniquement). Consultez <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Classe et méthodes Startup en fonction de l’environnement

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>Injecter IWebHostEnvironment dans Startup.Configure

Injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup.Configure`dans . Cette approche est utile lorsque l’application ne nécessite d’ajuster `Startup.Configure` que pour quelques environnements avec un minimum de différences de code par environnement.

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Injecter IWebHostEnvironment dans la classe Startup

Injecter <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup` le constructeur. Cette approche est utile lorsque l’application nécessite de configurer `Startup` pour seulement quelques environnements avec un minimum de différences de code par environnement.

Dans l’exemple suivant :

* L’environnement se `_env` tient sur le terrain.
* `_env`est utilisé `ConfigureServices` `Configure` dans et pour appliquer la configuration de démarrage basée sur l’environnement de l’application.

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
### <a name="startup-class-conventions"></a>Conventions de la classe Startup

Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application. L’application peut `Startup` définir des classes séparées pour différents environnements (par exemple, `StartupDevelopment`). La `Startup` classe appropriée est sélectionnée au moment de l’exécution. La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire. Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée. Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.

Pour implémenter des classes `Startup` basées sur l’environnement, créez une classe `Startup{EnvironmentName}` pour chaque environnement en cours d’utilisation et une classe `Startup` de base :

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

Utilisez la surcharge [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) qui accepte un nom d’assembly :

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

### <a name="startup-method-conventions"></a>Conventions de la méthode Startup

[Configurer](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigurerServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en `Configure<EnvironmentName>` charge `Configure<EnvironmentName>Services`les versions spécifiques à l’environnement du formulaire et . Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Environnements

ASP.NET Core lit la variable d’environnement `ASPNETCORE_ENVIRONMENT` au démarrage de l’application et stocke la valeur dans [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`peut être définie à n’importe quelle valeur, mais trois valeurs sont fournies par le cadre :

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (par défaut)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Le code précédent :

* Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.
* Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quand `ASPNETCORE_ENVIRONMENT` est définie sur l’une des valeurs :

  * `Staging`
  * `Production`
  * `Staging_2`

Le [Tag helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de la propriété `IHostingEnvironment.EnvironmentName` pour inclure ou exclure le balisage dans l’élément :

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Sur Windows et macOS, les valeurs et les variables d’environnement ne respectent pas la casse. Les valeurs et les variables d’environnement Linux **respectent la casse** par défaut.

### <a name="development"></a>Développement

L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées en production. Par exemple, les modèles ASP.NET Core activent la [page d’exceptions du développeur](xref:fundamentals/error-handling#developer-exception-page) dans l’environnement de développement.

L’environnement de développement de l’ordinateur local peut être défini dans le fichier *Properties\launchSettings.json* du projet. Les valeurs d’environnement définies dans *launchSettings.json* remplacent les valeurs définies dans l’environnement système.

Le code JSON suivant montre trois profils à partir d’un fichier *launchSettings.json* :

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
> La propriété `applicationUrl` dans *launchSettings.json* peut spécifier une liste d’URL de serveur. Utilisez un point-virgule entre les URL de la liste :
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

Quand l’application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run), le premier profil avec `"commandName": "Project"` est utilisé. La valeur de `commandName` spécifie le serveur web à lancer. `commandName` peut avoir l’une des valeurs suivantes :

* `IISExpress`
* `IIS`
* `Project` (qui lance Kestrel)

Quand une application est lancée avec [dotnet run](/dotnet/core/tools/dotnet-run) :

* *launchSettings.json* est lu, s’il est disponible. Les paramètres `environmentVariables` dans *launchSettings.json* remplacent les variables d’environnement.
* L’environnement d’hébergement s’affiche.

La sortie suivante montre une application démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run) :

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

L’onglet **Déboguer** des propriétés de projet Visual Studio fournit une interface graphique utilisateur qui permet de modifier le fichier *launchSettings.json* :

![Propriétés de projet, définition des variables d’environnement](environments/_static/project-properties-debug.png)

Les modifications apportées aux profils de projet peuvent ne prendre effet qu’une fois le serveur web redémarré. Vous devez redémarrer Kestrel pour qu’il puisse détecter les modifications apportées à son environnement.

> [!WARNING]
> *launchSettings.json* ne doit pas stocker de secrets. Vous pouvez utiliser [l’outil Secret Manager](xref:security/app-secrets) afin de stocker des secrets pour le développement local.

Quand vous utilisez [Visual Studio Code](https://code.visualstudio.com/), les variables d’environnement peuvent être définies dans le fichier *.vscode/launch.json*. L’exemple suivant définit `Development` comme environnement :

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

Un fichier *.vscode/launch.json* dans le projet n’est pas lu au démarrage de l’application avec `dotnet run` de la même façon que *Properties/launchSettings.json*. Lors du lancement d’une application en cours de développement qui n’a pas de fichier *launchSettings.json*, vous devez définir l’environnement avec une variable d’environnement ou un argument de ligne de commande pour la commande `dotnet run`.

### <a name="production"></a>Production

Vous devez configurer l’environnement de production pour optimiser la sécurité, les performances et la robustesse de l’application. Voici quelques paramètres courants qui diffèrent du développement :

* Mise en cache.
* Les ressources côté client sont groupées, réduites et éventuellement servies à partir d’un CDN.
* Les Pages d’erreur de diagnostic sont désactivées.
* Les pages d’erreur conviviales sont activées.
* La journalisation et la surveillance de la production sont activées. Par exemple, [Applications Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Définir l’environnement

Il est souvent utile de définir un environnement spécifique pour les tests avec une variable d’environnement ou un réglage de plate-forme. Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage. La méthode de configuration de l’environnement dépend du système d’exploitation.

Lorsque l’hôte est construit, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application. L’environnement de l’application ne peut pas être modifié pendant que l’application est en cours d’exécution.

### <a name="environment-variable-or-platform-setting"></a>Variable d’environnement ou réglage de plate-forme

#### <a name="azure-app-service"></a>Azure App Service

Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :

1. Sélectionnez l’application dans le panneau **App Services**.
1. Dans le groupe **Paramètres,** sélectionnez la lame **Configuration.**
1. Dans l’onglet **Paramètres d’application,** sélectionnez **nouveau paramètre d’application**.
1. Dans la fenêtre de réglage `ASPNETCORE_ENVIRONMENT` de **l’application Add/Edit,** prévoir le **nom**. Pour **la valeur**, fournir `Staging`l’environnement (par exemple, ).
1. Sélectionnez la case à cocher **de réglage de configuration de fente de déploiement** si vous souhaitez que le paramètre de l’environnement reste avec la fente actuelle lorsque les emplacements de déploiement sont échangés. Pour plus d’informations, consultez [les environnements de mise en scène d’Azure App Service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.
1. Sélectionnez **OK** pour fermer la fenêtre **de réglage d’application Add/Edit.**
1. Sélectionnez **Enregistrer** en haut de la lame **configuration.**

Azure App Service redémarre automatiquement l’application après qu’un paramètre d’application (variable d’environnement) est ajouté, changé ou supprimé dans le portail Azure.

#### <a name="windows"></a>Windows

Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :

**Commande rapide**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Ces commandes prennent effet uniquement pour la fenêtre active. Quand la fenêtre est fermée, le paramètre `ASPNETCORE_ENVIRONMENT` reprend la valeur de machine ou la valeur par défaut.

Pour définir la valeur globalement dans Windows, utilisez l’une des approches suivantes :

* Ouvrez le **Panneau de configuration** > **Système** > **Paramètres système avancés**, puis ajoutez ou modifiez la valeur `ASPNETCORE_ENVIRONMENT` :

  ![Propriétés système avancées](environments/_static/systemsetting_environment.png)

  ![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Ouvrez une invite de commandes d’administration, puis utilisez la commande `setx`, ou ouvrez une invite de commandes PowerShell d’administration et utilisez `[Environment]::SetEnvironmentVariable` :

  **Commande rapide**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Le commutateur `/M` indique de définir la variable d’environnement au niveau du système. Si le commutateur `/M` n’est pas utilisé, la variable d’environnement est définie pour le compte d’utilisateur.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  La valeur d’option `Machine` indique de définir la variable d’environnement au niveau du système. Si la valeur d’option est remplacée par `User`, la variable d’environnement est définie pour le compte d’utilisateur.

Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie globalement, elle prend effet pour `dotnet run` dans n’importe quelle fenêtre Commande ouverte une fois la valeur définie.

**web.config (en anglais seulement)**

Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Fichier projet ou profil de publication**

**Pour les déploiements Windows IIS :** Inclure `<EnvironmentName>` la propriété dans le [profil de publication (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier de projet. Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Par pool d’applications IIS**

Pour définir la variables d’environnement `ASPNETCORE_ENVIRONMENT` pour une application qui s’exécute dans un pool d’applications isolé (prie en charge sur IIS 10.0 ou versions ultérieures), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie pour un pool d’applications, sa valeur remplace un paramètre au niveau du système.

> [!IMPORTANT]
> Lors de l’hébergement d’une application dans IIS et de l’ajout ou du changement de la variable d’environnement `ASPNETCORE_ENVIRONMENT`, utilisez l’une des approches suivantes pour que la nouvelle valeur soit récupérée par des applications :
>
> * Exécutez la commande `net stop was /y` suivie de `net start w3svc` à partir d’une invite de commandes.
> * Redémarrez le serveur.

#### <a name="macos"></a>macOS

Vous pouvez définir l’environnement actuel pour macOS en ligne durant l’exécution de l’application :

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Vous pouvez également définir l’environnement avec `export` avant d’exécuter l’application :

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Les variables d’environnement de niveau machine sont définies dans le fichier *.bashrc* ou *.bash_profile*. Modifiez le fichier à l’aide d’un éditeur de texte. Ajoutez l’instruction suivante :

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Pour les versions Linux, exécutez la commande `export` à une invite de commandes pour les paramètres de variable basés sur la session, et le fichier *bash_profile* pour les paramètres d’environnement de niveau machine.

### <a name="set-the-environment-in-code"></a>Définir l’environnement dans le code

Appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> lors de la construction de l’hôte. Consultez <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Configuration par environnement

Pour charger la configuration par environnement, nous vous recommandons de disposer des éléments suivants :

* *fichiers appsettings* *(appsettings. Environnement.json*). Consultez <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variables de l’environnement (définies sur chaque système où l’application est hébergée). Localisez <xref:fundamentals/host/web-host#environment> et <xref:security/app-secrets#environment-variables>.
* Secret Manager (dans l’environnement de développement uniquement). Consultez <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Classe et méthodes Startup en fonction de l’environnement

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Injecter IHostingEnvironment dans Startup.Configure

Injecter <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> `Startup.Configure`dans . Cette approche est utile lorsque l’application ne nécessite de configurer `Startup.Configure` que pour quelques environnements avec un minimum de différences de code par environnement.

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Injecter IHostingEnvironment dans la classe Startup

Injecter <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> dans `Startup` le constructeur et affecter le service `Startup` à un champ pour une utilisation dans toute la classe. Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour seulement quelques environnements avec un minimum de différences de code par environnement.

Dans l’exemple suivant :

* L’environnement se `_env` tient sur le terrain.
* `_env`est utilisé `ConfigureServices` `Configure` dans et pour appliquer la configuration de démarrage basée sur l’environnement de l’application.

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

### <a name="startup-class-conventions"></a>Conventions de la classe Startup

Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application. L’application peut `Startup` définir des classes séparées pour différents environnements (par exemple, `StartupDevelopment`). La `Startup` classe appropriée est sélectionnée au moment de l’exécution. La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire. Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée. Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.

Pour implémenter des classes `Startup` basées sur l’environnement, créez une classe `Startup{EnvironmentName}` pour chaque environnement en cours d’utilisation et une classe `Startup` de base :

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

Utilisez la surcharge [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) qui accepte un nom d’assembly :

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

### <a name="startup-method-conventions"></a>Conventions de la méthode Startup

[Configurer](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigurerServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en `Configure<EnvironmentName>` charge `Configure<EnvironmentName>Services`les versions spécifiques à l’environnement du formulaire et . Cette approche est utile lorsque l’application nécessite de configurer le démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
