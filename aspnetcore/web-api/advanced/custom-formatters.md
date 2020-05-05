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
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="19801-103">Formateurs personnalisés dans l’API web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19801-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="19801-104">Par [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="19801-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="19801-105">ASP.NET Core MVC prend en charge l’échange de données dans les API Web à l’aide de formateurs d’entrée et de sortie.</span><span class="sxs-lookup"><span data-stu-id="19801-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="19801-106">Les formateurs d’entrée sont utilisés par la [liaison de modèle](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="19801-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="19801-107">Les formateurs de sortie sont utilisés pour [mettre en forme les réponses](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="19801-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="19801-108">L’infrastructure fournit des formateurs d’entrée et de sortie intégrés pour JSON et XML.</span><span class="sxs-lookup"><span data-stu-id="19801-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="19801-109">Il fournit un formateur de sortie intégré pour le texte brut, mais ne fournit pas de formateur d’entrée pour le texte brut.</span><span class="sxs-lookup"><span data-stu-id="19801-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="19801-110">Cet article montre comment ajouter la prise en charge de formats supplémentaires en créant des formateurs personnalisés.</span><span class="sxs-lookup"><span data-stu-id="19801-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="19801-111">Pour obtenir un exemple de formateur d’entrée personnalisé pour du texte brut, consultez [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="19801-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="19801-112">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="19801-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="19801-113">Quand utiliser les formateurs personnalisés</span><span class="sxs-lookup"><span data-stu-id="19801-113">When to use custom formatters</span></span>

<span data-ttu-id="19801-114">Utilisez un formateur personnalisé lorsque vous souhaitez que le processus de [négociation de contenu](xref:web-api/advanced/formatting#content-negotiation) prenne en charge un type de contenu qui n’est pas pris en charge par les formateurs intégrés.</span><span class="sxs-lookup"><span data-stu-id="19801-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="19801-115">Par exemple, si certains clients de votre API web peuvent prendre en charge le format [Protobuf](https://github.com/google/protobuf), vous pouvez être amené à utiliser Protobuf avec ces clients, car il est plus efficace.</span><span class="sxs-lookup"><span data-stu-id="19801-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="19801-116">Vous pouvez également demander à votre API web d’envoyer les noms et adresses des contacts au format [vCard](https://wikipedia.org/wiki/VCard), format couramment utilisé pour l’échange de données de contact.</span><span class="sxs-lookup"><span data-stu-id="19801-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="19801-117">L’exemple d’application fourni dans cet article implémente un formateur vCard simple.</span><span class="sxs-lookup"><span data-stu-id="19801-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="19801-118">Présentation de l’utilisation d’un formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="19801-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="19801-119">Voici les étapes à suivre pour créer et utiliser un formateur personnalisé :</span><span class="sxs-lookup"><span data-stu-id="19801-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="19801-120">Créez une classe de formateur de sortie si vous souhaitez sérialiser les données à envoyer au client.</span><span class="sxs-lookup"><span data-stu-id="19801-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="19801-121">Créez une classe de formateur d’entrée si vous souhaitez désérialiser les données reçues en provenance du client.</span><span class="sxs-lookup"><span data-stu-id="19801-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="19801-122">Ajoutez des instances de vos formateurs aux collections `InputFormatters` et `OutputFormatters` dans [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="19801-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="19801-123">Les sections suivantes fournissent de l’aide et des exemples de code pour chacune de ces étapes.</span><span class="sxs-lookup"><span data-stu-id="19801-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="19801-124">Guide pratique pour créer une classe de formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="19801-124">How to create a custom formatter class</span></span>

<span data-ttu-id="19801-125">Pour créer un formateur :</span><span class="sxs-lookup"><span data-stu-id="19801-125">To create a formatter:</span></span>

* <span data-ttu-id="19801-126">Faites dériver la classe de la classe de base appropriée.</span><span class="sxs-lookup"><span data-stu-id="19801-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="19801-127">Spécifiez les encodages et types de média valides dans le constructeur.</span><span class="sxs-lookup"><span data-stu-id="19801-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="19801-128">Substituez les méthodes `CanReadType`/`CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="19801-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="19801-129">Substituez les méthodes `ReadRequestBodyAsync`/`WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="19801-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="19801-130">Effectuer une dérivation à partir de la classe de base appropriée</span><span class="sxs-lookup"><span data-stu-id="19801-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="19801-131">Pour les médias de type texte (par exemple vCard), effectuez une dérivation à partir de la classe de base [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) ou [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter).</span><span class="sxs-lookup"><span data-stu-id="19801-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="19801-132">Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="19801-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="19801-133">Pour les types binaires, effectuez une dérivation à partir de la classe de base [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) ou [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter).</span><span class="sxs-lookup"><span data-stu-id="19801-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="19801-134">Spécifier les encodages et types de média valides</span><span class="sxs-lookup"><span data-stu-id="19801-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="19801-135">Dans le constructeur, spécifiez les encodages et types de média valides en effectuant les ajouts nécessaires aux collections `SupportedMediaTypes` et `SupportedEncodings`.</span><span class="sxs-lookup"><span data-stu-id="19801-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="19801-136">Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="19801-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="19801-137">Vous ne pouvez pas effectuer d’injection de dépendances de constructeur dans une classe de formateur.</span><span class="sxs-lookup"><span data-stu-id="19801-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="19801-138">Par exemple, vous ne pouvez pas obtenir un journaliseur en ajoutant un paramètre de journaliseur au constructeur.</span><span class="sxs-lookup"><span data-stu-id="19801-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="19801-139">Pour accéder aux services, vous devez utiliser l’objet de contexte passé à vos méthodes.</span><span class="sxs-lookup"><span data-stu-id="19801-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="19801-140">L’exemple de code [ci-dessous](#read-write) montre comment procéder.</span><span class="sxs-lookup"><span data-stu-id="19801-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="19801-141">Substituer CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="19801-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="19801-142">Spécifiez le type cible de la désérialisation ou de la sérialisation en remplaçant les méthodes `CanReadType` ou `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="19801-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="19801-143">Par exemple, vous pouvez peut-être créer uniquement un texte vCard à partir d’un type `Contact`, et inversement.</span><span class="sxs-lookup"><span data-stu-id="19801-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="19801-144">Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="19801-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="19801-145">Méthode CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="19801-145">The CanWriteResult method</span></span>

<span data-ttu-id="19801-146">Dans certains cas, vous devez substituer `CanWriteResult` au lieu de `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="19801-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="19801-147">Utilisez `CanWriteResult` si les conditions suivantes sont vraies :</span><span class="sxs-lookup"><span data-stu-id="19801-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="19801-148">Votre méthode d’action retourne une classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="19801-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="19801-149">Il existe des classes dérivées qui peuvent être retournées au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="19801-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="19801-150">Vous devez savoir au moment de l’exécution quelle est la classe dérivée retournée par l’action.</span><span class="sxs-lookup"><span data-stu-id="19801-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="19801-151">Par exemple, la signature de votre méthode d’action retourne un type `Person`, mais elle peut éventuellement retourner un type `Student` ou un type `Instructor` dérivé de `Person`.</span><span class="sxs-lookup"><span data-stu-id="19801-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="19801-152">Si vous souhaitez que votre formateur gère uniquement les objets `Student`, vérifiez le type d’[Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) dans l’objet de contexte fourni à la méthode `CanWriteResult`.</span><span class="sxs-lookup"><span data-stu-id="19801-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="19801-153">Notez qu’il n’est pas nécessaire d’utiliser `CanWriteResult` quand la méthode d’action retourne `IActionResult`. Dans ce cas, la méthode `CanWriteType` reçoit le type au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="19801-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="19801-154">Substituer ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="19801-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="19801-155">Vous effectuez le travail réel de désérialisation ou de sérialisation dans `ReadRequestBodyAsync` ou `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="19801-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="19801-156">Les lignes surlignées dans l’exemple suivant montrent comment obtenir des services à partir du conteneur d’injection de dépendances (vous ne pouvez pas les obtenir à partir des paramètres du constructeur).</span><span class="sxs-lookup"><span data-stu-id="19801-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="19801-157">Pour un exemple de formateur d’entrée, consultez l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="19801-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="19801-158">Guide pratique pour configurer MVC et utiliser un formateur personnalisé</span><span class="sxs-lookup"><span data-stu-id="19801-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="19801-159">Pour utiliser un formateur personnalisé, ajoutez une instance de la classe du formateur à la collection `InputFormatters` ou `OutputFormatters`.</span><span class="sxs-lookup"><span data-stu-id="19801-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="19801-160">Les formateurs sont évalués dans l’ordre dans lequel vous les insérez.</span><span class="sxs-lookup"><span data-stu-id="19801-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="19801-161">Le premier est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="19801-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="19801-162">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="19801-162">Next steps</span></span>

* <span data-ttu-id="19801-163">[Exemple d’application pour ce document](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), qui implémente de simples formateurs d’entrée et de sortie vCard.</span><span class="sxs-lookup"><span data-stu-id="19801-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="19801-164">L’application lit et écrit des vCard qui ressemblent à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="19801-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="19801-165">Pour afficher la sortie vCard, exécutez l’application et envoyez une requête Get avec « text/vcard » dans l’en-tête Accept à `http://localhost:63313/api/contacts/` (quand l’exécution a lieu à partir de Visual Studio) ou `http://localhost:5000/api/contacts/` (quand l’exécution a lieu à partir de la ligne de commande).</span><span class="sxs-lookup"><span data-stu-id="19801-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="19801-166">Pour ajouter un vCard à la collection de contacts en mémoire, envoyez une requête Post à la même URL, avec « text/vcard » dans l’en-tête Content-Type et le texte du vCard dans le corps, comme dans l’exemple ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="19801-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
