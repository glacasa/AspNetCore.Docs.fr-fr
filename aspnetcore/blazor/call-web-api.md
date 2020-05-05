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
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Appeler une API Web à partir de ASP.NET CoreBlazor

Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Les applications webassembly appellent des API Web à l' `HttpClient` aide d’un service préconfiguré. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Composez des requêtes, qui peuvent inclure des options de l' [API d’extraction](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScript, à l’aide Blazor des applications auxiliaires JSON ou de <xref:System.Net.Http.HttpRequestMessage>. Dans `HttpClient` les applications Blazor webassembly, le service est axé sur l’exécution de requêtes sur le serveur d’origine. Les instructions de cette rubrique concernent Blazor uniquement les applications webassembly.

Les applications serveur appellent des API <xref:System.Net.Http.HttpClient> Web à l’aide d' <xref:System.Net.Http.IHttpClientFactory>instances, généralement créées à l’aide de. [ Blazor ](xref:blazor/hosting-models#blazor-server) Les instructions de cette rubrique ne concernent pas Blazor les applications serveur. Lors du Blazor développement d’applications serveur, suivez les <xref:fundamentals/http-requests>instructions fournies dans.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample)) &ndash; sélectionnez l’application *BlazorWebAssemblySample* .

Consultez les composants suivants dans l’exemple d’application *BlazorWebAssemblySample* :

* Appeler l’API Web (*pages/CallWebAPI. Razor*)
* Testeur de requêtes HTTP (*composants/HTTPRequestTester. Razor*)

## <a name="packages"></a>.

Référencez le package NuGet [System .net. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) dans le fichier projet.

## <a name="add-the-httpclient-service"></a>Ajouter le service HttpClient

Dans `Program.Main`, ajoutez un `HttpClient` service s’il n’existe pas déjà :

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Applications auxiliaires HttpClient et JSON

Dans une Blazor application webassembly, [httpclient](xref:fundamentals/http-requests) est disponible en tant que service préconfiguré pour effectuer des demandes auprès du serveur d’origine.

Une Blazor application serveur n’inclut pas `HttpClient` de service par défaut. Fournissez `HttpClient` un à l’application à l’aide de l' [infrastructure de fabrique httpclient](xref:fundamentals/http-requests).

`HttpClient`et les auxiliaires JSON sont également utilisés pour appeler des points de terminaison d’API Web tiers. `HttpClient`est implémenté à l’aide de l' [API FETCH](https://developer.mozilla.org/docs/Web/API/Fetch_API) du navigateur et est soumis à ses limitations, y compris l’application de la même stratégie d’origine.

L’adresse de base du client est définie sur l’adresse du serveur d’origine. Injecter `HttpClient` une instance à `@inject` l’aide de la directive :

```razor
@using System.Net.Http
@inject HttpClient Http
```

Dans les exemples suivants, une API Web todo traite les opérations de création, lecture, mise à jour et suppression (CRUD). Les exemples sont basés sur une `TodoItem` classe qui stocke les éléments suivants :

* ID (`Id`, `long`) &ndash; ID unique de l’élément.
* Nom (`Name`, `string`) &ndash; nom de l’élément.
* État (`IsComplete`, `bool`) &ndash; indiquant si l’élément TODO est terminé.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Les méthodes d’assistance JSON envoient des demandes à un URI (une API Web dans les exemples suivants) et traitent la réponse :

* `GetFromJsonAsync`&ndash; Envoie une requête http obtenir et analyse le corps de la réponse JSON pour créer un objet.

  Dans le code suivant, les `_todoItems` sont affichés par le composant. La `GetTodoItems` méthode est déclenchée lorsque le rendu du composant est terminé ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Pour obtenir un exemple complet, consultez l’exemple d’application.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Envoie une requête http postérieure, y compris du contenu encodé JSON, et analyse le corps de la réponse JSON pour créer un objet.

  Dans le code suivant, `_newItemName` est fourni par un élément lié du composant. La `AddItem` méthode est déclenchée par la sélection `<button>` d’un élément. Pour obtenir un exemple complet, consultez l’exemple d’application.

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
  
  Appelle pour `PostAsJsonAsync` retourner un <xref:System.Net.Http.HttpResponseMessage>. Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; Envoie une requête HTTP PUT, y compris du contenu encodé JSON.

  Dans le code suivant, `_editItem` les valeurs `Name` pour `IsCompleted` et sont fournies par les éléments dépendants du composant. L’élément `Id` est défini lorsque l’élément est sélectionné dans une autre partie de l’interface utilisateur `EditItem` et est appelé. La `SaveItem` méthode est déclenchée par la sélection de `<button>` l’élément Save. Pour obtenir un exemple complet, consultez l’exemple d’application.

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
  
  Appelle pour `PutAsJsonAsync` retourner un <xref:System.Net.Http.HttpResponseMessage>. Pour désérialiser le contenu JSON du message de réponse, utilisez la `ReadFromJsonAsync<T>` méthode d’extension :
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>contient des méthodes d’extension supplémentaires pour envoyer des requêtes HTTP et recevoir des réponses HTTP. [Httpclient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) est utilisé pour envoyer une requête HTTP DELETE à une API Web.

Dans le code suivant, l’élément `<button>` delete appelle la `DeleteItem` méthode. L’élément `<input>` lié fournit le `id` de l’élément à supprimer. Pour obtenir un exemple complet, consultez l’exemple d’application.

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

La sécurité du navigateur empêche une page Web d’effectuer des demandes vers un autre domaine que celui qui a servi la page Web. Cette restriction est appelée *stratégie de même origine*. La stratégie de même origine empêche un site malveillant de lire des données sensibles à partir d’un autre site. Pour effectuer des demandes à partir du navigateur vers un point de terminaison avec une origine différente, le *point de terminaison* doit activer le [partage des ressources Cross-Origin (cors)](https://www.w3.org/TR/cors/).

L' [ Blazor exemple d’application webassembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) illustre l’utilisation de cors dans le composant appeler l’API Web (*pages/CallWebAPI. Razor*).

Pour permettre à d’autres sites d’effectuer des demandes de partage de ressources Cross-Origin (CORS) <xref:security/cors>à votre application, consultez.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Configuration du point de terminaison HTTPs Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross Origin Resource Sharing (CORS) au W3C](https://www.w3.org/TR/cors/)
