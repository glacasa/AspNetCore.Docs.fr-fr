---
title: JsonPatch dans l’API web ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625877"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch dans l’API web ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra) et [Kirk Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

Cet article explique comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.

## <a name="package-installation"></a>Installation de package

Pour activer le support JSON Patch dans votre application, remplissez les étapes suivantes :

1. Installez le package [NuGet Microsoft.AspNetCore.Mvc.NewtonsoftJson.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)
1. Mettre à jour `Startup.ConfigureServices` la <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>méthode d’appel du projet . Par exemple :

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson`est compatible avec les méthodes d’enregistrement des services MVC :

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>JSON Patch, AddNewtonsoftJson, et System.Text.Json

`AddNewtonsoftJson`remplace les `System.Text.Json`formateurs d’entrée et de sortie basés sur les entrées utilisés pour formater **tout** le contenu JSON. Pour ajouter de la `Newtonsoft.Json`prise en charge de JSON Patch à `Startup.ConfigureServices` l’aide de , tout en laissant les autres formateurs inchangés, mettre à jour la méthode du projet comme suit:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Le code précédent `Microsoft.AspNetCore.Mvc.NewtonsoftJson` exige le `using` paquet et les instructions suivantes :

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>Méthode de demande HTTP PATCH

Les méthodes PUT et [PATCH](https://tools.ietf.org/html/rfc5789) sont utilisées pour mettre à jour une ressource existante. La différence est que PUT remplace la ressource complète, tandis que PATCH spécifie uniquement les modifications.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) est un format qui spécifie des mises à jour à appliquer à une ressource. Un document JSON Patch possède un tableau des *opérations*. Chaque opération identifie un type particulier de changement. Parmi les exemples de ces changements, mentionnons l’ajout d’un élément de tableau ou le remplacement d’une valeur de propriété.

Par exemple, les documents JSON suivants représentent une ressource, un document JSON Patch pour la ressource et le résultat de l’application des opérations de patch.

### <a name="resource-example"></a>Exemple de ressource

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Exemple de correctif JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Dans le code JSON précédent :

* La propriété `op` indique le type d’opération.
* La propriété `path` indique l’élément à mettre à jour.
* La propriété `value` fournit la nouvelle valeur.

### <a name="resource-after-patch"></a>Ressources après correction

Voici la ressource après l’application du document JSON Patch précédent :

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Les modifications apportées par l’application d’un document JSON Patch à une ressource sont atomiques. Si une opération dans la liste échoue, aucune opération dans la liste n’est appliquée.

## <a name="path-syntax"></a>Syntaxe du chemin

Les différents niveaux de la propriété [path](https://tools.ietf.org/html/rfc6901) d’un objet de l’opération sont séparés par des barres obliques. Par exemple : `"/address/zipCode"`.

Les index de base zéro sont utilisés pour spécifier les éléments du tableau. Le premier élément du tableau `addresses` serait à `/addresses/0`. Jusqu’à `add` la fin d’un tableau, utilisez un trait d’union ()`-`plutôt qu’un numéro d’index : `/addresses/-`.

### <a name="operations"></a>Opérations

Le tableau suivant mentionne les opérations prises en charge telles qu’elles sont définies dans la [spécification JSON Patch](https://tools.ietf.org/html/rfc6902) :

|Opération  | Notes |
|-----------|--------------------------------|
| `add`     | Ajouter une propriété ou élément de tableau. Pour la propriété existante : définir la valeur.|
| `remove`  | Supprimer une propriété ou un élément de tableau. |
| `replace` | Identique à `remove` suivi de `add` au même emplacement. |
| `move`    | Identique à `remove` de la source suivi de `add` à la destination à l’aide de la valeur de la source. |
| `copy`    | Identique à `add` à la destination à l’aide de la valeur de la source. |
| `test`    | Retourne le code d’état de réussite si la valeur à `path` = `value` fournie.|

## <a name="json-patch-in-aspnet-core"></a>JSON Patch dans ASP.NET Core

L’implémentation ASP.NET Core de JSON Patch est fournie dans le package NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).

## <a name="action-method-code"></a>Code de méthode d’action

Dans un contrôleur d’API, une méthode d’action pour JSON Patch :

* est annotée avec l’attribut `HttpPatch` ;
* Accepte un `JsonPatchDocument<T>`, `[FromBody]`typiquement avec .
* appelle `ApplyTo` sur le document de correctif pour appliquer les modifications.

Voici un exemple :

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Ce code de l’application `Customer` d’échantillon fonctionne avec le modèle suivant :

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

L’exemple de méthode d’action :

* Construit un objet `Customer`.
* Applique le correctif.
* Retourne le résultat dans le corps de la réponse.

Dans une application réelle, le code récupérerait les données dans un magasin tel qu’une base de données et mettrait à jour la base de données après avoir appliqué le correctif.

### <a name="model-state"></a>État du modèle

L’exemple de méthode d’action précédent appelle une surcharge de `ApplyTo` qui prend l’état du modèle comme un de ses paramètres. Avec cette option, vous pouvez obtenir des messages d’erreur dans les réponses. L’exemple suivant montre le corps d’une demande-réponse incorrecte 400 pour une opération `test` :

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Objets dynamiques

L’exemple de méthode d’action suivante montre comment appliquer un patch à un objet dynamique :

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>L’opération d’ajout

* Si `path` pointe vers un élément du tableau : insère un nouvel élément avant celui spécifié par `path`.
* Si `path` pointe vers une propriété : définit la valeur de propriété.
* Si `path` pointe vers un emplacement qui n’existe pas :
  * Si la ressource à corriger est un objet dynamique : ajoute une propriété.
  * Si la ressource à corriger est un objet statique : la demande échoue.

L’exemple de document de correctif suivant définit la valeur de `CustomerName` et ajoute un objet `Order` à la fin du tableau `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>L’opération de suppression

* Si `path` pointe vers un élément du tableau : supprime l’élément.
* Si `path` pointe vers une propriété :
  * Si la ressource à corriger est un objet dynamique : supprime la propriété.
  * Si la ressource à corriger est un objet statique :
    * Si la propriété est Nullable : met la valeur sur Null.
    * Si la propriété n’est pas Nullable : met la valeur sur `default<T>`.

Le document de `CustomerName` correction d’échantillon `Orders[0]`suivant définit à annuler et supprime :

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>L’opération de remplacement

Cette opération est fonctionnellement identique à `remove` suivi de `add`.

Le document de correction d’échantillon `Orders[0]`suivant définit `Order` la valeur et `CustomerName` remplace par un nouvel objet :

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>L’opération de déplacement

* Si `path` pointe vers un élément du tableau : copie l’élément `from` à l’emplacement de l’élément `path`, puis exécute une opération `remove` sur l’élément `from`.
* Si `path` pointe vers une propriété : copie la valeur de la propriété `from` vers la propriété `path`, puis exécute une opération `remove` sur la propriété `from`.
* Si `path` pointe vers une propriété qui n’existe pas :
  * Si la ressource à corriger est un objet statique : la demande échoue.
  * Si la ressource à corriger est un objet dynamique : copie la propriété `from` à un emplacement indiqué par `path`, puis exécute une opération `remove` sur la propriété `from`.

L’exemple de document de correctif suivant :

* Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.
* Met `Orders[0].OrderName` sur la valeur Null.
* Déplace `Orders[1]` avant `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>L’opération de copie

Cette opération est fonctionnellement identique à une opération `move` sans l’étape `remove` finale.

L’exemple de document de correctif suivant :

* Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.
* Insère une copie de `Orders[1]` avant `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>L’opération de test

Si la valeur à l’emplacement indiqué par `path` est différente de la valeur fournie dans `value`, la demande échoue. Dans ce cas, toute la demande PATCH échoue, même si toutes les autres opérations dans le document de correctif réussiraient autrement.

L’opération `test` est généralement utilisée pour empêcher une mise à jour lorsqu’il y a un conflit d’accès concurrentiel.

L’exemple de document de correctif suivant n’a aucun effet si la valeur initiale de `CustomerName` est « John », car le test échoue :

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Obtenir le code

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples). ([Comment télécharger](xref:index#how-to-download-a-sample)).

Pour tester l’exemple, exécutez l’application et envoyez des demandes HTTP avec les paramètres suivants :

* URL : `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Méthode HTTP : `PATCH`
* En-tête : `Content-Type: application/json-patch+json`
* Corps : Copiez et collez l’un des échantillons de documents de patch JSON du dossier du projet *JSON.*

## <a name="additional-resources"></a>Ressources supplémentaires

* [Spécification de méthode PATCH IETF RFC 5789 PATCH](https://tools.ietf.org/html/rfc5789)
* [Spécification JSON Patch IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [Spécification de format de chemin JSON Patch IETF RFC 6901](https://tools.ietf.org/html/rfc6901)
* [Documentation JSON Patch](https://jsonpatch.com/). Inclut des liens vers des ressources pour la création de documents JSON Patch.
* [Code source ASP.NET Core JSON Patch](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article explique comment gérer les demandes de correctifs JSON dans une API web ASP.NET Core.

## <a name="patch-http-request-method"></a>Méthode de demande HTTP PATCH

Les méthodes PUT et [PATCH](https://tools.ietf.org/html/rfc5789) sont utilisées pour mettre à jour une ressource existante. La différence est que PUT remplace la ressource complète, tandis que PATCH spécifie uniquement les modifications.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) est un format qui spécifie des mises à jour à appliquer à une ressource. Un document JSON Patch possède un tableau des *opérations*. Chaque opération identifie un type particulier de modification, tel qu’ajouter un élément de tableau ou remplacer une valeur de propriété.

Par exemple, les documents JSON suivants représentent une ressource, un document de correctif JSON pour la ressource et le résultat de l’application de l’application des correctifs.

### <a name="resource-example"></a>Exemple de ressource

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Exemple de correctif JSON

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Dans le code JSON précédent :

* La propriété `op` indique le type d’opération.
* La propriété `path` indique l’élément à mettre à jour.
* La propriété `value` fournit la nouvelle valeur.

### <a name="resource-after-patch"></a>Ressources après correction

Voici la ressource après l’application du document JSON Patch précédent :

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Les modifications apportées en appliquant un document JSON Patch à une ressource sont atomiques : si une opération de la liste échoue, aucune opération de la liste n’est appliquée.

## <a name="path-syntax"></a>Syntaxe du chemin

Les différents niveaux de la propriété [path](https://tools.ietf.org/html/rfc6901) d’un objet de l’opération sont séparés par des barres obliques. Par exemple : `"/address/zipCode"`.

Les index de base zéro sont utilisés pour spécifier les éléments du tableau. Le premier élément du tableau `addresses` serait à `/addresses/0`. Pour `add` à la fin d’un tableau, utilisez un trait d’union (-) plutôt qu’un numéro d’index : `/addresses/-`.

### <a name="operations"></a>Opérations

Le tableau suivant mentionne les opérations prises en charge telles qu’elles sont définies dans la [spécification JSON Patch](https://tools.ietf.org/html/rfc6902) :

|Opération  | Notes |
|-----------|--------------------------------|
| `add`     | Ajouter une propriété ou élément de tableau. Pour la propriété existante : définir la valeur.|
| `remove`  | Supprimer une propriété ou un élément de tableau. |
| `replace` | Identique à `remove` suivi de `add` au même emplacement. |
| `move`    | Identique à `remove` de la source suivi de `add` à la destination à l’aide de la valeur de la source. |
| `copy`    | Identique à `add` à la destination à l’aide de la valeur de la source. |
| `test`    | Retourne le code d’état de réussite si la valeur à `path` = `value` fournie.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch dans ASP.NET Core

L’implémentation ASP.NET Core de JSON Patch est fournie dans le package NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/). Le package est inclus dans le métapackage [Microsoft.AspnetCore.App.](xref:fundamentals/metapackage-app)

## <a name="action-method-code"></a>Code de méthode d’action

Dans un contrôleur d’API, une méthode d’action pour JSON Patch :

* est annotée avec l’attribut `HttpPatch` ;
* Accepte un `JsonPatchDocument<T>`, `[FromBody]`typiquement avec .
* appelle `ApplyTo` sur le document de correctif pour appliquer les modifications.

Voici un exemple :

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Ce code de l’exemple d’application fonctionne avec le modèle `Customer` suivant.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

L’exemple de méthode d’action :

* Construit un objet `Customer`.
* Applique le correctif.
* Retourne le résultat dans le corps de la réponse.

 Dans une application réelle, le code récupérerait les données dans un magasin tel qu’une base de données et mettrait à jour la base de données après avoir appliqué le correctif.

### <a name="model-state"></a>État du modèle

L’exemple de méthode d’action précédent appelle une surcharge de `ApplyTo` qui prend l’état du modèle comme un de ses paramètres. Avec cette option, vous pouvez obtenir des messages d’erreur dans les réponses. L’exemple suivant montre le corps d’une demande-réponse incorrecte 400 pour une opération `test` :

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Objets dynamiques

L’exemple de méthode d’action suivant montre comment appliquer un correctif à un objet dynamique.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>L’opération d’ajout

* Si `path` pointe vers un élément du tableau : insère un nouvel élément avant celui spécifié par `path`.
* Si `path` pointe vers une propriété : définit la valeur de propriété.
* Si `path` pointe vers un emplacement qui n’existe pas :
  * Si la ressource à corriger est un objet dynamique : ajoute une propriété.
  * Si la ressource à corriger est un objet statique : la demande échoue.

L’exemple de document de correctif suivant définit la valeur de `CustomerName` et ajoute un objet `Order` à la fin du tableau `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>L’opération de suppression

* Si `path` pointe vers un élément du tableau : supprime l’élément.
* Si `path` pointe vers une propriété :
  * Si la ressource à corriger est un objet dynamique : supprime la propriété.
  * Si la ressource à corriger est un objet statique :
    * Si la propriété est Nullable : met la valeur sur Null.
    * Si la propriété n’est pas Nullable : met la valeur sur `default<T>`.

L’exemple suivant de document de correctif définit `CustomerName` sur Null et supprime `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>L’opération de remplacement

Cette opération est fonctionnellement identique à `remove` suivi de `add`.

L’exemple suivant de document de correctif définit la valeur de `CustomerName` et remplace `Orders[0]` avec un nouvel objet `Order`.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>L’opération de déplacement

* Si `path` pointe vers un élément du tableau : copie l’élément `from` à l’emplacement de l’élément `path`, puis exécute une opération `remove` sur l’élément `from`.
* Si `path` pointe vers une propriété : copie la valeur de la propriété `from` vers la propriété `path`, puis exécute une opération `remove` sur la propriété `from`.
* Si `path` pointe vers une propriété qui n’existe pas :
  * Si la ressource à corriger est un objet statique : la demande échoue.
  * Si la ressource à corriger est un objet dynamique : copie la propriété `from` à un emplacement indiqué par `path`, puis exécute une opération `remove` sur la propriété `from`.

L’exemple de document de correctif suivant :

* Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.
* Met `Orders[0].OrderName` sur la valeur Null.
* Déplace `Orders[1]` avant `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>L’opération de copie

Cette opération est fonctionnellement identique à une opération `move` sans l’étape `remove` finale.

L’exemple de document de correctif suivant :

* Copie la valeur de `Orders[0].OrderName` vers `CustomerName`.
* Insère une copie de `Orders[1]` avant `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>L’opération de test

Si la valeur à l’emplacement indiqué par `path` est différente de la valeur fournie dans `value`, la demande échoue. Dans ce cas, toute la demande PATCH échoue, même si toutes les autres opérations dans le document de correctif réussiraient autrement.

L’opération `test` est généralement utilisée pour empêcher une mise à jour lorsqu’il y a un conflit d’accès concurrentiel.

L’exemple de document de correctif suivant n’a aucun effet si la valeur initiale de `CustomerName` est « John », car le test échoue :

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Obtenir le code

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Comment télécharger](xref:index#how-to-download-a-sample)).

Pour tester l’exemple, exécutez l’application et envoyez des demandes HTTP avec les paramètres suivants :

* URL : `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Méthode HTTP : `PATCH`
* En-tête : `Content-Type: application/json-patch+json`
* Corps : Copiez et collez l’un des échantillons de documents de patch JSON du dossier du projet *JSON.*

## <a name="additional-resources"></a>Ressources supplémentaires

* [Spécification de méthode PATCH IETF RFC 5789 PATCH](https://tools.ietf.org/html/rfc5789)
* [Spécification JSON Patch IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [Spécification de format de chemin JSON Patch IETF RFC 6901](https://tools.ietf.org/html/rfc6901)
* [Documentation JSON Patch](https://jsonpatch.com/). Inclut des liens vers des ressources pour la création de documents JSON Patch.
* [Code source ASP.NET Core JSON Patch](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
