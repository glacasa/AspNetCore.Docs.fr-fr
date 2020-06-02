---
title: OWIN (Open Web Interface for .NET) avec ASP.NET Core
author: ardalis
description: Découvrez comment ASP.NET Core prend en charge OWIN (Open Web Interface pour .NET), ce qui permet aux applications web d’être dissociées des serveurs web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: defbf428634a04e6fa3862926136dc98b88da589
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775958"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="8fa99-103">OWIN (Open Web Interface for .NET) avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8fa99-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="8fa99-104">Par [Steve Smith](https://ardalis.com/) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8fa99-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8fa99-105">ASP.NET Core prend en charge l’interface Open Web pour .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="8fa99-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="8fa99-106">OWIN permet que les applications web soient dissociées des serveurs web.</span><span class="sxs-lookup"><span data-stu-id="8fa99-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="8fa99-107">Elle définit une façon standard d’utiliser un intergiciel (middleware) dans un pipeline pour gérer les requêtes et les réponses associées.</span><span class="sxs-lookup"><span data-stu-id="8fa99-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="8fa99-108">Les applications ASP.NET Core et l’intergiciel peuvent interagir avec les applications OWIN, les serveurs et l’intergiciel.</span><span class="sxs-lookup"><span data-stu-id="8fa99-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="8fa99-109">OWIN fournit une couche de dissociation qui permet à deux frameworks ayant des modèles d’objets distincts d’être utilisés ensemble.</span><span class="sxs-lookup"><span data-stu-id="8fa99-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="8fa99-110">Le package `Microsoft.AspNetCore.Owin` fournit deux implémentations d’adaptateurs :</span><span class="sxs-lookup"><span data-stu-id="8fa99-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="8fa99-111">ASP.NET Core sur OWIN</span><span class="sxs-lookup"><span data-stu-id="8fa99-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="8fa99-112">OWIN sur ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8fa99-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="8fa99-113">Cela permet à ASP.NET Core d’être hébergé sur un serveur/hôte compatible OWIN, ou à d’autres composants compatibles OWIN d’être exécutés sur ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8fa99-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="8fa99-114">L’utilisation de ces adaptateurs a un impact sur les performances.</span><span class="sxs-lookup"><span data-stu-id="8fa99-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="8fa99-115">Les applications utilisant uniquement des composants ASP.NET Core ne doivent pas utiliser les adaptateurs ou le package `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="8fa99-116">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8fa99-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="8fa99-117">Exécution de l’intergiciel (middleware) OWIN dans le pipeline ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8fa99-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="8fa99-118">La prise en charge de l’interface OWIN d’ASP.NET Core est déployée dans le cadre du package `Microsoft.AspNetCore.Owin`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="8fa99-119">Vous pouvez importer la prise en charge d’OWIN dans votre projet en installant ce package.</span><span class="sxs-lookup"><span data-stu-id="8fa99-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="8fa99-120">L’intergiciel OWIN est conforme à la [spécification OWIN](https://owin.org/spec/spec/owin-1.0.0.html), laquelle exige une interface `Func<IDictionary<string, object>, Task>` et la définition de clés spécifiques (comme `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="8fa99-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="8fa99-121">L’intergiciel OWIN simple suivant affiche « Hello World » :</span><span class="sxs-lookup"><span data-stu-id="8fa99-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="8fa99-122">L’exemple de signature retourne un `Task` et accepte un `IDictionary<string, object>`, comme l’exige OWIN.</span><span class="sxs-lookup"><span data-stu-id="8fa99-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="8fa99-123">Le code suivant montre comment ajouter le middleware `OwinHello` (présenté ci-dessus) au pipeline ASP.NET Core avec la méthode d’extension `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="8fa99-124">Vous pouvez configurer d’autres actions pour qu’elles soient effectuées dans le pipeline OWIN.</span><span class="sxs-lookup"><span data-stu-id="8fa99-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="8fa99-125">Les en-têtes des réponses doivent être modifiés uniquement avant la première écriture dans le flux de réponse.</span><span class="sxs-lookup"><span data-stu-id="8fa99-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="8fa99-126">Pour des raisons de performance, il est déconseillé d’effectuer plusieurs appels à `UseOwin`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="8fa99-127">Les composants OWIN fonctionnent mieux s’ils sont regroupés.</span><span class="sxs-lookup"><span data-stu-id="8fa99-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a><span data-ttu-id="8fa99-128">Utilisation de l’hébergement ASP.NET Core sur un serveur OWIN</span><span class="sxs-lookup"><span data-stu-id="8fa99-128">Using ASP.NET Core Hosting on an OWIN-based server</span></span>

<span data-ttu-id="8fa99-129">Les serveurs OWIN peuvent héberger des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8fa99-129">OWIN-based servers can host ASP.NET Core apps.</span></span> <span data-ttu-id="8fa99-130">[Nowin](https://github.com/Bobris/Nowin), serveur web OWIN .NET, est l’un de ces serveurs.</span><span class="sxs-lookup"><span data-stu-id="8fa99-130">One such server is [Nowin](https://github.com/Bobris/Nowin), a .NET OWIN web server.</span></span> <span data-ttu-id="8fa99-131">Dans l’exemple de cet article, j’ai inclus un projet qui fait référence à Nowin et qui l’utilise pour créer un `IServer` capable d’auto-héberger ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8fa99-131">In the sample for this article, I've included a project that references Nowin and uses it to create an `IServer` capable of self-hosting ASP.NET Core.</span></span>

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

<span data-ttu-id="8fa99-132">`IServer` est une interface qui nécessite une propriété `Features` et une méthode `Start`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-132">`IServer` is an interface that requires a `Features` property and a `Start` method.</span></span>

<span data-ttu-id="8fa99-133">`Start` est chargé de configurer et de démarrer le serveur, ce qui, dans le cas présent, s’effectue à l’aide d’une série d’appels d’API Fluent qui définissent des adresses analysées à partir d’IServerAddressesFeature.</span><span class="sxs-lookup"><span data-stu-id="8fa99-133">`Start` is responsible for configuring and starting the server, which in this case is done through a series of fluent API calls that set addresses parsed from the IServerAddressesFeature.</span></span> <span data-ttu-id="8fa99-134">Notez que la configuration Fluent de la variable `_builder` spécifie que les requêtes seront traitées par le `appFunc` défini précédemment dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="8fa99-134">Note that the fluent configuration of the `_builder` variable specifies that requests will be handled by the `appFunc` defined earlier in the method.</span></span> <span data-ttu-id="8fa99-135">Ce `Func` est appelé sur chaque requête pour traiter les requêtes entrantes.</span><span class="sxs-lookup"><span data-stu-id="8fa99-135">This `Func` is called on each request to process incoming requests.</span></span>

<span data-ttu-id="8fa99-136">Nous allons également ajouter une extension `IWebHostBuilder` pour faciliter l’ajout et la configuration du serveur Nowin.</span><span class="sxs-lookup"><span data-stu-id="8fa99-136">We'll also add an `IWebHostBuilder` extension to make it easy to add and configure the Nowin server.</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Hosting.Server;
using Microsoft.Extensions.DependencyInjection;
using Nowin;
using NowinSample;

namespace Microsoft.AspNetCore.Hosting
{
    public static class NowinWebHostBuilderExtensions
    {
        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder)
        {
            return builder.ConfigureServices(services =>
            {
                services.AddSingleton<IServer, NowinServer>();
            });
        }

        public static IWebHostBuilder UseNowin(this IWebHostBuilder builder, Action<ServerBuilder> configure)
        {
            builder.ConfigureServices(services =>
            {
                services.Configure(configure);
            });
            return builder.UseNowin();
        }
    }
}
```

<span data-ttu-id="8fa99-137">Ceci étant en place, appelez l’extension dans *Program.cs* pour exécuter une application ASP.NET Core avec ce serveur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-137">With this in place, invoke the extension in *Program.cs* to run an ASP.NET Core app using this custom server:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;

namespace NowinSample
{
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = new WebHostBuilder()
                .UseNowin()
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseIISIntegration()
                .UseStartup<Startup>()
                .Build();

            host.Run();
        }
    }
}
```

<span data-ttu-id="8fa99-138">Découvrez des informations supplémentaires sur les [serveurs ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="8fa99-138">Learn more about [ASP.NET Core Servers](xref:fundamentals/servers/index).</span></span>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="8fa99-139">Exécuter ASP.NET Core sur un serveur OWIN et utiliser sa prise en charge des WebSockets</span><span class="sxs-lookup"><span data-stu-id="8fa99-139">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="8fa99-140">L’accès à des fonctionnalités comme les WebSockets constitue un autre exemple de la façon dont les fonctionnalités de serveurs OWIN peuvent être exploitées par ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8fa99-140">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="8fa99-141">Le serveur web .NET OWIN utilisé dans l’exemple précédent prend en charge les WebSockets intégrés, qui peuvent être exploités par une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8fa99-141">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="8fa99-142">L’exemple ci-dessous montre une application web simple qui prend en charge les WebSockets et renvoie tout ce qui est envoyé au serveur via des WebSockets.</span><span class="sxs-lookup"><span data-stu-id="8fa99-142">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

<span data-ttu-id="8fa99-143">Cet [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) est configuré avec le même `NowinServer` que le précédent ; la seule différence réside dans la façon dont l’application est configurée dans sa méthode `Configure`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-143">This [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) is configured using the same `NowinServer` as the previous one - the only difference is in how the application is configured in its `Configure` method.</span></span> <span data-ttu-id="8fa99-144">Un test utilisant [un client WebSocket simple](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) présente l’application :</span><span class="sxs-lookup"><span data-stu-id="8fa99-144">A test using [a simple websocket client](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) demonstrates  the application:</span></span>

![Client test WebSocket](owin/_static/websocket-test.png)

## <a name="owin-environment"></a><span data-ttu-id="8fa99-146">Environnement OWIN</span><span class="sxs-lookup"><span data-stu-id="8fa99-146">OWIN environment</span></span>

<span data-ttu-id="8fa99-147">Vous pouvez construire un environnement OWIN avec le `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-147">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="8fa99-148">Clés OWIN</span><span class="sxs-lookup"><span data-stu-id="8fa99-148">OWIN keys</span></span>

<span data-ttu-id="8fa99-149">OWIN dépend d’un objet `IDictionary<string,object>` pour communiquer des informations tout au long d’un échange Requête HTTP/Réponse.</span><span class="sxs-lookup"><span data-stu-id="8fa99-149">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="8fa99-150">ASP.NET Core implémente les clés répertoriées ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="8fa99-150">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="8fa99-151">Consultez [la spécification principale, les extensions](https://owin.org/#spec) et les [recommandations relatives aux clés OWIN et aux clés communes](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="8fa99-151">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="8fa99-152">Données de requête (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="8fa99-152">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="8fa99-153">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-153">Key</span></span>               | <span data-ttu-id="8fa99-154">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-154">Value (type)</span></span> | <span data-ttu-id="8fa99-155">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-156">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="8fa99-156">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="8fa99-157">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="8fa99-157">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="8fa99-158">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="8fa99-158">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="8fa99-159">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="8fa99-159">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="8fa99-160">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="8fa99-160">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="8fa99-161">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="8fa99-161">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="8fa99-162">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="8fa99-162">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="8fa99-163">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="8fa99-163">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="8fa99-164">Données de requête (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="8fa99-164">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="8fa99-165">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-165">Key</span></span>               | <span data-ttu-id="8fa99-166">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-166">Value (type)</span></span> | <span data-ttu-id="8fa99-167">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-167">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-168">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="8fa99-168">owin.RequestId</span></span> | `String` | <span data-ttu-id="8fa99-169">Facultatif</span><span class="sxs-lookup"><span data-stu-id="8fa99-169">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="8fa99-170">Données de réponse (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="8fa99-170">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="8fa99-171">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-171">Key</span></span>               | <span data-ttu-id="8fa99-172">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-172">Value (type)</span></span> | <span data-ttu-id="8fa99-173">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-173">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-174">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="8fa99-174">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="8fa99-175">Facultatif</span><span class="sxs-lookup"><span data-stu-id="8fa99-175">Optional</span></span> |
| <span data-ttu-id="8fa99-176">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="8fa99-176">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="8fa99-177">Facultatif</span><span class="sxs-lookup"><span data-stu-id="8fa99-177">Optional</span></span> |
| <span data-ttu-id="8fa99-178">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="8fa99-178">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="8fa99-179">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="8fa99-179">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="8fa99-180">Autres données (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="8fa99-180">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="8fa99-181">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-181">Key</span></span>               | <span data-ttu-id="8fa99-182">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-182">Value (type)</span></span> | <span data-ttu-id="8fa99-183">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-183">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-184">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="8fa99-184">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="8fa99-185">owin.Version</span><span class="sxs-lookup"><span data-stu-id="8fa99-185">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="8fa99-186">Clés communes</span><span class="sxs-lookup"><span data-stu-id="8fa99-186">Common keys</span></span>

| <span data-ttu-id="8fa99-187">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-187">Key</span></span>               | <span data-ttu-id="8fa99-188">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-188">Value (type)</span></span> | <span data-ttu-id="8fa99-189">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-190">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="8fa99-190">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="8fa99-191">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="8fa99-191">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="8fa99-192">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="8fa99-192">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="8fa99-193">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="8fa99-193">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="8fa99-194">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="8fa99-194">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="8fa99-195">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="8fa99-195">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="8fa99-196">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="8fa99-196">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="8fa99-197">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="8fa99-197">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="8fa99-198">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="8fa99-198">SendFiles v0.3.0</span></span>

| <span data-ttu-id="8fa99-199">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-199">Key</span></span>               | <span data-ttu-id="8fa99-200">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-200">Value (type)</span></span> | <span data-ttu-id="8fa99-201">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-201">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-202">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="8fa99-202">sendfile.SendAsync</span></span> | <span data-ttu-id="8fa99-203">Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="8fa99-203">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="8fa99-204">Par requête</span><span class="sxs-lookup"><span data-stu-id="8fa99-204">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="8fa99-205">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="8fa99-205">Opaque v0.3.0</span></span>

| <span data-ttu-id="8fa99-206">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-206">Key</span></span>               | <span data-ttu-id="8fa99-207">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-207">Value (type)</span></span> | <span data-ttu-id="8fa99-208">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-208">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-209">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="8fa99-209">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="8fa99-210">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="8fa99-210">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="8fa99-211">Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="8fa99-211">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="8fa99-212">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="8fa99-212">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="8fa99-213">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="8fa99-213">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="8fa99-214">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="8fa99-214">WebSocket v0.3.0</span></span>

| <span data-ttu-id="8fa99-215">Clé :</span><span class="sxs-lookup"><span data-stu-id="8fa99-215">Key</span></span>               | <span data-ttu-id="8fa99-216">Valeur (type)</span><span class="sxs-lookup"><span data-stu-id="8fa99-216">Value (type)</span></span> | <span data-ttu-id="8fa99-217">Description</span><span class="sxs-lookup"><span data-stu-id="8fa99-217">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="8fa99-218">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="8fa99-218">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="8fa99-219">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="8fa99-219">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="8fa99-220">Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="8fa99-220">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="8fa99-221">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="8fa99-221">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="8fa99-222">Non spécifiée</span><span class="sxs-lookup"><span data-stu-id="8fa99-222">Non-spec</span></span> |
| <span data-ttu-id="8fa99-223">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="8fa99-223">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="8fa99-224">Voir l’étape 5.5 de la [RFC 6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2)</span><span class="sxs-lookup"><span data-stu-id="8fa99-224">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="8fa99-225">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="8fa99-225">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="8fa99-226">Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="8fa99-226">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="8fa99-227">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="8fa99-227">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="8fa99-228">Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="8fa99-228">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="8fa99-229">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="8fa99-229">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="8fa99-230">Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="8fa99-230">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="8fa99-231">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="8fa99-231">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="8fa99-232">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="8fa99-232">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="8fa99-233">Facultatif</span><span class="sxs-lookup"><span data-stu-id="8fa99-233">Optional</span></span> |
| <span data-ttu-id="8fa99-234">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="8fa99-234">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="8fa99-235">Facultatif</span><span class="sxs-lookup"><span data-stu-id="8fa99-235">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8fa99-236">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8fa99-236">Additional resources</span></span>

* [<span data-ttu-id="8fa99-237">Middleware</span><span class="sxs-lookup"><span data-stu-id="8fa99-237">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="8fa99-238">Serveurs</span><span class="sxs-lookup"><span data-stu-id="8fa99-238">Servers</span></span>](xref:fundamentals/servers/index)
