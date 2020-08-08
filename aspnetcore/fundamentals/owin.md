---
title: OWIN (Open Web Interface for .NET) avec ASP.NET Core
author: ardalis
description: Découvrez comment ASP.NET Core prend en charge OWIN (Open Web Interface pour .NET), ce qui permet aux applications web d’être dissociées des serveurs web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/owin
ms.openlocfilehash: e6069e5051530e88c43d99758d891b53c9f35233
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016893"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>OWIN (Open Web Interface for .NET) avec ASP.NET Core

Par [Steve Smith](https://ardalis.com/) et [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core prend en charge l’interface Open Web pour .NET (OWIN). OWIN permet que les applications web soient dissociées des serveurs web. Elle définit une façon standard d’utiliser un intergiciel (middleware) dans un pipeline pour gérer les requêtes et les réponses associées. Les applications ASP.NET Core et l’intergiciel peuvent interagir avec les applications OWIN, les serveurs et l’intergiciel.

OWIN fournit une couche de dissociation qui permet à deux frameworks ayant des modèles d’objets distincts d’être utilisés ensemble. Le package `Microsoft.AspNetCore.Owin` fournit deux implémentations d’adaptateurs :

* ASP.NET Core sur OWIN 
* OWIN sur ASP.NET Core

Cela permet à ASP.NET Core d’être hébergé sur un serveur/hôte compatible OWIN, ou à d’autres composants compatibles OWIN d’être exécutés sur ASP.NET Core.

> [!NOTE]
> L’utilisation de ces adaptateurs a un impact sur les performances. Les applications utilisant uniquement des composants ASP.NET Core ne doivent pas utiliser les adaptateurs ou le package `Microsoft.AspNetCore.Owin`.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Exécution de l’intergiciel (middleware) OWIN dans le pipeline ASP.NET Core

La prise en charge de l’interface OWIN d’ASP.NET Core est déployée dans le cadre du package `Microsoft.AspNetCore.Owin`. Vous pouvez importer la prise en charge d’OWIN dans votre projet en installant ce package.

L’intergiciel OWIN est conforme à la [spécification OWIN](https://owin.org/spec/spec/owin-1.0.0.html), laquelle exige une interface `Func<IDictionary<string, object>, Task>` et la définition de clés spécifiques (comme `owin.ResponseBody`). L’intergiciel OWIN simple suivant affiche « Hello World » :

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

L’exemple de signature retourne un `Task` et accepte un `IDictionary<string, object>`, comme l’exige OWIN.

Le code suivant montre comment ajouter le middleware `OwinHello` (présenté ci-dessus) au pipeline ASP.NET Core avec la méthode d’extension `UseOwin`.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Vous pouvez configurer d’autres actions pour qu’elles soient effectuées dans le pipeline OWIN.

> [!NOTE]
> Les en-têtes des réponses doivent être modifiés uniquement avant la première écriture dans le flux de réponse.

> [!NOTE]
> Pour des raisons de performance, il est déconseillé d’effectuer plusieurs appels à `UseOwin`. Les composants OWIN fonctionnent mieux s’ils sont regroupés.

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

## <a name="using-aspnet-core-hosting-on-an-owin-based-server"></a>Utilisation de l’hébergement ASP.NET Core sur un serveur OWIN

Les serveurs OWIN peuvent héberger des applications ASP.NET Core. [Nowin](https://github.com/Bobris/Nowin), serveur web OWIN .NET, est l’un de ces serveurs. Dans l’exemple de cet article, j’ai inclus un projet qui fait référence à Nowin et qui l’utilise pour créer un `IServer` capable d’auto-héberger ASP.NET Core.

[!code-csharp[](owin/sample/src/NowinSample/Program.cs?highlight=15)]

`IServer` est une interface qui nécessite une propriété `Features` et une méthode `Start`.

`Start` est chargé de configurer et de démarrer le serveur, ce qui, dans le cas présent, s’effectue à l’aide d’une série d’appels d’API Fluent qui définissent des adresses analysées à partir d’IServerAddressesFeature. Notez que la configuration Fluent de la variable `_builder` spécifie que les requêtes seront traitées par le `appFunc` défini précédemment dans la méthode. Ce `Func` est appelé sur chaque requête pour traiter les requêtes entrantes.

Nous allons également ajouter une extension `IWebHostBuilder` pour faciliter l’ajout et la configuration du serveur Nowin.

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

Ceci étant en place, appelez l’extension dans *Program.cs* pour exécuter une application ASP.NET Core avec ce serveur personnalisé :

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

Découvrez des informations supplémentaires sur les [serveurs ASP.NET Core](xref:fundamentals/servers/index).

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Exécuter ASP.NET Core sur un serveur OWIN et utiliser sa prise en charge des WebSockets

L’accès à des fonctionnalités comme les WebSockets constitue un autre exemple de la façon dont les fonctionnalités de serveurs OWIN peuvent être exploitées par ASP.NET Core. Le serveur web .NET OWIN utilisé dans l’exemple précédent prend en charge les WebSockets intégrés, qui peuvent être exploités par une application ASP.NET Core. L’exemple ci-dessous montre une application web simple qui prend en charge les WebSockets et renvoie tout ce qui est envoyé au serveur via des WebSockets.

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

Cet [exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) est configuré avec le même `NowinServer` que le précédent ; la seule différence réside dans la façon dont l’application est configurée dans sa méthode `Configure`. Un test utilisant [un client WebSocket simple](https://chrome.google.com/webstore/detail/simple-websocket-client/pfdhoblngboilpfeibdedpjgfnlcodoo?hl=en) présente l’application :

![Client test WebSocket](owin/_static/websocket-test.png)

## <a name="owin-environment"></a>Environnement OWIN

Vous pouvez construire un environnement OWIN avec le `HttpContext`.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>Clés OWIN

OWIN dépend d’un objet `IDictionary<string,object>` pour communiquer des informations tout au long d’un échange Requête HTTP/Réponse. ASP.NET Core implémente les clés répertoriées ci-dessous. Consultez [la spécification principale, les extensions](https://owin.org/#spec) et les [recommandations relatives aux clés OWIN et aux clés communes](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Données de requête (OWIN v1.0.0)

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.RequestScheme | `String` |  |
| owin.RequestMethod  | `String` | |    
| owin.RequestPathBase  | `String` | |    
| owin.RequestPath | `String` | |     
| owin.RequestQueryString  | `String` | |    
| owin.RequestProtocol  | `String` | |    
| owin.RequestHeaders | `IDictionary<string,string[]>`  | |
| owin.RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>Données de requête (OWIN v1.1.0)

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.RequestId | `String` | Facultatif |

### <a name="response-data-owin-v100"></a>Données de réponse (OWIN v1.0.0)

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.ResponseStatusCode | `int` | Facultatif |
| owin.ResponseReasonPhrase | `String` | Facultatif |
| owin.ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin.ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>Autres données (OWIN v1.0.0)

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| owin.CallCancelled | `CancellationToken` |  |
| owin.Version  | `String` | |   

### <a name="common-keys"></a>Clés communes

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| ssl.ClientCertificate | `X509Certificate` |  |
| ssl.LoadClientCertAsync  | `Func<Task>` | |    
| server.RemoteIpAddress  | `String` | |    
| server.RemotePort | `String` | |     
| server.LocalIpAddress  | `String` | |    
| server.LocalPort  | `String` | |    
| server.IsLocal  | `bool` | |    
| server.OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| sendfile.SendAsync | Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Par requête |

### <a name="opaque-v030"></a>Opaque v0.3.0

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| opaque.Version | `String` |  |
| opaque.Upgrade | `OpaqueUpgrade` | Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| opaque.Stream | `Stream` |  |
| opaque.CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Clé :               | Valeur (type) | Description |
| ----------------- | ------------ | ----------- |
| websocket.Version | `String` |  |
| websocket.Accept | `WebSocketAccept` | Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket.AcceptAlt |  | Non spécifiée |
| websocket.SubProtocol | `String` | Voir l’étape 5.5 de la [RFC 6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) |
| websocket.SendAsync | `WebSocketSendAsync` | Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.ReceiveAsync | `WebSocketReceiveAsync` | Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CloseAsync | `WebSocketCloseAsync` | Voir [Signature du délégué](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CallCancelled | `CancellationToken` |  |
| websocket.ClientCloseStatus | `int` | Facultatif |
| websocket.ClientCloseDescription | `String` | Facultatif |

## <a name="additional-resources"></a>Ressources complémentaires

* [Middleware](xref:fundamentals/middleware/index)
* [Serveurs](xref:fundamentals/servers/index)
