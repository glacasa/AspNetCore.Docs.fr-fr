---
title: Appelez une API web de Blazor ASP.NET Core WebAssembly
author: guardrex
description: Découvrez comment appeler une API Blazor Web à partir d’une application WebAssembly à l’aide d’aides JSON, y compris en faisant des demandes de partage de ressources inter-origine (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 2f2d4150f4fa1e7f47310f2a88b816f445cd1d3a
ms.sourcegitcommit: 49c91ad4b69f4f8032394cbf2d5ae1b19a7f863b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81544854"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Appelez une API web de ASP.NET CoreBlazor

Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), et Juan De la [Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Les applications `HttpClient` WebAssembly appellent les API Web à l’aide d’un service préconfiguré. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Composez les demandes, qui peuvent inclure Blazor des options [JavaScript Fetch API,](https://developer.mozilla.org/docs/Web/API/Fetch_API) en utilisant des aides JSON ou avec <xref:System.Net.Http.HttpRequestMessage>. Le `HttpClient` service Blazor dans les applications WebAssembly est axé sur la remise des demandes au serveur d’origine. Les conseils dans ce sujet Blazor ne concerne que les applications WebAssembly.

Les applications serveur appellent <xref:System.Net.Http.HttpClient> les API <xref:System.Net.Http.IHttpClientFactory>Web à l’aide d’instances, généralement créées à l’aide de . [ Blazor ](xref:blazor/hosting-models#blazor-server) Les conseils dans ce sujet Blazor ne se rapportent pas aux applications Server. Lors Blazor du développement d’applications <xref:fundamentals/http-requests>Server, suivez les conseils dans .

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([comment télécharger](xref:index#how-to-download-a-sample)) &ndash; Sélectionnez l’application *BlazorWebAssemblySample.*

Voir les composants suivants dans l’application *d’échantillon BlazorWebAssemblySample* :

* Appelez l’API Web (*Pages/CallWebAPI.razor*)
* HTTP Request Tester (*Composants/HTTPRequestTester.razor*)

## <a name="packages"></a>.

Référence du [paquet System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet dans le dossier du projet.

## <a name="add-the-httpclient-service"></a>Ajouter le service HttpClient

Dans `Program.Main`, `HttpClient` ajouter un service s’il n’existe pas déjà:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Aides httpClient et JSON

Dans Blazor une application WebAssembly, [HttpClient](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour faire des demandes de retour sur le serveur d’origine.

Une Blazor application Server n’inclut pas de `HttpClient` service par défaut. Fournir `HttpClient` un à l’application en utilisant l’infrastructure [de l’usine HttpClient](xref:fundamentals/http-requests).

`HttpClient`et les aides JSON sont également utilisés pour appeler des points de terminaison Web tiers API. `HttpClient`est implémenté à l’aide du navigateur [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) et est soumis à ses limites, y compris l’application de la même politique d’origine.

L’adresse de base du client est réglée à l’adresse du serveur d’origine. Injecter `HttpClient` une instance `@inject` à l’aide de la directive :

```razor
@using System.Net.Http
@inject HttpClient Http
```

Dans les exemples suivants, un API Web Todo crée, lit, met à jour et supprime les opérations (CRUD). Les exemples sont `TodoItem` basés sur une classe qui stocke le :

* ID`Id`( `long` &ndash; , ) ID unique de l’article.
* Nom`Name`( `string` &ndash; , ) Nom de l’article.
* État`IsComplete`( `bool` &ndash; , ) Indication si l’élément Todo est terminé.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :

* `GetFromJsonAsync`&ndash; Envoie une demande HTTP GET et analyse le corps de réponse JSON pour créer un objet.

  Dans le code `_todoItems` suivant, les sont affichés par le composant. La `GetTodoItems` méthode est déclenchée lorsque le composant est fini de rendu ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Consultez l’application de l’échantillon pour un exemple complet.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Envoie une demande HTTP POST, y compris le contenu codé par JSON, et analyse le corps de réponse JSON pour créer un objet.

  Dans le code `_newItemName` suivant, est fourni par un élément lié de la composante. La `AddItem` méthode est déclenchée `<button>` par la sélection d’un élément. Consultez l’application de l’échantillon pour un exemple complet.

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
  
  Appels `PostAsJsonAsync` à <xref:System.Net.Http.HttpResponseMessage>retourner un .

* `PutAsJsonAsync`&ndash; Envoie une demande HTTP PUT, y compris le contenu codé par JSON.

  Dans le code `_editItem` suivant, `IsCompleted` les valeurs et `Name` sont fournies par des éléments liés du composant. L’élément `Id` est défini lorsque l’élément est sélectionné dans `EditItem` une autre partie de l’interface utilisateur et est appelé. La `SaveItem` méthode est déclenchée par `<button>` la sélection de l’élément Enregistrer. Consultez l’application de l’échantillon pour un exemple complet.

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
  
  Appels `PutAsJsonAsync` à <xref:System.Net.Http.HttpResponseMessage>retourner un .

<xref:System.Net.Http>comprend des méthodes d’extension supplémentaires pour envoyer des demandes HTTP et recevoir des réponses HTTP. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) est utilisé pour envoyer une demande HTTP DELETE à une API web.

Dans le code suivant, l’élément Supprimer `<button>` appelle la `DeleteItem` méthode. L’élément `<input>` lié `id` fournit l’élément à supprimer. Consultez l’application de l’échantillon pour un exemple complet.

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

## <a name="cross-origin-resource-sharing-cors"></a>Partage des ressources cross-origin (CORS)

La sécurité du navigateur empêche une page Web de faire des demandes à un domaine différent de celui qui a servi la page Web. Cette restriction est appelée la *même politique d’origine*. La politique de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site. Pour faire des demandes du navigateur à un point de terminaison d’origine différente, le *critère d’évaluation* doit permettre le [partage des ressources inter-origine (CORS).](https://www.w3.org/TR/cors/)

[ Blazor L’application d’échantillonnage WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) démontre l’utilisation de CORS dans le composant Call Web API *(Pages/CallWebAPI.razor*).

Pour permettre à d’autres sites de faire des demandes de <xref:security/cors>partage de ressources inter-origine (CORS) à votre application, voir .

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient et HttpRequestMessage avec les options de demande d’API Fetch

Lorsque vous exécutez sur Blazor WebAssembly dans une application <xref:System.Net.Http.HttpRequestMessage> WebAssembly, utilisez [HttpClient](xref:fundamentals/http-requests) et personnalisez les demandes. Par exemple, vous pouvez spécifier la demande URI, la méthode HTTP et les en-têtes de demande souhaités.

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

Pour plus d’informations sur les options Fetch API, voir [docs web MDN: WindowOrWorkerGlobalScope.fetch () :Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Lors de l’envoi d’informations d’identification (cookies d’autorisation/en-têtes) sur les demandes CORS, l’en-tête `Authorization` doit être autorisé par la politique CORS.

La stratégie suivante comprend la configuration pour :

* Origines`http://localhost:5000`de `https://localhost:5001`la demande ( , ).
* Toute méthode (verbe).
* `Content-Type`et `Authorization` en-têtes. Pour permettre un en-tête `x-custom-header`personnalisé (par <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>exemple, ), énumérez l’en-tête lors de l’appel .
* Informations d’identification définies par`credentials` le code `include`JavaScript côté client (propriété définie à ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Pour plus d’informations, voir <xref:security/cors> et l’exemple de l’application HTTP Request Tester composant (*Composants/HTTPRequestTester.razor*).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuration de point de terminaison Kestrel HTTPS](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross Origin Resource Sharing (CORS) à W3C](https://www.w3.org/TR/cors/)
