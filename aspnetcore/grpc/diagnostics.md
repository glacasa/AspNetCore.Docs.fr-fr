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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="e7673-103">L’exploitation forestière et le diagnostic dans gRPC sur .NET</span><span class="sxs-lookup"><span data-stu-id="e7673-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="e7673-104">Par [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e7673-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e7673-105">Cet article fournit des conseils pour la collecte des diagnostics à partir d’une application gRPC pour aider les problèmes de dépannage.</span><span class="sxs-lookup"><span data-stu-id="e7673-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="e7673-106">Sont abordés les sujets suivants :</span><span class="sxs-lookup"><span data-stu-id="e7673-106">Topics covered include:</span></span>

* <span data-ttu-id="e7673-107">**Enregistrement** - Journaux structurés écrits à [.NET Core logging](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e7673-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e7673-108"><xref:Microsoft.Extensions.Logging.ILogger>est utilisé par les cadres d’applications pour écrire des journaux, et par les utilisateurs pour leur propre connexion dans une application.</span><span class="sxs-lookup"><span data-stu-id="e7673-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="e7673-109">**Traçage** - Événements liés `DiaganosticSource` à `Activity`une opération écrite à l’aide et .</span><span class="sxs-lookup"><span data-stu-id="e7673-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="e7673-110">Les traces provenant de sources diagnostiques sont couramment utilisées pour recueillir la télémétrie des applications par des bibliothèques telles que [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) et [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="e7673-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="e7673-111">**Mesures** - Représentation des mesures de données sur des intervalles de temps, par exemple, des demandes par seconde.</span><span class="sxs-lookup"><span data-stu-id="e7673-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="e7673-112">Les mesures sont `EventCounter` émises à l’aide et peuvent être observées à l’aide d’un outil de ligne de commande [à compteurs dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ou avec [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="e7673-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="e7673-113">Journalisation</span><span class="sxs-lookup"><span data-stu-id="e7673-113">Logging</span></span>

<span data-ttu-id="e7673-114">gRPC services et le client gRPC écrire des journaux en utilisant [l’enregistrement .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e7673-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e7673-115">Les journaux sont un bon endroit pour commencer lorsque vous avez besoin de débocher un comportement inattendu dans vos applications.</span><span class="sxs-lookup"><span data-stu-id="e7673-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="e7673-116">gRPC services d’enregistrement</span><span class="sxs-lookup"><span data-stu-id="e7673-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="e7673-117">Les journaux côté serveur peuvent contenir des informations sensibles à partir de votre application.</span><span class="sxs-lookup"><span data-stu-id="e7673-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e7673-118">**Ne publiez jamais** de journaux bruts depuis les applications de production jusqu’aux forums publics comme GitHub.</span><span class="sxs-lookup"><span data-stu-id="e7673-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e7673-119">Étant donné que les services gRPC sont hébergés sur ASP.NET Core, il utilise le système d’enregistrement ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7673-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="e7673-120">Dans la configuration par défaut, gRPC enregistre très peu d’informations, mais cela peut être configuré.</span><span class="sxs-lookup"><span data-stu-id="e7673-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="e7673-121">Voir la documentation sur [ASP.NET connexion de base](xref:fundamentals/logging/index#configuration) pour plus de détails sur la configuration ASP.NET l’enregistrement de base.</span><span class="sxs-lookup"><span data-stu-id="e7673-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="e7673-122">gRPC ajoute des `Grpc` journaux dans la catégorie.</span><span class="sxs-lookup"><span data-stu-id="e7673-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="e7673-123">Pour activer les journaux détaillés `Grpc` à partir de `Debug` gRPC, configurez les préfixes au niveau `LogLevel` de votre `Logging`fichier *appsettings.json* en ajoutant les éléments suivants à la sous-section dans :</span><span class="sxs-lookup"><span data-stu-id="e7673-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="e7673-124">Vous pouvez également *Startup.cs* configurer cela `ConfigureLogging`dans Startup.cs avec :</span><span class="sxs-lookup"><span data-stu-id="e7673-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="e7673-125">Si vous n’utilisez pas la configuration basée sur JSON, définissez la valeur de configuration suivante dans votre système de configuration :</span><span class="sxs-lookup"><span data-stu-id="e7673-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="e7673-126">Vérifiez la documentation de votre système de configuration pour déterminer comment spécifier les valeurs de configuration imbriquées.</span><span class="sxs-lookup"><span data-stu-id="e7673-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="e7673-127">Par exemple, lorsque vous `_` utilisez des variables `:` de l’environnement, deux caractères sont utilisés au lieu de la (par exemple, `Logging__LogLevel__Grpc`).</span><span class="sxs-lookup"><span data-stu-id="e7673-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="e7673-128">Nous vous `Debug` recommandons d’utiliser le niveau lors de la collecte de diagnostics plus détaillés pour votre application.</span><span class="sxs-lookup"><span data-stu-id="e7673-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="e7673-129">Le `Trace` niveau produit des diagnostics de très bas niveau et est rarement nécessaire pour diagnostiquer les problèmes dans votre application.</span><span class="sxs-lookup"><span data-stu-id="e7673-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="e7673-130">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="e7673-130">Sample logging output</span></span>

<span data-ttu-id="e7673-131">Voici un exemple de sortie `Debug` de console au niveau d’un service gRPC :</span><span class="sxs-lookup"><span data-stu-id="e7673-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="e7673-132">Accédez aux journaux côté serveur</span><span class="sxs-lookup"><span data-stu-id="e7673-132">Access server-side logs</span></span>

<span data-ttu-id="e7673-133">La façon dont vous accédez aux journaux côté serveur dépend de l’environnement dans lequel vous exécutez.</span><span class="sxs-lookup"><span data-stu-id="e7673-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="e7673-134">En tant qu’application console</span><span class="sxs-lookup"><span data-stu-id="e7673-134">As a console app</span></span>

<span data-ttu-id="e7673-135">Si vous êtes en cours d’exécution dans une application console, le [bûcheron Console](xref:fundamentals/logging/index#console-provider) doit être activé par défaut.</span><span class="sxs-lookup"><span data-stu-id="e7673-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="e7673-136">les journaux gRPC apparaîtront dans la console.</span><span class="sxs-lookup"><span data-stu-id="e7673-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="e7673-137">Autres environnements</span><span class="sxs-lookup"><span data-stu-id="e7673-137">Other environments</span></span>

<span data-ttu-id="e7673-138">Si l’application est déployée dans un autre environnement (par exemple, <xref:fundamentals/logging/index> Docker, Kubernetes ou Windows Service), consultez pour plus d’informations sur la façon de configurer les fournisseurs d’enregistrement adaptés à l’environnement.</span><span class="sxs-lookup"><span data-stu-id="e7673-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="e7673-139">gRPC enregistrement client</span><span class="sxs-lookup"><span data-stu-id="e7673-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e7673-140">Les journaux côté client peuvent contenir des informations sensibles à partir de votre application.</span><span class="sxs-lookup"><span data-stu-id="e7673-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e7673-141">**Ne publiez jamais** de journaux bruts depuis les applications de production jusqu’aux forums publics comme GitHub.</span><span class="sxs-lookup"><span data-stu-id="e7673-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e7673-142">Pour obtenir des journaux auprès du client `GrpcChannelOptions.LoggerFactory` .NET, vous pouvez définir la propriété lorsque le canal du client est créé.</span><span class="sxs-lookup"><span data-stu-id="e7673-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="e7673-143">Si vous appelez un service gRPC à partir d’une application ASP.NET Core, alors l’usine de bûcherons peut être résolue à partir de l’injection de dépendance (DI):</span><span class="sxs-lookup"><span data-stu-id="e7673-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="e7673-144">Une autre façon de permettre l’enregistrement des clients est d’utiliser [l’usine cliente gRPC](xref:grpc/clientfactory) pour créer le client.</span><span class="sxs-lookup"><span data-stu-id="e7673-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="e7673-145">Un client gRPC enregistré auprès de l’usine client et résolu à partir de DI utilisera automatiquement la connexion configurée de l’application.</span><span class="sxs-lookup"><span data-stu-id="e7673-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="e7673-146">Si votre application n’utilise pas DI, `ILoggerFactory` vous pouvez créer une nouvelle instance avec [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="e7673-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="e7673-147">Pour accéder à cette méthode, ajoutez le package [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) à votre application.</span><span class="sxs-lookup"><span data-stu-id="e7673-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="e7673-148">portées de journal des clients gRPC</span><span class="sxs-lookup"><span data-stu-id="e7673-148">gRPC client log scopes</span></span>

<span data-ttu-id="e7673-149">Le client gRPC ajoute une [portée d’enregistrement](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) aux journaux effectués lors d’un appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7673-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="e7673-150">La portée a métadonnées liées à l’appel gRPC:</span><span class="sxs-lookup"><span data-stu-id="e7673-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="e7673-151">**GrpcMethodType** - Le type de méthode gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7673-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="e7673-152">Les valeurs possibles `Grpc.Core.MethodType` sont des noms de l’enum, par exemple Unary</span><span class="sxs-lookup"><span data-stu-id="e7673-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="e7673-153">**GrpcUri** - L’URI relatif de la méthode gRPC, par exemple /greet. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="e7673-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="e7673-154">Exemple de sortie de la journalisation</span><span class="sxs-lookup"><span data-stu-id="e7673-154">Sample logging output</span></span>

<span data-ttu-id="e7673-155">Voici un exemple de sortie `Debug` de console au niveau d’un client gRPC :</span><span class="sxs-lookup"><span data-stu-id="e7673-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="e7673-156">Traçage</span><span class="sxs-lookup"><span data-stu-id="e7673-156">Tracing</span></span>

<span data-ttu-id="e7673-157">gRPC services et le client gRPC fournir des informations sur les appels gRPC en utilisant [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) et [Activité](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="e7673-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="e7673-158">.NET gRPC utilise une activité pour représenter un appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7673-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="e7673-159">Les événements de traçage sont écrits à la source diagnostique au début et à l’arrêt de l’activité d’appel gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7673-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="e7673-160">Le traçage ne capture pas d’informations sur le moment où les messages sont envoyés au cours de la durée de vie des appels en streaming gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7673-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="e7673-161">traçage du service gRPC</span><span class="sxs-lookup"><span data-stu-id="e7673-161">gRPC service tracing</span></span>

<span data-ttu-id="e7673-162">les services gRPC sont hébergés sur ASP.NET Core qui signale les événements concernant les demandes HTTP entrantes.</span><span class="sxs-lookup"><span data-stu-id="e7673-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="e7673-163">les métadonnées spécifiques de gRPC sont ajoutées aux diagnostics de demande HTTP existants que ASP.NET Core fournit.</span><span class="sxs-lookup"><span data-stu-id="e7673-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="e7673-164">Nom source `Microsoft.AspNetCore`diagnostique est .</span><span class="sxs-lookup"><span data-stu-id="e7673-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="e7673-165">Nom d’activité est `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="e7673-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="e7673-166">Nom de la méthode gRPC invoquée par l’appel gRPC est ajouté comme une étiquette avec le nom `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="e7673-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="e7673-167">Code d’état de l’appel gRPC lorsqu’il `grpc.status_code`est complet est ajouté comme une balise avec le nom .</span><span class="sxs-lookup"><span data-stu-id="e7673-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="e7673-168">recherche de clients gRPC</span><span class="sxs-lookup"><span data-stu-id="e7673-168">gRPC client tracing</span></span>

<span data-ttu-id="e7673-169">Le client .NET gRPC utilise `HttpClient` pour faire des appels gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7673-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="e7673-170">Bien `HttpClient` qu’il écrit des événements diagnostiques, le client .NET gRPC fournit une source de diagnostic personnalisée, une activité et des événements afin que des informations complètes sur un appel gRPC puissent être recueillies.</span><span class="sxs-lookup"><span data-stu-id="e7673-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="e7673-171">Nom source `Grpc.Net.Client`diagnostique est .</span><span class="sxs-lookup"><span data-stu-id="e7673-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="e7673-172">Nom d’activité est `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="e7673-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="e7673-173">Nom de la méthode gRPC invoquée par l’appel gRPC est ajouté comme une étiquette avec le nom `grpc.method`.</span><span class="sxs-lookup"><span data-stu-id="e7673-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="e7673-174">Code d’état de l’appel gRPC lorsqu’il `grpc.status_code`est complet est ajouté comme une balise avec le nom .</span><span class="sxs-lookup"><span data-stu-id="e7673-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="e7673-175">Collecte de traçage</span><span class="sxs-lookup"><span data-stu-id="e7673-175">Collecting tracing</span></span>

<span data-ttu-id="e7673-176">La façon la `DiagnosticSource` plus simple d’utiliser est de configurer une bibliothèque de télémétrie comme [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) ou [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) dans votre application.</span><span class="sxs-lookup"><span data-stu-id="e7673-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="e7673-177">La bibliothèque traitera des informations sur les appels gRPC le long d’autres télémétries d’applications.</span><span class="sxs-lookup"><span data-stu-id="e7673-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="e7673-178">Le traçage peut être consulté dans un service géré comme Application Insights, ou vous pouvez choisir d’exécuter votre propre système de traçage distribué.</span><span class="sxs-lookup"><span data-stu-id="e7673-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="e7673-179">OpenTelemetry prend en charge l’exportation de données de traçage vers [Jaeger](https://www.jaegertracing.io/) et [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="e7673-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="e7673-180">`DiagnosticSource`peut consommer des événements `DiagnosticListener`de traçage dans le code à l’aide de .</span><span class="sxs-lookup"><span data-stu-id="e7673-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="e7673-181">Pour plus d’informations sur l’écoute d’une source diagnostique avec du code, consultez le [guide de l’utilisateur DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="e7673-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="e7673-182">Les bibliothèques de télémétrie ne `Grpc.Net.Client.GrpcOut` capturent pas actuellement la télémétrie spécifique à gRPC.</span><span class="sxs-lookup"><span data-stu-id="e7673-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="e7673-183">Des travaux visant à améliorer les bibliothèques de télémétrie qui capturent ce tracé sont en cours.</span><span class="sxs-lookup"><span data-stu-id="e7673-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="e7673-184">Mesures</span><span class="sxs-lookup"><span data-stu-id="e7673-184">Metrics</span></span>

<span data-ttu-id="e7673-185">Les mesures sont une représentation des mesures de données sur des intervalles de temps, par exemple, des demandes par seconde.</span><span class="sxs-lookup"><span data-stu-id="e7673-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="e7673-186">Les données de métriques permettent d’observer l’état d’une application à un niveau élevé.</span><span class="sxs-lookup"><span data-stu-id="e7673-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="e7673-187">.NET gRPC mesures sont `EventCounter`émis à l’aide .</span><span class="sxs-lookup"><span data-stu-id="e7673-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="e7673-188">mesures de service gRPC</span><span class="sxs-lookup"><span data-stu-id="e7673-188">gRPC service metrics</span></span>

<span data-ttu-id="e7673-189">les mesures du serveur gRPC sont signalées sur `Grpc.AspNetCore.Server` la source de l’événement.</span><span class="sxs-lookup"><span data-stu-id="e7673-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="e7673-190">Nom</span><span class="sxs-lookup"><span data-stu-id="e7673-190">Name</span></span>                      | <span data-ttu-id="e7673-191">Description</span><span class="sxs-lookup"><span data-stu-id="e7673-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="e7673-192">Nombre total d’appels</span><span class="sxs-lookup"><span data-stu-id="e7673-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="e7673-193">Appels actuels</span><span class="sxs-lookup"><span data-stu-id="e7673-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="e7673-194">Nombre total d’appels échoués</span><span class="sxs-lookup"><span data-stu-id="e7673-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="e7673-195">Délai total des appels dépassé</span><span class="sxs-lookup"><span data-stu-id="e7673-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="e7673-196">Nombre total de messages envoyés</span><span class="sxs-lookup"><span data-stu-id="e7673-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="e7673-197">Total des messages reçus</span><span class="sxs-lookup"><span data-stu-id="e7673-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="e7673-198">Total des appels non exaérés</span><span class="sxs-lookup"><span data-stu-id="e7673-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="e7673-199">ASP.NET Core fournit également ses propres `Microsoft.AspNetCore.Hosting` mesures sur la source de l’événement.</span><span class="sxs-lookup"><span data-stu-id="e7673-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="e7673-200">mesures des clients gRPC</span><span class="sxs-lookup"><span data-stu-id="e7673-200">gRPC client metrics</span></span>

<span data-ttu-id="e7673-201">les mesures des clients gRPC sont indiquées sur `Grpc.Net.Client` la source de l’événement.</span><span class="sxs-lookup"><span data-stu-id="e7673-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="e7673-202">Nom</span><span class="sxs-lookup"><span data-stu-id="e7673-202">Name</span></span>                      | <span data-ttu-id="e7673-203">Description</span><span class="sxs-lookup"><span data-stu-id="e7673-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="e7673-204">Nombre total d’appels</span><span class="sxs-lookup"><span data-stu-id="e7673-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="e7673-205">Appels actuels</span><span class="sxs-lookup"><span data-stu-id="e7673-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="e7673-206">Nombre total d’appels échoués</span><span class="sxs-lookup"><span data-stu-id="e7673-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="e7673-207">Délai total des appels dépassé</span><span class="sxs-lookup"><span data-stu-id="e7673-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="e7673-208">Nombre total de messages envoyés</span><span class="sxs-lookup"><span data-stu-id="e7673-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="e7673-209">Total des messages reçus</span><span class="sxs-lookup"><span data-stu-id="e7673-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="e7673-210">Observer les mesures</span><span class="sxs-lookup"><span data-stu-id="e7673-210">Observe metrics</span></span>

<span data-ttu-id="e7673-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) est un outil de surveillance des performances pour la surveillance de la santé ad hoc et l’enquête de premier niveau sur le rendement.</span><span class="sxs-lookup"><span data-stu-id="e7673-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="e7673-212">Surveillez une application `Grpc.AspNetCore.Server` .NET avec ou `Grpc.Net.Client` comme nom du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="e7673-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="e7673-213">Une autre façon d’observer les mesures gRPC est de capturer les données de compteur à l’aide de [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="e7673-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="e7673-214">Une fois la configuration, Application Insights collecte des compteurs .NET courants au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e7673-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="e7673-215">les compteurs de gRPC ne sont pas collectés par défaut, mais App Insights peut être [personnalisé pour inclure des compteurs supplémentaires](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="e7673-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="e7673-216">Spécifier les compteurs gRPC pour Application Insight à collecter dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="e7673-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e7673-217">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e7673-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
