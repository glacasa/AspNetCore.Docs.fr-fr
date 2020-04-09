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
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="37222-103">Opérations de demande et de réponse dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="37222-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="37222-104">Par [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="37222-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="37222-105">Cet article explique comment lire à partir du corps de la demande et écrire dans le corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="37222-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="37222-106">Le code de ces opérations peut être exigé lors de la rédaction de middleware.</span><span class="sxs-lookup"><span data-stu-id="37222-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="37222-107">En dehors de l’écriture middleware, le code personnalisé n’est généralement pas nécessaire parce que les opérations sont traitées par MVC et Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="37222-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="37222-108">Il y a deux abstractions pour <xref:System.IO.Stream> <xref:System.IO.Pipelines.Pipe>les organes de demande et de réponse : et .</span><span class="sxs-lookup"><span data-stu-id="37222-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="37222-109">Pour la lecture de la demande, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) est un <xref:System.IO.Stream>, et `HttpRequest.BodyReader` est un <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="37222-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="37222-110">Pour l’écriture de réponse, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) est un <xref:System.IO.Stream>, et `HttpResponse.BodyWriter` est un <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="37222-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="37222-111">[Les pipelines](/dotnet/standard/io/pipelines) sont recommandés sur les cours d’eau.</span><span class="sxs-lookup"><span data-stu-id="37222-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="37222-112">Les flux peuvent être plus faciles à utiliser pour des opérations simples, mais les pipelines présentent un avantage de performances et sont plus faciles à utiliser dans la plupart des scénarios.</span><span class="sxs-lookup"><span data-stu-id="37222-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="37222-113">ASP.NET Core commence à utiliser des pipelines au lieu de cours d’eau à l’interne.</span><span class="sxs-lookup"><span data-stu-id="37222-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="37222-114">Voici quelques exemples :</span><span class="sxs-lookup"><span data-stu-id="37222-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="37222-115">Les flux ne sont pas retirés du cadre.</span><span class="sxs-lookup"><span data-stu-id="37222-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="37222-116">Les flux continuent d’être utilisés tout au long de .NET, `FileStreams` et `ResponseCompression`de nombreux types de flux n’ont pas d’équivalents de tuyaux, tels que et .</span><span class="sxs-lookup"><span data-stu-id="37222-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="37222-117">Exemples de flux</span><span class="sxs-lookup"><span data-stu-id="37222-117">Stream examples</span></span>

<span data-ttu-id="37222-118">Supposons que le but est de créer un middleware qui lit l’ensemble du corps de demande comme une liste de chaînes, se fendant sur de nouvelles lignes.</span><span class="sxs-lookup"><span data-stu-id="37222-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="37222-119">Une implémentation simple de flux peut se présenter comme dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="37222-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="37222-120">Ce code fonctionne, mais il existe certains problèmes :</span><span class="sxs-lookup"><span data-stu-id="37222-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="37222-121">Avant d’ajouter à `StringBuilder`, l’exemple crée une autre chaîne (`encodedString`) qui est immédiatement rejetée.</span><span class="sxs-lookup"><span data-stu-id="37222-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="37222-122">Ce processus se produit pour tous les octets dans le flux, il en résulte une allocation de mémoire supplémentaire de la taille de la totalité du corps de la demande.</span><span class="sxs-lookup"><span data-stu-id="37222-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="37222-123">L’exemple lit la chaîne entière avant de fractionner sur les nouvelles lignes.</span><span class="sxs-lookup"><span data-stu-id="37222-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="37222-124">Il est plus efficace de vérifier les nouvelles lignes dans le tableau byte.</span><span class="sxs-lookup"><span data-stu-id="37222-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="37222-125">Voici un exemple qui corrige certaines des questions précédentes :</span><span class="sxs-lookup"><span data-stu-id="37222-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="37222-126">Cet exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="37222-126">This preceding example:</span></span>

* <span data-ttu-id="37222-127">Ne met pas en mémoire tampon le corps entier de la demande dans un `StringBuilder` , sauf s’il n’y a pas de caractère de nouvelle ligne.</span><span class="sxs-lookup"><span data-stu-id="37222-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="37222-128">N’appelle pas `Split` sur la chaîne.</span><span class="sxs-lookup"><span data-stu-id="37222-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="37222-129">Toutefois, il existe toujours quelques problèmes :</span><span class="sxs-lookup"><span data-stu-id="37222-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="37222-130">Si les caractères newline sont clairsemés, une grande partie du corps de demande est tamponnée dans la chaîne.</span><span class="sxs-lookup"><span data-stu-id="37222-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="37222-131">Le code continue de`remainingString`créer des chaînes ( ) et les ajoute au tampon de chaîne, ce qui se traduit par une allocation supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="37222-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="37222-132">Ces problèmes sont réparables, mais le code devient progressivement plus compliqué avec peu d’amélioration.</span><span class="sxs-lookup"><span data-stu-id="37222-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="37222-133">Les pipelines permettent de résoudre ces problèmes avec un code peu compliqué.</span><span class="sxs-lookup"><span data-stu-id="37222-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="37222-134">Pipelines</span><span class="sxs-lookup"><span data-stu-id="37222-134">Pipelines</span></span>

<span data-ttu-id="37222-135">L’exemple suivant indique comment le même scénario peut être géré à l’aide d’un `PipeReader` :</span><span class="sxs-lookup"><span data-stu-id="37222-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="37222-136">Cet exemple résout de nombreux problèmes trouvés dans les implémentations de flux :</span><span class="sxs-lookup"><span data-stu-id="37222-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="37222-137">Il n’est pas nécessaire d’avoir un tampon de chaîne parce que les `PipeReader` poignées d’octets qui n’ont pas été utilisées.</span><span class="sxs-lookup"><span data-stu-id="37222-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="37222-138">Les chaînes codées sont ajoutées directement à la liste des chaînes retournées.</span><span class="sxs-lookup"><span data-stu-id="37222-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="37222-139">La création de chaînes est libre d’allocation en dehors de la mémoire utilisée par la chaîne (à l’exception de l’appel `ToArray()`).</span><span class="sxs-lookup"><span data-stu-id="37222-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="37222-140">Adaptateurs</span><span class="sxs-lookup"><span data-stu-id="37222-140">Adapters</span></span>

<span data-ttu-id="37222-141">Les `Body` `BodyReader/BodyWriter` deux et `HttpRequest` les `HttpResponse`propriétés sont disponibles pour et .</span><span class="sxs-lookup"><span data-stu-id="37222-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="37222-142">Lorsque vous `Body` définissez un flux différent, un nouvel ensemble d’adaptateurs adapte automatiquement chaque type à l’autre.</span><span class="sxs-lookup"><span data-stu-id="37222-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="37222-143">Si vous `HttpRequest.Body` définissez un `HttpRequest.BodyReader` nouveau flux, `PipeReader` est automatiquement `HttpRequest.Body`réglé sur un nouveau qui s’enroule .</span><span class="sxs-lookup"><span data-stu-id="37222-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="37222-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="37222-144">StartAsync</span></span>

<span data-ttu-id="37222-145">`HttpResponse.StartAsync`est utilisé pour indiquer que les en-têtes `OnStarting` sont inmodifiables et pour exécuter des rappels.</span><span class="sxs-lookup"><span data-stu-id="37222-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="37222-146">Lors de l’utilisation de `StartAsync` Kestrel `PipeReader` comme un `GetMemory` serveur, appeler avant d’utiliser les garanties que la mémoire retournée par appartient à l’interne <xref:System.IO.Pipelines.Pipe> de Kestrel plutôt que d’un tampon externe.</span><span class="sxs-lookup"><span data-stu-id="37222-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37222-147">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="37222-147">Additional resources</span></span>

* [<span data-ttu-id="37222-148">Présentation de System.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="37222-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
