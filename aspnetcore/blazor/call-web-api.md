---
<span data-ttu-id="87a72-101">titre : « appeler une API Web à partir de ASP.NET Core Blazor Webassembly » auteur : Description : « Découvrez comment appeler une API Web à partir d’une Blazor application webassembly à l’aide des applications auxiliaires JSON, y compris la création de requêtes de partage des ressources Cross-Origin (cors). »</span><span class="sxs-lookup"><span data-stu-id="87a72-101">title: 'Call a web API from ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to call a web API from a Blazor WebAssembly app using JSON helpers, including making cross-origin resource sharing (CORS) requests.'</span></span>
<span data-ttu-id="87a72-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="87a72-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="87a72-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="87a72-103">'Blazor'</span></span>
- <span data-ttu-id="87a72-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="87a72-104">'Identity'</span></span>
- <span data-ttu-id="87a72-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="87a72-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="87a72-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="87a72-106">'Razor'</span></span>
- <span data-ttu-id="87a72-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="87a72-107">'SignalR' uid:</span></span> 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="87a72-108">Appeler une API Web à partir de ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="87a72-108">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="87a72-109">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="87a72-109">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

<span data-ttu-id="87a72-110">Les applications [ Blazor webassembly](xref:blazor/hosting-models#blazor-webassembly) appellent des API Web à l’aide d’un <xref:System.Net.Http.HttpClient> service préconfiguré.</span><span class="sxs-lookup"><span data-stu-id="87a72-110">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="87a72-111">Composez des requêtes, qui peuvent inclure des options de l' [API d’extraction](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, à l’aide Blazor des applications auxiliaires JSON ou de <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="87a72-111">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="87a72-112"><xref:System.Net.Http.HttpClient>Dans les Blazor applications webassembly, le service est axé sur l’exécution de requêtes sur le serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="87a72-112">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="87a72-113">Les instructions de cette rubrique concernent uniquement les Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="87a72-113">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="87a72-114">Les applications [ Blazor serveur](xref:blazor/hosting-models#blazor-server) appellent des API Web à l’aide d' <xref:System.Net.Http.HttpClient> instances, généralement créées à l’aide de <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="87a72-114">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="87a72-115">Les instructions de cette rubrique ne concernent pas les Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="87a72-115">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="87a72-116">Lors Blazor du développement d’applications serveur, suivez les instructions fournies dans <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="87a72-116">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="87a72-117">[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)) : sélectionnez l’application *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="87a72-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="87a72-118">Consultez les composants suivants dans l’exemple d’application *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="87a72-118">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="87a72-119">Appeler l’API Web (*pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="87a72-119">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="87a72-120">Testeur de requêtes HTTP (*composants/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="87a72-120">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="87a72-121">Packages</span><span class="sxs-lookup"><span data-stu-id="87a72-121">Packages</span></span>

<span data-ttu-id="87a72-122">Référencez le package NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="87a72-122">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="87a72-123">Ajouter le service HttpClient</span><span class="sxs-lookup"><span data-stu-id="87a72-123">Add the HttpClient service</span></span>

<span data-ttu-id="87a72-124">Dans `Program.Main` , ajoutez un <xref:System.Net.Http.HttpClient> service s’il n’existe pas déjà :</span><span class="sxs-lookup"><span data-stu-id="87a72-124">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="87a72-125">Applications auxiliaires HttpClient et JSON</span><span class="sxs-lookup"><span data-stu-id="87a72-125">HttpClient and JSON helpers</span></span>

<span data-ttu-id="87a72-126">Dans une Blazor application Webassembly, [httpclient](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour effectuer des demandes auprès du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="87a72-126">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="87a72-127">Une Blazor application serveur n’inclut pas <xref:System.Net.Http.HttpClient> de service par défaut.</span><span class="sxs-lookup"><span data-stu-id="87a72-127">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="87a72-128">Fournissez un <xref:System.Net.Http.HttpClient> à l’application à l’aide de l' [infrastructure de fabrique httpclient](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="87a72-128">Provide an <xref:System.Net.Http.HttpClient> to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="87a72-129"><xref:System.Net.Http.HttpClient>et les auxiliaires JSON sont également utilisés pour appeler des points de terminaison d’API Web tiers.</span><span class="sxs-lookup"><span data-stu-id="87a72-129"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="87a72-130"><xref:System.Net.Http.HttpClient>est implémenté à l’aide de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API) du navigateur et est soumis à ses limitations, y compris l’application de la même stratégie d’origine.</span><span class="sxs-lookup"><span data-stu-id="87a72-130"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="87a72-131">L’adresse de base du client est définie sur l’adresse du serveur d’origine.</span><span class="sxs-lookup"><span data-stu-id="87a72-131">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="87a72-132">Injecter une <xref:System.Net.Http.HttpClient> instance à l’aide de la [`@inject`](xref:mvc/views/razor#inject) directive :</span><span class="sxs-lookup"><span data-stu-id="87a72-132">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="87a72-133">Dans les exemples suivants, une API Web todo traite les opérations de création, lecture, mise à jour et suppression (CRUD).</span><span class="sxs-lookup"><span data-stu-id="87a72-133">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="87a72-134">Les exemples sont basés sur une `TodoItem` classe qui stocke les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="87a72-134">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="87a72-135">ID ( `Id` , `long` ) : ID unique de l’élément.</span><span class="sxs-lookup"><span data-stu-id="87a72-135">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="87a72-136">Nom ( `Name` , `string` ) : nom de l’élément.</span><span class="sxs-lookup"><span data-stu-id="87a72-136">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="87a72-137">Status ( `IsComplete` , `bool` ) : indique si l’élément TODO est terminé.</span><span class="sxs-lookup"><span data-stu-id="87a72-137">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="87a72-138">Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :</span><span class="sxs-lookup"><span data-stu-id="87a72-138">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="87a72-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Envoie une requête HTTP obtenir et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="87a72-139"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="87a72-140">Dans le code suivant, les `todoItems` sont affichés par le composant.</span><span class="sxs-lookup"><span data-stu-id="87a72-140">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="87a72-141">La `GetTodoItems` méthode est déclenchée lorsque le rendu du composant est terminé ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="87a72-141">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="87a72-142">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87a72-142">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="87a72-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Envoie une requête HTTP POSTÉRIEURe, y compris du contenu encodé JSON, et analyse le corps de la réponse JSON pour créer un objet.</span><span class="sxs-lookup"><span data-stu-id="87a72-143"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="87a72-144">Dans le code suivant, `newItemName` est fourni par un élément lié du composant.</span><span class="sxs-lookup"><span data-stu-id="87a72-144">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="87a72-145">La `AddItem` méthode est déclenchée par la sélection d’un `<button>` élément.</span><span class="sxs-lookup"><span data-stu-id="87a72-145">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="87a72-146">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87a72-146">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="87a72-147">Appelle pour <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> retourner un <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="87a72-147">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="87a72-148">Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="87a72-148">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="87a72-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Envoie une requête HTTP PUT, y compris le contenu encodé JSON.</span><span class="sxs-lookup"><span data-stu-id="87a72-149"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="87a72-150">Dans le code suivant, les `editItem` valeurs pour `Name` et `IsCompleted` sont fournies par les éléments dépendants du composant.</span><span class="sxs-lookup"><span data-stu-id="87a72-150">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="87a72-151">L’élément `Id` est défini lorsque l’élément est sélectionné dans une autre partie de l’interface utilisateur et `EditItem` est appelé.</span><span class="sxs-lookup"><span data-stu-id="87a72-151">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="87a72-152">La `SaveItem` méthode est déclenchée par la sélection de l' `<button>` élément Save.</span><span class="sxs-lookup"><span data-stu-id="87a72-152">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="87a72-153">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87a72-153">See the sample app for a complete example.</span></span>

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
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="87a72-154">Appelle pour <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> retourner un <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="87a72-154">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="87a72-155">Pour désérialiser le contenu JSON du message de réponse, utilisez la <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> méthode d’extension :</span><span class="sxs-lookup"><span data-stu-id="87a72-155">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="87a72-156"><xref:System.Net.Http>contient des méthodes d’extension supplémentaires pour envoyer des requêtes HTTP et recevoir des réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="87a72-156"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="87a72-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>est utilisé pour envoyer une requête HTTP DELETE à une API Web.</span><span class="sxs-lookup"><span data-stu-id="87a72-157"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="87a72-158">Dans le code suivant, l' `<button>` élément delete appelle la `DeleteItem` méthode.</span><span class="sxs-lookup"><span data-stu-id="87a72-158">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="87a72-159">L' `<input>` élément lié fournit le `id` de l’élément à supprimer.</span><span class="sxs-lookup"><span data-stu-id="87a72-159">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="87a72-160">Pour obtenir un exemple complet, consultez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="87a72-160">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="87a72-161">Nommé HttpClient avec IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="87a72-161">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="87a72-162"><xref:System.Net.Http.IHttpClientFactory>les services et la configuration d’un nommé <xref:System.Net.Http.HttpClient> sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="87a72-162"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="87a72-163">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="87a72-163">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="87a72-164">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="87a72-164">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="87a72-165">HttpClient typé</span><span class="sxs-lookup"><span data-stu-id="87a72-165">Typed HttpClient</span></span>

<span data-ttu-id="87a72-166">Typé <xref:System.Net.Http.HttpClient> utilise une ou plusieurs instances de l’application <xref:System.Net.Http.HttpClient> , default ou named, pour retourner des données à partir d’un ou plusieurs points de terminaison d’API Web.</span><span class="sxs-lookup"><span data-stu-id="87a72-166">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="87a72-167">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="87a72-167">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="87a72-168">`Program.Main`(*Program.cs*) :</span><span class="sxs-lookup"><span data-stu-id="87a72-168">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="87a72-169">Les composants injectent le typé <xref:System.Net.Http.HttpClient> pour appeler l’API Web.</span><span class="sxs-lookup"><span data-stu-id="87a72-169">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="87a72-170">`FetchData`composant (*pages/fetchData. Razor*) :</span><span class="sxs-lookup"><span data-stu-id="87a72-170">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="handle-errors"></a><span data-ttu-id="87a72-171">Gérer les erreurs</span><span class="sxs-lookup"><span data-stu-id="87a72-171">Handle errors</span></span>

<span data-ttu-id="87a72-172">Lorsque des erreurs se produisent lors de l’interaction avec une API Web, elles peuvent être gérées par le code du développeur.</span><span class="sxs-lookup"><span data-stu-id="87a72-172">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="87a72-173">Par exemple, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> attend une réponse JSON de l’API du serveur avec un `Content-Type` de `application/json` .</span><span class="sxs-lookup"><span data-stu-id="87a72-173">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="87a72-174">Si la réponse n’est pas au format JSON, la validation du contenu lève une exception <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="87a72-174">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="87a72-175">Dans l’exemple suivant, le point de terminaison d’URI pour la demande de données de prévision météo est mal orthographié.</span><span class="sxs-lookup"><span data-stu-id="87a72-175">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="87a72-176">L’URI doit être à, `WeatherForecast` mais apparaît dans l’appel en tant que `WeatherForcast` (« e » manquant).</span><span class="sxs-lookup"><span data-stu-id="87a72-176">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="87a72-177">L' <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> appel attend que JSON soit retourné, mais le serveur retourne du code HTML pour une exception non gérée sur le serveur avec un `Content-Type` de `text/html` .</span><span class="sxs-lookup"><span data-stu-id="87a72-177">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="87a72-178">L’exception non gérée se produit sur le serveur, car le chemin d’accès est introuvable et l’intergiciel ne peut pas traiter une page ou une vue pour la demande.</span><span class="sxs-lookup"><span data-stu-id="87a72-178">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="87a72-179">Dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> sur le client, <xref:System.NotSupportedException> est levée lorsque le contenu de la réponse est validé en tant que non-JSON.</span><span class="sxs-lookup"><span data-stu-id="87a72-179">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="87a72-180">L’exception est interceptée dans le `catch` bloc, où la logique personnalisée peut consigner l’erreur ou présenter un message d’erreur convivial à l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="87a72-180">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="87a72-181">L’exemple précédent est fourni à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="87a72-181">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="87a72-182">Une application serveur d’API Web peut être configurée pour retourner JSON même lorsqu’un point de terminaison n’existe pas ou qu’un exception non géré est présent sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="87a72-182">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="87a72-183">Pour plus d'informations, consultez <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="87a72-183">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="87a72-184">Partage des ressources cross-origin (CORS)</span><span class="sxs-lookup"><span data-stu-id="87a72-184">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="87a72-185">La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web.</span><span class="sxs-lookup"><span data-stu-id="87a72-185">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="87a72-186">Cette restriction est appelée *stratégie de même origine*.</span><span class="sxs-lookup"><span data-stu-id="87a72-186">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="87a72-187">La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site.</span><span class="sxs-lookup"><span data-stu-id="87a72-187">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="87a72-188">Pour effectuer des demandes à partir du navigateur vers un point de terminaison avec une origine différente, le *point de terminaison* doit activer le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="87a72-188">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="87a72-189">L' [ Blazor exemple d’application webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustre l’utilisation de cors dans le composant appeler l’API Web (*pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="87a72-189">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="87a72-190">Pour permettre à d’autres sites d’effectuer des demandes de partage de ressources Cross-Origin (CORS) à votre application, consultez <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="87a72-190">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="87a72-191">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="87a72-191">Additional resources</span></span>

* <span data-ttu-id="87a72-192"><xref:security/blazor/webassembly/additional-scenarios>: Comprend la couverture de l’utilisation <xref:System.Net.Http.HttpClient> de pour créer des demandes d’API Web sécurisées.</span><span class="sxs-lookup"><span data-stu-id="87a72-192"><xref:security/blazor/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="87a72-193">Configuration du point de terminaison HTTPs Kestrel</span><span class="sxs-lookup"><span data-stu-id="87a72-193">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="87a72-194">Cross Origin Resource Sharing (CORS) au W3C</span><span class="sxs-lookup"><span data-stu-id="87a72-194">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
