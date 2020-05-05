---
title: Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core
author: stevejgordon
description: Découvrez plus d’informations sur l’utilisation de l’interface IHttpClientFactory pour gérer des instances HttpClient logiques dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766548"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="b8f07-103">Effectuer des requêtes HTTP en utilisant IHttpClientFactory dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8f07-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b8f07-104">Par [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT)et [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b8f07-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="b8f07-105">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="b8f07-106">`IHttpClientFactory`offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="b8f07-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="b8f07-107">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-108">Par exemple, un client nommé *GitHub* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="b8f07-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="b8f07-109">Un client par défaut peut être inscrit pour un accès général.</span><span class="sxs-lookup"><span data-stu-id="b8f07-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="b8f07-110">Codifie le concept d’intergiciel (middleware) sortant via la délégation de gestionnaires `HttpClient`dans.</span><span class="sxs-lookup"><span data-stu-id="b8f07-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="b8f07-111">Fournit des extensions pour l’intergiciel (middleware) basé sur Polly pour tirer parti des gestionnaires de `HttpClient`délégation dans.</span><span class="sxs-lookup"><span data-stu-id="b8f07-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="b8f07-112">Gère le regroupement et la durée de vie `HttpClientMessageHandler` des instances sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="b8f07-113">La gestion automatique évite les problèmes courants liés au DNS (Domain Name System) qui se `HttpClient` produisent lors de la gestion manuelle des durées de vie.</span><span class="sxs-lookup"><span data-stu-id="b8f07-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="b8f07-114">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b8f07-115">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b8f07-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b8f07-116">L’exemple de code de cette rubrique utilise <xref:System.Text.Json> pour désérialiser le contenu JSON renvoyé dans les réponses http.</span><span class="sxs-lookup"><span data-stu-id="b8f07-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="b8f07-117">Pour obtenir des exemples `Json.NET` qui `ReadAsAsync<T>`utilisent et, utilisez le sélecteur de version pour sélectionner une version 2. x de cette rubrique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="b8f07-118">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="b8f07-118">Consumption patterns</span></span>

<span data-ttu-id="b8f07-119">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="b8f07-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="b8f07-120">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="b8f07-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="b8f07-121">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="b8f07-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="b8f07-122">Clients typés</span><span class="sxs-lookup"><span data-stu-id="b8f07-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="b8f07-123">Clients générés</span><span class="sxs-lookup"><span data-stu-id="b8f07-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="b8f07-124">La meilleure approche dépend des exigences de l’application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="b8f07-125">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="b8f07-125">Basic usage</span></span>

<span data-ttu-id="b8f07-126">`IHttpClientFactory`peut être inscrit en appelant `AddHttpClient`:</span><span class="sxs-lookup"><span data-stu-id="b8f07-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="b8f07-127">Un `IHttpClientFactory` peut être demandé à l’aide [de l’injection de dépendances (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b8f07-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b8f07-128">Le code suivant utilise `IHttpClientFactory` pour créer une `HttpClient` instance :</span><span class="sxs-lookup"><span data-stu-id="b8f07-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="b8f07-129">L' `IHttpClientFactory` utilisation de like dans l’exemple précédent est un bon moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="b8f07-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="b8f07-130">Elle n’a aucun impact sur `HttpClient` la façon dont est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="b8f07-131">Dans les emplacements `HttpClient` où les instances sont créées dans une application existante, remplacez-les par des <xref:System.Net.Http.IHttpClientFactory.CreateClient*>appels à.</span><span class="sxs-lookup"><span data-stu-id="b8f07-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="b8f07-132">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="b8f07-132">Named clients</span></span>

<span data-ttu-id="b8f07-133">Les clients nommés sont un bon choix lorsque :</span><span class="sxs-lookup"><span data-stu-id="b8f07-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="b8f07-134">L’application requiert de nombreuses utilisations distinctes `HttpClient`de.</span><span class="sxs-lookup"><span data-stu-id="b8f07-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="b8f07-135">De `HttpClient`nombreux s ont une configuration différente.</span><span class="sxs-lookup"><span data-stu-id="b8f07-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="b8f07-136">La configuration d’un `HttpClient` nom peut être spécifiée lors de `Startup.ConfigureServices`l’inscription dans :</span><span class="sxs-lookup"><span data-stu-id="b8f07-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="b8f07-137">Dans le code précédent, le client est configuré avec :</span><span class="sxs-lookup"><span data-stu-id="b8f07-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="b8f07-138">Adresse `https://api.github.com/`de base.</span><span class="sxs-lookup"><span data-stu-id="b8f07-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="b8f07-139">Deux en-têtes nécessaires à l’utilisation de l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="b8f07-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="b8f07-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="b8f07-140">CreateClient</span></span>

<span data-ttu-id="b8f07-141">Chaque fois <xref:System.Net.Http.IHttpClientFactory.CreateClient*> que est appelé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="b8f07-142">Une nouvelle instance de `HttpClient` est créée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="b8f07-143">L’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-143">The configuration action is called.</span></span>

<span data-ttu-id="b8f07-144">Pour créer un client nommé, transmettez son nom `CreateClient`à :</span><span class="sxs-lookup"><span data-stu-id="b8f07-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="b8f07-145">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="b8f07-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="b8f07-146">Le code peut passer uniquement le chemin d’accès, dans la mesure où l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="b8f07-147">Clients typés</span><span class="sxs-lookup"><span data-stu-id="b8f07-147">Typed clients</span></span>

<span data-ttu-id="b8f07-148">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="b8f07-148">Typed clients:</span></span>

* <span data-ttu-id="b8f07-149">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="b8f07-150">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="b8f07-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="b8f07-151">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="b8f07-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="b8f07-152">Par exemple, un seul client typé peut être utilisé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="b8f07-153">Pour un seul point de terminaison backend.</span><span class="sxs-lookup"><span data-stu-id="b8f07-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="b8f07-154">Pour encapsuler toute la logique traitant le point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="b8f07-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="b8f07-155">Utilisez DI et pouvez les injecter lorsque cela est requis dans l’application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="b8f07-156">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="b8f07-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="b8f07-157">Dans le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b8f07-157">In the preceding code:</span></span>

* <span data-ttu-id="b8f07-158">La configuration est déplacée vers le client typé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="b8f07-159">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="b8f07-160">Vous pouvez créer des méthodes spécifiques à l’API `HttpClient` qui exposent les fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="b8f07-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="b8f07-161">Par exemple, la `GetAspNetDocsIssues` méthode encapsule du code pour récupérer des problèmes ouverts.</span><span class="sxs-lookup"><span data-stu-id="b8f07-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="b8f07-162">Le code suivant appelle <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> dans `Startup.ConfigureServices` pour inscrire une classe de client typée :</span><span class="sxs-lookup"><span data-stu-id="b8f07-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="b8f07-163">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="b8f07-164">Dans le code précédent, `AddHttpClient` s’inscrit `GitHubService` en tant que service temporaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="b8f07-165">Cette inscription utilise une méthode de fabrique pour :</span><span class="sxs-lookup"><span data-stu-id="b8f07-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="b8f07-166">Créez une instance de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="b8f07-167">Crée une instance de `GitHubService`, en passant l’instance de `HttpClient` à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="b8f07-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="b8f07-168">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="b8f07-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="b8f07-169">La configuration d’un client typé peut être spécifiée lors de l' `Startup.ConfigureServices`inscription dans, plutôt que dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="b8f07-170">`HttpClient` Peut être encapsulé dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="b8f07-171">Au lieu de l’exposer en tant que propriété, définissez une méthode qui appelle `HttpClient` l’instance en interne :</span><span class="sxs-lookup"><span data-stu-id="b8f07-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="b8f07-172">Dans le code précédent, le `HttpClient` est stocké dans un champ privé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="b8f07-173">L’accès à `HttpClient` est par la méthode `GetRepos` publique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="b8f07-174">Clients générés</span><span class="sxs-lookup"><span data-stu-id="b8f07-174">Generated clients</span></span>

<span data-ttu-id="b8f07-175">`IHttpClientFactory`peut être utilisé en association avec des bibliothèques tierces telles que la [réajuster](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="b8f07-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="b8f07-176">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="b8f07-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="b8f07-177">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="b8f07-178">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="b8f07-179">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="b8f07-179">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="b8f07-180">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="b8f07-180">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="b8f07-181">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="b8f07-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="b8f07-182">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="b8f07-182">Outgoing request middleware</span></span>

<span data-ttu-id="b8f07-183">`HttpClient`présente le concept de délégation de gestionnaires qui peuvent être liés entre eux pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="b8f07-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="b8f07-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="b8f07-185">Simplifie la définition des gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="b8f07-186">Prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour générer un pipeline d’intergiciel (middleware) de demande sortante.</span><span class="sxs-lookup"><span data-stu-id="b8f07-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b8f07-187">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="b8f07-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="b8f07-188">Ce modèle :</span><span class="sxs-lookup"><span data-stu-id="b8f07-188">This pattern:</span></span>

  * <span data-ttu-id="b8f07-189">Est semblable au pipeline d’intergiciel (middleware) entrant dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8f07-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="b8f07-190">Fournit un mécanisme permettant de gérer les problèmes transversaux autour des requêtes HTTP, par exemple :</span><span class="sxs-lookup"><span data-stu-id="b8f07-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="b8f07-191">caching</span><span class="sxs-lookup"><span data-stu-id="b8f07-191">caching</span></span>
    * <span data-ttu-id="b8f07-192">gestion des erreurs</span><span class="sxs-lookup"><span data-stu-id="b8f07-192">error handling</span></span>
    * <span data-ttu-id="b8f07-193">sérialisation</span><span class="sxs-lookup"><span data-stu-id="b8f07-193">serialization</span></span>
    * <span data-ttu-id="b8f07-194">journalisation</span><span class="sxs-lookup"><span data-stu-id="b8f07-194">logging</span></span>

<span data-ttu-id="b8f07-195">Pour créer un gestionnaire de délégation :</span><span class="sxs-lookup"><span data-stu-id="b8f07-195">To create a delegating handler:</span></span>

* <span data-ttu-id="b8f07-196">Dériver <xref:System.Net.Http.DelegatingHandler>de.</span><span class="sxs-lookup"><span data-stu-id="b8f07-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="b8f07-197">Substituez <xref:System.Net.Http.DelegatingHandler.SendAsync*></span><span class="sxs-lookup"><span data-stu-id="b8f07-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="b8f07-198">Exécutez le code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="b8f07-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="b8f07-199">Le code précédent vérifie si l' `X-API-KEY` en-tête est dans la demande.</span><span class="sxs-lookup"><span data-stu-id="b8f07-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="b8f07-200">Si `X-API-KEY` est manquant, <xref:System.Net.HttpStatusCode.BadRequest> est retourné.</span><span class="sxs-lookup"><span data-stu-id="b8f07-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="b8f07-201">Plusieurs gestionnaires peuvent être ajoutés à la configuration d’un `HttpClient` avec : <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName></span><span class="sxs-lookup"><span data-stu-id="b8f07-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="b8f07-202">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="b8f07-203">`IHttpClientFactory` crée une étendue DI distincte pour chaque gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="b8f07-204">Les gestionnaires peuvent dépendre des services de toute portée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="b8f07-205">Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="b8f07-206">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="b8f07-207">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="b8f07-208">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="b8f07-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="b8f07-209">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="b8f07-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="b8f07-210">Transmettez les données dans le gestionnaire à l’aide de [HttpRequestMessage. Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span><span class="sxs-lookup"><span data-stu-id="b8f07-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="b8f07-211">Utilisez <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="b8f07-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="b8f07-212">Créez un objet de stockage <xref:System.Threading.AsyncLocal`1> personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="b8f07-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="b8f07-213">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-213">Use Polly-based handlers</span></span>

<span data-ttu-id="b8f07-214">`IHttpClientFactory`s’intègre à la bibliothèque tierce [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="b8f07-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="b8f07-215">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="b8f07-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="b8f07-216">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="b8f07-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="b8f07-217">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-218">Les extensions Polly prennent en charge l’ajout de gestionnaires basés sur Polly aux clients.</span><span class="sxs-lookup"><span data-stu-id="b8f07-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="b8f07-219">Polly requiert le package NuGet [Microsoft. extensions. http. Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) .</span><span class="sxs-lookup"><span data-stu-id="b8f07-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="b8f07-220">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="b8f07-220">Handle transient faults</span></span>

<span data-ttu-id="b8f07-221">Les erreurs se produisent généralement lorsque les appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="b8f07-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="b8f07-223">Les stratégies configurées avec `AddTransientHttpErrorPolicy` gèrent les réponses suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="b8f07-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="b8f07-224">HTTP 5xx</span></span>
* <span data-ttu-id="b8f07-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="b8f07-225">HTTP 408</span></span>

<span data-ttu-id="b8f07-226">`AddTransientHttpErrorPolicy`fournit l’accès à `PolicyBuilder` un objet configuré pour gérer les erreurs qui représentent une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="b8f07-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="b8f07-227">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="b8f07-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="b8f07-228">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="b8f07-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="b8f07-229">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="b8f07-229">Dynamically select policies</span></span>

<span data-ttu-id="b8f07-230">Les méthodes d’extension sont fournies pour ajouter des gestionnaires basés sur Polly, par <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>exemple,.</span><span class="sxs-lookup"><span data-stu-id="b8f07-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="b8f07-231">La surcharge `AddPolicyHandler` suivante inspecte la demande pour décider de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="b8f07-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="b8f07-232">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="b8f07-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="b8f07-233">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="b8f07-234">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="b8f07-235">Il est courant d’imbriquer les stratégies Polly :</span><span class="sxs-lookup"><span data-stu-id="b8f07-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="b8f07-236">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="b8f07-236">In the preceding example:</span></span>

* <span data-ttu-id="b8f07-237">Deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-237">Two handlers are added.</span></span>
* <span data-ttu-id="b8f07-238">Le premier gestionnaire utilise <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="b8f07-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="b8f07-239">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="b8f07-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="b8f07-240">Le deuxième `AddTransientHttpErrorPolicy` appel ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="b8f07-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="b8f07-241">Les demandes externes supplémentaires sont bloquées pendant 30 secondes si 5 tentatives ayant échoué se produisent de façon séquentielle.</span><span class="sxs-lookup"><span data-stu-id="b8f07-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="b8f07-242">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="b8f07-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="b8f07-243">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="b8f07-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="b8f07-244">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="b8f07-245">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="b8f07-246">Dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="b8f07-246">In the following code:</span></span>

* <span data-ttu-id="b8f07-247">Les stratégies « régulières » et « longues » sont ajoutées.</span><span class="sxs-lookup"><span data-stu-id="b8f07-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="b8f07-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>Ajoute les stratégies « standard » et « longues » à partir du Registre.</span><span class="sxs-lookup"><span data-stu-id="b8f07-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="b8f07-249">Pour plus d’informations `IHttpClientFactory` sur les intégrations de et de Polly, consultez le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="b8f07-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="b8f07-250">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="b8f07-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="b8f07-251">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="b8f07-252">Un <xref:System.Net.Http.HttpMessageHandler> est créé par client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="b8f07-253">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="b8f07-254">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="b8f07-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="b8f07-255">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="b8f07-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="b8f07-256">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="b8f07-257">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="b8f07-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="b8f07-258">Certains gestionnaires maintiennent également les connexions ouvertes indéfiniment, ce qui peut empêcher le gestionnaire de réagir aux modifications DNS (Domain Name System).</span><span class="sxs-lookup"><span data-stu-id="b8f07-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="b8f07-259">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="b8f07-260">La valeur par défaut peut être substituée pour chaque client nommé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="b8f07-261">`HttpClient`les instances peuvent généralement être traitées comme des objets .NET qui **ne nécessitent pas** de suppression.</span><span class="sxs-lookup"><span data-stu-id="b8f07-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="b8f07-262">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="b8f07-263">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="b8f07-264">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="b8f07-265">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="b8f07-266">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b8f07-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="b8f07-267">L' `IHttpClientFactory` utilisation de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="b8f07-268">Problèmes d’épuisement des ressources par `HttpMessageHandler` les instances de regroupement.</span><span class="sxs-lookup"><span data-stu-id="b8f07-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="b8f07-269">Problèmes DNS périmés par `HttpMessageHandler` les instances cycliques à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="b8f07-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="b8f07-270">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d' <xref:System.Net.Http.SocketsHttpHandler> une instance de longue durée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="b8f07-271">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="b8f07-272">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="b8f07-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="b8f07-273">Créez `HttpClient` des instances `new HttpClient(handler, disposeHandler: false)` en utilisant si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="b8f07-274">Les approches précédentes résolvent les problèmes de gestion `IHttpClientFactory` des ressources qui sont résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="b8f07-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="b8f07-275">Le `SocketsHttpHandler` partage les connexions `HttpClient` entre les instances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-276">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="b8f07-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="b8f07-277">Le `SocketsHttpHandler` cycle des connexions en `PooledConnectionLifetime` fonction de pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="b8f07-278">Cookies</span><span class="sxs-lookup"><span data-stu-id="b8f07-278">Cookies</span></span>

<span data-ttu-id="b8f07-279">Les `HttpMessageHandler` instances regroupées entraînent le partage des `CookieContainer` objets.</span><span class="sxs-lookup"><span data-stu-id="b8f07-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="b8f07-280">Le partage `CookieContainer` d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="b8f07-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="b8f07-281">Pour les applications qui nécessitent des cookies, utilisez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="b8f07-282">Désactivation de la gestion automatique des cookies</span><span class="sxs-lookup"><span data-stu-id="b8f07-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="b8f07-283">Éviter`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="b8f07-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="b8f07-284">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la gestion automatique des cookies :</span><span class="sxs-lookup"><span data-stu-id="b8f07-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="b8f07-285">Journalisation</span><span class="sxs-lookup"><span data-stu-id="b8f07-285">Logging</span></span>

<span data-ttu-id="b8f07-286">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="b8f07-287">Activez le niveau d’information approprié dans la configuration de la journalisation pour voir les messages du journal par défaut.</span><span class="sxs-lookup"><span data-stu-id="b8f07-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="b8f07-288">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="b8f07-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="b8f07-289">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="b8f07-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="b8f07-290">Un client nommé *MyNamedClient*, par exemple, journalise des messages avec la catégorie « System .net. http. httpclient ». **MyNamedClient**. LogicalHandler".</span><span class="sxs-lookup"><span data-stu-id="b8f07-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="b8f07-291">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="b8f07-292">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="b8f07-293">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="b8f07-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="b8f07-294">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="b8f07-295">Dans l’exemple *MyNamedClient* , ces messages sont journalisés avec la catégorie de journal « System .net. http. httpclient ». **MyNamedClient**. ClientHandler".</span><span class="sxs-lookup"><span data-stu-id="b8f07-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="b8f07-296">Pour la requête, cela se produit une fois que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la demande.</span><span class="sxs-lookup"><span data-stu-id="b8f07-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="b8f07-297">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="b8f07-298">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="b8f07-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="b8f07-299">Cela peut inclure des modifications apportées aux en-têtes de demande ou au code d’état de réponse.</span><span class="sxs-lookup"><span data-stu-id="b8f07-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="b8f07-300">L’inclusion du nom du client dans la catégorie journal active le filtrage des journaux pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="b8f07-301">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b8f07-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="b8f07-302">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="b8f07-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="b8f07-303">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="b8f07-304">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="b8f07-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="b8f07-305">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="b8f07-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="b8f07-306">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="b8f07-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="b8f07-307">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="b8f07-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="b8f07-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="b8f07-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="b8f07-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="b8f07-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="b8f07-310">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b8f07-310">In the following example:</span></span>

* <span data-ttu-id="b8f07-311"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b8f07-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="b8f07-312">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="b8f07-313">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="b8f07-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="b8f07-314">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="b8f07-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="b8f07-315">Intergiciel (middleware) de propagation d’en-tête</span><span class="sxs-lookup"><span data-stu-id="b8f07-315">Header propagation middleware</span></span>

<span data-ttu-id="b8f07-316">La propagation d’en-tête est un intergiciel (middleware) ASP.NET Core pour propager les en-têtes HTTP de la requête entrante vers les demandes du client HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="b8f07-317">Pour utiliser la propagation d’en-tête :</span><span class="sxs-lookup"><span data-stu-id="b8f07-317">To use header propagation:</span></span>

* <span data-ttu-id="b8f07-318">Référencez le package [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) .</span><span class="sxs-lookup"><span data-stu-id="b8f07-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="b8f07-319">Configurer l’intergiciel et `HttpClient` dans : `Startup`</span><span class="sxs-lookup"><span data-stu-id="b8f07-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="b8f07-320">Le client comprend les en-têtes configurés pour les demandes sortantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="b8f07-321">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b8f07-321">Additional resources</span></span>

* [<span data-ttu-id="b8f07-322">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="b8f07-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="b8f07-323">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b8f07-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="b8f07-324">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="b8f07-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="b8f07-325">Comment sérialiser et désérialiser JSON dans .NET</span><span class="sxs-lookup"><span data-stu-id="b8f07-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b8f07-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="b8f07-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="b8f07-327">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="b8f07-328">Elle offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="b8f07-328">It offers the following benefits:</span></span>

* <span data-ttu-id="b8f07-329">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-330">Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="b8f07-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="b8f07-331">Un client par défaut peut être inscrit à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="b8f07-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="b8f07-332">Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.</span><span class="sxs-lookup"><span data-stu-id="b8f07-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="b8f07-333">Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="b8f07-334">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b8f07-335">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8f07-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="b8f07-336">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="b8f07-336">Consumption patterns</span></span>

<span data-ttu-id="b8f07-337">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="b8f07-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="b8f07-338">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="b8f07-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="b8f07-339">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="b8f07-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="b8f07-340">Clients typés</span><span class="sxs-lookup"><span data-stu-id="b8f07-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="b8f07-341">Clients générés</span><span class="sxs-lookup"><span data-stu-id="b8f07-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="b8f07-342">Aucune d’entre elles n’est meilleure qu’une autre.</span><span class="sxs-lookup"><span data-stu-id="b8f07-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="b8f07-343">La meilleure approche dépend des contraintes de l’application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="b8f07-344">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="b8f07-344">Basic usage</span></span>

<span data-ttu-id="b8f07-345">Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="b8f07-346">Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b8f07-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b8f07-347">Le `IHttpClientFactory` peut être utilisé pour créer une `HttpClient` instance de :</span><span class="sxs-lookup"><span data-stu-id="b8f07-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="b8f07-348">L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="b8f07-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="b8f07-349">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="b8f07-350">Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="b8f07-351">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="b8f07-351">Named clients</span></span>

<span data-ttu-id="b8f07-352">Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**.</span><span class="sxs-lookup"><span data-stu-id="b8f07-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="b8f07-353">La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="b8f07-354">Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*.</span><span class="sxs-lookup"><span data-stu-id="b8f07-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="b8f07-355">Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="b8f07-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="b8f07-356">Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="b8f07-357">Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="b8f07-358">Spécifiez le nom du client à créer :</span><span class="sxs-lookup"><span data-stu-id="b8f07-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="b8f07-359">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="b8f07-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="b8f07-360">Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="b8f07-361">Clients typés</span><span class="sxs-lookup"><span data-stu-id="b8f07-361">Typed clients</span></span>

<span data-ttu-id="b8f07-362">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="b8f07-362">Typed clients:</span></span>

* <span data-ttu-id="b8f07-363">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="b8f07-364">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="b8f07-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="b8f07-365">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="b8f07-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="b8f07-366">Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="b8f07-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="b8f07-367">Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="b8f07-368">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="b8f07-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="b8f07-369">Dans le code précédent, la configuration est déplacée dans le client typé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="b8f07-370">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="b8f07-371">Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="b8f07-372">La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.</span><span class="sxs-lookup"><span data-stu-id="b8f07-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="b8f07-373">Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="b8f07-374">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="b8f07-375">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="b8f07-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="b8f07-376">Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="b8f07-377">Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="b8f07-378">Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.</span><span class="sxs-lookup"><span data-stu-id="b8f07-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="b8f07-379">Dans le code précédent, le `HttpClient` est stocké en tant que champ privé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="b8f07-380">Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="b8f07-381">Clients générés</span><span class="sxs-lookup"><span data-stu-id="b8f07-381">Generated clients</span></span>

<span data-ttu-id="b8f07-382">`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="b8f07-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="b8f07-383">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="b8f07-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="b8f07-384">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="b8f07-385">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="b8f07-386">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="b8f07-386">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="b8f07-387">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="b8f07-387">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="b8f07-388">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="b8f07-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="b8f07-389">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="b8f07-389">Outgoing request middleware</span></span>

<span data-ttu-id="b8f07-390">`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="b8f07-391">Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="b8f07-392">Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b8f07-393">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="b8f07-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="b8f07-394">Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8f07-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="b8f07-395">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="b8f07-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="b8f07-396">Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="b8f07-397">Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="b8f07-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="b8f07-398">Le code précédent définit un gestionnaire de base.</span><span class="sxs-lookup"><span data-stu-id="b8f07-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="b8f07-399">Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="b8f07-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="b8f07-400">Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="b8f07-401">Au cours de l’inscription, un ou plusieurs gestionnaires peuvent être ajoutés à la configuration `HttpClient`d’un.</span><span class="sxs-lookup"><span data-stu-id="b8f07-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="b8f07-402">Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="b8f07-403">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="b8f07-404">`IHttpClientFactory` crée une étendue DI distincte pour chaque gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="b8f07-405">Les gestionnaires peuvent librement dépendre des services de n’importe quelle étendue.</span><span class="sxs-lookup"><span data-stu-id="b8f07-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="b8f07-406">Les services dont dépendent les gestionnaires sont supprimés lorsque le gestionnaire est supprimé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="b8f07-407">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type pour le gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="b8f07-408">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="b8f07-409">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="b8f07-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="b8f07-410">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="b8f07-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="b8f07-411">Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="b8f07-412">Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="b8f07-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="b8f07-413">Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="b8f07-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="b8f07-414">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-414">Use Polly-based handlers</span></span>

<span data-ttu-id="b8f07-415">`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="b8f07-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="b8f07-416">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="b8f07-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="b8f07-417">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="b8f07-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="b8f07-418">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-419">Les extensions Polly :</span><span class="sxs-lookup"><span data-stu-id="b8f07-419">The Polly extensions:</span></span>

* <span data-ttu-id="b8f07-420">Prennent en charge l’ajout de gestionnaires Polly à des clients.</span><span class="sxs-lookup"><span data-stu-id="b8f07-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="b8f07-421">Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="b8f07-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="b8f07-422">Ce package n’est pas inclus dans le framework partagé ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8f07-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="b8f07-423">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="b8f07-423">Handle transient faults</span></span>

<span data-ttu-id="b8f07-424">Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="b8f07-425">Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="b8f07-426">Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="b8f07-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="b8f07-427">L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8f07-428">L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="b8f07-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="b8f07-429">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="b8f07-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="b8f07-430">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="b8f07-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="b8f07-431">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="b8f07-431">Dynamically select policies</span></span>

<span data-ttu-id="b8f07-432">Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly.</span><span class="sxs-lookup"><span data-stu-id="b8f07-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="b8f07-433">Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges.</span><span class="sxs-lookup"><span data-stu-id="b8f07-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="b8f07-434">Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="b8f07-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="b8f07-435">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="b8f07-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="b8f07-436">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="b8f07-437">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="b8f07-438">Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :</span><span class="sxs-lookup"><span data-stu-id="b8f07-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="b8f07-439">Dans l’exemple précédent, deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="b8f07-440">Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="b8f07-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="b8f07-441">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="b8f07-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="b8f07-442">Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="b8f07-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="b8f07-443">Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent.</span><span class="sxs-lookup"><span data-stu-id="b8f07-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="b8f07-444">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="b8f07-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="b8f07-445">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="b8f07-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="b8f07-446">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="b8f07-447">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="b8f07-448">Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :</span><span class="sxs-lookup"><span data-stu-id="b8f07-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="b8f07-449">Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="b8f07-450">Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.</span><span class="sxs-lookup"><span data-stu-id="b8f07-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="b8f07-451">Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="b8f07-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="b8f07-452">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="b8f07-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="b8f07-453">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="b8f07-454">Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="b8f07-455">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="b8f07-456">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="b8f07-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="b8f07-457">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="b8f07-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="b8f07-458">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="b8f07-459">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="b8f07-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="b8f07-460">Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.</span><span class="sxs-lookup"><span data-stu-id="b8f07-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="b8f07-461">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="b8f07-462">La valeur par défaut peut être remplacée pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="b8f07-463">Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :</span><span class="sxs-lookup"><span data-stu-id="b8f07-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="b8f07-464">La suppression du client n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="b8f07-465">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="b8f07-466">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-467">Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.</span><span class="sxs-lookup"><span data-stu-id="b8f07-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="b8f07-468">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="b8f07-469">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="b8f07-470">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b8f07-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="b8f07-471">L' `IHttpClientFactory` utilisation de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="b8f07-472">Problèmes d’épuisement des ressources par `HttpMessageHandler` les instances de regroupement.</span><span class="sxs-lookup"><span data-stu-id="b8f07-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="b8f07-473">Problèmes DNS périmés par `HttpMessageHandler` les instances cycliques à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="b8f07-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="b8f07-474">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d' <xref:System.Net.Http.SocketsHttpHandler> une instance de longue durée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="b8f07-475">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="b8f07-476">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="b8f07-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="b8f07-477">Créez `HttpClient` des instances `new HttpClient(handler, disposeHandler: false)` en utilisant si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="b8f07-478">Les approches précédentes résolvent les problèmes de gestion `IHttpClientFactory` des ressources qui sont résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="b8f07-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="b8f07-479">Le `SocketsHttpHandler` partage les connexions `HttpClient` entre les instances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-480">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="b8f07-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="b8f07-481">Le `SocketsHttpHandler` cycle des connexions en `PooledConnectionLifetime` fonction de pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="b8f07-482">Cookies</span><span class="sxs-lookup"><span data-stu-id="b8f07-482">Cookies</span></span>

<span data-ttu-id="b8f07-483">Les `HttpMessageHandler` instances regroupées entraînent le partage des `CookieContainer` objets.</span><span class="sxs-lookup"><span data-stu-id="b8f07-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="b8f07-484">Le partage `CookieContainer` d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="b8f07-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="b8f07-485">Pour les applications qui nécessitent des cookies, utilisez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="b8f07-486">Désactivation de la gestion automatique des cookies</span><span class="sxs-lookup"><span data-stu-id="b8f07-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="b8f07-487">Éviter`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="b8f07-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="b8f07-488">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la gestion automatique des cookies :</span><span class="sxs-lookup"><span data-stu-id="b8f07-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="b8f07-489">Journalisation</span><span class="sxs-lookup"><span data-stu-id="b8f07-489">Logging</span></span>

<span data-ttu-id="b8f07-490">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="b8f07-491">Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="b8f07-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="b8f07-492">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="b8f07-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="b8f07-493">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="b8f07-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="b8f07-494">Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="b8f07-495">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="b8f07-496">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="b8f07-497">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="b8f07-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="b8f07-498">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="b8f07-499">Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="b8f07-500">Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="b8f07-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="b8f07-501">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="b8f07-502">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="b8f07-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="b8f07-503">Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="b8f07-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="b8f07-504">L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="b8f07-505">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b8f07-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="b8f07-506">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="b8f07-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="b8f07-507">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="b8f07-508">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="b8f07-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="b8f07-509">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="b8f07-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="b8f07-510">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="b8f07-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="b8f07-511">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="b8f07-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="b8f07-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="b8f07-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="b8f07-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="b8f07-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="b8f07-514">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b8f07-514">In the following example:</span></span>

* <span data-ttu-id="b8f07-515"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b8f07-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="b8f07-516">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="b8f07-517">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="b8f07-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="b8f07-518">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="b8f07-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="b8f07-519">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b8f07-519">Additional resources</span></span>

* [<span data-ttu-id="b8f07-520">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="b8f07-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="b8f07-521">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b8f07-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="b8f07-522">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="b8f07-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b8f07-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak) et [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="b8f07-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="b8f07-524">Une <xref:System.Net.Http.IHttpClientFactory> peut être inscrite et utilisée pour configurer et créer des instances de <xref:System.Net.Http.HttpClient> dans une application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="b8f07-525">Elle offre les avantages suivants :</span><span class="sxs-lookup"><span data-stu-id="b8f07-525">It offers the following benefits:</span></span>

* <span data-ttu-id="b8f07-526">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-527">Par exemple, un client *github* peut être inscrit et configuré pour accéder à [GitHub](https://github.com/).</span><span class="sxs-lookup"><span data-stu-id="b8f07-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="b8f07-528">Un client par défaut peut être inscrit à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="b8f07-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="b8f07-529">Codifie le concept de middleware (intergiciel) sortant via la délégation de gestionnaires dans `HttpClient` et fournit des extensions permettant au middleware Polly d’en tirer parti.</span><span class="sxs-lookup"><span data-stu-id="b8f07-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="b8f07-530">Gère le regroupement et la durée de vie des instances de `HttpClientMessageHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="b8f07-531">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="b8f07-532">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8f07-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b8f07-533">Prérequis</span><span class="sxs-lookup"><span data-stu-id="b8f07-533">Prerequisites</span></span>

<span data-ttu-id="b8f07-534">Les projets ciblant .NET Framework nécessitent l’installation du package NuGet [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/).</span><span class="sxs-lookup"><span data-stu-id="b8f07-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="b8f07-535">Les projets qui ciblent .NET Core et référencent le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) incluent déjà le package `Microsoft.Extensions.Http`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="b8f07-536">Modèles de consommation</span><span class="sxs-lookup"><span data-stu-id="b8f07-536">Consumption patterns</span></span>

<span data-ttu-id="b8f07-537">Vous pouvez utiliser `IHttpClientFactory` dans une application de plusieurs façons :</span><span class="sxs-lookup"><span data-stu-id="b8f07-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="b8f07-538">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="b8f07-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="b8f07-539">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="b8f07-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="b8f07-540">Clients typés</span><span class="sxs-lookup"><span data-stu-id="b8f07-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="b8f07-541">Clients générés</span><span class="sxs-lookup"><span data-stu-id="b8f07-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="b8f07-542">Aucune d’entre elles n’est meilleure qu’une autre.</span><span class="sxs-lookup"><span data-stu-id="b8f07-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="b8f07-543">La meilleure approche dépend des contraintes de l’application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="b8f07-544">Utilisation de base</span><span class="sxs-lookup"><span data-stu-id="b8f07-544">Basic usage</span></span>

<span data-ttu-id="b8f07-545">Vous pouvez inscrire la `IHttpClientFactory` en appelant la méthode d’extension `AddHttpClient` sur la `IServiceCollection`, à l’intérieur la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="b8f07-546">Une fois inscrit, le code peut accepter un `IHttpClientFactory` partout où des services peuvent être injectés avec [une injection de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="b8f07-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b8f07-547">Le `IHttpClientFactory` peut être utilisé pour créer une `HttpClient` instance de :</span><span class="sxs-lookup"><span data-stu-id="b8f07-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="b8f07-548">L’utilisation de `IHttpClientFactory` de cette façon est un excellent moyen de refactoriser une application existante.</span><span class="sxs-lookup"><span data-stu-id="b8f07-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="b8f07-549">Elle n’a aucun impact sur la façon dont `HttpClient` est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="b8f07-550">Dans les endroits où les instances de `HttpClient` sont actuellement créées, remplacez ces occurrences par un appel à <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="b8f07-551">Clients nommés</span><span class="sxs-lookup"><span data-stu-id="b8f07-551">Named clients</span></span>

<span data-ttu-id="b8f07-552">Si une application nécessite plusieurs utilisations distinctes de `HttpClient`, chacune avec une configuration différente, une option consiste à utiliser des **clients nommés**.</span><span class="sxs-lookup"><span data-stu-id="b8f07-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="b8f07-553">La configuration d’un `HttpClient` nommé peut être spécifiée lors de l’inscription dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="b8f07-554">Dans le code précédent, `AddHttpClient` est appelé en fournissant le nom *github*.</span><span class="sxs-lookup"><span data-stu-id="b8f07-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="b8f07-555">Une configuration par défaut est appliquée à ce client : l’adresse de base et deux en-têtes nécessaires pour utiliser l’API GitHub.</span><span class="sxs-lookup"><span data-stu-id="b8f07-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="b8f07-556">Chaque fois que `CreateClient` est appelée, une nouvelle instance de `HttpClient` est créée et l’action de configuration est appelée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="b8f07-557">Pour utiliser un client nommé, un paramètre de chaîne peut être passé à `CreateClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="b8f07-558">Spécifiez le nom du client à créer :</span><span class="sxs-lookup"><span data-stu-id="b8f07-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="b8f07-559">Dans le code précédent, la requête n’a pas besoin de spécifier un nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="b8f07-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="b8f07-560">Elle peut simplement passer le chemin, car l’adresse de base configurée pour le client est utilisée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="b8f07-561">Clients typés</span><span class="sxs-lookup"><span data-stu-id="b8f07-561">Typed clients</span></span>

<span data-ttu-id="b8f07-562">Clients typés :</span><span class="sxs-lookup"><span data-stu-id="b8f07-562">Typed clients:</span></span>

* <span data-ttu-id="b8f07-563">Fournissent les mêmes fonctionnalités que les clients nommés, sans qu’il soit nécessaire d’utiliser des chaînes comme clés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="b8f07-564">Bénéficie de l’aide d’IntelliSense et du compilateur lors de l’utilisation des clients.</span><span class="sxs-lookup"><span data-stu-id="b8f07-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="b8f07-565">Fournissent un emplacement unique pour configurer et interagir avec un `HttpClient` particulier.</span><span class="sxs-lookup"><span data-stu-id="b8f07-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="b8f07-566">Par exemple, un même client typé peut être utilisé pour un point de terminaison de backend et pour encapsuler la logique relative à ce point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="b8f07-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="b8f07-567">Fonctionnent avec l’injection de dépendances et peuvent être injectés là où c’est nécessaire dans votre application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="b8f07-568">Un client typé accepte un `HttpClient` paramètre dans son constructeur :</span><span class="sxs-lookup"><span data-stu-id="b8f07-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="b8f07-569">Dans le code précédent, la configuration est déplacée dans le client typé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="b8f07-570">L’objet `HttpClient` est exposé en tant que propriété publique.</span><span class="sxs-lookup"><span data-stu-id="b8f07-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="b8f07-571">Il est possible de définir des méthodes d’API spécifiques qui exposent les fonctionnalités de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="b8f07-572">La méthode `GetAspNetDocsIssues` encapsule le code nécessaire pour interroger et analyser les problèmes ouverts les plus récents d’un dépôt GitHub.</span><span class="sxs-lookup"><span data-stu-id="b8f07-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="b8f07-573">Pour inscrire un client typé, la méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> générique peut être utilisée dans `Startup.ConfigureServices`, en spécifiant la classe du client typé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="b8f07-574">Le client typé est inscrit comme étant transitoire avec injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="b8f07-575">Le client typé peut être injecté et utilisé directement :</span><span class="sxs-lookup"><span data-stu-id="b8f07-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="b8f07-576">Si vous préférez, vous pouvez spécifier la configuration d’un client typé lors de l’inscription dans `Startup.ConfigureServices` au lieu de le faire dans le constructeur du client typé :</span><span class="sxs-lookup"><span data-stu-id="b8f07-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="b8f07-577">Il est possible d’encapsuler entièrement le `HttpClient` dans un client typé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="b8f07-578">Au lieu de l’exposer en tant que propriété, vous pouvez fournir des méthodes publiques qui appellent l’instance de `HttpClient` en interne.</span><span class="sxs-lookup"><span data-stu-id="b8f07-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="b8f07-579">Dans le code précédent, le `HttpClient` est stocké en tant que champ privé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="b8f07-580">Tous les accès nécessaires pour effectuer des appels externes passent par la méthode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="b8f07-581">Clients générés</span><span class="sxs-lookup"><span data-stu-id="b8f07-581">Generated clients</span></span>

<span data-ttu-id="b8f07-582">`IHttpClientFactory` peut être utilisé en combinaison avec d’autres bibliothèques tierces, comme [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="b8f07-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="b8f07-583">Refit est une bibliothèque REST pour .NET.</span><span class="sxs-lookup"><span data-stu-id="b8f07-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="b8f07-584">Il convertit les API REST en interfaces dynamiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="b8f07-585">Une implémentation de l’interface est générée dynamiquement par le `RestService`, avec `HttpClient` pour faire les appels HTTP externes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="b8f07-586">Une interface et une réponse sont définies pour représenter l’API externe et sa réponse :</span><span class="sxs-lookup"><span data-stu-id="b8f07-586">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="b8f07-587">Vous pouvez ajouter un client typé en utilisant Refit pour générer l’implémentation :</span><span class="sxs-lookup"><span data-stu-id="b8f07-587">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="b8f07-588">L’interface définie peut être utilisée quand c’est nécessaire, avec l’implémentation fournie par l’injection de dépendances et Refit :</span><span class="sxs-lookup"><span data-stu-id="b8f07-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="b8f07-589">Middleware pour les requêtes sortantes</span><span class="sxs-lookup"><span data-stu-id="b8f07-589">Outgoing request middleware</span></span>

<span data-ttu-id="b8f07-590">`HttpClient` intègre déjà le concept de délégation des gestionnaires qui peuvent être liés ensemble pour les requêtes HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="b8f07-591">Le `IHttpClientFactory` permet de définir facilement les gestionnaires à appliquer pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="b8f07-592">Il prend en charge l’inscription et le chaînage de plusieurs gestionnaires pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="b8f07-593">Chacun de ces gestionnaires peut effectuer un travail avant et après la requête sortante.</span><span class="sxs-lookup"><span data-stu-id="b8f07-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="b8f07-594">Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8f07-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="b8f07-595">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="b8f07-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="b8f07-596">Pour créer un gestionnaire, définissez une classe dérivant de <xref:System.Net.Http.DelegatingHandler>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="b8f07-597">Remplacez la méthode `SendAsync` de façon à exécuter du code avant de passer la requête au gestionnaire suivant dans le pipeline :</span><span class="sxs-lookup"><span data-stu-id="b8f07-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="b8f07-598">Le code précédent définit un gestionnaire de base.</span><span class="sxs-lookup"><span data-stu-id="b8f07-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="b8f07-599">Il vérifie si un en-tête `X-API-KEY` a été inclus dans la requête.</span><span class="sxs-lookup"><span data-stu-id="b8f07-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="b8f07-600">Si l’en-tête est manquant, il peut éviter l’appel HTTP et retourner une réponse appropriée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="b8f07-601">Au cours de l’inscription, un ou plusieurs gestionnaires peuvent être ajoutés à la configuration `HttpClient`d’un.</span><span class="sxs-lookup"><span data-stu-id="b8f07-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="b8f07-602">Cette tâche est accomplie via des méthodes d’extension sur le <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="b8f07-603">Dans le code précédent, le `ValidateHeaderHandler` est inscrit avec une injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="b8f07-604">Le gestionnaire **doit** être inscrit dans l’injection de dépendances en tant que service temporaire, sans étendue.</span><span class="sxs-lookup"><span data-stu-id="b8f07-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="b8f07-605">Si le gestionnaire est inscrit en tant que service étendu et que des services dont dépend le gestionnaire peuvent être supprimés :</span><span class="sxs-lookup"><span data-stu-id="b8f07-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="b8f07-606">Les services du gestionnaire peuvent être supprimés avant que le gestionnaire ne soit hors de portée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="b8f07-607">Les services du gestionnaire supprimés entraînent un échec du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="b8f07-608">Une fois inscrit, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> peut être appelé en passant en entrée le type du gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="b8f07-609">Vous pouvez inscrire plusieurs gestionnaires dans l’ordre où ils doivent être exécutés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="b8f07-610">Chaque gestionnaire wrappe le gestionnaire suivant jusqu’à ce que le dernier `HttpClientHandler` exécute la requête :</span><span class="sxs-lookup"><span data-stu-id="b8f07-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="b8f07-611">Utilisez l’une des approches suivantes pour partager l’état de chaque requête avec les gestionnaires de messages :</span><span class="sxs-lookup"><span data-stu-id="b8f07-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="b8f07-612">Passez des données dans le gestionnaire en utilisant `HttpRequestMessage.Properties`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="b8f07-613">Utilisez `IHttpContextAccessor` pour accéder à la requête en cours.</span><span class="sxs-lookup"><span data-stu-id="b8f07-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="b8f07-614">Créez un objet de stockage `AsyncLocal` personnalisé pour passer les données.</span><span class="sxs-lookup"><span data-stu-id="b8f07-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="b8f07-615">Utiliser les gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-615">Use Polly-based handlers</span></span>

<span data-ttu-id="b8f07-616">`IHttpClientFactory` s’intègre à une bibliothèque tierce très utilisée, appelée [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="b8f07-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="b8f07-617">Polly est une bibliothèque complète de gestion des erreurs transitoires et de résilience pour .NET.</span><span class="sxs-lookup"><span data-stu-id="b8f07-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="b8f07-618">Elle permet aux développeurs de formuler facilement et de façon thread-safe des stratégies, comme Retry (Nouvelle tentative), Circuit Breaker (Disjoncteur), Timeout (Délai d’attente), Bulkhead Isolation (Isolation par cloisonnement) et Fallback (Alternative de repli).</span><span class="sxs-lookup"><span data-stu-id="b8f07-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="b8f07-619">Des méthodes d’extension sont fournies pour permettre l’utilisation de stratégies Polly avec les instances configurées de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-620">Les extensions Polly :</span><span class="sxs-lookup"><span data-stu-id="b8f07-620">The Polly extensions:</span></span>

* <span data-ttu-id="b8f07-621">Prennent en charge l’ajout de gestionnaires Polly à des clients.</span><span class="sxs-lookup"><span data-stu-id="b8f07-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="b8f07-622">Peuvent être utilisées après l’installation du package NuGet [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/).</span><span class="sxs-lookup"><span data-stu-id="b8f07-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="b8f07-623">Ce package n’est pas inclus dans le framework partagé ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b8f07-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="b8f07-624">Gérer les erreurs temporaires</span><span class="sxs-lookup"><span data-stu-id="b8f07-624">Handle transient faults</span></span>

<span data-ttu-id="b8f07-625">Les erreurs courantes se produisent lorsque des appels HTTP externes sont temporaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="b8f07-626">Une méthode d’extension pratique nommée `AddTransientHttpErrorPolicy` est incluse : elle permet de définir une stratégie pour gérer les erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="b8f07-627">Les stratégies configurées avec cette méthode d’extension gèrent `HttpRequestException`, les réponses HTTP 5xx et les réponses HTTP 408.</span><span class="sxs-lookup"><span data-stu-id="b8f07-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="b8f07-628">L’extension `AddTransientHttpErrorPolicy` peut être utilisée dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8f07-629">L’extension fournit l’accès à un objet `PolicyBuilder` configuré pour gérer les erreurs représentant une erreur temporaire possible :</span><span class="sxs-lookup"><span data-stu-id="b8f07-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="b8f07-630">Dans le code précédent, une stratégie `WaitAndRetryAsync` est définie.</span><span class="sxs-lookup"><span data-stu-id="b8f07-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="b8f07-631">Les requêtes qui ont échoué sont retentées jusqu’à trois fois avec un délai de 600 ms entre les tentatives.</span><span class="sxs-lookup"><span data-stu-id="b8f07-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="b8f07-632">Sélectionner dynamiquement des stratégies</span><span class="sxs-lookup"><span data-stu-id="b8f07-632">Dynamically select policies</span></span>

<span data-ttu-id="b8f07-633">Il existe d’autres méthodes d’extension que vous pouvez utiliser pour ajouter des gestionnaires Polly.</span><span class="sxs-lookup"><span data-stu-id="b8f07-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="b8f07-634">Une de ces extensions est `AddPolicyHandler`, qui a plusieurs surcharges.</span><span class="sxs-lookup"><span data-stu-id="b8f07-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="b8f07-635">Une de ces surcharges permet l’inspection de la requête lors de la définition de la stratégie à appliquer :</span><span class="sxs-lookup"><span data-stu-id="b8f07-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="b8f07-636">Dans le code précédent, si la requête sortante est un HTTP GET, un délai d’attente de 10 secondes est appliqué.</span><span class="sxs-lookup"><span data-stu-id="b8f07-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="b8f07-637">Pour toutes les autres méthodes HTTP, un délai d’attente de 30 secondes est utilisé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="b8f07-638">Ajouter plusieurs gestionnaires Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="b8f07-639">Il est courant d’imbriquer des stratégies Polly pour fournir des fonctionnalités améliorées :</span><span class="sxs-lookup"><span data-stu-id="b8f07-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="b8f07-640">Dans l’exemple précédent, deux gestionnaires sont ajoutés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="b8f07-641">Le premier utilise l’extension `AddTransientHttpErrorPolicy` pour ajouter une stratégie de nouvelle tentative.</span><span class="sxs-lookup"><span data-stu-id="b8f07-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="b8f07-642">Les requêtes qui ont échoué sont retentées jusqu’à trois fois.</span><span class="sxs-lookup"><span data-stu-id="b8f07-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="b8f07-643">Le deuxième appel à `AddTransientHttpErrorPolicy` ajoute une stratégie de disjoncteur.</span><span class="sxs-lookup"><span data-stu-id="b8f07-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="b8f07-644">Les requêtes externes supplémentaires sont bloquées pendant 30 secondes si cinq tentatives successives échouent.</span><span class="sxs-lookup"><span data-stu-id="b8f07-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="b8f07-645">Les stratégies de disjoncteur sont avec état.</span><span class="sxs-lookup"><span data-stu-id="b8f07-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="b8f07-646">Tous les appels effectués via ce client partagent le même état du circuit.</span><span class="sxs-lookup"><span data-stu-id="b8f07-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="b8f07-647">Ajouter des stratégies à partir du Registre Polly</span><span class="sxs-lookup"><span data-stu-id="b8f07-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="b8f07-648">Une approche de la gestion des stratégies régulièrement utilisées consiste à les définir une seule fois et à les inscrire avec un `PolicyRegistry`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="b8f07-649">Il existe une méthode d’extension qui permet l’ajout d’un gestionnaire avec une stratégie du Registre :</span><span class="sxs-lookup"><span data-stu-id="b8f07-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="b8f07-650">Dans le code précédent, deux stratégies sont inscrites lorsque `PolicyRegistry` est ajouté à `ServiceCollection`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="b8f07-651">Pour utiliser une stratégie du Registre, la méthode `AddPolicyHandlerFromRegistry` est utilisée, en passant le nom de la stratégie à appliquer.</span><span class="sxs-lookup"><span data-stu-id="b8f07-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="b8f07-652">Vous trouverez plus d’informations sur les intégrations de `IHttpClientFactory` et de Polly dans le [wiki Polly](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="b8f07-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="b8f07-653">HttpClient et gestion de la durée de vie</span><span class="sxs-lookup"><span data-stu-id="b8f07-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="b8f07-654">Une nouvelle instance `HttpClient` est retournée à chaque fois que `CreateClient` est appelé sur `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="b8f07-655">Il existe un <xref:System.Net.Http.HttpMessageHandler> par client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="b8f07-656">La fabrique gère les durées de vie des instances `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="b8f07-657">`IHttpClientFactory` regroupe dans un pool les instances de `HttpMessageHandler` créées par la fabrique pour réduire la consommation des ressources.</span><span class="sxs-lookup"><span data-stu-id="b8f07-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="b8f07-658">Une instance de `HttpMessageHandler` peut être réutilisée à partir du pool quand vous créez une instance de `HttpClient` si sa durée de vie n’a pas expiré.</span><span class="sxs-lookup"><span data-stu-id="b8f07-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="b8f07-659">Le regroupement de gestionnaires en pools est souhaitable, car chaque gestionnaire gère habituellement ses propres connexions HTTP sous-jacentes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="b8f07-660">La création de plus de gestionnaires que nécessaire peut entraîner des délais de connexion.</span><span class="sxs-lookup"><span data-stu-id="b8f07-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="b8f07-661">Certains gestionnaires conservent aussi les connexions indéfiniment ouvertes, ce qui peut empêcher le gestionnaire de réagir aux changements du DNS.</span><span class="sxs-lookup"><span data-stu-id="b8f07-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="b8f07-662">La durée de vie par défaut d’un gestionnaire est de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="b8f07-663">La valeur par défaut peut être remplacée pour chaque client nommé.</span><span class="sxs-lookup"><span data-stu-id="b8f07-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="b8f07-664">Pour la remplacer, appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> sur le `IHttpClientBuilder` qui est retourné lors de la création du client :</span><span class="sxs-lookup"><span data-stu-id="b8f07-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="b8f07-665">La suppression du client n’est pas nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="b8f07-666">La suppression annule les requêtes sortantes et garantit que l’instance `HttpClient` donnée ne peut pas être utilisée après avoir appelé <xref:System.IDisposable.Dispose*>.</span><span class="sxs-lookup"><span data-stu-id="b8f07-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="b8f07-667">`IHttpClientFactory` effectue le suivi et libère les ressources utilisées par les instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-668">Les instances `HttpClient` peuvent généralement être traitées en tant qu’objets .NET ne nécessitant pas une suppression.</span><span class="sxs-lookup"><span data-stu-id="b8f07-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="b8f07-669">Le fait de conserver une seule instance `HttpClient` active pendant une longue durée est un modèle commun utilisé avant le lancement de `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="b8f07-670">Ce modèle devient inutile après la migration vers `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="b8f07-671">Alternatives à IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b8f07-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="b8f07-672">L' `IHttpClientFactory` utilisation de dans une application di-Enabled évite les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="b8f07-673">Problèmes d’épuisement des ressources par `HttpMessageHandler` les instances de regroupement.</span><span class="sxs-lookup"><span data-stu-id="b8f07-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="b8f07-674">Problèmes DNS périmés par `HttpMessageHandler` les instances cycliques à intervalles réguliers.</span><span class="sxs-lookup"><span data-stu-id="b8f07-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="b8f07-675">Il existe d’autres façons de résoudre les problèmes précédents à l’aide d' <xref:System.Net.Http.SocketsHttpHandler> une instance de longue durée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="b8f07-676">Créez une instance de `SocketsHttpHandler` lorsque l’application démarre et utilisez-la pendant toute la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="b8f07-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="b8f07-677">Configurez <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> avec une valeur appropriée en fonction des temps d’actualisation DNS.</span><span class="sxs-lookup"><span data-stu-id="b8f07-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="b8f07-678">Créez `HttpClient` des instances `new HttpClient(handler, disposeHandler: false)` en utilisant si nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b8f07-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="b8f07-679">Les approches précédentes résolvent les problèmes de gestion `IHttpClientFactory` des ressources qui sont résolus de la même façon.</span><span class="sxs-lookup"><span data-stu-id="b8f07-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="b8f07-680">Le `SocketsHttpHandler` partage les connexions `HttpClient` entre les instances.</span><span class="sxs-lookup"><span data-stu-id="b8f07-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="b8f07-681">Ce partage empêche l’épuisement des sockets.</span><span class="sxs-lookup"><span data-stu-id="b8f07-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="b8f07-682">Le `SocketsHttpHandler` cycle des connexions en `PooledConnectionLifetime` fonction de pour éviter les problèmes DNS périmés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="b8f07-683">Cookies</span><span class="sxs-lookup"><span data-stu-id="b8f07-683">Cookies</span></span>

<span data-ttu-id="b8f07-684">Les `HttpMessageHandler` instances regroupées entraînent le partage des `CookieContainer` objets.</span><span class="sxs-lookup"><span data-stu-id="b8f07-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="b8f07-685">Le partage `CookieContainer` d’objets imprévus aboutit souvent à un code incorrect.</span><span class="sxs-lookup"><span data-stu-id="b8f07-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="b8f07-686">Pour les applications qui nécessitent des cookies, utilisez l’une des deux opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="b8f07-687">Désactivation de la gestion automatique des cookies</span><span class="sxs-lookup"><span data-stu-id="b8f07-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="b8f07-688">Éviter`IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="b8f07-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="b8f07-689">Appelez <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> pour désactiver la gestion automatique des cookies :</span><span class="sxs-lookup"><span data-stu-id="b8f07-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="b8f07-690">Journalisation</span><span class="sxs-lookup"><span data-stu-id="b8f07-690">Logging</span></span>

<span data-ttu-id="b8f07-691">Les clients créés via `IHttpClientFactory` enregistrent les messages de journalisation pour toutes les requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="b8f07-692">Activez le niveau d’informations approprié dans votre configuration de journalisation pour voir les messages de journalisation par défaut.</span><span class="sxs-lookup"><span data-stu-id="b8f07-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="b8f07-693">Une journalisation supplémentaire, comme celle des en-têtes des requêtes, est incluse seulement au niveau de trace.</span><span class="sxs-lookup"><span data-stu-id="b8f07-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="b8f07-694">La catégorie de journal utilisée pour chaque client comprend le nom du client.</span><span class="sxs-lookup"><span data-stu-id="b8f07-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="b8f07-695">Par exemple, un client nommé *MyNamedClient* journalise les messages avec la catégorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="b8f07-696">Les messages avec le suffixe *LogicalHandler* se produisent en dehors du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="b8f07-697">Lors d’une requête, les messages sont journalisés avant que d’autres gestionnaires du pipeline l’aient traitée.</span><span class="sxs-lookup"><span data-stu-id="b8f07-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="b8f07-698">Lors d’une réponse, les messages sont journalisés une fois que tous les autres gestionnaires du pipeline ont reçu la réponse.</span><span class="sxs-lookup"><span data-stu-id="b8f07-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="b8f07-699">La journalisation se produit également à l’intérieur du pipeline du gestionnaire de requêtes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="b8f07-700">Dans l’exemple *MyNamedClient*, ces messages sont journalisés avec la catégorie de journalisation `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="b8f07-701">Pour la requête, cela se produit après que tous les autres gestionnaires ont été exécutés et immédiatement avant l’envoi de la requête sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="b8f07-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="b8f07-702">Lors de la réponse, cette journalisation inclut l’état de la réponse avant qu’elle repasse à travers le pipeline de gestionnaires.</span><span class="sxs-lookup"><span data-stu-id="b8f07-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="b8f07-703">L’activation de la journalisation à l’extérieur et à l’intérieur du pipeline permet l’inspection des changements apportés par les autres gestionnaires du pipeline.</span><span class="sxs-lookup"><span data-stu-id="b8f07-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="b8f07-704">Par exemple, cela peut comprendre des changements apportés aux en-têtes des requêtes ou au code d’état de la réponse.</span><span class="sxs-lookup"><span data-stu-id="b8f07-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="b8f07-705">L’ajout du nom du client dans la catégorie de journalisation permet si nécessaire de filtrer le journal pour des clients nommés spécifiques.</span><span class="sxs-lookup"><span data-stu-id="b8f07-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="b8f07-706">Configurer le HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="b8f07-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="b8f07-707">Il peut être nécessaire de contrôler la configuration du `HttpMessageHandler` interne utilisé par un client.</span><span class="sxs-lookup"><span data-stu-id="b8f07-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="b8f07-708">Un `IHttpClientBuilder` est retourné quand vous ajoutez des clients nommés ou typés.</span><span class="sxs-lookup"><span data-stu-id="b8f07-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="b8f07-709">La méthode d’extension <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> peut être utilisée pour définir un délégué.</span><span class="sxs-lookup"><span data-stu-id="b8f07-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="b8f07-710">Le délégué est utilisé pour créer et configurer le `HttpMessageHandler` principal utilisé par ce client :</span><span class="sxs-lookup"><span data-stu-id="b8f07-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="b8f07-711">Utiliser IHttpClientFactory dans une application console</span><span class="sxs-lookup"><span data-stu-id="b8f07-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="b8f07-712">Dans une application console, ajoutez les références de package suivantes au projet :</span><span class="sxs-lookup"><span data-stu-id="b8f07-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="b8f07-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="b8f07-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="b8f07-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="b8f07-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="b8f07-715">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b8f07-715">In the following example:</span></span>

* <span data-ttu-id="b8f07-716"><xref:System.Net.Http.IHttpClientFactory> est inscrit dans le conteneur de service dans de l’[hôte générique](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b8f07-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="b8f07-717">`MyService` crée une instance de fabrique cliente à partir du service, qui est utilisée pour créer un `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="b8f07-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="b8f07-718">`HttpClient` est utilisé pour récupérer une page web.</span><span class="sxs-lookup"><span data-stu-id="b8f07-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="b8f07-719">`Main` crée une étendue pour exécuter la méthode `GetPage` du service et écrire les 500 premiers caractères du contenu de la page web dans la console.</span><span class="sxs-lookup"><span data-stu-id="b8f07-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="b8f07-720">Intergiciel (middleware) de propagation d’en-tête</span><span class="sxs-lookup"><span data-stu-id="b8f07-720">Header propagation middleware</span></span>

<span data-ttu-id="b8f07-721">La propagation d’en-tête est un intergiciel (middleware) pris en charge par la communauté pour propager les en-têtes HTTP de la demande entrante aux demandes du client HTTP sortantes.</span><span class="sxs-lookup"><span data-stu-id="b8f07-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="b8f07-722">Pour utiliser la propagation d’en-tête :</span><span class="sxs-lookup"><span data-stu-id="b8f07-722">To use header propagation:</span></span>

* <span data-ttu-id="b8f07-723">Référencez le port de la communauté pris en charge du package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="b8f07-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="b8f07-724">ASP.NET Core 3,1 et versions ultérieures prennent en charge [Microsoft. AspNetCore. HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="b8f07-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="b8f07-725">Configurer l’intergiciel et `HttpClient` dans : `Startup`</span><span class="sxs-lookup"><span data-stu-id="b8f07-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="b8f07-726">Le client comprend les en-têtes configurés pour les demandes sortantes :</span><span class="sxs-lookup"><span data-stu-id="b8f07-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="b8f07-727">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="b8f07-727">Additional resources</span></span>

* [<span data-ttu-id="b8f07-728">Utilisez HttpClientFactory pour implémenter des requêtes HTTP résilientes</span><span class="sxs-lookup"><span data-stu-id="b8f07-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="b8f07-729">Implémenter de nouvelles tentatives d’appel HTTP avec interruption exponentielle avec des stratégies Polly et HttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="b8f07-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="b8f07-730">Implémenter le modèle Disjoncteur</span><span class="sxs-lookup"><span data-stu-id="b8f07-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
