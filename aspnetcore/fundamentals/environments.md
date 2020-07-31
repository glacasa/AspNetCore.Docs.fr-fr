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
# <a name="use-multiple-environments-in-aspnet-core"></a>Utiliser plusieurs environnements dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Kirk Larkin](https://twitter.com/serpent5)

ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Environnements

Pour déterminer l’environnement d’exécution, ASP.NET Core lit les variables d’environnement suivantes :

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. `ASPNETCORE_ENVIRONMENT`Lorsque <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> est appelé. L’appel des modèles d’application Web par défaut ASP.NET Core `ConfigureWebHostDefaults` . La `ASPNETCORE_ENVIRONMENT` valeur se substitue à `DOTNET_ENVIRONMENT` .

`IHostEnvironment.EnvironmentName`peut être défini sur n’importe quelle valeur, mais les valeurs suivantes sont fournies par le Framework :

* <xref:Microsoft.Extensions.Hosting.Environments.Development>: L' [launchSettings.jssur](#lsj) les jeux de fichiers sur `ASPNETCORE_ENVIRONMENT` `Development` sur l’ordinateur local.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>: Valeur par défaut si `DOTNET_ENVIRONMENT` et `ASPNETCORE_ENVIRONMENT` n’ont pas été définis.

Le code suivant :

* Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.
* Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) lorsque la valeur de `ASPNETCORE_ENVIRONMENT` est définie sur `Staging` , `Production` ou `Staging_2` .
* Injecte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans `Startup.Configure` . Cette approche est utile lorsque l’application doit uniquement être ajustée `Startup.Configure` pour quelques environnements avec des différences de code minimales par environnement.
* Est semblable au code généré par les modèles ASP.NET Core.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

Le [tag Helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de [IHostEnvironment. EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) pour inclure ou exclure le balisage dans l’élément :

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

La [page about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) de l' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) comprend le balisage précédent et affiche la valeur de `IWebHostEnvironment.EnvironmentName` .

Sur Windows et macOS, les variables d’environnement et les valeurs ne respectent pas la casse. Les valeurs et les variables d’environnement Linux respectent la **casse** par défaut.

### <a name="create-environmentssample"></a>Créer EnvironmentsSample

L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) utilisé dans ce document est basé sur un Razor projet de pages nommé *EnvironmentsSample*.

Le code suivant crée et exécute une application Web nommée *EnvironmentsSample*:

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

Lorsque l’application s’exécute, elle affiche une partie des résultats suivants :

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

### <a name="development-and-launchsettingsjson"></a>Développement et launchSettings.jssur

L’environnement de développement peut activer des fonctionnalités qui ne doivent pas être exposées en production. Par exemple, les modèles ASP.NET Core activent la [page d’exceptions du développeur](xref:fundamentals/error-handling#developer-exception-page) dans l’environnement de développement.

L’environnement de développement de l’ordinateur local peut être défini dans le fichier *Properties\launchSettings.json* du projet. Les valeurs d’environnement définies dans *launchSettings.json* remplacent les valeurs définies dans l’environnement système.

Le *launchSettings.jssur* le fichier :

* Est utilisé uniquement sur l’ordinateur de développement local.
* N’est pas déployé.
* contient des paramètres de profil.

Le code JSON suivant montre l' *launchSettings.jssur* un fichier pour un ASP.net Core Web projeté nommé *EnvironmentsSample* créé avec Visual Studio ou `dotnet new` :

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

Le balisage précédent contient deux profils :

* `IIS Express`: Profil par défaut utilisé lors du lancement de l’application à partir de Visual Studio. La `"commandName"` clé a la valeur `"IISExpress"` , par conséquent, [iisexpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) est le serveur Web. Vous pouvez définir le profil de lancement sur le projet ou tout autre profil inclus. Par exemple, dans l’image ci-dessous, sélectionnez le nom du projet pour lancer le [serveur Web Kestrel](xref:fundamentals/servers/kestrel).

  ![IIS Express lancer dans le menu](environments/_static/iisx2.png)
* `EnvironmentsSample`: Le nom du profil est le nom du projet. Ce profil est utilisé par défaut lors du lancement de l’application avec `dotnet run` .  La `"commandName"` clé a la valeur `"Project"` , par conséquent, le [serveur Web Kestrel](xref:fundamentals/servers/kestrel) est lancé.

La valeur de `commandName` peut spécifier le serveur Web à lancer. `commandName` peut avoir l’une des valeurs suivantes :

* `IISExpress`: Lance IIS Express.
* `IIS`: Aucun serveur Web n’a été lancé. Les services Internet (IIS) sont censés être disponibles.
* `Project`: Lance Kestrel.

L’onglet **Déboguer** des propriétés de projet Visual Studio fournit une interface utilisateur graphique pour modifier les *launchSettings.jssur* le fichier. Les modifications apportées aux profils de projet peuvent ne prendre effet qu’une fois le serveur web redémarré. Vous devez redémarrer Kestrel pour qu’il puisse détecter les modifications apportées à son environnement.

![Propriétés de projet, définition des variables d’environnement](environments/_static/project-properties-debug.png)

Le *launchSettings.jssuivant sur* le fichier contient plusieurs profils :

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

Les profils peuvent être sélectionnés :

* À partir de l’interface utilisateur de Visual Studio.
* À l’aide [`dotnet run`](/dotnet/core/tools/dotnet-run) de la commande dans une interface de commande avec l' `--launch-profile` option définie sur le nom du profil. *Cette approche ne prend en charge que les profils Kestrel.*

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> *launchSettings.json* ne doit pas stocker de secrets. Vous pouvez utiliser [l’outil Secret Manager](xref:security/app-secrets) afin de stocker des secrets pour le développement local.

Quand vous utilisez [Visual Studio Code](https://code.visualstudio.com/), les variables d’environnement peuvent être définies dans le fichier *.vscode/launch.json*. L’exemple suivant définit plusieurs [valeurs de configuration d’hôte variables d’environnement](xref:fundamentals/host/web-host#host-configuration-values):

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

Le fichier *. vscode/launch.js* n’est utilisé que par Visual Studio code.

### <a name="production"></a>Production

L’environnement de production doit être configuré pour optimiser la sécurité, les [performances](xref:performance/performance-best-practices)et la robustesse des applications. Voici quelques paramètres courants qui diffèrent du développement :

* [Mise en cache](xref:performance/caching/memory).
* Les ressources côté client sont groupées, réduites et éventuellement servies à partir d’un CDN.
* Les Pages d’erreur de diagnostic sont désactivées.
* Les pages d’erreur conviviales sont activées.
* [Journalisation](xref:fundamentals/logging/index) et surveillance de la production activée. Par exemple, à l’aide de [application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Définir l’environnement

Il est souvent utile de définir un environnement spécifique pour le test avec une variable d’environnement ou un paramètre de plateforme. Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage. La méthode de configuration de l’environnement dépend du système d’exploitation.

Lorsque l’hôte est généré, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application. L’environnement de l’application ne peut pas être modifié tant que l’application est en cours d’exécution.

La [page about](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) de l' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) affiche la valeur de `IWebHostEnvironment.EnvironmentName` .

### <a name="azure-app-service"></a>Azure App Service

Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :

1. Sélectionnez l’application dans le panneau **App Services**.
1. Dans le groupe **paramètres** , sélectionnez le panneau **configuration** .
1. Dans l’onglet Paramètres de l' **application** , sélectionnez **nouveau paramètre d’application**.
1. Dans la fenêtre **Ajouter/modifier un paramètre d’application** , indiquez `ASPNETCORE_ENVIRONMENT` le **nom**. Pour **valeur**, fournissez l’environnement (par exemple, `Staging` ).
1. Activez la case à cocher **paramètre d’emplacement de déploiement** si vous souhaitez que le paramètre d’environnement reste avec l’emplacement actuel lorsque les emplacements de déploiement sont permutés. Pour plus d’informations, consultez [configurer des environnements intermédiaires dans Azure App service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.
1. Sélectionnez **OK** pour fermer la fenêtre **Ajouter/modifier un paramètre d’application** .
1. Sélectionnez **Enregistrer** en haut du panneau de **configuration** .

Azure App Service redémarre automatiquement l’application après l’ajout, la modification ou la suppression d’un paramètre d’application dans le Portail Azure.

### <a name="windows"></a>Windows

Valeurs d’environnement danslaunchSettings.jsen cas *de remplacement des* valeurs définies dans l’environnement système.

Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :

**Invite de commandes**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

La commande précédente définit `ASPNETCORE_ENVIRONMENT` uniquement pour les processus lancés à partir de cette fenêtre de commande.

Pour définir la valeur globalement dans Windows, utilisez l’une des approches suivantes :

* Ouvrez le **Panneau de configuration** > **Système** > **Paramètres système avancés**, puis ajoutez ou modifiez la valeur `ASPNETCORE_ENVIRONMENT` :

  ![Propriétés système avancées](environments/_static/systemsetting_environment.png)

  ![Variable d’environnement ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Ouvrez une invite de commandes d’administration, puis utilisez la commande `setx`, ou ouvrez une invite de commandes PowerShell d’administration et utilisez `[Environment]::SetEnvironmentVariable` :

  **Invite de commandes**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  Le commutateur `/M` indique de définir la variable d’environnement au niveau du système. Si le commutateur `/M` n’est pas utilisé, la variable d’environnement est définie pour le compte d’utilisateur.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  La valeur d’option `Machine` indique de définir la variable d’environnement au niveau du système. Si la valeur d’option est remplacée par `User`, la variable d’environnement est définie pour le compte d’utilisateur.

Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie globalement, elle prend effet pour `dotnet run` dans n’importe quelle fenêtre Commande ouverte une fois la valeur définie. Valeurs d’environnement danslaunchSettings.jsen cas *de remplacement des* valeurs définies dans l’environnement système.

**web.config**

Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Fichier projet ou profil de publication**

**Pour les déploiements de Windows IIS :** Incluez la `<EnvironmentName>` propriété dans le [profil de publication (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet. Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Par pool d’applications IIS**

Pour définir la variables d’environnement `ASPNETCORE_ENVIRONMENT` pour une application qui s’exécute dans un pool d’applications isolé (prie en charge sur IIS 10.0 ou versions ultérieures), consultez la section *Commande AppCmd.exe* de la rubrique [Variables d’environnement &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Quand la variable d’environnement `ASPNETCORE_ENVIRONMENT` est définie pour un pool d’applications, sa valeur remplace un paramètre au niveau du système.

Lors de l’hébergement d’une application dans IIS et de l’ajout ou du changement de la variable d’environnement `ASPNETCORE_ENVIRONMENT`, utilisez l’une des approches suivantes pour que la nouvelle valeur soit récupérée par des applications :

* Exécutez la commande `net stop was /y` suivie de `net start w3svc` à partir d’une invite de commandes.
* Redémarrez le serveur.

#### <a name="macos"></a>macOS

Vous pouvez définir l’environnement actuel pour macOS en ligne durant l’exécution de l’application :

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

Vous pouvez également définir l’environnement avec `export` avant d’exécuter l’application :

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

Les variables d’environnement de niveau machine sont définies dans le fichier *.bashrc* ou *.bash_profile*. Modifiez le fichier à l’aide d’un éditeur de texte. Ajoutez l’instruction suivante :

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

Pour les distributions Linux, utilisez la `export` commande à partir d’une invite de commandes pour les paramètres de variable basés sur session et *bash_profile* fichier pour les paramètres d’environnement au niveau de l’ordinateur.

### <a name="set-the-environment-in-code"></a>Définir l’environnement dans le code

Appelez <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> lors de la génération de l’hôte. Consultez <xref:fundamentals/host/generic-host#environmentname>.

### <a name="configuration-by-environment"></a>Configuration par environnement

Pour charger la configuration par environnement, consultez <xref:fundamentals/configuration/index#json-configuration-provider> .

## <a name="environment-based-startup-class-and-methods"></a>Classe et méthodes Startup en fonction de l’environnement

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Injecter IWebHostEnvironment dans la classe Startup

Injectez <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> dans le `Startup` constructeur. Cette approche est utile lorsque l’application ne nécessite la configuration `Startup` que pour quelques environnements avec des différences de code minimales par environnement.

Dans l’exemple suivant :

* L’environnement est conservé dans le `_env` champ.
* `_env`est utilisé dans `ConfigureServices` et `Configure` pour appliquer la configuration de démarrage en fonction de l’environnement de l’application.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Conventions de la classe Startup

Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application. L’application peut définir plusieurs `Startup` classes pour différents environnements. La `Startup` classe appropriée est sélectionnée au moment de l’exécution. La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire. Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée. Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement. Les applications classiques n’ont pas besoin de cette approche.

Pour implémenter des classes basées sur l’environnement `Startup` , créez `Startup{EnvironmentName}` des classes et une classe de secours `Startup` :

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

Utilisez la surcharge [UseStartup (IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) qui accepte un nom d’assembly :

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Conventions de la méthode Startup

[Configurez](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en charge des versions spécifiques à l’environnement du formulaire `Configure<EnvironmentName>` et `Configure<EnvironmentName>Services` . Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement :

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core configure le comportement de l’application en fonction de l’environnement d’exécution à l’aide d’une variable d’environnement.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Environnements

ASP.NET Core lit la variable d’environnement `ASPNETCORE_ENVIRONMENT` au démarrage de l’application et stocke la valeur dans [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). `ASPNETCORE_ENVIRONMENT`peut être défini sur n’importe quelle valeur, mais trois valeurs sont fournies par l’infrastructure :

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (par défaut)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Le code précédent :

* Appelle [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) quand `ASPNETCORE_ENVIRONMENT` a la valeur `Development`.
* Appelle [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) quand `ASPNETCORE_ENVIRONMENT` est définie sur l’une des valeurs :

  * `Staging`
  * `Production`
  * `Staging_2`

Le [Tag helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) utilise la valeur de la propriété `IHostingEnvironment.EnvironmentName` pour inclure ou exclure le balisage dans l’élément :

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Sur Windows et macOS, les variables d’environnement et les valeurs ne respectent pas la casse. Les valeurs et les variables d’environnement Linux respectent la casse par défaut.

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
* La journalisation et la surveillance de la production sont activées. Par exemple, [application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Définir l’environnement

Il est souvent utile de définir un environnement spécifique pour le test avec une variable d’environnement ou un paramètre de plateforme. Si l’environnement n’est pas défini, il prend par défaut la valeur `Production`, ce qui désactive la plupart des fonctionnalités de débogage. La méthode de configuration de l’environnement dépend du système d’exploitation.

Lorsque l’hôte est généré, le dernier paramètre d’environnement lu par l’application détermine l’environnement de l’application. L’environnement de l’application ne peut pas être modifié tant que l’application est en cours d’exécution.

### <a name="environment-variable-or-platform-setting"></a>Variable d’environnement ou paramètre de plateforme

#### <a name="azure-app-service"></a>Azure App Service

Pour définir l’environnement dans [Azure App Service](https://azure.microsoft.com/services/app-service/), effectuez les étapes suivantes :

1. Sélectionnez l’application dans le panneau **App Services**.
1. Dans le groupe **paramètres** , sélectionnez le panneau **configuration** .
1. Dans l’onglet Paramètres de l' **application** , sélectionnez **nouveau paramètre d’application**.
1. Dans la fenêtre **Ajouter/modifier un paramètre d’application** , indiquez `ASPNETCORE_ENVIRONMENT` le **nom**. Pour **valeur**, fournissez l’environnement (par exemple, `Staging` ).
1. Activez la case à cocher **paramètre d’emplacement de déploiement** si vous souhaitez que le paramètre d’environnement reste avec l’emplacement actuel lorsque les emplacements de déploiement sont permutés. Pour plus d’informations, consultez [configurer des environnements intermédiaires dans Azure App service](/azure/app-service/web-sites-staged-publishing) dans la documentation Azure.
1. Sélectionnez **OK** pour fermer la fenêtre **Ajouter/modifier un paramètre d’application** .
1. Sélectionnez **Enregistrer** en haut du panneau de **configuration** .

Azure App Service redémarre automatiquement l’application après qu’un paramètre d’application (variable d’environnement) est ajouté, changé ou supprimé dans le portail Azure.

#### <a name="windows"></a>Windows

Pour définir `ASPNETCORE_ENVIRONMENT` pour la session actuelle quand l’application est démarrée avec [dotnet run](/dotnet/core/tools/dotnet-run), les commandes suivantes sont utilisées :

**Invite de commandes**

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

  **Invite de commandes**

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

**web.config**

Pour définir la variable d’environnement `ASPNETCORE_ENVIRONMENT` avec *web.config*, consultez la section *Définition des variables d’environnement* à l’adresse <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Fichier projet ou profil de publication**

**Pour les déploiements de Windows IIS :** Incluez la `<EnvironmentName>` propriété dans le [profil de publication (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet. Cette approche définit l’environnement dans *web.config* lorsque le projet est publié :

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

Pour les distributions Linux, utilisez la `export` commande à partir d’une invite de commandes pour les paramètres de variable basés sur session et *bash_profile* fichier pour les paramètres d’environnement au niveau de l’ordinateur.

### <a name="set-the-environment-in-code"></a>Définir l’environnement dans le code

Appelez <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> lors de la génération de l’hôte. Consultez <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Configuration par environnement

Pour charger la configuration par environnement, nous vous recommandons de disposer des éléments suivants :

* fichiers *appSettings* (*appSettings. { Environment}. JSON*). Consultez <xref:fundamentals/configuration/index#json-configuration-provider>.
* Variables d’environnement (définies sur chaque système sur lequel l’application est hébergée). Localisez <xref:fundamentals/host/web-host#environment> et <xref:security/app-secrets#environment-variables>.
* Secret Manager (dans l’environnement de développement uniquement). Consultez <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Classe et méthodes Startup en fonction de l’environnement

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Injecter IHostingEnvironment dans Startup.Configurer

Injectez <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> dans `Startup.Configure` . Cette approche est utile lorsque l’application ne nécessite que la configuration `Startup.Configure` de quelques environnements avec des différences de code minimales par environnement.

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

Injectez <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> dans le `Startup` constructeur et assignez le service à un champ à utiliser dans toute la `Startup` classe. Cette approche est utile lorsque l’application requiert la configuration du démarrage pour quelques environnements avec des différences de code minimales par environnement.

Dans l’exemple suivant :

* L’environnement est conservé dans le `_env` champ.
* `_env`est utilisé dans `ConfigureServices` et `Configure` pour appliquer la configuration de démarrage en fonction de l’environnement de l’application.

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

Quand une application ASP.NET Core démarre, la [classe Startup](xref:fundamentals/startup) amorce l’application. L’application peut définir `Startup` des classes distinctes pour différents environnements (par exemple, `StartupDevelopment` ). La `Startup` classe appropriée est sélectionnée au moment de l’exécution. La classe dont le suffixe du nom correspond à l'environnement actuel est prioritaire. Si aucune classe `Startup{EnvironmentName}` correspondante n’est trouvée, la classe `Startup` est utilisée. Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.

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

[Configurez](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) et [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) prennent en charge des versions spécifiques à l’environnement du formulaire `Configure<EnvironmentName>` et `Configure<EnvironmentName>Services` . Cette approche est utile lorsque l’application requiert la configuration du démarrage pour plusieurs environnements avec de nombreuses différences de code par environnement.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
