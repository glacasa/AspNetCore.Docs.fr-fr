---
title: Tester l’intergiciel (middleware) ASP.NET Core
author: tratcher
description: Découvrez comment tester ASP.NET Core intergiciel avec TestServer.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876436"
---
# <a name="test-aspnet-core-middleware"></a>Tester l’intergiciel (middleware) ASP.NET Core

Par [Chris Ross](https://github.com/Tratcher)

L’intergiciel peut être testé en isolation avec <xref:Microsoft.AspNetCore.TestHost.TestServer>. Vous pouvez ainsi :

* Instanciez un pipeline d’application contenant uniquement les composants que vous devez tester.
* Envoyer des demandes personnalisées pour vérifier le comportement de l’intergiciel (middleware).

Avantages :

* Les demandes sont envoyées en mémoire au lieu d’être sérialisées sur le réseau.
* Cela évite des préoccupations supplémentaires, telles que la gestion des ports et les certificats HTTPs.
* Les exceptions dans l’intergiciel (middleware) peuvent être directement transmises au test appelant.
* Il est possible de personnaliser les structures de données du serveur <xref:Microsoft.AspNetCore.Http.HttpContext>, telles que, directement dans le test.

## <a name="set-up-the-testserver"></a>Configurer TestServer

Dans le projet de test, créez un test :

* Créez et démarrez un hôte qui utilise <xref:Microsoft.AspNetCore.TestHost.TestServer>.
* Ajoutez les services requis que l’intergiciel utilise.
* Configurez le pipeline de traitement pour utiliser l’intergiciel (middleware) pour le test.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Envoyer des demandes avec HttpClient
Envoyer une demande à <xref:System.Net.Http.HttpClient>l’aide de :

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Déclarez le résultat. Tout d’abord, faites d’une assertion l’inverse du résultat attendu. Une exécution initiale avec une assertion false positive confirme que le test échoue quand l’intergiciel s’exécute correctement. Exécutez le test et vérifiez que le test échoue.

Dans l’exemple suivant, l’intergiciel (middleware) doit retourner un code d’État 404 (*introuvable*) quand le point de terminaison racine est demandé. Effectuez la première série de tests `Assert.NotEqual( ... );`avec, qui doit échouer :

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Modifiez l’assertion pour tester l’intergiciel dans des conditions de fonctionnement normales. Le test final utilise `Assert.Equal( ... );`. Exécutez à nouveau le test pour confirmer qu’il réussit.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Envoyer des demandes avec HttpContext

Une application de test peut également envoyer une demande à l’aide de [SendAsync (action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A). Dans l’exemple suivant, plusieurs vérifications sont effectuées lorsque `https://example.com/A/Path/?and=query` est traité par l’intergiciel (middleware) :

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>autorise la <xref:System.Net.Http.HttpClient> configuration directe d' <xref:Microsoft.AspNetCore.Http.HttpContext> un objet plutôt que l’utilisation des abstractions. Utilisez <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> pour manipuler des structures uniquement disponibles sur le serveur, telles que [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) ou [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Comme dans l’exemple précédent qui a testé une réponse *404-introuvable* , vérifiez l’inverse pour `Assert` chaque instruction du test précédent. La vérification confirme que le test échoue correctement lorsque l’intergiciel fonctionne normalement. Une fois que vous avez confirmé que le test de faux positif fonctionne, `Assert` définissez les instructions finales pour les conditions et les valeurs attendues du test. Exécutez-le à nouveau pour confirmer que le test réussit.
