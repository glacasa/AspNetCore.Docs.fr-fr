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
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="dca40-103">Tester l’intergiciel (middleware) ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dca40-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="dca40-104">Par [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="dca40-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="dca40-105">L’intergiciel peut être testé en isolation avec <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="dca40-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="dca40-106">Vous pouvez ainsi :</span><span class="sxs-lookup"><span data-stu-id="dca40-106">It allows you to:</span></span>

* <span data-ttu-id="dca40-107">Instanciez un pipeline d’application contenant uniquement les composants que vous devez tester.</span><span class="sxs-lookup"><span data-stu-id="dca40-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="dca40-108">Envoyer des demandes personnalisées pour vérifier le comportement de l’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="dca40-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="dca40-109">Avantages :</span><span class="sxs-lookup"><span data-stu-id="dca40-109">Advantages:</span></span>

* <span data-ttu-id="dca40-110">Les demandes sont envoyées en mémoire au lieu d’être sérialisées sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="dca40-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="dca40-111">Cela évite des préoccupations supplémentaires, telles que la gestion des ports et les certificats HTTPs.</span><span class="sxs-lookup"><span data-stu-id="dca40-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="dca40-112">Les exceptions dans l’intergiciel (middleware) peuvent être directement transmises au test appelant.</span><span class="sxs-lookup"><span data-stu-id="dca40-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="dca40-113">Il est possible de personnaliser les structures de données du serveur <xref:Microsoft.AspNetCore.Http.HttpContext>, telles que, directement dans le test.</span><span class="sxs-lookup"><span data-stu-id="dca40-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="dca40-114">Configurer TestServer</span><span class="sxs-lookup"><span data-stu-id="dca40-114">Set up the TestServer</span></span>

<span data-ttu-id="dca40-115">Dans le projet de test, créez un test :</span><span class="sxs-lookup"><span data-stu-id="dca40-115">In the test project, create a test:</span></span>

* <span data-ttu-id="dca40-116">Créez et démarrez un hôte qui utilise <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span><span class="sxs-lookup"><span data-stu-id="dca40-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="dca40-117">Ajoutez les services requis que l’intergiciel utilise.</span><span class="sxs-lookup"><span data-stu-id="dca40-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="dca40-118">Configurez le pipeline de traitement pour utiliser l’intergiciel (middleware) pour le test.</span><span class="sxs-lookup"><span data-stu-id="dca40-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="dca40-119">Envoyer des demandes avec HttpClient</span><span class="sxs-lookup"><span data-stu-id="dca40-119">Send requests with HttpClient</span></span>
<span data-ttu-id="dca40-120">Envoyer une demande à <xref:System.Net.Http.HttpClient>l’aide de :</span><span class="sxs-lookup"><span data-stu-id="dca40-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="dca40-121">Déclarez le résultat.</span><span class="sxs-lookup"><span data-stu-id="dca40-121">Assert the result.</span></span> <span data-ttu-id="dca40-122">Tout d’abord, faites d’une assertion l’inverse du résultat attendu.</span><span class="sxs-lookup"><span data-stu-id="dca40-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="dca40-123">Une exécution initiale avec une assertion false positive confirme que le test échoue quand l’intergiciel s’exécute correctement.</span><span class="sxs-lookup"><span data-stu-id="dca40-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="dca40-124">Exécutez le test et vérifiez que le test échoue.</span><span class="sxs-lookup"><span data-stu-id="dca40-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="dca40-125">Dans l’exemple suivant, l’intergiciel (middleware) doit retourner un code d’État 404 (*introuvable*) quand le point de terminaison racine est demandé.</span><span class="sxs-lookup"><span data-stu-id="dca40-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="dca40-126">Effectuez la première série de tests `Assert.NotEqual( ... );`avec, qui doit échouer :</span><span class="sxs-lookup"><span data-stu-id="dca40-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="dca40-127">Modifiez l’assertion pour tester l’intergiciel dans des conditions de fonctionnement normales.</span><span class="sxs-lookup"><span data-stu-id="dca40-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="dca40-128">Le test final utilise `Assert.Equal( ... );`.</span><span class="sxs-lookup"><span data-stu-id="dca40-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="dca40-129">Exécutez à nouveau le test pour confirmer qu’il réussit.</span><span class="sxs-lookup"><span data-stu-id="dca40-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="dca40-130">Envoyer des demandes avec HttpContext</span><span class="sxs-lookup"><span data-stu-id="dca40-130">Send requests with HttpContext</span></span>

<span data-ttu-id="dca40-131">Une application de test peut également envoyer une demande à l’aide de [SendAsync (action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span><span class="sxs-lookup"><span data-stu-id="dca40-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="dca40-132">Dans l’exemple suivant, plusieurs vérifications sont effectuées lorsque `https://example.com/A/Path/?and=query` est traité par l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="dca40-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="dca40-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>autorise la <xref:System.Net.Http.HttpClient> configuration directe d' <xref:Microsoft.AspNetCore.Http.HttpContext> un objet plutôt que l’utilisation des abstractions.</span><span class="sxs-lookup"><span data-stu-id="dca40-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="dca40-134">Utilisez <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> pour manipuler des structures uniquement disponibles sur le serveur, telles que [HttpContext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) ou [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="dca40-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="dca40-135">Comme dans l’exemple précédent qui a testé une réponse *404-introuvable* , vérifiez l’inverse pour `Assert` chaque instruction du test précédent.</span><span class="sxs-lookup"><span data-stu-id="dca40-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="dca40-136">La vérification confirme que le test échoue correctement lorsque l’intergiciel fonctionne normalement.</span><span class="sxs-lookup"><span data-stu-id="dca40-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="dca40-137">Une fois que vous avez confirmé que le test de faux positif fonctionne, `Assert` définissez les instructions finales pour les conditions et les valeurs attendues du test.</span><span class="sxs-lookup"><span data-stu-id="dca40-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="dca40-138">Exécutez-le à nouveau pour confirmer que le test réussit.</span><span class="sxs-lookup"><span data-stu-id="dca40-138">Run it again to confirm that the test passes.</span></span>
