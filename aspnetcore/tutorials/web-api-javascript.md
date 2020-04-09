---
title: 'Tutorial: Appelez une ASP.NET’API Web Core avec JavaScript'
author: rick-anderson
description: Découvrez comment appeler une API web ASP.NET Core avec JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655252"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Tutorial: Appelez une ASP.NET’API Web Core avec JavaScript

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce tutoriel montre comment appeler une API web ASP.NET Core avec JavaScript à l’aide de l’[API Fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Pour ASP.NET Core 2.2, consultez la version 2.2 de [Appeler l’API web avec JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Prérequis

* [Tutoriel complet: Créer une API web](xref:tutorials/first-web-api)
* Connaissance de CSS, HTML et JavaScript

## <a name="call-the-web-api-with-javascript"></a>Appelez l’API web avec JavaScript

Dans cette section, vous allez ajouter une page HTML contenant des formulaires permettant de créer et de gérer des éléments de tâche. Des gestionnaires d’événements sont joints aux éléments de la page. Les gestionnaires d’événements génèrent des requêtes HTTP pour les méthodes d’action de l’API web. La fonction `fetch` de l’API Fetch lance chaque requête HTTP.

La `fetch` fonction renvoie un objet [Promise,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) qui `Response` contient une réponse HTTP représentée comme objet. Un modèle courant consiste à extraire le corps de réponse JSON en appelant la fonction `json` sur l'objet `Response`. JavaScript met à jour la page avec les détails de la réponse de l’API Web.

L'appel `fetch` le plus simple accepte un seul paramètre représentant l’itinéraire. Un deuxième paramètre, connu sous le nom d’objet `init`, est facultatif. `init` est utilisé pour configurer la requête HTTP.

1. Configurez l’application pour [servir des fichiers statiques](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) et [activez la cartographie par défaut des fichiers](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). Le code en surbrillance suivant est nécessaire dans la méthode `Configure` de *Startup.cs* :

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Créez un dossier *wwwroot* dans la racine du projet.

1. Créez un dossier *js* à l’intérieur du dossier *wwwroot.*

1. Ajoutez un fichier HTML nommé *index.html* au dossier *wwwroot.* Remplacer le contenu de *index.html* par la balisage suivante :

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Ajoutez un fichier JavaScript nommé *site.js* au dossier *wwwroot/js.* Remplacer le contenu de *site.js* par le code suivant :

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Vous devrez peut-être changer les paramètres de lancement du projet ASP.NET Core pour tester la page HTML localement :

1. Ouvrez *Properties\launchSettings.json*.
1. Supprimer `launchUrl` la propriété pour forcer l’application à ouvrir à *index.html*&mdash;le fichier par défaut du projet.

Cet exemple appelle toutes les méthodes CRUD de l’API web. Les explications suivantes traitent des demandes de l’API web.

### <a name="get-a-list-of-to-do-items"></a>Obtenir une liste de tâches

Dans le code suivant, une requête HTTP GET est envoyée à l'itinéraire *api/TodoItems* :

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Quand l’API web retourne un code d’état de réussite, la fonction `_displayItems` est appelée. Chaque élément de tâche du paramètre de tableau accepté par `_displayItems` est ajouté à une table avec les boutons **Modifier** et **Supprimer**. Si la demande de l’API Web échoue, une erreur est consignée dans la console du navigateur.

### <a name="add-a-to-do-item"></a>Ajouter une tâche

Dans le code suivant :

* Une variable `item` est déclarée pour construire une représentation littérale d’objet de l’élément de tâche.
* Une requête Fetch est configurée avec les options suivantes :
  * `method`&mdash; spécifie le verbe d’action POST HTTP.
  * `body`&mdash; spécifie la représentation JSON du corps de la demande. Le JSON est généré en passant le littéral d’objet stocké dans `item` à la fonction [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).
  * `headers`&mdash; spécifie les en-têtes de requête HTTP `Accept` et `Content-Type`. Les deux en-têtes sont définies sur `application/json` pour spécifier le type de média respectivement reçu et envoyé.
* Une requête HTTP POST est envoyée à l’itinéraire *api/TodoItems*.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Quand l’API web retourne un code d’état de réussite, la fonction `getItems` est appelée pour mettre à jour la table HTML. Si la demande de l’API Web échoue, une erreur est consignée dans la console du navigateur.

### <a name="update-a-to-do-item"></a>Mettre à jour une tâche

La mise à jour d’un élément de tâche est semblable à l’ajout d’un élément. Toutefois, il y a deux différences importantes :

* L’itinéraire est suivi de l’identificateur unique de l’élément à mettre à jour. Par exemple, *api/TodoItems/1*.
* Le verbe d’action HTTP est PUT, comme indiqué par l’option `method`.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Supprimer une tâche

Pour supprimer un élément de tâche, définissez l’option de la demande `method` sur `DELETE` et spécifiez l’identificateur unique de l’élément dans l’URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Passez au tutoriel suivant pour apprendre à générer des pages d’aide d’API web :

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
