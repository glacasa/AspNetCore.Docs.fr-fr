---
title: Formateurs personnalisés dans l’API web ASP.NET Core
author: rick-anderson
description: Découvrez comment créer et utiliser des formateurs personnalisés pour les API web dans ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777317"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Formateurs personnalisés dans l’API web ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core MVC prend en charge l’échange de données dans les API Web à l’aide de formateurs d’entrée et de sortie. Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding). Les formateurs de sortie sont utilisés pour [mettre en forme les réponses](xref:web-api/advanced/formatting).

L’infrastructure fournit des formateurs d’entrée et de sortie intégrés pour JSON et XML. Il fournit un formateur de sortie intégré pour le texte brut, mais ne fournit pas de formateur d’entrée pour le texte brut.

Cet article montre comment ajouter la prise en charge de formats supplémentaires en créant des formateurs personnalisés. Pour obtenir un exemple de formateur d’entrée personnalisé pour du texte brut, consultez [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) sur GitHub.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Quand utiliser les formateurs personnalisés

Utilisez un formateur personnalisé lorsque vous souhaitez que le processus de [négociation de contenu](xref:web-api/advanced/formatting#content-negotiation) prenne en charge un type de contenu qui n’est pas pris en charge par les formateurs intégrés.

Par exemple, si certains clients de votre API web peuvent prendre en charge le format [Protobuf](https://github.com/google/protobuf), vous pouvez être amené à utiliser Protobuf avec ces clients, car il est plus efficace. Vous pouvez également demander à votre API web d’envoyer les noms et adresses des contacts au format [vCard](https://wikipedia.org/wiki/VCard), format couramment utilisé pour l’échange de données de contact. L’exemple d’application fourni dans cet article implémente un formateur vCard simple.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Présentation de l’utilisation d’un formateur personnalisé

Voici les étapes à suivre pour créer et utiliser un formateur personnalisé :

* Créez une classe de formateur de sortie si vous souhaitez sérialiser les données à envoyer au client.
* Créez une classe de formateur d’entrée si vous souhaitez désérialiser les données reçues en provenance du client.
* Ajoutez des instances de vos formateurs aux collections `InputFormatters` et `OutputFormatters` dans [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).

Les sections suivantes fournissent de l’aide et des exemples de code pour chacune de ces étapes.

## <a name="how-to-create-a-custom-formatter-class"></a>Guide pratique pour créer une classe de formateur personnalisé

Pour créer un formateur :

* Faites dériver la classe de la classe de base appropriée.
* Spécifiez les encodages et types de média valides dans le constructeur.
* Substituez les méthodes `CanReadType`/`CanWriteType`.
* Substituez les méthodes `ReadRequestBodyAsync`/`WriteResponseBodyAsync`.
  
### <a name="derive-from-the-appropriate-base-class"></a>Effectuer une dérivation à partir de la classe de base appropriée

Pour les médias de type texte (par exemple vCard), effectuez une dérivation à partir de la classe de base [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) ou [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

Pour les types binaires, effectuez une dérivation à partir de la classe de base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) ou [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).

### <a name="specify-valid-media-types-and-encodings"></a>Spécifier les encodages et types de média valides

Dans le constructeur, spécifiez les encodages et types de média valides en effectuant les ajouts nécessaires aux collections `SupportedMediaTypes` et `SupportedEncodings`.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

> [!NOTE]
> Vous ne pouvez pas effectuer d’injection de dépendances de constructeur dans une classe de formateur. Par exemple, vous ne pouvez pas obtenir un journaliseur en ajoutant un paramètre de journaliseur au constructeur. Pour accéder aux services, vous devez utiliser l’objet de contexte passé à vos méthodes. L’exemple de code [ci-dessous](#read-write) montre comment procéder.

### <a name="override-canreadtypecanwritetype"></a>Substituer CanReadType/CanWriteType

Spécifiez le type cible de la désérialisation ou de la sérialisation en remplaçant les méthodes `CanReadType` ou `CanWriteType`. Par exemple, vous pouvez peut-être créer uniquement un texte vCard à partir d’un type `Contact`, et inversement.

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

#### <a name="the-canwriteresult-method"></a>Méthode CanWriteResult

Dans certains cas, vous devez substituer `CanWriteResult` au lieu de `CanWriteType`. Utilisez `CanWriteResult` si les conditions suivantes sont vraies :

* Votre méthode d’action retourne une classe de modèle.
* Il existe des classes dérivées qui peuvent être retournées au moment de l’exécution.
* Vous devez savoir au moment de l’exécution quelle est la classe dérivée retournée par l’action.

Par exemple, la signature de votre méthode d’action retourne un type `Person`, mais elle peut éventuellement retourner un type `Student` ou un type `Instructor` dérivé de `Person`. Si vous souhaitez que votre formateur gère uniquement les objets `Student`, vérifiez le type d’[Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) dans l’objet de contexte fourni à la méthode `CanWriteResult`. Notez qu’il n’est pas nécessaire d’utiliser `CanWriteResult` quand la méthode d’action retourne `IActionResult`. Dans ce cas, la méthode `CanWriteType` reçoit le type au moment de l’exécution.

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a>Substituer ReadRequestBodyAsync/WriteResponseBodyAsync

Vous effectuez le travail réel de désérialisation ou de sérialisation dans `ReadRequestBodyAsync` ou `WriteResponseBodyAsync`. Les lignes surlignées dans l’exemple suivant montrent comment obtenir des services à partir du conteneur d’injection de dépendances (vous ne pouvez pas les obtenir à partir des paramètres du constructeur).

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Guide pratique pour configurer MVC et utiliser un formateur personnalisé

Pour utiliser un formateur personnalisé, ajoutez une instance de la classe du formateur à la collection `InputFormatters` ou `OutputFormatters`.

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

Les formateurs sont évalués dans l’ordre dans lequel vous les insérez. Le premier est prioritaire.

## <a name="next-steps"></a>Étapes suivantes

* [Exemple d’application pour ce document](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), qui implémente de simples formateurs d’entrée et de sortie vCard. L’application lit et écrit des vCard qui ressemblent à l’exemple suivant :

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

Pour afficher la sortie vCard, exécutez l’application et envoyez une requête Get avec « text/vcard » dans l’en-tête Accept à `http://localhost:63313/api/contacts/` (quand l’exécution a lieu à partir de Visual Studio) ou `http://localhost:5000/api/contacts/` (quand l’exécution a lieu à partir de la ligne de commande).

Pour ajouter un vCard à la collection de contacts en mémoire, envoyez une requête Post à la même URL, avec « text/vcard » dans l’en-tête Content-Type et le texte du vCard dans le corps, comme dans l’exemple ci-dessus.
