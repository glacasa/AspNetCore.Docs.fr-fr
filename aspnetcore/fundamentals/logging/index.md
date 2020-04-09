---
title: Journalisation dans .NET Core et ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser le framework de journalisation fourni par le package NuGet Microsoft.Extensions.Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 58e236ad7f0863b87907d5585e1cb6bf61d46e99
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663302"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Journalisation dans .NET Core et ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra) et [Steve Smith](https://ardalis.com/)

.NET Core prend en charge une API de journalisation qui fonctionne avec différents fournisseurs de journalisation tiers et intégrés. Cet article explique comment utiliser cette API de journalisation avec les fournisseurs de journalisation intégrés.

::: moniker range=">= aspnetcore-3.0"

La plupart des exemples de code présentés dans cet article proviennent d’applications ASP.NET Core. Les parties spécifiques à l’enregistrement de ces extraits de code s’appliquent à toute application .NET Core qui utilise [l’hôte générique](xref:fundamentals/host/generic-host). Pour un exemple de la façon d’utiliser l’hôte générique dans une application de console<xref:fundamentals/host/hosted-services>non-web, consultez le fichier *Program.cs* de l’application [d’échantillon De tâches d’arrière-plan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( ).

Le code de journalisation pour les applications sans hôte générique diffère dans la façon dont les [fournisseurs sont ajoutés](#add-providers) et les [enregistreurs d'événements créés](#create-logs). Des exemples de code non hôte sont présentés dans ces sections de l’article.

::: moniker-end

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Ajouter des fournisseurs

Un fournisseur de journalisation affiche ou stocke des journaux. Par exemple, le fournisseur Console affiche les journaux dans la console, et le fournisseur Azure Application Insights les stocke dans Azure Application Insights. Il est possible d’envoyer les journaux à plusieurs endroits en ajoutant plusieurs fournisseurs.

::: moniker range=">= aspnetcore-3.0"

Pour ajouter un fournisseur dans une application qui utilise un hôte générique, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

Dans une application de console non hôte, appelez la méthode d’extension `Add{provider name}` du fournisseur lors de la création d’un `LoggerFactory` :

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` et `AddConsole` requièrent une instruction `using` pour `Microsoft.Extensions.Logging`.

Les modèles de projet ASP.NET Core par défaut appellent <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :

* [Console](#console-provider)
* [Débogage](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (seulement lorsqu’il s’exécute sur Windows)

Vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix. Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

Pour ajouter un fournisseur, appelez la méthode d’extension `Add{provider name}` du fournisseur dans *Program.cs* :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Le code précédent nécessite des références à `Microsoft.Extensions.Logging` et `Microsoft.Extensions.Configuration`.

Le modèle de projet par défaut appelle <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, qui ajoute les fournisseurs de journalisation suivants :

* Console
* Débogage
* EventSource (à partir d’ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Si vous utilisez `CreateDefaultBuilder`, vous pouvez remplacer les fournisseurs par défaut par ceux de votre choix. Appelez <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> et ajoutez les fournisseurs que vous souhaitez.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

Vous trouverez des informations sur les [fournisseurs de journalisation intégrés](#built-in-logging-providers) et les [fournisseurs de journalisation tiers](#third-party-logging-providers) plus loin dans cet article.

## <a name="create-logs"></a>Créer des journaux

Pour créer des journaux, utilisez un objet <xref:Microsoft.Extensions.Logging.ILogger%601>. Dans une application web ou un service hébergé, obtenez un `ILogger` à partir de l’injection de dépendances (DI). Dans les applications de console non hôtes, utilisez le `LoggerFactory` pour créer un `ILogger`.

L’exemple d’ASP.NET Core suivant crée un enregistreur d’événements de catégorie `TodoApiSample.Pages.AboutModel`. La *catégorie* du journal est une chaîne associée à chaque journal. L’instance `ILogger<T>` fournie par l’injection de dépendances crée des journaux ayant comme catégorie un nom complet de type `T`. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

L’exemple d’application de console non hôte suivant crée un enregistreur d’événements de catégorie `LoggingConsoleApp.Program`.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

Dans les exemples d’ASP.NET Core et d’application de console suivants, l’enregistreur d’événements est utilisé pour créer des journaux de niveau `Information`. Le *niveau* du journal indique la gravité de l’événement consigné. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

Les [niveaux](#log-level) et les [catégories](#log-category) sont expliqués plus en détail plus loin dans cet article. 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Créer des journaux dans la classe Programme

Pour écrire des journaux dans la classe `Program` d’une application ASP.NET Core, récupérez une instance `ILogger` de l’injection de dépendances après la création de l’hôte :

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

L’exploitation forestière pendant la construction de l’hôte n’est pas directement prise en charge. Cependant, un bûcheron séparé peut être utilisé. Dans l’exemple suivant, un bûcheron `CreateHostBuilder` [Serilog](https://serilog.net/) est utilisé pour se connecter . `AddSerilog`utilise la configuration `Log.Logger`statique spécifiée dans :

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a>Créer des journaux dans la classe de démarrage

Pour écrire des journaux dans la méthode `Startup.Configure` d’une application ASP.NET Core, incluez un paramètre `ILogger` dans la signature de la méthode :

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

L’écriture de journaux avant la fin de l’installation du conteneur d’injection de dépendances dans la méthode `Startup.ConfigureServices` n’est pas prise en charge :

* L’injection d’un enregistreur d’événements dans le constructeur `Startup` n’est pas prise en charge.
* L’injection d’un enregistreur d’événements dans la signature de méthode `Startup.ConfigureServices` n’est pas prise en charge.

La raison de cette restriction est que la journalisation dépend de l’injection de dépendances et de la configuration qui, à son tour, dépend de l’injection de dépendances. Le conteneur d’injection de dépendances n’est pas configuré avant que `ConfigureServices` soit terminé.

L’injection de constructeur d’un enregistreur d’événements dans `Startup` fonctionne dans les versions antérieures d’ASP.NET Core, car un conteneur d’injection de dépendances distinct est créé pour l’hôte web. Pour plus d’informations sur la raison de la création d’un seul conteneur pour l’hôte générique, consultez l’[annonce de changement cassant](https://github.com/aspnet/Announcements/issues/353).

Si vous devez configurer un service qui dépend de `ILogger<T>`, vous pouvez toujours le faire à l’aide de l’injection de constructeur, ou avec une méthode de fabrique. L’approche de la méthode de fabrique est recommandée uniquement s’il n’y a aucune autre option. Supposons, par exemple, que vous deviez remplir une propriété avec un service à partir de l’injection de dépendances :

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Le code en surbrillance précédent est une `Func` qui s’exécute la première fois que le conteneur d’injection de dépendances doit construire une instance de `MyService`. Vous pouvez accéder à tous les services inscrits de cette manière.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Créer des journaux au démarrage

Pour écrire des journaux dans la classe `Startup`, ajoutez un paramètre `ILogger` dans la signature de constructeur :

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Créer des journaux dans la classe Programme

Pour écrire des journaux dans la classe `Program`, récupérez une instance `ILogger` auprès de l’injection de dépendances :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

L’exploitation forestière pendant la construction de l’hôte n’est pas directement prise en charge. Cependant, un bûcheron séparé peut être utilisé. Dans l’exemple suivant, un bûcheron `CreateWebHostBuilder` [Serilog](https://serilog.net/) est utilisé pour se connecter . `AddSerilog`utilise la configuration `Log.Logger`statique spécifiée dans :

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Aucune méthode d’enregistreur d’événements asynchrone

La journalisation doit être suffisamment rapide par rapport au coût du code asynchrone en matière de performances. Si votre magasin de données de journalisation est lent, n’écrivez pas directement dedans. Écrivez au départ les messages de journal dans un magasin rapide, puis déplacez-les dans le magasin lent. Par exemple, si vous vous connectez à SQL Server, évitez de le faire directement dans une méthode `Log`, car les méthodes `Log` sont synchrones. Ajoutez plutôt de façon synchronisée des messages de journal à une file d’attente en mémoire, puis configurez un traitement en arrière-plan afin d’extraire les messages de la file d’attente et d’effectuer le travail asynchrone d’envoi des données vers SQL Server. Pour plus d’informations, voir [ce](https://github.com/dotnet/AspNetCore.Docs/issues/11801) numéro GitHub.

## <a name="configuration"></a>Configuration

La configuration de fournisseur de journalisation est fournie par un ou plusieurs fournisseurs de configuration :

* Formats de fichiers (INI, JSON et XML).
* Arguments de ligne de commande
* Variables d'environnement.
* Objets .NET en mémoire.
* Stockage [Secret Manager](xref:security/app-secrets) non chiffré.
* Magasin utilisateur chiffré comme [Azure Key Vault](xref:security/key-vault-configuration).
* Fournisseurs personnalisés (installés ou créés).

Par exemple, la configuration de journalisation est généralement fournie par la section `Logging` des fichiers de paramètres d’application. L’exemple suivant montre le contenu d’un fichier *appsettings.Development.json* standard :

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

La propriété `Logging` peut avoir un niveau `LogLevel` et des propriétés de module fournisseur d'informations (Console ici).

La propriété `LogLevel` sous `Logging` spécifie le [niveau](#log-level) de journalisation minimal pour les catégories sélectionnées. Dans l’exemple, les catégories `System` et `Microsoft` sont consignées au niveau `Information`, et toutes les autres au niveau `Debug`.

Les autres propriétés sous `Logging` spécifient les fournisseurs de journalisation. Cet exemple concerne le fournisseur Console. Si un fournisseur prend `IncludeScopes` en charge les portées journal, indique s’ils sont [activés.](#log-scopes) Une propriété de fournisseur (comme `Console` dans l’exemple) peut également spécifier une propriété `LogLevel`. `LogLevel` sous un fournisseur spécifie les niveaux à consigner pour ce fournisseur.

Si les niveaux sont spécifiés dans `Logging.{providername}.LogLevel`, ils remplacent ce qui est défini dans `Logging.LogLevel`.

L’API de connexion n’inclut pas de scénario pour modifier les niveaux de journal pendant qu’une application est en cours d’exécution. Cependant, certains fournisseurs de configuration sont capables de recharger la configuration, ce qui prend effet immédiatement sur la configuration de l’enregistrement. Par exemple, le fournisseur de configuration `CreateDefaultBuilder` de [fichiers](xref:fundamentals/configuration/index#file-configuration-provider), qui est ajouté par pour lire les fichiers de paramètres, recharge la configuration de connexion par défaut. Si la configuration est modifiée dans le code pendant qu’une application est en cours d’exécution, l’application peut appeler [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) pour mettre à jour la configuration de connexion de l’application.

Pour plus d’informations sur l’implémentation des fournisseurs de configuration, consultez <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Exemple de sortie de la journalisation

Avec l’exemple de code présenté dans la section précédente, les journaux s’affichent dans la console lorsque l’application est exécutée en ligne de commande. Voici un exemple de sortie de la console :

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

Les journaux précédents ont été générés par une requête HTTP Get à l’exemple d’application à l’adresse `http://localhost:5000/api/todo/0`.

Voici un exemple des mêmes journaux tels qu’ils s’affichent dans la fenêtre Débogage quand vous exécutez l’exemple d’application dans Visual Studio :

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApiSample ». Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core. ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Les journaux créés par les appels de `ILogger` illustrés dans la section précédente commencent par « TodoApi ». Ceux qui commencent par les catégories « Microsoft » proviennent du code du framework ASP.NET Core. ASP.NET Core et le code d’application utilisent la même API de journalisation et les mêmes fournisseurs.

::: moniker-end

Les autres sections de cet article détaillent certains points et présentent les options de journalisation.

## <a name="nuget-packages"></a>Packages NuGet

Les interfaces `ILogger` et `ILoggerFactory` se trouvent dans [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), et leurs implémentations par défaut se trouvent dans [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Catégorie de journal

Quand un objet `ILogger` est créé, une *catégorie* lui est spécifiée. Cette catégorie est incluse dans tous les messages de journal créés par cette instance de `ILogger`. Si la catégorie peut être n’importe quelle chaîne, la convention est d’utiliser le nom de la classe, par exemple « TodoApi.Controllers.TodoController ».

Utilisez `ILogger<T>` pour obtenir une instance de `ILogger` qui utilise le nom de type complet `T` comme catégorie :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Pour spécifier explicitement la catégorie, appelez `ILoggerFactory.CreateLogger` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` équivaut à appeler `CreateLogger` avec le nom de type complet `T`.

## <a name="log-level"></a>Log level

Chaque journal spécifie une valeur <xref:Microsoft.Extensions.Logging.LogLevel>. Le niveau de journalisation indique la gravité ou l’importance. Vous pourriez par exemple écrire un journal `Information` lorsqu’une méthode se termine normalement et un journal `Warning` lorsqu’une méthode retourne un code de statut *404 Not Found*.

Le code suivant crée des journaux `Information` et `Warning` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Dans le code précédent, le premier paramètre est [l’ID de l’événement de journal](#log-event-id). Le deuxième paramètre est un modèle de message contenant des espaces réservés pour les valeurs d’argument fournies par les autres paramètres de méthode. Les paramètres de méthode sont expliqués dans la section [Modèle de message](#log-message-template) plus loin dans cet article.

Les méthodes de journal qui comportent le niveau dans leur nom (par exemple, `LogInformation` et `LogWarning`) sont des [méthodes d’extension pour ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Elles appellent une méthode `Log` qui prend un paramètre `LogLevel`. Vous pouvez appeler la méthode `Log` directement au lieu d’appeler ces méthodes d’extension, mais la syntaxe est relativement complexe. Pour plus d’informations, voir <xref:Microsoft.Extensions.Logging.ILogger> et le [code source des extensions d’enregistreur d'événements](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core définit les niveaux de journalisation suivants, classés selon leur degré de gravité (du plus faible au plus élevé).

* Trace = 0

  Informations en général exclusivement utiles à des fins de débogage. Ces messages peuvent contenir des données d’application sensibles. Ils ne doivent donc pas être activés dans un environnement de production. *Désactivé par défaut.*

* Debug = 1

  Informations qui peuvent être utiles dans le développement et le débogage. Exemple : `Entering method Configure with flag set to true.` En raison de leur volume élevé, n’activez les journaux de niveau `Debug` en production que pour résoudre des problèmes.

* Information = 2

  Informations de suivi du flux général de l’application. Ces journaux ont généralement une utilité à long terme. Exemple : `Request received for path /api/todo`

* Warning = 3

  Informations sur les événements anormaux ou inattendus dans le flux de l’application. Il peut s’agir d’erreurs ou d’autres situations qui ne provoquent pas l’arrêt de l’application, mais qu’il peut être intéressant d’examiner. Le niveau de journalisation `Warning` est généralement utilisé pour les exceptions gérées. Exemple : `FileNotFoundException for file quotes.txt.`

* Error = 4

  Fournit des informations sur des erreurs et des exceptions qui ne peuvent pas être gérées. Ces messages indiquent un échec de l’activité ou de l’opération en cours (par exemple, la requête HTTP actuellement exécutée), pas un échec au niveau de l’application. Exemple de message de journal : `Cannot insert record due to duplicate key violation.`

* Critical = 5

  Fournit des informations sur des échecs qui nécessitent un examen immédiat. Exemples : perte de données, espace disque insuffisant.

Le niveau de journalisation permet de contrôler le volume de la sortie de journal écrite sur un support de stockage ou dans une fenêtre d’affichage. Par exemple :

* En production :
  * L’enregistrement `Trace` aux `Information` niveaux à travers produit un volume élevé de messages journal détaillés. Pour contrôler les coûts et ne `Trace` `Information` pas dépasser les limites de stockage de données, connectez-vous à des messages de niveau à un magasin de données à volume élevé et peu coûteux.
  * L’enregistrement `Warning` `Critical` à travers les niveaux produit généralement moins de messages journal plus petits. Par conséquent, les coûts et les limites de stockage ne sont généralement pas une préoccupation, ce qui se traduit par une plus grande flexibilité du choix des magasins de données.
* Pendant le développement :
  * Connectez-vous `Warning` à travers `Critical` les messages à la console.
  * Ajoutez `Trace` `Information` des messages lors du dépannage.

La section [Filtrage de journal](#log-filtering) plus loin dans cet article explique comment déterminer les niveaux de journalisation gérés par un fournisseur.

ASP.NET Core écrit des journaux pour les événements de framework. Aucun journal du niveau `Debug` ou `Trace` n’était créé dans les exemples de journaux présentés plus haut dans cet article, puisque les journaux au-dessous du niveau `Information` étaient exclus. Voici un exemple de journaux Console produits par l’exemple d’application configurée pour afficher les journaux `Debug` :

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a>ID d’événement de log

Chaque journal peut spécifier un *ID d’événement*. Pour cela, l’exemple d’application utilise une classe `LoggingEvents` définie localement :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

Un ID d’événement associe un jeu d’événements. Par exemple, tous les journaux liés à l’affichage d’une liste d’éléments sur une page peuvent être 1001.

Le fournisseur de journalisation peut stocker l’ID d’événement dans un champ ID, dans le message de journalisation, ou pas du tout. Le fournisseur Debug n’affiche pas les ID d’événements. Le fournisseur Console affiche les ID d’événements entre crochets après la catégorie :

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Modèle de message de journal

Chaque journal spécifie un modèle de message. Ce dernier peut contenir des espaces réservés pour lesquels les arguments sont fournis. Utilisez des noms et non des nombres pour les espaces réservés.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

L’ordre des espaces réservés, pas leurs noms, détermine quels paramètres sont utilisés pour fournir leurs valeurs. Dans le code suivant, on voit que les noms de paramètres sont hors séquence dans le modèle de message :

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Ce code crée un message de journal avec les valeurs des paramètres dans la séquence :

```text
Parameter values: parm1, parm2
```

Le framework de journalisation fonctionne ainsi pour permettre aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Les arguments proprement dits, et pas seulement le modèle de message mis en forme, sont transmis au système de journalisation. C’est grâce à ces informations que les fournisseurs de journalisation peuvent stocker les valeurs des paramètres sous forme de champs. Supposons par exemple que les appels de méthodes d’enregistreur d’événements se présentent ainsi :

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Si vous envoyez les journaux au Stockage Table Azure, chaque entité Table Azure peut avoir les propriétés `ID` et `RequestTime`, ce qui simplifie les requêtes sur les données de journaux. Une requête peut rechercher tous les journaux compris dans une plage `RequestTime` spécifique sans analyser le délai d’expiration du message texte.

## <a name="logging-exceptions"></a>Journalisation des exceptions

Les méthodes logger ont des surcharges qui vous permettent de passer une exception, comme dans l’exemple suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Tous les fournisseurs ne gèrent pas les informations sur les exceptions de la même façon. Voici un exemple de sortie du fournisseur Debug extrait du code montré plus haut.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrage de journal

Vous pouvez spécifier un niveau de journalisation minimum pour une catégorie ou un fournisseur spécifique, ou pour l’ensemble des fournisseurs ou des catégories. Les enregistrements de journal en dessous du niveau minimum ne sont pas passés à ce fournisseur, et ne sont donc pas affichés ou stockés.

Pour supprimer tous les journaux, choisissez `LogLevel.None` comme niveau de journalisation minimal. La valeur entière de `LogLevel.None` est 6, soit un niveau supérieur à `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Créer des règles de filtre dans la configuration

Le code de `CreateDefaultBuilder` modèle de projet appelle à configurer la connexion pour les fournisseurs console, Debug et EventSource (ASP.NET Core 2.2 ou plus tard). La méthode `CreateDefaultBuilder` définit également la journalisation pour rechercher la configuration dans une section `Logging`, conformément à ce qui a été expliqué [plus haut dans cet article](#configuration).

Les données de configuration spécifient des niveaux de journalisation minimum par fournisseur et par catégorie, comme dans l’exemple suivant :

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Ce code JSON crée six règles de filtre : une pour le fournisseur Debug, quatre pour le fournisseur Console et une pour tous les fournisseurs. Une seule règle est choisie pour chaque fournisseur à la création d’un objet `ILogger`.

### <a name="filter-rules-in-code"></a>Règles de filtre dans le code

L'exemple suivant montre comment enregistrer des règles de filtre dans le code :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

Le second `AddFilter` spécifie le fournisseur Debug par son nom de type. Le premier `AddFilter` s’applique à tous les fournisseurs, car il ne spécifie aucun type de fournisseur.

### <a name="how-filtering-rules-are-applied"></a>Mode d’application des règles de filtre

Les données de configuration et le code `AddFilter` contenus dans les exemples précédents créent les règles présentées dans le tableau suivant. Les six premières proviennent de l’exemple de configuration et les deux dernières, de l’exemple de code.

| Number | Fournisseur      | Catégories commençant par...          | Niveau de journalisation minimum |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Débogage         | Toutes les catégories                          | Information       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | Avertissement           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Débogage             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | Error             |
| 5      | Console       | Toutes les catégories                          | Information       |
| 6      | Tous les fournisseurs | Toutes les catégories                          | Débogage             |
| 7      | Tous les fournisseurs | Système                                  | Débogage             |
| 8      | Débogage         | Microsoft                               | Trace             |

À la création d’un objet `ILogger`, l’objet `ILoggerFactory` sélectionne une seule règle à appliquer à cet enregistrement d’événements par fournisseur. Tous les messages écrits par une instance `ILogger` sont filtrés selon les règles sélectionnées. La règle la plus spécifique pouvant être appliquée à chaque paire catégorie/fournisseur est sélectionnée parmi les règles disponibles.

L’algorithme suivant est utilisé pour chaque fournisseur quand un objet `ILogger` est créé pour une catégorie donnée :

* Sélectionnez toutes les règles qui correspondent au fournisseur ou à son alias. Si aucune correspondance n’est trouvée, sélectionnez toutes les règles avec un fournisseur vide.
* À partir du résultat de l’étape précédente, sélectionnez les règles ayant le plus long préfixe de catégorie correspondant. Si aucune correspondance n’est trouvée, sélectionnez toutes les règles qui ne spécifient pas de catégorie.
* Si plusieurs règles sont sélectionnées, prenez la **dernière**.
* Si aucune règle n’est sélectionnée, utilisez `MinimumLevel`.

Avec la liste de règles précédente, supposons que vous créez un objet `ILogger` pour la catégorie « Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine » :

* Les règles 1, 6 et 8 s’appliquent au fournisseur Debug. La règle 8 est sélectionnée, car c’est la plus spécifique.
* Les règles 3, 4, 5 et 6 s’appliquent au fournisseur Console. La règle 3 est la plus spécifique.

L’instance `ILogger` ainsi produite envoie des journaux de niveau `Trace` ou supérieur au fournisseur Debug. Les journaux de niveau `Debug` et supérieurs sont envoyés au fournisseur Console.

### <a name="provider-aliases"></a>Alias de fournisseur

Chaque fournisseur définit un *alias* qui peut être utilisé dans la configuration à la place du nom de type complet.  Pour les fournisseurs intégrés, utilisez les alias suivants :

* Console
* Débogage
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Niveau minimum par défaut

Un paramètre de niveau minimum est utilisé uniquement si aucune règle de la configuration ou du code ne s’applique à une catégorie ou un fournisseur spécifique. L’exemple suivant montre comment définir le niveau minimum :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

Si vous ne définissez pas explicitement le niveau minimum, la valeur par défaut est `Information`, ce qui signifie que les niveaux `Trace` et `Debug` sont ignorés.

### <a name="filter-functions"></a>Fonctions de filtrage

Une fonction de filtre est appelée pour tous les fournisseurs et toutes les catégories pour lesquels la configuration ou le code n’applique aucune règle. Le code de la fonction a accès au type de fournisseur, à la catégorie et au niveau de journalisation. Par exemple :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>Niveaux et les catégories de système

Voici quelques catégories utilisées par ASP.NET Core et Entity Framework Core, avec des notes sur les journaux associés :

| Category                            | Notes |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostics ASP.NET Core généraux. |
| Microsoft.AspNetCore.DataProtection | Liste des clés considérées, trouvées et utilisées. |
| Microsoft.AspNetCore.HostFiltering  | Hôtes autorisés. |
| Microsoft.AspNetCore.Hosting        | Temps de traitement des requêtes HTTP et heure de démarrage. Liste des assemblys de démarrage d’hébergement chargés. |
| Microsoft.AspNetCore.Mvc            | Diagnostics MVC et Razor. Liaison de données, exécution de filtres, compilation de vues, sélection d’actions. |
| Microsoft.AspNetCore.Routing        | Informations de correspondance des itinéraires. |
| Microsoft.AspNetCore.Server         | Démarrage et arrêt de la connexion, et réponses persistantes. Informations du certificat HTTPS. |
| Microsoft.AspNetCore.StaticFiles    | Fichiers pris en charge. |
| Microsoft.EntityFrameworkCore       | Diagnostics Entity Framework Core généraux. Activité et configuration de la base de données, détection des modifications, migrations. |

## <a name="log-scopes"></a>Étendues de journal

 Une *étendue* peut regrouper un ensemble d’opérations logiques. Ce regroupement permet de joindre les mêmes données à tous les journaux créés au sein d’un ensemble. Par exemple, chaque journal créé dans le cadre du traitement d’une transaction peut contenir l’ID de la transaction.

Une étendue est un type `IDisposable` qui est retourné par la méthode <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>. Elle s’applique tant qu’elle n’est pas supprimée. Utilisez une étendue en incluant les appels de l’enregistrement d’événements dans un bloc `using` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

Le code suivant active les étendues pour le fournisseur Console :

*Program.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> La configuration de l’option logger `IncludeScopes` pour la console est nécessaire pour activer la journalisation basée sur des étendues.
>
> Pour plus d'informations sur la configuration, reportez-vous à la section [Configuration](#configuration).

Chaque message de journal fournit les informations incluses dans l’étendue :

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Fournisseurs de journalisation intégrés

ASP.NET Core contient les fournisseurs suivants :

* [Console](#console-provider)
* [Débogage](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationsInsights](#azure-application-insights-trace-logging)

Pour plus d’informations sur stdout et sur la journalisation du débogage avec le module ASP.NET Core, consultez <xref:test/troubleshoot-azure-iis> et <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Fournisseur Console

Le package de fournisseur [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) envoie la sortie de journal dans la console. 

```csharp
logging.AddConsole();
```

Pour voir la sortie de la journalisation Console, ouvrez une invite de commandes dans le dossier du projet et exécutez la commande suivante :

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Fournisseur Debug

Le package de fournisseur [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) écrit la sortie de journal à l’aide de la classe [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (appels de la méthode `Debug.WriteLine`).

Sur Linux, ce fournisseur écrit la sortie de log dans */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Fournisseur de source d’événements

Le forfait [fournisseur Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) écrit à une plateforme `Microsoft-Extensions-Logging`transversale Event Source avec le nom . Sur Windows, le fournisseur utilise [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Le fournisseur Event Source `CreateDefaultBuilder` est ajouté automatiquement lorsqu’il est appelé pour construire l’hôte.

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a>outil de trace de dotnet

[L’outil de trace de dotnet](/dotnet/core/diagnostics/dotnet-trace) est un outil mondial CLI multiplateforme qui permet la collecte de traces .NET Core d’un processus en cours d’exécution. L’outil <xref:Microsoft.Extensions.Logging.EventSource> recueille les <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>données du fournisseur à l’aide d’un .

Installez l’outillage de trace de dotnet avec la commande suivante :

```dotnetcli
dotnet tool install --global dotnet-trace
```

Utilisez l’outillage de trace dotnet pour recueillir une trace d’une application :

1. Si l’application ne construit `CreateDefaultBuilder`pas l’hôte avec , ajouter le [fournisseur Event Source](#event-source-provider) à la configuration de connexion de l’application.

1. Exécutez l’application avec la `dotnet run` commande.

1. Déterminer l’identifiant de processus (PID) de l’application .NET Core :

   * Sur Windows, utilisez l’une des approches suivantes :
     * Gestionnaire de tâches (Ctrl-Alt-Del)
     * [commande tasklist](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process Powershell commande](/powershell/module/microsoft.powershell.management/get-process)
   * Sur Linux, utilisez la [commande pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Trouvez la MIP pour le processus qui porte le même nom que l’assemblage de l’application.

1. Exécutez `dotnet trace` la commande.

   Syntaxe de commande générale :

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   Lors de l’utilisation d’une coque `--providers` de commande`'`PowerShell, enfermez la valeur en citations simples ( ):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Sur les plates-formes `-f speedscope` non Windows, ajoutez la possibilité `speedscope`de modifier le format du fichier de trace de sortie à .

   | Mot clé | Description |
   | :-----: | ----------- |
   | 1       | Log méta événements `LoggingEventSource`sur le . Ne enregistre pas `ILogger`les événements de ). |
   | 2       | Allume `Message` l’événement `ILogger.Log()` lorsqu’il est appelé. Fournit de l’information de façon programmatique (non formatée). |
   | 4       | Allume `FormatMessage` l’événement `ILogger.Log()` lorsqu’il est appelé. Fournit la version de chaîne formatée de l’information. |
   | 8       | Allume `MessageJson` l’événement `ILogger.Log()` lorsqu’il est appelé. Fournit une représentation JSON des arguments. |

   | Niveau d'événement | Description     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`entrées `{Logger Category}` et `{Event Level}` représentent des conditions supplémentaires de filtrage des journaux. Entrées `FilterSpecs` séparées avec un`;`point-virgule ( ).

   Exemple à l’aide d’une `--providers` coque de commande Windows **(pas** de citations uniques autour de la valeur) :

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   La commande précédente active :

   * L’enregistreur Event Source pour produire`4`des cordes`2`formatées ( ) pour les erreurs ( ).
   * `Microsoft.AspNetCore.Hosting`l’exploitation `Informational` forestière au`4`niveau de l’exploitation forestière ( ).

1. Arrêtez l’outillage de trace de dotnet en appuyant sur la clé Enter ou Ctrl-C.

   La trace est enregistrée avec le nom *trace.nettrace* dans le dossier où la `dotnet trace` commande est exécutée.

1. Ouvrez la trace avec [Perfview](#perfview). Ouvrez le fichier *trace.nettrace* et explorez les traces.

Pour plus d'informations, consultez les pages suivantes :

* [Trace pour l’utilité d’analyse de performance (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)
* [Trace pour l’utilité d’analyse de performance (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics documentation de dépôt GitHub)
* [Classe LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource source de référence (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Pour obtenir la source `release/{Version}`de `{Version}` référence pour une version différente, changer la branche à , où est la version de ASP.NET Core souhaité.
* [Perfview](#perfview) &ndash; Utile pour voir les traces event Source.

#### <a name="perfview"></a>Perfview (Perfview)

::: moniker-end

Utilisez [l’utilitaire PerfView](https://github.com/Microsoft/perfview) pour collecter et afficher les journaux. Il existe d’autres outils d’affichage des journaux ETW, mais PerfView est l’outil recommandé pour gérer les événements ETW générés par ASP.NET Core.

Pour configurer PerfView afin qu’il collecte les événements enregistrés par ce fournisseur, ajoutez la chaîne `*Microsoft-Extensions-Logging` à la liste des **fournisseurs supplémentaires**. (N’oubliez pas d’inclure l’astérisque au début de la chaîne.)

![Fournisseurs supplémentaires dans PerfView](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Fournisseur EventLog Windows

Le package de fournisseur [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) envoie la sortie de journal dans le journal des événements Windows.

```csharp
logging.AddEventLog();
```

Les [surcharges AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) vous permettent de passer à <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Si `null` elles sont spécifiées ou non, les paramètres par défaut suivants sont utilisés :

* `LogName`&ndash; "Application"
* `SourceName`&ndash; ".NET Runtime"
* `MachineName` &ndash; ordinateur local

Les événements sont enregistrés pour [le niveau d’avertissement et plus élevé](#log-level). Pour enregistrer les `Warning`événements inférieurs à, définissez explicitement le niveau de journal. Par exemple, ajoutez ce qui suit au fichier *appsettings.json* :

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>Fournisseur TraceSource

Le package de fournisseur [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) utilise les bibliothèques et fournisseurs <xref:System.Diagnostics.TraceSource>.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

Les [surcharges AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) vous permettent de passer un commutateur de source et un écouteur de suivi.

Pour pouvoir utiliser ce fournisseur, il faut que l’application s’exécute sur .NET Framework (au lieu de .NET Core). Le fournisseur peut acheminer les messages vers différents [détecteurs d’événements](/dotnet/framework/debug-trace-profile/trace-listeners), comme <xref:System.Diagnostics.TextWriterTraceListener> (utilisé dans l’exemple d’application).

### <a name="azure-app-service-provider"></a>Fournisseur Azure App Service

Le package de fournisseur [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) écrit les enregistrements de journal dans des fichiers texte dans le système de fichiers d’une application Azure App Service, et dans un [stockage Blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) dans un compte de stockage Azure.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

Le fournisseur de package n’est pas inclus dans le framework partagé. Pour utiliser le fournisseur, ajoutez le package du fournisseur au projet.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Le package du fournisseur n’est pas inclus dans le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Lorsque vous ciblez .NET framework ou référencez le métapackage `Microsoft.AspNetCore.App`, ajoutez le package de fournisseur dans le projet. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Pour configurer les paramètres du fournisseur, utilisez <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> et <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, comme illustré dans l’exemple suivant :

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Pour configurer les paramètres du fournisseur, utilisez <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> et <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, comme illustré dans l’exemple suivant :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Une surcharge <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> permet de transmettre <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. L’objet de paramètres peut remplacer les paramètres par défaut, comme le modèle de sortie de journalisation, le nom d’objet blob et la limite de taille de fichier. (*Modèle de sortie* est un modèle de message qui s’applique à tous les journaux en plus de ce qui est fourni avec un appel de méthode `ILogger`.)

::: moniker-end

En cas de déploiement sur une application App Service, celle-ci applique les paramètres définis dans la section [Journaux App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) de la page **App Service** du portail Azure. Quand les paramètres suivants sont mis à jour, les changements prennent effet immédiatement sans avoir besoin de redémarrer ou redéployer l’application.

* **Journalisation des applications (système de fichiers)**
* **Journalisation des applications (objet blob)**

L’emplacement par défaut des fichiers journaux est le dossier *D:\\racine\\LogFiles\\Application*, et le nom de fichier par défaut est *diagnostics-aaaammjj.txt*. La limite de taille de fichier par défaut est de 10 Mo, et le nombre maximal de fichiers conservés par défaut est de 2. Le nom par défaut du blob est *{app-name}{timestamp}/aaaa/mm/jj/hh/{guid}-applicationLog.txt*.

Le fournisseur fonctionne uniquement quand le projet s’exécute dans l’environnement Azure. Il n’a aucun effet si le projet s’exécute localement &mdash; il n’écrit pas d’enregistrements dans les fichiers locaux ou dans le stockage de développement local pour les objets blob.

#### <a name="azure-log-streaming"></a>Streaming des journaux Azure

La diffusion en continu des journaux Azure permet d’afficher l’activité de journalisation en temps réel aux emplacements suivants :

* Serveur d'applications
* Serveur web
* Suivi des demandes ayant échoué

Pour configurer le streaming des journaux Azure :

* Accédez à la page **Journaux App Service** dans le portail de votre application.
* Définissez **Journal des applications (Système de fichiers)** sur **Activé**.
* Choisissez le **niveau** du journal. Ce paramètre ne s’applique qu’au streaming en rondins Azure, et non aux autres fournisseurs de connexion de l’application.

Accédez à la page **Streaming des journaux** pour voir les messages d’application. Ils sont consignés par application par le biais de l’interface `ILogger`.

### <a name="azure-application-insights-trace-logging"></a>Journalisation des traces Azure Application Insights

Le package de fournisseur [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) écrit des journaux dans Azure Application Insights. Application Insights est un service qui surveille une application web et fournit des outils pour interroger et analyser les données de télémétrie. Si vous utilisez ce fournisseur, vous pouvez interroger et analyser vos journaux à l’aide des outils Application Insights.

Le fournisseur de journalisation est inclus en tant que dépendance de [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), le package qui fournit toutes les données de télémétrie disponibles pour ASP.NET Core. Si vous utilisez ce package, vous n’avez pas besoin d’installer le package du fournisseur.

N’utilisez pas le [package Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;destiné à ASP.NET 4.x.

Pour plus d’informations, consultez les ressources suivantes :

* [Vue d'ensemble d’Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights pour les applications ASP.NET Core](/azure/azure-monitor/app/asp-net-core) : commencez ici si vous souhaitez implémenter la gamme complète des données de télémétrie d’Application Insights en même temps que la journalisation.
* [Journaux ApplicationInsightsLoggerProvider pour .NET Core ILogger](/azure/azure-monitor/app/ilogger) : commencez ici si vous souhaitez implémenter le fournisseur de journalisation sans le reste des données de télémétrie Application Insights.
* [Adaptateurs de journalisation Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Installer, configurer et initialiser le kit de développement logiciel (SDK) Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) : tutoriel interactif sur le site Microsoft Learn.

## <a name="third-party-logging-providers"></a>Fournisseurs de journalisation tiers

Frameworks de journalisation tiers qui sont pris en charge dans ASP.NET Core :

* [elmah.io](https://elmah.io/) ([dépôt GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Dépôt GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([dépôt GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([référentiel GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([repo GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([dépôt GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([dépôt GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([dépôt GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([dépôt GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))

Certains frameworks tiers prennent en charge la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

L’utilisation d’un framework tiers est semblable à l’utilisation des fournisseurs intégrés :

1. Ajoutez un package NuGet à votre projet.
1. Appelez `ILoggerFactory` une méthode d’extension fournie par le cadre d’enregistrement.

Pour plus d’informations, consultez la documentation de chaque fournisseur. Les fournisseurs de journalisation tiers ne sont pas pris en charge par Microsoft.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/loggermessage>
