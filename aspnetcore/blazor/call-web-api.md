---
title: Appelez une API web de Blazor ASP.NET Core WebAssembly
author: guardrex
description: Découvrez comment appeler une API Blazor Web à partir d’une application WebAssembly à l’aide d’aides JSON, y compris en faisant des demandes de partage de ressources inter-origine (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 943f9d440adbe11ac1977f28aebee53a5510a86b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661577"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="1f84a-103">Appelez une API web de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="1f84a-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="1f84a-104">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), et Juan De la [Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="1f84a-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="1f84a-105">Les applications `HttpClient` WebAssembly appellent les API Web à l’aide d’un service préconfiguré. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="1f84a-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="1f84a-106">Composez les demandes, qui peuvent inclure Blazor des options [JavaScript Fetch API,](https://developer.mozilla.org/docs/Web/API/Fetch_API) en utilisant des aides JSON ou avec <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="1f84a-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="1f84a-107">Le `HttpClient` service Blazor dans les applications WebAssembly est axé sur la remise des demandes au serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="1f84a-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="1f84a-108">Les conseils dans ce sujet Blazor ne concerne que les applications WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="1f84a-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="1f84a-109">Les applications serveur appellent <xref:System.Net.Http.HttpClient> les API <xref:System.Net.Http.IHttpClientFactory>Web à l’aide d’instances, généralement créées à l’aide de . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="1f84a-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="1f84a-110">Les conseils dans ce sujet Blazor ne se rapportent pas aux applications Server.</span><span class="sxs-lookup"><span data-stu-id="1f84a-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="1f84a-111">Lors Blazor du développement d’applications <xref:fundamentals/http-requests>Server, suivez les conseils dans .</span><span class="sxs-lookup"><span data-stu-id="1f84a-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="1f84a-112">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample)) &ndash; Sélectionnez l’application *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="1f84a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="1f84a-113">Voir les composants suivants dans l’application *d’échantillon BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="1f84a-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="1f84a-114">Appelez l’API Web (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="1f84a-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="1f84a-115">HTTP Request Tester (*Composants/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="1f84a-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="1f84a-116">.</span><span class="sxs-lookup"><span data-stu-id="1f84a-116">Packages</span></span>

<span data-ttu-id="1f84a-117">Référence du [paquet System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="1f84a-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="1f84a-118">Ajouter le service HttpClient</span><span class="sxs-lookup"><span data-stu-id="1f84a-118">Add the HttpClient service</span></span>

<span data-ttu-id="1f84a-119">Dans `Program.Main`, `HttpClient` ajouter un service s’il n’existe pas déjà:</span><span class="sxs-lookup"><span data-stu-id="1f84a-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="1f84a-120">Aides httpClient et JSON</span><span class="sxs-lookup"><span data-stu-id="1f84a-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="1f84a-121">Dans Blazor une application WebAssembly, [HttpClient](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour faire des demandes de retour sur le serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="1f84a-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="1f84a-122">Une Blazor application Server n’inclut pas de `HttpClient` service par défaut.</span><span class="sxs-lookup"><span data-stu-id="1f84a-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="1f84a-123">Fournir `HttpClient` un à l’application en utilisant l’infrastructure [de l’usine HttpClient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="1f84a-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="1f84a-124">`HttpClient`et les aides JSON sont également utilisés pour appeler des points de terminaison Web tiers API.</span><span class="sxs-lookup"><span data-stu-id="1f84a-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="1f84a-125">`HttpClient`est implémenté à l’aide du navigateur [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) et est soumis à ses limites, y compris l’application de la même politique d’origine.</span><span class="sxs-lookup"><span data-stu-id="1f84a-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="1f84a-126">L’adresse de base du client est réglée à l’adresse du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="1f84a-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="1f84a-127">Injecter `HttpClient` une instance `@inject` à l’aide de la directive :</span><span class="sxs-lookup"><span data-stu-id="1f84a-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="1f84a-128">Dans les exemples suivants, un API Web Todo crée, lit, met à jour et supprime les opérations (CRUD).</span><span class="sxs-lookup"><span data-stu-id="1f84a-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="1f84a-129">Les exemples sont `TodoItem` basés sur une classe qui stocke le :</span><span class="sxs-lookup"><span data-stu-id="1f84a-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="1f84a-130">ID`Id`( `long` &ndash; , ) ID unique de l’article.</span><span class="sxs-lookup"><span data-stu-id="1f84a-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="1f84a-131">Nom`Name`( `string` &ndash; , ) Nom de l’article.</span><span class="sxs-lookup"><span data-stu-id="1f84a-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="1f84a-132">État`IsComplete`( `bool` &ndash; , ) Indication si l’élément Todo est terminé.</span><span class="sxs-lookup"><span data-stu-id="1f84a-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="1f84a-133">Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :</span><span class="sxs-lookup"><span data-stu-id="1f84a-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="1f84a-134">`GetFromJsonAsync`&ndash; Envoie une demande HTTP GET et analyse le corps de réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="1f84a-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="1f84a-135">Dans le code `_todoItems` suivant, les sont affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="1f84a-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="1f84a-136">La `GetTodoItems` méthode est déclenchée lorsque le composant est fini de rendu ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="1f84a-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="1f84a-137">Consultez l’application de l’échantillon pour un exemple complet.</span><span class="sxs-lookup"><span data-stu-id="1f84a-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="1f84a-138">`PostAsJsonAsync`&ndash; Envoie une demande HTTP POST, y compris le contenu codé par JSON, et analyse le corps de réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="1f84a-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="1f84a-139">Dans le code `_newItemName` suivant, est fourni par un élément lié de la composante.</span><span class="sxs-lookup"><span data-stu-id="1f84a-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="1f84a-140">La `AddItem` méthode est déclenchée `<button>` par la sélection d’un élément.</span><span class="sxs-lookup"><span data-stu-id="1f84a-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="1f84a-141">Consultez l’application de l’échantillon pour un exemple complet.</span><span class="sxs-lookup"><span data-stu-id="1f84a-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="1f84a-142">Appels `PostAsJsonAsync` à <xref:System.Net.Http.HttpResponseMessage>retourner un .</span><span class="sxs-lookup"><span data-stu-id="1f84a-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="1f84a-143">Pour déséialiser le contenu JSON à `ReadFromJsonAsync<T>` partir du message de réponse, utilisez la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="1f84a-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="1f84a-144">`PutAsJsonAsync`&ndash; Envoie une demande HTTP PUT, y compris le contenu codé par JSON.</span><span class="sxs-lookup"><span data-stu-id="1f84a-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="1f84a-145">Dans le code `_editItem` suivant, `IsCompleted` les valeurs et `Name` sont fournies par des éléments liés du composant.</span><span class="sxs-lookup"><span data-stu-id="1f84a-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="1f84a-146">L’élément `Id` est défini lorsque l’élément est sélectionné dans `EditItem` une autre partie de l’interface utilisateur et est appelé.</span><span class="sxs-lookup"><span data-stu-id="1f84a-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="1f84a-147">La `SaveItem` méthode est déclenchée par `<button>` la sélection de l’élément Enregistrer.</span><span class="sxs-lookup"><span data-stu-id="1f84a-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="1f84a-148">Consultez l’application de l’échantillon pour un exemple complet.</span><span class="sxs-lookup"><span data-stu-id="1f84a-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="1f84a-149">Appels `PutAsJsonAsync` à <xref:System.Net.Http.HttpResponseMessage>retourner un .</span><span class="sxs-lookup"><span data-stu-id="1f84a-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="1f84a-150">Pour déséialiser le contenu JSON à `ReadFromJsonAsync<T>` partir du message de réponse, utilisez la méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="1f84a-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="1f84a-151"><xref:System.Net.Http>comprend des méthodes d’extension supplémentaires pour envoyer des demandes HTTP et recevoir des réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="1f84a-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="1f84a-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) est utilisé pour envoyer une demande HTTP DELETE à une API web.</span><span class="sxs-lookup"><span data-stu-id="1f84a-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="1f84a-153">Dans le code suivant, l’élément Supprimer `<button>` appelle la `DeleteItem` méthode.</span><span class="sxs-lookup"><span data-stu-id="1f84a-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="1f84a-154">L’élément `<input>` lié `id` fournit l’élément à supprimer.</span><span class="sxs-lookup"><span data-stu-id="1f84a-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="1f84a-155">Consultez l’application de l’échantillon pour un exemple complet.</span><span class="sxs-lookup"><span data-stu-id="1f84a-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="1f84a-156">Partage des ressources cross-origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="1f84a-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="1f84a-157">La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="1f84a-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="1f84a-158">Cette restriction est appelée la *même politique d’origine*.</span><span class="sxs-lookup"><span data-stu-id="1f84a-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="1f84a-159">La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="1f84a-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="1f84a-160">Pour faire des demandes du navigateur à un point de terminaison d’origine différente, le *critère d’évaluation* doit permettre le [partage des ressources inter-origine (CORS).](https://www.w3.org/TR/cors/)</span><span class="sxs-lookup"><span data-stu-id="1f84a-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="1f84a-161">[ Blazor L’application d’échantillonnage WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) démontre l’utilisation de CORS dans le composant Call Web API *(Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="1f84a-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="1f84a-162">Pour permettre à d’autres sites de faire des demandes de <xref:security/cors>partage de ressources inter-origine (CORS) à votre application, voir .</span><span class="sxs-lookup"><span data-stu-id="1f84a-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="1f84a-163">HttpClient et HttpRequestMessage avec les options de demande d’API Fetch</span><span class="sxs-lookup"><span data-stu-id="1f84a-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="1f84a-164">Lorsque vous exécutez sur Blazor WebAssembly dans une application <xref:System.Net.Http.HttpRequestMessage> WebAssembly, utilisez [HttpClient](xref:fundamentals/http-requests) et personnalisez les demandes.</span><span class="sxs-lookup"><span data-stu-id="1f84a-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="1f84a-165">Par exemple, vous pouvez spécifier la demande URI, la méthode HTTP et les en-têtes de demande souhaités.</span><span class="sxs-lookup"><span data-stu-id="1f84a-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="1f84a-166">Pour plus d’informations sur les options Fetch API, voir [docs web MDN: WindowOrWorkerGlobalScope.fetch () :Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="1f84a-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="1f84a-167">Lors de l’envoi d’informations d’identification (cookies d’autorisation/en-têtes) sur les demandes CORS, l’en-tête `Authorization` doit être autorisé par la politique CORS.</span><span class="sxs-lookup"><span data-stu-id="1f84a-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="1f84a-168">La stratégie suivante comprend la configuration pour :</span><span class="sxs-lookup"><span data-stu-id="1f84a-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="1f84a-169">Origines`http://localhost:5000`de `https://localhost:5001`la demande ( , ).</span><span class="sxs-lookup"><span data-stu-id="1f84a-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="1f84a-170">Toute méthode (verbe).</span><span class="sxs-lookup"><span data-stu-id="1f84a-170">Any method (verb).</span></span>
* <span data-ttu-id="1f84a-171">`Content-Type`et `Authorization` en-têtes.</span><span class="sxs-lookup"><span data-stu-id="1f84a-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="1f84a-172">Pour permettre un en-tête `x-custom-header`personnalisé (par <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>exemple, ), énumérez l’en-tête lors de l’appel .</span><span class="sxs-lookup"><span data-stu-id="1f84a-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="1f84a-173">Informations d’identification définies par`credentials` le code `include`JavaScript côté client (propriété définie à ).</span><span class="sxs-lookup"><span data-stu-id="1f84a-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="1f84a-174">Pour plus d’informations, voir <xref:security/cors> et l’exemple de l’application HTTP Request Tester composant (*Composants/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="1f84a-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f84a-175">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="1f84a-175">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="1f84a-176">Configuration de point de terminaison Kestrel HTTPS</span><span class="sxs-lookup"><span data-stu-id="1f84a-176">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="1f84a-177">Cross Origin Resource Sharing (CORS) à W3C</span><span class="sxs-lookup"><span data-stu-id="1f84a-177">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
