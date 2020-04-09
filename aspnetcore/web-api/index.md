---
title: Créer des API web avec ASP.NET Core
author: scottaddie
description: Découvrez les principes fondamentaux de la création d’une API web dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666004"
---
# <a name="create-web-apis-with-aspnet-core"></a>Créer des API web avec ASP.NET Core

De [Scott Addie](https://github.com/scottaddie) et [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core prend en charge la création de services RESTful, également appelés API web, à l’aide de C#. Pour traiter les demandes, une API web utilise des contrôleurs. Les *contrôleurs* dans une API web sont des classes qui dérivent de `ControllerBase`. Cet article montre comment utiliser les contrôleurs pour traiter les demandes d’API Web.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([Comment télécharger](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>Classe ControllerBase

Une API web se compose d’une <xref:Microsoft.AspNetCore.Mvc.ControllerBase>ou plusieurs classes de contrôleur qui dérivent de . Le modèle de projet web API fournit un contrôleur de démarrage :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Ne créez pas de contrôleur d’API web en effectuant une dérivation de la classe <xref:Microsoft.AspNetCore.Mvc.Controller>. `Controller` dérive de `ControllerBase` et ajoute la prise en charge pour les vues ; ainsi, il est destiné à la gestion des pages web, pas des demandes d’API web. Il ya une exception à cette règle: si vous prévoyez d’utiliser le même `Controller`contrôleur pour les vues et les API web, le tirer de .

La classe `ControllerBase` fournit de nombreuses propriétés et méthodes qui sont utiles pour gérer les requêtes HTTP. Par exemple, `ControllerBase.CreatedAtAction` retourne un code d’état 201 :

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Voici d’autres exemples de méthodes fournies par `ControllerBase`.

|Méthode   |Notes    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Retourne le code d’état 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Retourne le code d’état 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Retourne un fichier.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Appelle la [liaison de modèle](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Appelle la [validation de modèle](xref:mvc/models/validation).|

Pour obtenir la liste complète des méthodes et propriétés disponibles, consultez <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Attributs

L’espace de noms <xref:Microsoft.AspNetCore.Mvc> fournit des attributs qui peuvent être utilisés pour configurer le comportement des contrôleurs d’API web et des méthodes d’action. L’exemple suivant utilise des attributs pour spécifier le verbe d’action HTTP pris en charge et tous les codes de statut HTTP connus qui pourraient être retournés :

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Voici d’autres exemples d’attributs disponibles.

|Attribut|Notes|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Spécifie le modèle d’URL pour un contrôleur ou une action.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Spécifie le préfixe et les propriétés à inclure pour la liaison de modèle.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifie une action qui prend en charge le verbe d’action HTTP GET.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Spécifie les types de données acceptés par une action.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Spécifie les types de données retournés par une action.|

Pour obtenir la liste des attributs disponibles, consultez l’espace de noms <xref:Microsoft.AspNetCore.Mvc>.

## <a name="apicontroller-attribute"></a>Attribut ApiController

L’attribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) peut être appliqué à une classe de contrôleur pour activer les comportements suivants, spécifiques à l’API :

::: moniker range=">= aspnetcore-2.2"

* [Exigence du routage d’attribut](#attribute-routing-requirement)
* [Réponses HTTP 400 automatiques](#automatic-http-400-responses)
* [Inférence de paramètre de source de liaison](#binding-source-parameter-inference)
* [Inférence de demande multipart/form-data](#multipartform-data-request-inference)
* [Fonctionnalité Détails du problème pour les codes d’état erreur](#problem-details-for-error-status-codes)

Les détails du problème pour la fonction *de codes d’état d’erreur* nécessitent une version de [compatibilité](xref:mvc/compatibility-version) de 2.2 ou plus tard. Les autres fonctionnalités nécessitent une version de compatibilité de 2.1 ou plus tard.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [Exigence du routage d’attribut](#attribute-routing-requirement)
* [Réponses HTTP 400 automatiques](#automatic-http-400-responses)
* [Inférence de paramètre de source de liaison](#binding-source-parameter-inference)
* [Inférence de demande multipart/form-data](#multipartform-data-request-inference)

Ces fonctionnalités nécessitent une [version de compatibilité](xref:mvc/compatibility-version) 2.1 ou ultérieure.

::: moniker-end

### <a name="attribute-on-specific-controllers"></a>Attribut sur des contrôleurs spécifiques

L’attribut `[ApiController]` peut être appliqué à des contrôleurs spécifiques, comme dans l’exemple suivant à partir du modèle de projet :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Attribut sur plusieurs contrôleurs

Une façon d’utiliser l’attribut sur plusieurs contrôleurs consiste à créer une classe de contrôleur de base personnalisée annotée avec l’attribut `[ApiController]`. L’exemple suivant montre une classe de base personnalisée et un contrôleur qui en dérive :

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Attribut sur une assemblée

Si la [version de compatibilité](xref:mvc/compatibility-version) est définie sur 2.2 ou une version ultérieure, l’attribut `[ApiController]` peut être appliqué à un assembly. De cette manière, l’annotation applique le comportement de l’API web à tous les contrôleurs de l’assembly. Les contrôleurs individuels n’ont aucun moyen de refuser. Appliquer l’attribut de niveau d’assemblage `Startup` à la déclaration d’espace de nom entourant la classe :

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Exigence du routage d’attribut

L’attribut `[ApiController]` rend nécessaire le routage d’attributs. Par exemple :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Les actions sont inaccessibles `UseEndpoints` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>via <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> des `Startup.Configure` [itinéraires conventionnels définis](xref:mvc/controllers/routing#conventional-routing) par , , ou en .

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Les actions sont inaccessibles par le biais de [routes conventionnelles](xref:mvc/controllers/routing#conventional-routing) définies par <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> ou <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> dans `Startup.Configure`.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Réponses HTTP 400 automatiques

Grâce à l’attribut `[ApiController]`, les erreurs de validation de modèles déclenchent automatiquement une réponse HTTP 400. Ainsi, le code suivant est inutile dans une méthode d’action :

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC utilise <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> le filtre d’action pour effectuer la vérification précédente.

### <a name="default-badrequest-response"></a>Réponse BadRequest par défaut

Avec une version de compatibilité de 2.1, le type de <xref:Microsoft.AspNetCore.Mvc.SerializableError>réponse par défaut pour une réponse HTTP 400 est . Le corps de demande suivant est un exemple du type sérialisé :

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

Avec une version de compatibilité de 2.2 ou plus tard, le <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>type de réponse par défaut pour une réponse HTTP 400 est . Le corps de demande suivant est un exemple du type sérialisé :

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

Le `ValidationProblemDetails` type:

* Fournit un format lisible à la machine pour spécifier les erreurs dans les réponses de l’API Web.
* Conforme aux [spécifications du RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

### <a name="log-automatic-400-responses"></a>Consigner automatiquement 400 réponses

Voir Guide pratique pour consigner automatiquement 400 réponses en cas d’erreurs de validation de modèle (aspnet/AspNetCore.Docs no 12157).

### <a name="disable-automatic-400-response"></a>Désactiver la réponse automatique 400

Pour désactiver le comportement 400 automatique, définissez la propriété <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> sur `true`. Ajoutez le code en surbrillance suivant dans `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Inférence de paramètre de source de liaison

Un attribut de source de liaison définit l’emplacement auquel se trouve la valeur d’un paramètre d’action. Les attributs de source de liaison suivants existent :

|Attribut|Source de liaison |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Corps de la demande |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Données de formulaire dans le corps de la demande |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | En-tête de requête |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Paramètre de la chaîne de requête de la demande |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Données d’itinéraire à partir de la demande actuelle |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Service de demande injecté comme paramètre d’action |

> [!WARNING]
> N’utilisez pas `[FromRoute]` lorsque les valeurs risquent de contenir `%2f` (c’est-à-dire `/`). `%2f` ne sera pas sans la séquence d’échappement `/`. Utilisez `[FromQuery]` si la valeur peut contenir `%2f`.

Sans l’attribut `[ApiController]` ou des attributs de source de liaison comme `[FromQuery]`, le runtime ASP.NET Core tente d’utiliser le classeur de modèles objet complexe. Le classeur de modèles objet complexe extrait des données à partir de fournisseurs de valeurs dans un ordre défini.

Dans l’exemple suivant, l’attribut `[FromQuery]` indique que la valeur du paramètre `discontinuedOnly` est fournie dans la chaîne de requête de l’URL de demande :

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

L’attribut `[ApiController]` applique des règles d’inférence pour les sources de données par défaut des paramètres d’action. Ces règles vous évitent d’avoir à identifier les sources de liaison manuellement en appliquant des attributs aux paramètres d’action. Les règles d’inférence de source de liaison se comportent comme suit :

* `[FromBody]` est déduit des paramètres de type complexe. Une exception à cette règle d’inférence `[FromBody]` est tout type complexe intégré ayant une signification spéciale, comme <xref:Microsoft.AspNetCore.Http.IFormCollection> et <xref:System.Threading.CancellationToken>. Le code de l’inférence de la source de liaison ignore ces types spéciaux.
* `[FromForm]` est déduit pour les paramètres d’action de type <xref:Microsoft.AspNetCore.Http.IFormFile> et <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. Il n’est pas déduit pour les types simples ou définis par l’utilisateur.
* `[FromRoute]` est déduit pour tout nom de paramètre d’action correspondant à un paramètre dans le modèle d’itinéraire. Quand plus d’un itinéraire correspond à un paramètre d’action, toute valeur d’itinéraire est considérée comme `[FromRoute]`.
* `[FromQuery]` est déduit pour tous les autres paramètres d’action.

### <a name="frombody-inference-notes"></a>Notes sur l’inférence FromBody

`[FromBody]` n’est pas déduit pour les types simples tels que `string` ou `int`. Vous devez donc utiliser l’attribut `[FromBody]` pour les types simples quand cette fonctionnalité est nécessaire.

Quand une action a plusieurs paramètres liés à partir du corps de la demande, une exception est levée. Par exemple, toutes les signatures de méthode d’action suivantes génèrent une exception :

* `[FromBody]` est déduit sur les deux paramètres, car ce sont des types complexes.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* Attribut `[FromBody]` sur un paramètre, déduit sur l’autre, car c’est un type complexe.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* Attribut `[FromBody]` sur les deux paramètres.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> Dans ASP.NET Core 2.1, les paramètres de type collection comme les listes et les tableaux sont déduits de manière incorrecte en tant que `[FromQuery]`. L’attribut `[FromBody]` doit être utilisé pour ces paramètres s’ils doivent être liés à partir du corps de la demande. Ce problème est corrigé dans ASP.NET Core version 2.2 ou ultérieure, où les paramètres de type collection sont déduits pour être liés à partir du corps par défaut.

::: moniker-end

### <a name="disable-inference-rules"></a>Désactiver les règles d’inférence

Pour désactiver l’inférence de la source de liaison, définissez <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> sur `true`. Ajoutez le code suivant dans `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Inférence de demande multipart/form-data

L’attribut `[ApiController]` applique une règle d’inférence lorsqu’un paramètre d’action est annoté avec l’attribut. [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) Le `multipart/form-data` type de contenu de demande est déduit.

Pour désactiver le comportement par <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> défaut, définissez la propriété `true` dans `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Fonctionnalité Détails du problème pour les codes d’état erreur

Quand la version de compatibilité est 2.2 ou ultérieure, MVC transforme un résultat d’erreur (résultat avec un code d’état égal ou supérieur à 400) en résultat avec <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. Le type `ProblemDetails` est basé sur la [spécification RFC 7807](https://tools.ietf.org/html/rfc7807) pour fournir des détails de l’erreur lisibles par machine dans une réponse HTTP.

Prenons le code suivant dans une action de contrôleur :

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

La `NotFound` méthode produit un code d’état `ProblemDetails` HTTP 404 avec un corps. Par exemple :

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Désactiver la réponse ProblemDetails

La création automatique `ProblemDetails` d’un code de <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> statut d’erreur est désactivée lorsque la propriété est réglée à `true`. Ajoutez le code suivant dans `Startup.ConfigureServices` :

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Définir les types de contenu de demande pris en charge avec l’attribut [Consumes]

Par défaut, une action prend en charge tous les types de contenu de demande disponibles. Par exemple, si une application est configurée pour prendre en charge les [formateurs d’entrée](xref:mvc/models/model-binding#input-formatters)JSON et XML, une action prend en charge plusieurs types de contenu, y compris `application/json` et `application/xml`.

[L’attribut [Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) permet une action pour limiter les types de contenu de demande pris en charge. Appliquer `[Consumes]` l’attribut à une action ou à un contrôleur, en spécifiant un ou plusieurs types de contenu :

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

Dans le code `CreateProduct` précédent, l’action `application/xml`spécifie le type de contenu . Les demandes acheminées à `Content-Type` cette `application/xml`action doivent spécifier un en-tête de . Demandes qui ne spécifient pas un `Content-Type` en-tête d’aboutir `application/xml` à une réponse de type média [415 non étayée.](https://developer.mozilla.org/docs/Web/HTTP/Status/415)

L’attribut `[Consumes]` permet également à une action d’influencer sa sélection en fonction du type de contenu d’une demande entrante en appliquant une contrainte de type. Prenons l’exemple suivant :

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

Dans le code `ConsumesController` précédent, est configuré `https://localhost:5001/api/Consumes` pour traiter les demandes envoyées à l’URL. Les deux actions du `PostJson` contrôleur, et `PostForm`, gérer les demandes POST avec la même URL. Sans `[Consumes]` l’attribut appliquant une contrainte de type, une exception de match ambigue est jetée.

L’attribut `[Consumes]` est appliqué aux deux actions. L’action `PostJson` gère les `Content-Type` demandes `application/json`envoyées avec un en-tête de . L’action `PostForm` gère les `Content-Type` demandes `application/x-www-form-urlencoded`envoyées avec un en-tête de . 

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
