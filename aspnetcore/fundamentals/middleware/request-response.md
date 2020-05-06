---
title: Opérations de demande et de réponse dans ASP.NET Core
author: jkotalik
description: Découvrez comment lire le corps de demande et écrire le corps de la réponse dans ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: f16bc7ec61c10600fe72a763fef96987210fbe76
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775997"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Opérations de demande et de réponse dans ASP.NET Core

Par [Justin Kotalik](https://github.com/jkotalik)

Cet article explique comment lire à partir du corps de la demande et écrire dans le corps de la réponse. Le code de ces opérations peut être nécessaire lors de l’écriture d’un intergiciel (middleware). En dehors de l’intergiciel d’écriture, le code personnalisé n’est généralement pas obligatoire, car les Razor opérations sont gérées par MVC et les pages.

Il existe deux abstractions pour les corps de demande et de <xref:System.IO.Stream> réponse <xref:System.IO.Pipelines.Pipe>: et. Pour la lecture de la demande, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) est un <xref:System.IO.Stream>, et `HttpRequest.BodyReader` est un <xref:System.IO.Pipelines.PipeReader>. Pour l’écriture de réponse, [HttpResponse. Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) est <xref:System.IO.Stream>un `HttpResponse.BodyWriter` , et <xref:System.IO.Pipelines.PipeWriter>est un.

Les [pipelines](/dotnet/standard/io/pipelines) sont recommandés par rapport aux flux. Les flux peuvent être plus faciles à utiliser pour des opérations simples, mais les pipelines présentent un avantage de performances et sont plus faciles à utiliser dans la plupart des scénarios. ASP.NET Core commence à utiliser des pipelines plutôt que des flux en interne. Voici quelques exemples :

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Les flux ne sont pas supprimés de l’infrastructure. Les flux continuent à être utilisés dans .NET, et de nombreux types de flux n’ont pas d’équivalents de canal, tels que `FileStreams` et `ResponseCompression`.

## <a name="stream-examples"></a>Exemples de flux

Supposons que l’objectif est de créer un intergiciel qui lit l’intégralité du corps de la requête sous la forme d’une liste de chaînes, en la fractionnant sur de nouvelles lignes. Une implémentation simple de flux peut se présenter comme dans l’exemple suivant :

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Ce code fonctionne, mais il existe certains problèmes :

* Avant d’ajouter à `StringBuilder`, l’exemple crée une autre chaîne (`encodedString`) qui est immédiatement rejetée. Ce processus se produit pour tous les octets dans le flux, il en résulte une allocation de mémoire supplémentaire de la taille de la totalité du corps de la demande.
* L’exemple lit la chaîne entière avant de fractionner sur les nouvelles lignes. Il est plus efficace de rechercher de nouvelles lignes dans le tableau d’octets.

Voici un exemple qui résout certains des problèmes précédents :

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

L’exemple précédent :

* Ne met pas en mémoire tampon le corps entier de la demande dans un `StringBuilder` , sauf s’il n’y a pas de caractère de nouvelle ligne.
* N’appelle pas `Split` sur la chaîne.

Toutefois, il existe toujours quelques problèmes :

* Si les caractères de saut de ligne sont éparpillés, la majeure partie du corps de la demande est mise en mémoire tampon dans la chaîne.
* Le code continue à créer des chaînes`remainingString`() et les ajoute à la mémoire tampon de chaîne, ce qui entraîne une allocation supplémentaire.

Ces problèmes sont réparables, mais le code devient progressivement plus complexe avec une amélioration minime. Les pipelines permettent de résoudre ces problèmes avec un code peu compliqué.

## <a name="pipelines"></a>Pipelines

L’exemple suivant indique comment le même scénario peut être géré à l’aide d’un `PipeReader` :

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Cet exemple résout de nombreux problèmes trouvés dans les implémentations de flux :

* Il n’est pas nécessaire de disposer d’une mémoire `PipeReader` tampon de chaîne, car le gère les octets qui n’ont pas été utilisés.
* Les chaînes codées sont ajoutées directement à la liste des chaînes retournées.
* La création de chaînes est libre d’allocation en dehors de la mémoire utilisée par la chaîne (à l’exception de l’appel `ToArray()`).

## <a name="adapters"></a>Adaptateurs

Les `Body` propriétés `BodyReader/BodyWriter` et sont toutes deux `HttpRequest` disponibles `HttpResponse`pour et. Lorsque vous définissez `Body` sur un autre flux, un nouvel ensemble d’adaptateurs adapte automatiquement chaque type à l’autre. Si vous définissez `HttpRequest.Body` sur un nouveau flux, `HttpRequest.BodyReader` est automatiquement défini sur un nouveau `PipeReader` qui encapsule `HttpRequest.Body`.

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`est utilisé pour indiquer que les en-têtes ne sont pas modifiables `OnStarting` et pour exécuter des rappels. Lors de l’utilisation de Kestrel en tant `StartAsync` que serveur, `PipeReader` l’appel de avant d' `GetMemory` utiliser la garantie que la <xref:System.IO.Pipelines.Pipe> mémoire retournée par appartient au interne de Kestrel plutôt qu’à une mémoire tampon externe.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Présentation de System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
