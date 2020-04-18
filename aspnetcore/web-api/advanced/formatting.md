---
title: Mettre en forme les données des réponses dans l’API web ASP.NET Core
author: ardalis
description: Découvrez comment mettre en forme les données des réponses dans l’API web ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642710"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Mettre en forme les données des réponses dans l’API web ASP.NET Core

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC a la prise en charge pour le formatage des données de réponse. Les données de réponse peuvent être formatées à l’aide de formats spécifiques ou en réponse au format demandé par le client.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Résultats d’action spécifiques au format

Certains types de résultats d’action sont spécifiques à un format particulier, comme <xref:Microsoft.AspNetCore.Mvc.JsonResult> et <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Les actions peuvent retourner les résultats qui sont formatés dans un format particulier, indépendamment des préférences des clients. Par exemple, `JsonResult` le retour renvoie les données formatées par JSON. Le `ContentResult` retour ou une chaîne renvoie des données de chaîne formatées de texte simple.

Une action n’est pas nécessaire pour retourner un type spécifique. ASP.NET Core prend en charge toute valeur de retour d’objet.  Les résultats d’actions qui <xref:Microsoft.AspNetCore.Mvc.IActionResult> renvoient des objets <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> qui ne sont pas des types sont sérialisés à l’aide de la mise en œuvre appropriée. Pour plus d’informations, consultez <xref:web-api/action-return-types>.

La méthode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> d’assistance intégrée renvoie les données formatées par JSON :[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Le téléchargement de l’échantillon renvoie la liste des auteurs. Utilisation des outils de développeur de navigateur F12 ou [Postman](https://www.getpostman.com/tools) avec le code précédent:

* L’en-tête de réponse contenant **du type de contenu est** `application/json; charset=utf-8` affiché.
* Les en-têtes de demande sont affichés. Par exemple, `Accept` l’en-tête. L’en-tête `Accept` est ignoré par le code précédent.

Pour retourner des données mises en forme en texte brut, utilisez <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> et le helper <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

Dans le code `Content-Type` précédent, `text/plain`le retour est . Le retour `Content-Type` d’une chaîne délivre : `text/plain`

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Pour les actions avec `IActionResult`plusieurs types de retour, retour . Par exemple, le retour de différents codes d’état HTTP en fonction du résultat des opérations effectuées.

## <a name="content-negotiation"></a>Négociation de contenu

La négociation de contenu se produit lorsque le client spécifie un [en-tête Accept](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html). Le format par défaut utilisé par ASP.NET Core est [JSON](https://json.org/). La négociation de contenu est la suivante :

* Implémenté par <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.
* Intégré dans les résultats d’action spécifiques au code d’état retournés des méthodes d’aide. Les méthodes d’aide aux `ObjectResult`résultats d’action sont basées sur .

Quand un type de modèle est `ObjectResult`retourné, le type de retour est .

La méthode d’action suivante utilise les méthodes helper `Ok` et `NotFound` :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Par défaut, ASP.NET Core `application/json`prend `text/json`en `text/plain` charge, et les types de médias. Des outils tels que [Fiddler](https://www.telerik.com/fiddler) `Accept` ou [Postman](https://www.getpostman.com/tools) peuvent définir l’en-tête de la demande pour spécifier le format de retour. Lorsque `Accept` l’en-tête contient un type que le serveur prend en charge, ce type est retourné. La section suivante montre comment ajouter des formateurs supplémentaires.

Les actions de contrôleur peuvent retourner les POCO (objets CLR de plaine ancienne). Lorsqu’un POCO est retourné, le `ObjectResult` temps d’exécution crée automatiquement un qui enveloppe l’objet. Le client reçoit l’objet sérialisé formaté. Si l’objet `null`retourné `204 No Content` est, une réponse est retournée.

Retour d’un type d’objet :

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

Dans le code précédent, une demande d’un pseudonyme d’auteur valide renvoie une `200 OK` réponse avec les données de l’auteur. Une demande d’alias `204 No Content` invalide renvoie une réponse.

### <a name="the-accept-header"></a>L’en-tête Accept

La *négociation* de `Accept` contenu a lieu lorsqu’un en-tête apparaît dans la demande. Lorsqu’une demande contient un en-tête d’acceptation, ASP.NET Core :

* Énumère les types de médias dans l’en-tête d’acceptation dans l’ordre de préférence.
* Essaye de trouver un formateur qui peut produire une réponse dans l’un des formats spécifiés.

Si aucun formateur n’est trouvé qui peut satisfaire à la demande du client, ASP.NET Core :

* `406 Not Acceptable` Retours <xref:Microsoft.AspNetCore.Mvc.MvcOptions> si a été défini, ou -
* Essaye de trouver le premier formateur qui peut produire une réponse.

Si aucun formateur n’est configuré pour le format demandé, le premier formateur qui peut formater l’objet est utilisé. Si `Accept` aucun en-tête n’apparaît dans la demande :

* Le premier formateur qui peut manipuler l’objet est utilisé pour sérialiser la réponse.
* Il n’y a pas de négociation en cours. Le serveur détermine le format à retourner.

Si l’en-tête Accept contient, `*/*`l’en-tête est ignoré à moins d’être `RespectBrowserAcceptHeader` concrétisée sur <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.

### <a name="browsers-and-content-negotiation"></a>Navigateurs et négociation de contenu

Contrairement aux clients API typiques, les navigateurs Web fournissent des `Accept` en-têtes. Navigateur Web spécifient de nombreux formats, y compris wildcards. Par défaut, lorsque le cadre détecte que la demande provient d’un navigateur :

* L’en-tête `Accept` est ignoré.
* Le contenu est retourné dans JSON, sauf configuré autrement.

Cela offre une expérience plus cohérente à travers les navigateurs lors de la consommation d’API.

Pour configurer une application pour honorer <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> `true`les en-têtes du navigateur, définissez :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Configurer les formateurs

Les applications qui ont besoin de prendre en charge des formats supplémentaires peuvent ajouter les paquets NuGet appropriés et configurer le support. Il existe des formateurs distincts pour les entrées et pour les sorties. Les formateurs d’entrée sont utilisés par [Model Binding](xref:mvc/models/model-binding). Les formateurs de sortie sont utilisés pour formater les réponses. Pour plus d’informations sur la création d’un formateur personnalisé, voir [Custom Formatters](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Ajouter la prise en charge du format XML

Les formateurs XML mis en œuvre à l’aide <xref:System.Xml.Serialization.XmlSerializer> sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Le code précédent sérialis les résultats à l’aide `XmlSerializer`de .

Lors de l’utilisation du code précédent, les méthodes `Accept` de contrôleur renvoient le format approprié en fonction de l’en-tête de la demande.

### <a name="configure-systemtextjson-based-formatters"></a>Configurer des formateurs basés sur System.Text.Json

Les fonctionnalités pour les formateurs basés sur `System.Text.Json` peuvent être configurées avec `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Les options de sérialisation des sorties, par `JsonResult`action, peuvent être configurées à l’aide de . Par exemple :

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Ajouter la prise en charge du format JSON basé sur Newtonsoft.Json

Avant ASP.NET Core 3.0, les formateurs JSON utilisés `Newtonsoft.Json` par défaut ont été mis en œuvre à l’aide du paquet. Dans ASP.NET Core 3.0 ou version ultérieure, les formateurs JSON par défaut sont basés sur `System.Text.Json`. La `Newtonsoft.Json` prise en charge des formateurs et fonctionnalités basés est disponible en installant le paquet [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet et en le configurant dans `Startup.ConfigureServices`.

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Certaines fonctionnalités peuvent `System.Text.Json`ne pas bien fonctionner avec `Newtonsoft.Json`les formateurs basés et nécessitent une référence aux formateurs basés. Continuer à `Newtonsoft.Json`utiliser les formateurs basés si l’application :

* Utilise `Newtonsoft.Json` des attributs. Par exemple, `[JsonProperty]` ou `[JsonIgnore]`.
* Personnalise les paramètres de sérialisation.
* S’appuie `Newtonsoft.Json` sur les fonctionnalités qui fournissent.
* Configure `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`. Avant ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepte une instance de `JsonSerializerSettings` spécifique à `Newtonsoft.Json`.
* Génère une documentation [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>).

Les fonctionnalités des formateurs basés peuvent être configurées à l’aide `Newtonsoft.Json`de `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Les options de sérialisation des sorties, par `JsonResult`action, peuvent être configurées à l’aide de . Par exemple :

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Ajouter la prise en charge du format XML

Le formatage XML nécessite le package [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet.

Les formateurs XML mis en œuvre à l’aide <xref:System.Xml.Serialization.XmlSerializer> sont configurés en appelant <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Le code précédent sérialis les résultats à l’aide `XmlSerializer`de .

Lors de l’utilisation du code précédent, les méthodes `Accept` de contrôleur doivent retourner le format approprié en fonction de l’en-tête de la demande.

::: moniker-end

### <a name="specify-a-format"></a>Spécifier un format

Pour restreindre les formats [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) de réponse, appliquez le filtre. Comme la plupart `[Produces]` des [filtres](xref:mvc/controllers/filters), peut être appliqué à l’action, contrôleur, ou portée globale:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Le [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre précédent :

* Force toutes les actions au sein du contrôleur à retourner les réponses formatées par JSON.
* Si d’autres formateurs sont configurés et que le client spécifie un format différent, JSON est retourné.

Pour plus d’informations, voir [Filtres](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formateurs de cas spéciaux

Certains cas spéciaux sont implémentés avec des formateurs intégrés. Par `string` défaut, les types de retour sont formatés comme `Accept` *texte/plaine* *(texte/html* si demandé via l’en-tête). Ce comportement peut être supprimé <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>en supprimant le . Les formateurs sont `ConfigureServices` supprimés dans la méthode. Actions qui ont un modèle `204 No Content` de `null`retour de type d’objet retour retour lors du retour . Ce comportement peut être supprimé <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>en supprimant le . Le code suivant supprime `StringOutputFormatter` et `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Sans `StringOutputFormatter`le , le formatter intégré `string` JSON formatter types de retour. Si le formateur JSON intégré est supprimé et qu’un formateur XML `string` est disponible, les formateurs XML retournent les types. Sinon, `string` les `406 Not Acceptable`types de retour reviennent .

Sans `HttpNoContentOutputFormatter`, les objets null sont mis en forme avec le formateur configuré. Par exemple :

* Le formateur JSON retourne une `null`réponse avec un corps de .
* Le formateur XML renvoie un élément `xsi:nil="true"` XML vide avec l’ensemble d’attributs.

## <a name="response-format-url-mappings"></a>Cartographies URL de format de réponse

Les clients peuvent demander un format particulier dans le cadre de l’URL, par exemple :

* Dans la chaîne de requête ou une partie du chemin.
* En utilisant une extension de fichier spécifique au format tel que .xml ou .json.

Le mappage du chemin de la requête doit être spécifié dans la route utilisée par l’API. Par exemple :

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

L’itinéraire précédent permet de préciser le format demandé comme extension de fichier facultative. L’attribut [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) vérifie l’existence de `RouteData` la valeur du format dans le format et cartographie le format de réponse au formateur approprié lorsque la réponse est créée.

|           Routage        |             Formateur              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Le formateur de sortie par défaut    |
| `/api/products/5.json` | Le formateur JSON (s’il est configuré) |
| `/api/products/5.xml`  | Le formateur XML (s’il est configuré)  |
