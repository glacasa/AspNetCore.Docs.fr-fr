---
title: Présentation d’ASP.NET Core
author: rick-anderson
description: Obtenez une introduction à ASP.NET Core, un cadre interplateforme, haute performance et open source pour la construction d’applications modernes, connectées au cloud et connectées à Internet.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615945"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="b6822-103">Présentation d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6822-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="b6822-104">Par [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="b6822-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b6822-105">ASP.NET Core est un cadre interplateforme, haute performance et [open source](https://github.com/dotnet/aspnetcore) pour la construction d’applications modernes, connectées au cloud et connectées à Internet.</span><span class="sxs-lookup"><span data-stu-id="b6822-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="b6822-106">Avec ASP.NET Core, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="b6822-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="b6822-107">Construisez des applications et des services Web, des applications [Internet des objets (IoT)](https://www.microsoft.com/internet-of-things/) et des backends mobiles.</span><span class="sxs-lookup"><span data-stu-id="b6822-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="b6822-108">Utiliser vos outils de développement préférés sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="b6822-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="b6822-109">Déployer dans le cloud ou localement.</span><span class="sxs-lookup"><span data-stu-id="b6822-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="b6822-110">Exécuter sur [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="b6822-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="b6822-111">Pourquoi utiliser ASP.NET Core ?</span><span class="sxs-lookup"><span data-stu-id="b6822-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="b6822-112">Des millions de développeurs utilisent ou ont utilisé [ASP.NET 4.x](/aspnet/overview) pour créer des applications Web.</span><span class="sxs-lookup"><span data-stu-id="b6822-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="b6822-113">ASP.NET Core est une refonte de ASP.NET 4.x, y compris les changements architecturaux qui se traduisent par un cadre plus maigre, plus modulaire.</span><span class="sxs-lookup"><span data-stu-id="b6822-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="b6822-114">Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6822-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="b6822-115">Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :</span><span class="sxs-lookup"><span data-stu-id="b6822-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="b6822-116">Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.</span><span class="sxs-lookup"><span data-stu-id="b6822-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="b6822-117">[Razor Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui facilite et rend la construction d’interface utilisateur Web plus facile et plus productive.</span><span class="sxs-lookup"><span data-stu-id="b6822-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="b6822-118">Le [balisage Razor](xref:mvc/views/razor) fournit une syntaxe efficace pour les [pages Razor](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="b6822-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="b6822-119">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="b6822-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="b6822-120">La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="b6822-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="b6822-121">Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="b6822-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="b6822-122">La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="b6822-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="b6822-123">Développement côté client</span><span class="sxs-lookup"><span data-stu-id="b6822-123">Client-side development</span></span>

<span data-ttu-id="b6822-124">ASP.NET Core s’intègre parfaitement avec les frameworks et les bibliothèques populaires côté client, notamment [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) et [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="b6822-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="b6822-125">Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client*.</span><span class="sxs-lookup"><span data-stu-id="b6822-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="b6822-126">cadres cibles ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6822-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="b6822-127">ASP.NET Core 3.x et plus tard ne peut cibler .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6822-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="b6822-128">En général, ASP.NET Core est composé de bibliothèques [.NET Standard.](/dotnet/standard/net-standard)</span><span class="sxs-lookup"><span data-stu-id="b6822-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="b6822-129">Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="b6822-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="b6822-130">Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version.</span><span class="sxs-lookup"><span data-stu-id="b6822-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="b6822-131">Voici certains avantages de .NET Core par rapport à .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="b6822-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="b6822-132">Multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="b6822-132">Cross-platform.</span></span> <span data-ttu-id="b6822-133">S’exécute sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="b6822-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="b6822-134">performances améliorées</span><span class="sxs-lookup"><span data-stu-id="b6822-134">Improved performance</span></span>
* [<span data-ttu-id="b6822-135">Version côte à côte</span><span class="sxs-lookup"><span data-stu-id="b6822-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="b6822-136">Nouvelles API</span><span class="sxs-lookup"><span data-stu-id="b6822-136">New APIs</span></span>
* <span data-ttu-id="b6822-137">Open source</span><span class="sxs-lookup"><span data-stu-id="b6822-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="b6822-138">Parcours d’apprentissage recommandé</span><span class="sxs-lookup"><span data-stu-id="b6822-138">Recommended learning path</span></span>

<span data-ttu-id="b6822-139">Nous recommandons la séquence suivante de tutoriels pour une introduction au développement d’applications ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b6822-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="b6822-140">Suivez un tutoriel pour le type d’application que vous souhaitez développer ou maintenir.</span><span class="sxs-lookup"><span data-stu-id="b6822-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="b6822-141">Type d’application</span><span class="sxs-lookup"><span data-stu-id="b6822-141">App type</span></span>  |<span data-ttu-id="b6822-142">Scénario</span><span class="sxs-lookup"><span data-stu-id="b6822-142">Scenario</span></span>  |<span data-ttu-id="b6822-143">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="b6822-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="b6822-144">Application web</span><span class="sxs-lookup"><span data-stu-id="b6822-144">Web app</span></span>                   | <span data-ttu-id="b6822-145">Nouveau développement d’interface utilisateur web côté serveur</span><span class="sxs-lookup"><span data-stu-id="b6822-145">New server-side web UI development</span></span> |[<span data-ttu-id="b6822-146">Bien démarrer avec les pages Razor</span><span class="sxs-lookup"><span data-stu-id="b6822-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="b6822-147">Application web</span><span class="sxs-lookup"><span data-stu-id="b6822-147">Web app</span></span>                   | <span data-ttu-id="b6822-148">Maintenir une application MVC</span><span class="sxs-lookup"><span data-stu-id="b6822-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="b6822-149">Bien démarrer avec MVC</span><span class="sxs-lookup"><span data-stu-id="b6822-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="b6822-150">Application web</span><span class="sxs-lookup"><span data-stu-id="b6822-150">Web app</span></span>                   | <span data-ttu-id="b6822-151">Développement de l’interface utilisateur Web côté client</span><span class="sxs-lookup"><span data-stu-id="b6822-151">Client-side web UI development</span></span> |[<span data-ttu-id="b6822-152">Commence avec Blazor</span><span class="sxs-lookup"><span data-stu-id="b6822-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="b6822-153">API web</span><span class="sxs-lookup"><span data-stu-id="b6822-153">Web API</span></span>                   | <span data-ttu-id="b6822-154">Services HTTP RESTful</span><span class="sxs-lookup"><span data-stu-id="b6822-154">RESTful HTTP services</span></span> |<span data-ttu-id="b6822-155">[Créer une API web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="b6822-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="b6822-156">Application d’appel de procédure à distance</span><span class="sxs-lookup"><span data-stu-id="b6822-156">Remote Procedure Call app</span></span> | <span data-ttu-id="b6822-157">Services de premier contrat utilisant Protocol Buffers</span><span class="sxs-lookup"><span data-stu-id="b6822-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="b6822-158">Bien démarrer avec un service gRPC</span><span class="sxs-lookup"><span data-stu-id="b6822-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="b6822-159">Application en temps réel</span><span class="sxs-lookup"><span data-stu-id="b6822-159">Real-time app</span></span>             | <span data-ttu-id="b6822-160">Communication bidirectionnelle entre serveurs et clients connectés</span><span class="sxs-lookup"><span data-stu-id="b6822-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="b6822-161">Bien démarrer avec SignalR</span><span class="sxs-lookup"><span data-stu-id="b6822-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="b6822-162">Suivez un tutoriel qui montre comment faire l’accès aux données de base.</span><span class="sxs-lookup"><span data-stu-id="b6822-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="b6822-163">Scénario</span><span class="sxs-lookup"><span data-stu-id="b6822-163">Scenario</span></span>  |<span data-ttu-id="b6822-164">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="b6822-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="b6822-165">Nouveau développement</span><span class="sxs-lookup"><span data-stu-id="b6822-165">New development</span></span>        |[<span data-ttu-id="b6822-166">Pages Razor avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b6822-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="b6822-167">Maintenir une application MVC</span><span class="sxs-lookup"><span data-stu-id="b6822-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="b6822-168">MVC avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b6822-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="b6822-169">Lisez un aperçu des [principes fondamentaux](xref:fundamentals/index) ASP.NET Core qui s’appliquent à tous les types d’applications.</span><span class="sxs-lookup"><span data-stu-id="b6822-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="b6822-170">Parcourez la table du contenu pour d’autres sujets d’intérêt.</span><span class="sxs-lookup"><span data-stu-id="b6822-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="b6822-171">&dagger;Il ya aussi un [tutoriel interactif aPI web](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="b6822-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="b6822-172">Aucune installation locale d’outils de développement n’est requise.</span><span class="sxs-lookup"><span data-stu-id="b6822-172">No local installation of development tools is required.</span></span> <span data-ttu-id="b6822-173">Le code s’exécute dans une [coquille Cloud Azure](https://azure.microsoft.com/features/cloud-shell/) dans votre navigateur, et [curl](https://curl.haxx.se/) est utilisé pour les tests.</span><span class="sxs-lookup"><span data-stu-id="b6822-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="b6822-174">Migrer à partir de .NET Framework</span><span class="sxs-lookup"><span data-stu-id="b6822-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="b6822-175">Pour un guide de référence sur la migration ASP.NET applications 4.x <xref:migration/proper-to-2x/index>pour ASP.NET Core, voir .</span><span class="sxs-lookup"><span data-stu-id="b6822-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b6822-176">ASP.NET Core est un cadre interplateforme, haute performance et [open source](https://github.com/dotnet/aspnetcore) pour la construction d’applications modernes, connectées au cloud et connectées à Internet.</span><span class="sxs-lookup"><span data-stu-id="b6822-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="b6822-177">Avec ASP.NET Core, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="b6822-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="b6822-178">Construisez des applications et des services Web, des applications [Internet des objets (IoT)](https://www.microsoft.com/internet-of-things/) et des backends mobiles.</span><span class="sxs-lookup"><span data-stu-id="b6822-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="b6822-179">Utiliser vos outils de développement préférés sur Windows, macOS et Linux.</span><span class="sxs-lookup"><span data-stu-id="b6822-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="b6822-180">Déployer dans le cloud ou localement.</span><span class="sxs-lookup"><span data-stu-id="b6822-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="b6822-181">Exécuter sur [.NET Core ou .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="b6822-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="b6822-182">Pourquoi utiliser ASP.NET Core ?</span><span class="sxs-lookup"><span data-stu-id="b6822-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="b6822-183">Des millions de développeurs utilisent ou ont utilisé [ASP.NET 4.x](/aspnet/overview) pour créer des applications Web.</span><span class="sxs-lookup"><span data-stu-id="b6822-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="b6822-184">ASP.NET Core est une refonte d’ASP.NET 4.x, avec des modifications d’architecture qui aboutissent à un framework plus léger et modulaire.</span><span class="sxs-lookup"><span data-stu-id="b6822-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="b6822-185">Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6822-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="b6822-186">Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :</span><span class="sxs-lookup"><span data-stu-id="b6822-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="b6822-187">Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.</span><span class="sxs-lookup"><span data-stu-id="b6822-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="b6822-188">[Razor Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui facilite et rend la construction d’interface utilisateur Web plus facile et plus productive.</span><span class="sxs-lookup"><span data-stu-id="b6822-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="b6822-189">Le [balisage Razor](xref:mvc/views/razor) fournit une syntaxe efficace pour les [pages Razor](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="b6822-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="b6822-190">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="b6822-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="b6822-191">La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.</span><span class="sxs-lookup"><span data-stu-id="b6822-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="b6822-192">Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="b6822-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="b6822-193">La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.</span><span class="sxs-lookup"><span data-stu-id="b6822-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="b6822-194">Développement côté client</span><span class="sxs-lookup"><span data-stu-id="b6822-194">Client-side development</span></span>

<span data-ttu-id="b6822-195">ASP.NET Core s’intègre parfaitement avec les frameworks et les bibliothèques populaires côté client, notamment [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) et [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="b6822-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="b6822-196">Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client*.</span><span class="sxs-lookup"><span data-stu-id="b6822-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="b6822-197">ASP.NET Core ciblant .NET Framework</span><span class="sxs-lookup"><span data-stu-id="b6822-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="b6822-198">ASP.NET Core 2.x peut cibler .NET Core ou le .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b6822-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="b6822-199">Les applications ASP.NET Core ciblant .NET Framework ne sont pas multiplateformes : elles s’exécutent seulement sur Windows.</span><span class="sxs-lookup"><span data-stu-id="b6822-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="b6822-200">D’une façon générale, ASP.NET Core 2.x est constitué de bibliothèques [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="b6822-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="b6822-201">Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="b6822-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="b6822-202">ASP.NET Core 2.x est pris en charge sur les versions .NET Framework compatibles avec .NET Standard 2.0 :</span><span class="sxs-lookup"><span data-stu-id="b6822-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="b6822-203">.NET Framework dernière version est recommandée.</span><span class="sxs-lookup"><span data-stu-id="b6822-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="b6822-204">.NET Framework 4.6.1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="b6822-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="b6822-205">ASP.NET Core 3.0 et ultérieur s’exécute uniquement sur .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6822-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="b6822-206">Pour plus de détails concernant ce changement, consultez [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="b6822-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="b6822-207">Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version.</span><span class="sxs-lookup"><span data-stu-id="b6822-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="b6822-208">Voici certains avantages de .NET Core par rapport à .NET Framework :</span><span class="sxs-lookup"><span data-stu-id="b6822-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="b6822-209">Multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="b6822-209">Cross-platform.</span></span> <span data-ttu-id="b6822-210">S’exécute sur macOS, Linux et Windows</span><span class="sxs-lookup"><span data-stu-id="b6822-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="b6822-211">performances améliorées</span><span class="sxs-lookup"><span data-stu-id="b6822-211">Improved performance</span></span>
* [<span data-ttu-id="b6822-212">Version côte à côte</span><span class="sxs-lookup"><span data-stu-id="b6822-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="b6822-213">Nouvelles API</span><span class="sxs-lookup"><span data-stu-id="b6822-213">New APIs</span></span>
* <span data-ttu-id="b6822-214">Open source</span><span class="sxs-lookup"><span data-stu-id="b6822-214">Open source</span></span>

<span data-ttu-id="b6822-215">Nous nous efforçons de combler l’écart d’API qui existe entre .NET Framework et .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b6822-215">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="b6822-216">Le [Pack de compatibilité Windows](/dotnet/core/porting/windows-compat-pack) a rendu disponible dans .NET Core des milliers d’API fonctionnant seulement dans Windows.</span><span class="sxs-lookup"><span data-stu-id="b6822-216">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="b6822-217">Ces API n’étaient pas disponibles dans .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="b6822-217">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="b6822-218">Parcours d’apprentissage recommandé</span><span class="sxs-lookup"><span data-stu-id="b6822-218">Recommended learning path</span></span>

<span data-ttu-id="b6822-219">Nous vous recommandons la séquence de tutoriels et d’articles suivante comme introduction au développement des applications ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="b6822-219">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="b6822-220">Suivez un tutoriel pour le type d’application que vous souhaitez développer ou maintenir.</span><span class="sxs-lookup"><span data-stu-id="b6822-220">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="b6822-221">Type d’application</span><span class="sxs-lookup"><span data-stu-id="b6822-221">App type</span></span>  |<span data-ttu-id="b6822-222">Scénario</span><span class="sxs-lookup"><span data-stu-id="b6822-222">Scenario</span></span>  |<span data-ttu-id="b6822-223">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="b6822-223">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="b6822-224">Application web</span><span class="sxs-lookup"><span data-stu-id="b6822-224">Web app</span></span>                   | <span data-ttu-id="b6822-225">Pour un nouveau développement</span><span class="sxs-lookup"><span data-stu-id="b6822-225">For new development</span></span>        |[<span data-ttu-id="b6822-226">Bien démarrer avec les pages Razor</span><span class="sxs-lookup"><span data-stu-id="b6822-226">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="b6822-227">Application web</span><span class="sxs-lookup"><span data-stu-id="b6822-227">Web app</span></span>                   | <span data-ttu-id="b6822-228">Pour maintenir une application MVC</span><span class="sxs-lookup"><span data-stu-id="b6822-228">For maintaining an MVC app</span></span> |[<span data-ttu-id="b6822-229">Bien démarrer avec MVC</span><span class="sxs-lookup"><span data-stu-id="b6822-229">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="b6822-230">API web</span><span class="sxs-lookup"><span data-stu-id="b6822-230">Web API</span></span>                   |                            |<span data-ttu-id="b6822-231">[Créer une API web](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="b6822-231">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="b6822-232">Application en temps réel</span><span class="sxs-lookup"><span data-stu-id="b6822-232">Real-time app</span></span>             |                            |[<span data-ttu-id="b6822-233">Bien démarrer avec SignalR</span><span class="sxs-lookup"><span data-stu-id="b6822-233">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="b6822-234">Suivez un tutoriel qui montre comment faire l’accès aux données de base.</span><span class="sxs-lookup"><span data-stu-id="b6822-234">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="b6822-235">Scénario</span><span class="sxs-lookup"><span data-stu-id="b6822-235">Scenario</span></span>  |<span data-ttu-id="b6822-236">Didacticiel</span><span class="sxs-lookup"><span data-stu-id="b6822-236">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="b6822-237">Pour un nouveau développement</span><span class="sxs-lookup"><span data-stu-id="b6822-237">For new development</span></span>        |[<span data-ttu-id="b6822-238">Pages Razor avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b6822-238">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="b6822-239">Pour maintenir une application MVC</span><span class="sxs-lookup"><span data-stu-id="b6822-239">For maintaining an MVC app</span></span> |[<span data-ttu-id="b6822-240">MVC avec Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b6822-240">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="b6822-241">Lisez un aperçu des [principes fondamentaux](xref:fundamentals/index) ASP.NET Core qui s’appliquent à tous les types d’applications.</span><span class="sxs-lookup"><span data-stu-id="b6822-241">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="b6822-242">Parcourez la Table des matières pour d’autres rubriques qui vous intéressent.</span><span class="sxs-lookup"><span data-stu-id="b6822-242">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="b6822-243">&dagger;Il ya aussi un [tutoriel API web que vous suivez entièrement dans le navigateur](/learn/modules/build-web-api-net-core), aucune installation locale IDE nécessaire.</span><span class="sxs-lookup"><span data-stu-id="b6822-243">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="b6822-244">Le code s’exécute dans [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), et [curl](https://curl.haxx.se/) est utilisé à des fins de test.</span><span class="sxs-lookup"><span data-stu-id="b6822-244">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="b6822-245">Migrer à partir de .NET Framework</span><span class="sxs-lookup"><span data-stu-id="b6822-245">Migrate from .NET Framework</span></span>

<span data-ttu-id="b6822-246">Pour un guide de référence sur la migration des applications ASP.NET <xref:migration/proper-to-2x/index>pour ASP.NET Core, voir .</span><span class="sxs-lookup"><span data-stu-id="b6822-246">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="b6822-247">Comment télécharger un exemple</span><span class="sxs-lookup"><span data-stu-id="b6822-247">How to download a sample</span></span>

<span data-ttu-id="b6822-248">La plupart des articles et tutoriels contiennent des liens vers des exemples de code.</span><span class="sxs-lookup"><span data-stu-id="b6822-248">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="b6822-249">[Téléchargez le fichier zip du référentiel ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="b6822-249">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="b6822-250">Décompressez le fichier *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="b6822-250">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="b6822-251">Utilisez l’URL contenue dans l’exemple de lien pour vous aider à naviguer dans l’exemple de répertoire.</span><span class="sxs-lookup"><span data-stu-id="b6822-251">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="b6822-252">Directives de préprocesseur dans l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="b6822-252">Preprocessor directives in sample code</span></span>

<span data-ttu-id="b6822-253">Pour démontrer plusieurs scénarios, les `#define` `#if-#else/#elif-#endif` applications d’échantillons utilisent les directives et les préprocesseurs pour compiler sélectivement et exécuter différentes sections de code d’échantillon.</span><span class="sxs-lookup"><span data-stu-id="b6822-253">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="b6822-254">Pour les échantillons qui utilisent cette `#define` approche, définissez la directive en haut des fichiers C pour définir le symbole associé au scénario que vous souhaitez exécuter.</span><span class="sxs-lookup"><span data-stu-id="b6822-254">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="b6822-255">Certains échantillons nécessitent la définition du symbole en haut de plusieurs fichiers afin d’exécuter un scénario.</span><span class="sxs-lookup"><span data-stu-id="b6822-255">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="b6822-256">Par exemple, la liste des symboles `#define` suivante indique que les quatre scénarios sont disponibles (un scénario par symbole).</span><span class="sxs-lookup"><span data-stu-id="b6822-256">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="b6822-257">La configuration actuelle de l’exemple exécute le scénario `TemplateCode` :</span><span class="sxs-lookup"><span data-stu-id="b6822-257">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="b6822-258">Pour que l’exemple exécute le scénario `ExpandDefault`, définissez le symbole `ExpandDefault` et laissez les symboles restants commentés :</span><span class="sxs-lookup"><span data-stu-id="b6822-258">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="b6822-259">Pour plus d’informations sur l’utilisation des [directives de préprocesseur C#](/dotnet/csharp/language-reference/preprocessor-directives/) pour compiler de façon sélective des sections de code, consultez [#define (Référence C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) et [#if (Référence C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="b6822-259">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="b6822-260">Régions dans l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="b6822-260">Regions in sample code</span></span>

<span data-ttu-id="b6822-261">Certains exemples d’applications contiennent des sections de code entourées [de #region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) et [de #endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) directives C.</span><span class="sxs-lookup"><span data-stu-id="b6822-261">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="b6822-262">Le système de génération de documentation injecte ces régions dans les rubriques de documentation affichées.</span><span class="sxs-lookup"><span data-stu-id="b6822-262">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="b6822-263">Les noms des régions contiennent généralement le mot « snippet ».</span><span class="sxs-lookup"><span data-stu-id="b6822-263">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="b6822-264">L’exemple suivant montre une région nommée `snippet_WebHostDefaults` :</span><span class="sxs-lookup"><span data-stu-id="b6822-264">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="b6822-265">L’extrait de code C# précédent est référencé dans le fichier Markdown de la rubrique avec la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="b6822-265">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="b6822-266">Vous pouvez ignorer (ou supprimer) en toute sécurité les `#region` directives et `#endregion` directives qui entourent le code.</span><span class="sxs-lookup"><span data-stu-id="b6822-266">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="b6822-267">Ne modifiez pas le code dans ces directives si vous prévoyez d’exécuter les scénarios d’exemple décrits dans le sujet.</span><span class="sxs-lookup"><span data-stu-id="b6822-267">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="b6822-268">N’hésitez pas à modifier le code quand vous testez d’autres scénarios.</span><span class="sxs-lookup"><span data-stu-id="b6822-268">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="b6822-269">Pour plus d’informations, consultez [Contribuer à la documentation ASP.NET : extraits de code](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="b6822-269">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b6822-270">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="b6822-270">Next steps</span></span>

<span data-ttu-id="b6822-271">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="b6822-271">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="b6822-272">Notions de base d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b6822-272">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="b6822-273">[Le point hebdomadaire de la communauté ASP.NET](https://live.asp.net/) couvre l’avancement et les plans des équipes de développement.</span><span class="sxs-lookup"><span data-stu-id="b6822-273">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="b6822-274">Il met en avant de nouveaux blogs et des logiciels de tiers.</span><span class="sxs-lookup"><span data-stu-id="b6822-274">It features new blogs and third-party software.</span></span>
