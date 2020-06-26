---
title: Activation d’un intergiciel (middleware) avec un conteneur tiers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser un middleware fortement typé avec une activation basée sur une fabrique et un conteneur tiers dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a4224d62c11b4fee767c7b1c9b7d29f7e4f7d858
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407952"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="ff5db-103">Activation d’un intergiciel (middleware) avec un conteneur tiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff5db-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff5db-104">Cet article montre comment utiliser <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> et <xref:Microsoft.AspNetCore.Http.IMiddleware> comme point d’extensibilité pour l’activation d’un [middleware](xref:fundamentals/middleware/index) avec un conteneur tiers.</span><span class="sxs-lookup"><span data-stu-id="ff5db-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="ff5db-105">Pour obtenir des informations sur `IMiddlewareFactory` et `IMiddleware`, consultez <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="ff5db-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="ff5db-106">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff5db-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ff5db-107">L’exemple d’application montre l’activation d’un middleware par une implémentation de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="ff5db-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="ff5db-108">L’exemple utilise le conteneur d’injection de dépendances [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="ff5db-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="ff5db-109">L’implémentation du middleware de l’exemple enregistre la valeur fournie par un paramètre de la chaîne de requête (`key`).</span><span class="sxs-lookup"><span data-stu-id="ff5db-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="ff5db-110">Le middleware utilise un contexte de base de données injecté (un service délimité) pour enregistrer la valeur de la chaîne de requête dans une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ff5db-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="ff5db-111">L’exemple d’application utilise [Simple Injector](https://github.com/simpleinjector/SimpleInjector) seulement à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="ff5db-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="ff5db-112">Sa simple utilisation ne constitue pas une publicité pour ce produit.</span><span class="sxs-lookup"><span data-stu-id="ff5db-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="ff5db-113">Les approches de l’activation des middlewares décrites dans la documentation de Simple Injector et les problèmes GitHub sont recommandées par les développeurs de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="ff5db-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="ff5db-114">Pour plus d’informations, consultez la [documentation de Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) et le [dépôt GitHub de Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="ff5db-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="ff5db-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="ff5db-115">IMiddlewareFactory</span></span>

<span data-ttu-id="ff5db-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fournit des méthodes pour créer un middleware.</span><span class="sxs-lookup"><span data-stu-id="ff5db-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="ff5db-117">Dans l’exemple d’application, une fabrique de middleware est implémentée pour créer une instance de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="ff5db-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="ff5db-118">La fabrique de middleware utilise le conteneur Simple Injector pour résoudre le middleware :</span><span class="sxs-lookup"><span data-stu-id="ff5db-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="ff5db-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="ff5db-119">IMiddleware</span></span>

<span data-ttu-id="ff5db-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> définit le middleware pour le pipeline des requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="ff5db-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="ff5db-121">Middleware activé par une implémentation de `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.css*) :</span><span class="sxs-lookup"><span data-stu-id="ff5db-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="ff5db-122">Une extension est créée pour le middleware (*Middleware/MiddlewareExtensions.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ff5db-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="ff5db-123">`Startup.ConfigureServices` doit effectuer plusieurs tâches :</span><span class="sxs-lookup"><span data-stu-id="ff5db-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="ff5db-124">Configurer le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="ff5db-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="ff5db-125">Inscrire la fabrique et le middleware.</span><span class="sxs-lookup"><span data-stu-id="ff5db-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="ff5db-126">Rendez le contexte de base de données de l’application disponible depuis le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="ff5db-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ff5db-127">Le middleware est inscrit dans le pipeline de traitement des requêtes, dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="ff5db-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff5db-128">Cet article montre comment utiliser <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> et <xref:Microsoft.AspNetCore.Http.IMiddleware> comme point d’extensibilité pour l’activation d’un [middleware](xref:fundamentals/middleware/index) avec un conteneur tiers.</span><span class="sxs-lookup"><span data-stu-id="ff5db-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="ff5db-129">Pour obtenir des informations sur `IMiddlewareFactory` et `IMiddleware`, consultez <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="ff5db-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="ff5db-130">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff5db-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ff5db-131">L’exemple d’application montre l’activation d’un middleware par une implémentation de `IMiddlewareFactory`, `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="ff5db-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="ff5db-132">L’exemple utilise le conteneur d’injection de dépendances [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="ff5db-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="ff5db-133">L’implémentation du middleware de l’exemple enregistre la valeur fournie par un paramètre de la chaîne de requête (`key`).</span><span class="sxs-lookup"><span data-stu-id="ff5db-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="ff5db-134">Le middleware utilise un contexte de base de données injecté (un service délimité) pour enregistrer la valeur de la chaîne de requête dans une base de données en mémoire.</span><span class="sxs-lookup"><span data-stu-id="ff5db-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="ff5db-135">L’exemple d’application utilise [Simple Injector](https://github.com/simpleinjector/SimpleInjector) seulement à des fins de démonstration.</span><span class="sxs-lookup"><span data-stu-id="ff5db-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="ff5db-136">Sa simple utilisation ne constitue pas une publicité pour ce produit.</span><span class="sxs-lookup"><span data-stu-id="ff5db-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="ff5db-137">Les approches de l’activation des middlewares décrites dans la documentation de Simple Injector et les problèmes GitHub sont recommandées par les développeurs de Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="ff5db-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="ff5db-138">Pour plus d’informations, consultez la [documentation de Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) et le [dépôt GitHub de Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="ff5db-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="ff5db-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="ff5db-139">IMiddlewareFactory</span></span>

<span data-ttu-id="ff5db-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> fournit des méthodes pour créer un middleware.</span><span class="sxs-lookup"><span data-stu-id="ff5db-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="ff5db-141">Dans l’exemple d’application, une fabrique de middleware est implémentée pour créer une instance de `SimpleInjectorActivatedMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="ff5db-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="ff5db-142">La fabrique de middleware utilise le conteneur Simple Injector pour résoudre le middleware :</span><span class="sxs-lookup"><span data-stu-id="ff5db-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="ff5db-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="ff5db-143">IMiddleware</span></span>

<span data-ttu-id="ff5db-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> définit le middleware pour le pipeline des requêtes de l’application.</span><span class="sxs-lookup"><span data-stu-id="ff5db-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="ff5db-145">Middleware activé par une implémentation de `IMiddlewareFactory` (*Middleware/SimpleInjectorActivatedMiddleware.css*) :</span><span class="sxs-lookup"><span data-stu-id="ff5db-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="ff5db-146">Une extension est créée pour le middleware (*Middleware/MiddlewareExtensions.cs*) :</span><span class="sxs-lookup"><span data-stu-id="ff5db-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="ff5db-147">`Startup.ConfigureServices` doit effectuer plusieurs tâches :</span><span class="sxs-lookup"><span data-stu-id="ff5db-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="ff5db-148">Configurer le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="ff5db-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="ff5db-149">Inscrire la fabrique et le middleware.</span><span class="sxs-lookup"><span data-stu-id="ff5db-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="ff5db-150">Rendez le contexte de base de données de l’application disponible depuis le conteneur Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="ff5db-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ff5db-151">Le middleware est inscrit dans le pipeline de traitement des requêtes, dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="ff5db-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ff5db-152">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="ff5db-152">Additional resources</span></span>

* [<span data-ttu-id="ff5db-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="ff5db-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ff5db-154">Activation d’intergiciel (middleware) basée sur une fabrique</span><span class="sxs-lookup"><span data-stu-id="ff5db-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="ff5db-155">Dépôt GitHub de Simple Injector</span><span class="sxs-lookup"><span data-stu-id="ff5db-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="ff5db-156">Documentation de Simple Injector</span><span class="sxs-lookup"><span data-stu-id="ff5db-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
