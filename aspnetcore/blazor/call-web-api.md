---
title: Appeler une API Web à partir de ASP.NET Core Blazor Webassembly
author: guardrex
description: Découvrez comment appeler une API Web à partir d’une Blazor application Webassembly à l’aide des applications auxiliaires JSON, y compris la création de demandes de partage des ressources Cross-Origin (cors).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 7ed2d51c0d41a50a2e139d739a0a06cd9f392a83
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153503"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="ccab5-103">Appeler une API Web à partir de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="ccab5-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="ccab5-104">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="ccab5-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ccab5-105">Les applications [ Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) appellent des API Web à l’aide d’un `HttpClient` service préconfiguré.</span><span class="sxs-lookup"><span data-stu-id="ccab5-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="ccab5-106">Composez des requêtes, qui peuvent inclure des options de l' [API d’extraction](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, à l’aide Blazor des applications auxiliaires JSON ou de <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="ccab5-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="ccab5-107">`HttpClient`Dans les Blazor applications webassembly, le service est axé sur l’exécution de requêtes sur le serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="ccab5-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="ccab5-108">Les instructions de cette rubrique concernent uniquement les Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="ccab5-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="ccab5-109">Les applications [ Blazor serveur](xref:blazor/hosting-models#blazor-server) appellent des API Web à l’aide d' <xref:System.Net.Http.HttpClient> instances, généralement créées à l’aide de <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="ccab5-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="ccab5-110">Les instructions de cette rubrique ne concernent pas les Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="ccab5-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="ccab5-111">Lors Blazor du développement d’applications serveur, suivez les instructions fournies dans <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="ccab5-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="ccab5-112">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)) &ndash; Sélectionnez l’application *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="ccab5-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="ccab5-113">Consultez les composants suivants dans l’exemple d’application *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="ccab5-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="ccab5-114">Appeler l’API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="ccab5-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="ccab5-115">Testeur de requêtes HTTP (*composants/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="ccab5-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="ccab5-116">.</span><span class="sxs-lookup"><span data-stu-id="ccab5-116">Packages</span></span>

<span data-ttu-id="ccab5-117">Référencez le package NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="ccab5-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="ccab5-118">Ajouter le service HttpClient</span><span class="sxs-lookup"><span data-stu-id="ccab5-118">Add the HttpClient service</span></span>

<span data-ttu-id="ccab5-119">Dans `Program.Main` , ajoutez un `HttpClient` service s’il n’existe pas déjà :</span><span class="sxs-lookup"><span data-stu-id="ccab5-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="ccab5-120">Applications auxiliaires HttpClient et JSON</span><span class="sxs-lookup"><span data-stu-id="ccab5-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="ccab5-121">Dans une Blazor application Webassembly, [httpclient](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour effectuer des demandes auprès du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="ccab5-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="ccab5-122">Une Blazor application serveur n’inclut pas `HttpClient` de service par défaut.</span><span class="sxs-lookup"><span data-stu-id="ccab5-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="ccab5-123">Fournissez un `HttpClient` à l’application à l’aide de l' [infrastructure de fabrique httpclient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="ccab5-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="ccab5-124">`HttpClient`et les auxiliaires JSON sont également utilisés pour appeler des points de terminaison d’API Web tiers.</span><span class="sxs-lookup"><span data-stu-id="ccab5-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="ccab5-125">`HttpClient`est implémenté à l’aide de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API) du navigateur et est soumis à ses limitations, y compris l’application de la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="ccab5-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="ccab5-126">L’adresse de base du client est définie sur l’adresse du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="ccab5-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="ccab5-127">Injecter une `HttpClient` instance à l’aide de la `@inject` directive :</span><span class="sxs-lookup"><span data-stu-id="ccab5-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="ccab5-128">Dans les exemples suivants, une API Web todo traite les opérations de création, lecture, mise à jour et suppression (CRUD).</span><span class="sxs-lookup"><span data-stu-id="ccab5-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="ccab5-129">Les exemples sont basés sur une `TodoItem` classe qui stocke les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="ccab5-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="ccab5-130">ID ( `Id` , `long` ) &ndash; ID unique de l’élément.</span><span class="sxs-lookup"><span data-stu-id="ccab5-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="ccab5-131">Nom ( `Name` , `string` ) &ndash; nom de l’élément.</span><span class="sxs-lookup"><span data-stu-id="ccab5-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="ccab5-132">État ( `IsComplete` , `bool` ) &ndash; indiquant si l’élément TODO est terminé.</span><span class="sxs-lookup"><span data-stu-id="ccab5-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="ccab5-133">Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :</span><span class="sxs-lookup"><span data-stu-id="ccab5-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="ccab5-134">`GetFromJsonAsync`&ndash;Envoie une requête http obtenir et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="ccab5-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="ccab5-135">Dans le code suivant, les `todoItems` sont affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="ccab5-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="ccab5-136">La `GetTodoItems` méthode est déclenchée lorsque le rendu du composant est terminé ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="ccab5-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="ccab5-137">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="ccab5-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="ccab5-138">`PostAsJsonAsync`&ndash;Envoie une requête http postérieure, y compris du contenu encodé JSON, et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="ccab5-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="ccab5-139">Dans le code suivant, `newItemName` est fourni par un élément lié du composant.</span><span class="sxs-lookup"><span data-stu-id="ccab5-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="ccab5-140">La `AddItem` méthode est déclenchée par la sélection d’un `<button>` élément.</span><span class="sxs-lookup"><span data-stu-id="ccab5-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="ccab5-141">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="ccab5-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="ccab5-142">Appelle pour `PostAsJsonAsync` retourner un <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="ccab5-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="ccab5-143">Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="ccab5-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="ccab5-144">`PutAsJsonAsync`&ndash;Envoie une requête HTTP PUT, y compris du contenu encodé JSON.</span><span class="sxs-lookup"><span data-stu-id="ccab5-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="ccab5-145">Dans le code suivant, les `editItem` valeurs pour `Name` et `IsCompleted` sont fournies par les éléments dépendants du composant.</span><span class="sxs-lookup"><span data-stu-id="ccab5-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="ccab5-146">L’élément `Id` est défini lorsque l’élément est sélectionné dans une autre partie de l’interface utilisateur et `EditItem` est appelé.</span><span class="sxs-lookup"><span data-stu-id="ccab5-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="ccab5-147">La `SaveItem` méthode est déclenchée par la sélection de l' `<button>` élément Save.</span><span class="sxs-lookup"><span data-stu-id="ccab5-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="ccab5-148">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="ccab5-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="ccab5-149">Appelle pour `PutAsJsonAsync` retourner un <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="ccab5-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="ccab5-150">Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="ccab5-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="ccab5-151"><xref:System.Net.Http>contient des méthodes d’extension supplémentaires pour envoyer des requêtes HTTP et recevoir des réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="ccab5-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="ccab5-152">[Httpclient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) est utilisé pour envoyer une requête HTTP DELETE à une API Web.</span><span class="sxs-lookup"><span data-stu-id="ccab5-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="ccab5-153">Dans le code suivant, l' `<button>` élément delete appelle la `DeleteItem` méthode.</span><span class="sxs-lookup"><span data-stu-id="ccab5-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="ccab5-154">L' `<input>` élément lié fournit le `id` de l’élément à supprimer.</span><span class="sxs-lookup"><span data-stu-id="ccab5-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="ccab5-155">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="ccab5-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="ccab5-156">Nommé HttpClient avec IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="ccab5-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="ccab5-157"><xref:System.Net.Http.IHttpClientFactory>les services et la configuration d’un nommé <xref:System.Net.Http.HttpClient> sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ccab5-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="ccab5-158">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ccab5-158">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ccab5-159">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ccab5-159">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="ccab5-160">HttpClient typé</span><span class="sxs-lookup"><span data-stu-id="ccab5-160">Typed HttpClient</span></span>

<span data-ttu-id="ccab5-161">Typé <xref:System.Net.Http.HttpClient> utilise une ou plusieurs instances de l’application <xref:System.Net.Http.HttpClient> , default ou named, pour retourner des données à partir d’un ou plusieurs points de terminaison d’API Web.</span><span class="sxs-lookup"><span data-stu-id="ccab5-161">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="ccab5-162">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="ccab5-162">*WeatherForecastClient.cs*:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="ccab5-163">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ccab5-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="ccab5-164">Les composants injectent le typé `HttpClient` pour appeler l’API Web.</span><span class="sxs-lookup"><span data-stu-id="ccab5-164">Components inject the typed `HttpClient` to call the web API.</span></span>

<span data-ttu-id="ccab5-165">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="ccab5-165">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="ccab5-166">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="ccab5-166">Handle errors</span></span>

<span data-ttu-id="ccab5-167">Lorsque des erreurs se produisent lors de l’interaction avec une API Web, elles peuvent être gérées par le code du développeur.</span><span class="sxs-lookup"><span data-stu-id="ccab5-167">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="ccab5-168">Par exemple, `GetFromJsonAsync` attend une réponse JSON de l’API du serveur avec un `Content-Type` de `application/json` .</span><span class="sxs-lookup"><span data-stu-id="ccab5-168">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="ccab5-169">Si la réponse n’est pas au format JSON, la validation du contenu lève une exception <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="ccab5-169">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="ccab5-170">Dans l’exemple suivant, le point de terminaison d’URI pour la demande de données de prévision météo est mal orthographié.</span><span class="sxs-lookup"><span data-stu-id="ccab5-170">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="ccab5-171">L’URI doit être à, `WeatherForecast` mais apparaît dans l’appel en tant que `WeatherForcast` (« e » manquant).</span><span class="sxs-lookup"><span data-stu-id="ccab5-171">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="ccab5-172">L' `GetFromJsonAsync` appel attend que JSON soit retourné, mais le serveur retourne du code HTML pour une exception non gérée sur le serveur avec un `Content-Type` de `text/html` .</span><span class="sxs-lookup"><span data-stu-id="ccab5-172">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="ccab5-173">L’exception non gérée se produit sur le serveur, car le chemin d’accès est introuvable et l’intergiciel ne peut pas traiter une page ou une vue pour la demande.</span><span class="sxs-lookup"><span data-stu-id="ccab5-173">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="ccab5-174">Dans `OnInitializedAsync` sur le client, <xref:System.NotSupportedException> est levée lorsque le contenu de la réponse est validé en tant que non-JSON.</span><span class="sxs-lookup"><span data-stu-id="ccab5-174">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="ccab5-175">L’exception est interceptée dans le `catch` bloc, où la logique personnalisée peut consigner l’erreur ou présenter un message d’erreur convivial à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="ccab5-175">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="ccab5-176">L’exemple précédent est fourni à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="ccab5-176">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="ccab5-177">Une application serveur d’API Web peut être configurée pour retourner JSON même lorsqu’un point de terminaison n’existe pas ou qu’un exception non géré est présent sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="ccab5-177">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="ccab5-178">Pour plus d'informations, consultez <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="ccab5-178">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="ccab5-179">Partage des ressources cross-origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="ccab5-179">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="ccab5-180">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="ccab5-180">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="ccab5-181">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="ccab5-181">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="ccab5-182">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="ccab5-182">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="ccab5-183">Pour effectuer des demandes à partir du navigateur vers un point de terminaison avec une origine différente, le *point de terminaison* doit activer le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="ccab5-183">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="ccab5-184">L' [ Blazor exemple d’application webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustre l’utilisation de cors dans le composant appeler l’API Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ccab5-184">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="ccab5-185">Pour permettre à d’autres sites d’effectuer des demandes de partage de ressources Cross-Origin (CORS) à votre application, consultez <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="ccab5-185">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ccab5-186">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ccab5-186">Additional resources</span></span>

* <span data-ttu-id="ccab5-187"><xref:security/blazor/webassembly/additional-scenarios>&ndash;Comprend la couverture de l’utilisation `HttpClient` de pour créer des demandes d’API Web sécurisées.</span><span class="sxs-lookup"><span data-stu-id="ccab5-187"><xref:security/blazor/webassembly/additional-scenarios> &ndash; Includes coverage on using `HttpClient` to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="ccab5-188">Configuration du point de terminaison HTTPs Kestrel</span><span class="sxs-lookup"><span data-stu-id="ccab5-188">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="ccab5-189">Cross Origin Resource Sharing (CORS) au W3C</span><span class="sxs-lookup"><span data-stu-id="ccab5-189">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
