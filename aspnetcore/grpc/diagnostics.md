---
title: L’exploitation forestière et le diagnostic dans gRPC sur .NET
author: jamesnk
description: Apprenez à recueillir des diagnostics à partir de votre application gRPC sur .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 131144bf7a2c637eb2c1a1d5c54990dd4d429502
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417522"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>L’exploitation forestière et le diagnostic dans gRPC sur .NET

Par [James Newton-King](https://twitter.com/jamesnk)

Cet article fournit des conseils pour la collecte des diagnostics à partir d’une application gRPC pour aider les problèmes de dépannage. Sont abordés les sujets suivants :

* **Enregistrement** - Journaux structurés écrits à [.NET Core logging](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger>est utilisé par les cadres d’applications pour écrire des journaux, et par les utilisateurs pour leur propre connexion dans une application.
* **Traçage** - Événements liés `DiaganosticSource` à `Activity`une opération écrite à l’aide et . Les traces provenant de sources diagnostiques sont couramment utilisées pour recueillir la télémétrie des applications par des bibliothèques telles que [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) et [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Mesures** - Représentation des mesures de données sur des intervalles de temps, par exemple, des demandes par seconde. Les mesures sont `EventCounter` émises à l’aide et peuvent être observées à l’aide d’un outil de ligne de commande [à compteurs dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ou avec [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Journalisation

gRPC services et le client gRPC écrire des journaux en utilisant [l’enregistrement .NET Core](xref:fundamentals/logging/index). Les journaux sont un bon endroit pour commencer lorsque vous avez besoin de débocher un comportement inattendu dans vos applications.

### <a name="grpc-services-logging"></a>gRPC services d’enregistrement

> [!WARNING]
> Les journaux côté serveur peuvent contenir des informations sensibles à partir de votre application. **Ne publiez jamais** de journaux bruts depuis les applications de production jusqu’aux forums publics comme GitHub.

Étant donné que les services gRPC sont hébergés sur ASP.NET Core, il utilise le système d’enregistrement ASP.NET Core. Dans la configuration par défaut, gRPC enregistre très peu d’informations, mais cela peut être configuré. Voir la documentation sur [ASP.NET connexion de base](xref:fundamentals/logging/index#configuration) pour plus de détails sur la configuration ASP.NET l’enregistrement de base.

gRPC ajoute des `Grpc` journaux dans la catégorie. Pour activer les journaux détaillés `Grpc` à partir de `Debug` gRPC, configurez les préfixes au niveau `LogLevel` de votre `Logging`fichier *appsettings.json* en ajoutant les éléments suivants à la sous-section dans :

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Vous pouvez également *Startup.cs* configurer cela `ConfigureLogging`dans Startup.cs avec :

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Si vous n’utilisez pas la configuration basée sur JSON, définissez la valeur de configuration suivante dans votre système de configuration :

* `Logging:LogLevel:Grpc` = `Debug`

Vérifiez la documentation de votre système de configuration pour déterminer comment spécifier les valeurs de configuration imbriquées. Par exemple, lorsque vous `_` utilisez des variables `:` de l’environnement, deux caractères sont utilisés au lieu de la (par exemple, `Logging__LogLevel__Grpc`).

Nous vous `Debug` recommandons d’utiliser le niveau lors de la collecte de diagnostics plus détaillés pour votre application. Le `Trace` niveau produit des diagnostics de très bas niveau et est rarement nécessaire pour diagnostiquer les problèmes dans votre application.

#### <a name="sample-logging-output"></a>Exemple de sortie de la journalisation

Voici un exemple de sortie `Debug` de console au niveau d’un service gRPC :

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>Accédez aux journaux côté serveur

La façon dont vous accédez aux journaux côté serveur dépend de l’environnement dans lequel vous exécutez.

#### <a name="as-a-console-app"></a>En tant qu’application console

Si vous êtes en cours d’exécution dans une application console, le [bûcheron Console](xref:fundamentals/logging/index#console-provider) doit être activé par défaut. les journaux gRPC apparaîtront dans la console.

#### <a name="other-environments"></a>Autres environnements

Si l’application est déployée dans un autre environnement (par exemple, <xref:fundamentals/logging/index> Docker, Kubernetes ou Windows Service), consultez pour plus d’informations sur la façon de configurer les fournisseurs d’enregistrement adaptés à l’environnement.

### <a name="grpc-client-logging"></a>gRPC enregistrement client

> [!WARNING]
> Les journaux côté client peuvent contenir des informations sensibles à partir de votre application. **Ne publiez jamais** de journaux bruts depuis les applications de production jusqu’aux forums publics comme GitHub.

Pour obtenir des journaux auprès du client `GrpcChannelOptions.LoggerFactory` .NET, vous pouvez définir la propriété lorsque le canal du client est créé. Si vous appelez un service gRPC à partir d’une application ASP.NET Core, alors l’usine de bûcherons peut être résolue à partir de l’injection de dépendance (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Une autre façon de permettre l’enregistrement des clients est d’utiliser [l’usine cliente gRPC](xref:grpc/clientfactory) pour créer le client. Un client gRPC enregistré auprès de l’usine client et résolu à partir de DI utilisera automatiquement la connexion configurée de l’application.

Si votre application n’utilise pas DI, `ILoggerFactory` vous pouvez créer une nouvelle instance avec [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Pour accéder à cette méthode, ajoutez le package [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) à votre application.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>portées de journal des clients gRPC

Le client gRPC ajoute une [portée d’enregistrement](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) aux journaux effectués lors d’un appel gRPC. La portée a métadonnées liées à l’appel gRPC:

* **GrpcMethodType** - Le type de méthode gRPC. Les valeurs possibles `Grpc.Core.MethodType` sont des noms de l’enum, par exemple Unary
* **GrpcUri** - L’URI relatif de la méthode gRPC, par exemple /greet. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Exemple de sortie de la journalisation

Voici un exemple de sortie `Debug` de console au niveau d’un client gRPC :

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>Traçage

gRPC services et le client gRPC fournir des informations sur les appels gRPC en utilisant [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) et [Activité](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).

* .NET gRPC utilise une activité pour représenter un appel gRPC.
* Les événements de traçage sont écrits à la source diagnostique au début et à l’arrêt de l’activité d’appel gRPC.
* Le traçage ne capture pas d’informations sur le moment où les messages sont envoyés au cours de la durée de vie des appels en streaming gRPC.

### <a name="grpc-service-tracing"></a>traçage du service gRPC

les services gRPC sont hébergés sur ASP.NET Core qui signale les événements concernant les demandes HTTP entrantes. les métadonnées spécifiques de gRPC sont ajoutées aux diagnostics de demande HTTP existants que ASP.NET Core fournit.

* Nom source `Microsoft.AspNetCore`diagnostique est .
* Nom d’activité est `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * Nom de la méthode gRPC invoquée par l’appel gRPC est ajouté comme une étiquette avec le nom `grpc.method`.
  * Code d’état de l’appel gRPC lorsqu’il `grpc.status_code`est complet est ajouté comme une balise avec le nom .

### <a name="grpc-client-tracing"></a>recherche de clients gRPC

Le client .NET gRPC utilise `HttpClient` pour faire des appels gRPC. Bien `HttpClient` qu’il écrit des événements diagnostiques, le client .NET gRPC fournit une source de diagnostic personnalisée, une activité et des événements afin que des informations complètes sur un appel gRPC puissent être recueillies.

* Nom source `Grpc.Net.Client`diagnostique est .
* Nom d’activité est `Grpc.Net.Client.GrpcOut`.
  * Nom de la méthode gRPC invoquée par l’appel gRPC est ajouté comme une étiquette avec le nom `grpc.method`.
  * Code d’état de l’appel gRPC lorsqu’il `grpc.status_code`est complet est ajouté comme une balise avec le nom .

### <a name="collecting-tracing"></a>Collecte de traçage

La façon la `DiagnosticSource` plus simple d’utiliser est de configurer une bibliothèque de télémétrie comme [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) ou [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) dans votre application. La bibliothèque traitera des informations sur les appels gRPC le long d’autres télémétries d’applications.

Le traçage peut être consulté dans un service géré comme Application Insights, ou vous pouvez choisir d’exécuter votre propre système de traçage distribué. OpenTelemetry prend en charge l’exportation de données de traçage vers [Jaeger](https://www.jaegertracing.io/) et [Zipkin](https://zipkin.io/).

`DiagnosticSource`peut consommer des événements `DiagnosticListener`de traçage dans le code à l’aide de . Pour plus d’informations sur l’écoute d’une source diagnostique avec du code, consultez le [guide de l’utilisateur DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Les bibliothèques de télémétrie ne `Grpc.Net.Client.GrpcOut` capturent pas actuellement la télémétrie spécifique à gRPC. Des travaux visant à améliorer les bibliothèques de télémétrie qui capturent ce tracé sont en cours.

## <a name="metrics"></a>Mesures

Les mesures sont une représentation des mesures de données sur des intervalles de temps, par exemple, des demandes par seconde. Les données de métriques permettent d’observer l’état d’une application à un niveau élevé. .NET gRPC mesures sont `EventCounter`émis à l’aide .

### <a name="grpc-service-metrics"></a>mesures de service gRPC

les mesures du serveur gRPC sont signalées sur `Grpc.AspNetCore.Server` la source de l’événement.

| Nom                      | Description                   |
| --------------------------|-------------------------------|
| `total-calls`             | Nombre total d’appels                   |
| `current-calls`           | Appels actuels                 |
| `calls-failed`            | Nombre total d’appels échoués            |
| `calls-deadline-exceeded` | Délai total des appels dépassé |
| `messages-sent`           | Nombre total de messages envoyés           |
| `messages-received`       | Total des messages reçus       |
| `calls-unimplemented`     | Total des appels non exaérés     |

ASP.NET Core fournit également ses propres `Microsoft.AspNetCore.Hosting` mesures sur la source de l’événement.

### <a name="grpc-client-metrics"></a>mesures des clients gRPC

les mesures des clients gRPC sont indiquées sur `Grpc.Net.Client` la source de l’événement.

| Nom                      | Description                   |
| --------------------------|-------------------------------|
| `total-calls`             | Nombre total d’appels                   |
| `current-calls`           | Appels actuels                 |
| `calls-failed`            | Nombre total d’appels échoués            |
| `calls-deadline-exceeded` | Délai total des appels dépassé |
| `messages-sent`           | Nombre total de messages envoyés           |
| `messages-received`       | Total des messages reçus       |

### <a name="observe-metrics"></a>Observer les mesures

[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) est un outil de surveillance des performances pour la surveillance de la santé ad hoc et l’enquête de premier niveau sur le rendement. Surveillez une application `Grpc.AspNetCore.Server` .NET avec ou `Grpc.Net.Client` comme nom du fournisseur.

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

Une autre façon d’observer les mesures gRPC est de capturer les données de compteur à l’aide de [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters). Une fois la configuration, Application Insights collecte des compteurs .NET courants au moment de l’exécution. les compteurs de gRPC ne sont pas collectés par défaut, mais App Insights peut être [personnalisé pour inclure des compteurs supplémentaires](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Spécifier les compteurs gRPC pour Application Insight à collecter dans *Startup.cs*:

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
