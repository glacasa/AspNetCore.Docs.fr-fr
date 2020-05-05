---
title: Appeler une API Web à partir Blazor de ASP.net Core webassembly
author: guardrex
description: Découvrez comment appeler une API Web à partir d' Blazor une application webassembly à l’aide des applications auxiliaires JSON, y compris la création de demandes de partage des ressources Cross-Origin (cors).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767146"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="418b4-103">Appeler une API Web à partir de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="418b4-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="418b4-104">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="418b4-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="418b4-105">Les applications webassembly appellent des API Web à l' `HttpClient` aide d’un service préconfiguré. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="418b4-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="418b4-106">Composez des requêtes, qui peuvent inclure des options de l' [API d’extraction](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, à l’aide Blazor des applications auxiliaires JSON ou de <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="418b4-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="418b4-107">Dans `HttpClient` les applications Blazor webassembly, le service est axé sur l’exécution de requêtes sur le serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="418b4-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="418b4-108">Les instructions de cette rubrique concernent Blazor uniquement les applications webassembly.</span><span class="sxs-lookup"><span data-stu-id="418b4-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="418b4-109">Les applications serveur appellent des API <xref:System.Net.Http.HttpClient> Web à l’aide d' <xref:System.Net.Http.IHttpClientFactory>instances, généralement créées à l’aide de. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="418b4-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="418b4-110">Les instructions de cette rubrique ne concernent pas Blazor les applications serveur.</span><span class="sxs-lookup"><span data-stu-id="418b4-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="418b4-111">Lors du Blazor développement d’applications serveur, suivez les <xref:fundamentals/http-requests>instructions fournies dans.</span><span class="sxs-lookup"><span data-stu-id="418b4-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="418b4-112">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)) &ndash; sélectionnez l’application *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="418b4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="418b4-113">Consultez les composants suivants dans l’exemple d’application *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="418b4-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="418b4-114">Appeler l’API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="418b4-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="418b4-115">Testeur de requêtes HTTP (*composants/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="418b4-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="418b4-116">.</span><span class="sxs-lookup"><span data-stu-id="418b4-116">Packages</span></span>

<span data-ttu-id="418b4-117">Référencez le package NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="418b4-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="418b4-118">Ajouter le service HttpClient</span><span class="sxs-lookup"><span data-stu-id="418b4-118">Add the HttpClient service</span></span>

<span data-ttu-id="418b4-119">Dans `Program.Main`, ajoutez un `HttpClient` service s’il n’existe pas déjà :</span><span class="sxs-lookup"><span data-stu-id="418b4-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="418b4-120">Applications auxiliaires HttpClient et JSON</span><span class="sxs-lookup"><span data-stu-id="418b4-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="418b4-121">Dans une Blazor application webassembly, [httpclient](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour effectuer des demandes auprès du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="418b4-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="418b4-122">Une Blazor application serveur n’inclut pas `HttpClient` de service par défaut.</span><span class="sxs-lookup"><span data-stu-id="418b4-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="418b4-123">Fournissez `HttpClient` un à l’application à l’aide de l' [infrastructure de fabrique httpclient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="418b4-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="418b4-124">`HttpClient`et les auxiliaires JSON sont également utilisés pour appeler des points de terminaison d’API Web tiers.</span><span class="sxs-lookup"><span data-stu-id="418b4-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="418b4-125">`HttpClient`est implémenté à l’aide de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API) du navigateur et est soumis à ses limitations, y compris l’application de la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="418b4-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="418b4-126">L’adresse de base du client est définie sur l’adresse du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="418b4-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="418b4-127">Injecter `HttpClient` une instance à `@inject` l’aide de la directive :</span><span class="sxs-lookup"><span data-stu-id="418b4-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="418b4-128">Dans les exemples suivants, une API Web todo traite les opérations de création, lecture, mise à jour et suppression (CRUD).</span><span class="sxs-lookup"><span data-stu-id="418b4-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="418b4-129">Les exemples sont basés sur une `TodoItem` classe qui stocke les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="418b4-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="418b4-130">ID (`Id`, `long`) &ndash; ID unique de l’élément.</span><span class="sxs-lookup"><span data-stu-id="418b4-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="418b4-131">Nom (`Name`, `string`) &ndash; nom de l’élément.</span><span class="sxs-lookup"><span data-stu-id="418b4-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="418b4-132">État (`IsComplete`, `bool`) &ndash; indiquant si l’élément TODO est terminé.</span><span class="sxs-lookup"><span data-stu-id="418b4-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="418b4-133">Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :</span><span class="sxs-lookup"><span data-stu-id="418b4-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="418b4-134">`GetFromJsonAsync`&ndash; Envoie une requête http obtenir et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="418b4-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="418b4-135">Dans le code suivant, les `_todoItems` sont affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="418b4-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="418b4-136">La `GetTodoItems` méthode est déclenchée lorsque le rendu du composant est terminé ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="418b4-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="418b4-137">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="418b4-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="418b4-138">`PostAsJsonAsync`&ndash; Envoie une requête http postérieure, y compris du contenu encodé JSON, et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="418b4-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="418b4-139">Dans le code suivant, `_newItemName` est fourni par un élément lié du composant.</span><span class="sxs-lookup"><span data-stu-id="418b4-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="418b4-140">La `AddItem` méthode est déclenchée par la sélection `<button>` d’un élément.</span><span class="sxs-lookup"><span data-stu-id="418b4-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="418b4-141">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="418b4-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="418b4-142">Appelle pour `PostAsJsonAsync` retourner un <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="418b4-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="418b4-143">Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="418b4-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="418b4-144">`PutAsJsonAsync`&ndash; Envoie une requête HTTP PUT, y compris du contenu encodé JSON.</span><span class="sxs-lookup"><span data-stu-id="418b4-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="418b4-145">Dans le code suivant, `_editItem` les valeurs `Name` pour `IsCompleted` et sont fournies par les éléments dépendants du composant.</span><span class="sxs-lookup"><span data-stu-id="418b4-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="418b4-146">L’élément `Id` est défini lorsque l’élément est sélectionné dans une autre partie de l’interface utilisateur `EditItem` et est appelé.</span><span class="sxs-lookup"><span data-stu-id="418b4-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="418b4-147">La `SaveItem` méthode est déclenchée par la sélection de `<button>` l’élément Save.</span><span class="sxs-lookup"><span data-stu-id="418b4-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="418b4-148">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="418b4-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  <span data-ttu-id="418b4-149">Appelle pour `PutAsJsonAsync` retourner un <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="418b4-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="418b4-150">Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="418b4-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="418b4-151"><xref:System.Net.Http>contient des méthodes d’extension supplémentaires pour envoyer des requêtes HTTP et recevoir des réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="418b4-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="418b4-152">[Httpclient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) est utilisé pour envoyer une requête HTTP DELETE à une API Web.</span><span class="sxs-lookup"><span data-stu-id="418b4-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="418b4-153">Dans le code suivant, l’élément `<button>` delete appelle la `DeleteItem` méthode.</span><span class="sxs-lookup"><span data-stu-id="418b4-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="418b4-154">L’élément `<input>` lié fournit le `id` de l’élément à supprimer.</span><span class="sxs-lookup"><span data-stu-id="418b4-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="418b4-155">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="418b4-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="418b4-156">Partage des ressources cross-origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="418b4-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="418b4-157">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="418b4-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="418b4-158">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="418b4-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="418b4-159">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="418b4-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="418b4-160">Pour effectuer des demandes à partir du navigateur vers un point de terminaison avec une origine différente, le *point de terminaison* doit activer le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="418b4-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="418b4-161">L' [ Blazor exemple d’application webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustre l’utilisation de cors dans le composant appeler l’API Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="418b4-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="418b4-162">Pour permettre à d’autres sites d’effectuer des demandes de partage de ressources Cross-Origin (CORS) <xref:security/cors>à votre application, consultez.</span><span class="sxs-lookup"><span data-stu-id="418b4-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="418b4-163">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="418b4-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="418b4-164">Configuration du point de terminaison HTTPs Kestrel</span><span class="sxs-lookup"><span data-stu-id="418b4-164">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="418b4-165">Cross Origin Resource Sharing (CORS) au W3C</span><span class="sxs-lookup"><span data-stu-id="418b4-165">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
