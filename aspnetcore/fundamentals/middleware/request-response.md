---
title: Opérations de demande et de réponse dans ASP.NET Core
author: jkotalik
description: Découvrez comment lire le corps de demande et écrire le corps de la réponse dans ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b473fa02e1d23f02bc5d2e15fa54ab7b1dbbb17c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667215"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Opérations de demande et de réponse dans ASP.NET Core

Par [Justin Kotalik](https://github.com/jkotalik)

Cet article explique comment lire à partir du corps de la demande et écrire dans le corps de la réponse. Le code de ces opérations peut être exigé lors de la rédaction de middleware. En dehors de l’écriture middleware, le code personnalisé n’est généralement pas nécessaire parce que les opérations sont traitées par MVC et Razor Pages.

Il y a deux abstractions pour <xref:System.IO.Stream> <xref:System.IO.Pipelines.Pipe>les organes de demande et de réponse : et . Pour la lecture de la demande, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) est un <xref:System.IO.Stream>, et `HttpRequest.BodyReader` est un <xref:System.IO.Pipelines.PipeReader>. Pour l’écriture de réponse, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) est un <xref:System.IO.Stream>, et `HttpResponse.BodyWriter` est un <xref:System.IO.Pipelines.PipeWriter>.

[Les pipelines](/dotnet/standard/io/pipelines) sont recommandés sur les cours d’eau. Les flux peuvent être plus faciles à utiliser pour des opérations simples, mais les pipelines présentent un avantage de performances et sont plus faciles à utiliser dans la plupart des scénarios. ASP.NET Core commence à utiliser des pipelines au lieu de cours d’eau à l’interne. Voici quelques exemples :

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Les flux ne sont pas retirés du cadre. Les flux continuent d’être utilisés tout au long de .NET, `FileStreams` et `ResponseCompression`de nombreux types de flux n’ont pas d’équivalents de tuyaux, tels que et .

## <a name="stream-examples"></a>Exemples de flux

Supposons que le but est de créer un middleware qui lit l’ensemble du corps de demande comme une liste de chaînes, se fendant sur de nouvelles lignes. Une implémentation simple de flux peut se présenter comme dans l’exemple suivant :

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Ce code fonctionne, mais il existe certains problèmes :

* Avant d’ajouter à `StringBuilder`, l’exemple crée une autre chaîne (`encodedString`) qui est immédiatement rejetée. Ce processus se produit pour tous les octets dans le flux, il en résulte une allocation de mémoire supplémentaire de la taille de la totalité du corps de la demande.
* L’exemple lit la chaîne entière avant de fractionner sur les nouvelles lignes. Il est plus efficace de vérifier les nouvelles lignes dans le tableau byte.

Voici un exemple qui corrige certaines des questions précédentes :

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Cet exemple précédent :

* Ne met pas en mémoire tampon le corps entier de la demande dans un `StringBuilder` , sauf s’il n’y a pas de caractère de nouvelle ligne.
* N’appelle pas `Split` sur la chaîne.

Toutefois, il existe toujours quelques problèmes :

* Si les caractères newline sont clairsemés, une grande partie du corps de demande est tamponnée dans la chaîne.
* Le code continue de`remainingString`créer des chaînes ( ) et les ajoute au tampon de chaîne, ce qui se traduit par une allocation supplémentaire.

Ces problèmes sont réparables, mais le code devient progressivement plus compliqué avec peu d’amélioration. Les pipelines permettent de résoudre ces problèmes avec un code peu compliqué.

## <a name="pipelines"></a>Pipelines

L’exemple suivant indique comment le même scénario peut être géré à l’aide d’un `PipeReader` :

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Cet exemple résout de nombreux problèmes trouvés dans les implémentations de flux :

* Il n’est pas nécessaire d’avoir un tampon de chaîne parce que les `PipeReader` poignées d’octets qui n’ont pas été utilisées.
* Les chaînes codées sont ajoutées directement à la liste des chaînes retournées.
* La création de chaînes est libre d’allocation en dehors de la mémoire utilisée par la chaîne (à l’exception de l’appel `ToArray()`).

## <a name="adapters"></a>Adaptateurs

Les `Body` `BodyReader/BodyWriter` deux et `HttpRequest` les `HttpResponse`propriétés sont disponibles pour et . Lorsque vous `Body` définissez un flux différent, un nouvel ensemble d’adaptateurs adapte automatiquement chaque type à l’autre. Si vous `HttpRequest.Body` définissez un `HttpRequest.BodyReader` nouveau flux, `PipeReader` est automatiquement `HttpRequest.Body`réglé sur un nouveau qui s’enroule .

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`est utilisé pour indiquer que les en-têtes `OnStarting` sont inmodifiables et pour exécuter des rappels. Lors de l’utilisation de `StartAsync` Kestrel `PipeReader` comme un `GetMemory` serveur, appeler avant d’utiliser les garanties que la mémoire retournée par appartient à l’interne <xref:System.IO.Pipelines.Pipe> de Kestrel plutôt que d’un tampon externe.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Présentation de System.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
