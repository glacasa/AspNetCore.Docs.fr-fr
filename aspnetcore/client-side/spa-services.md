---
title: Utilisez les services JavaScript pour créer des applications à une page unique dans ASP.NET Core
author: scottaddie
description: Renseignez-vous sur les avantages de l’utilisation de JavaScript Services pour créer une application à une page unique (SPA) soutenue par ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: c0c73882afd579510ad9cdf5b485c1d6fbeadd1c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663778"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="21024-103">Utilisez les services JavaScript pour créer des applications à une page unique dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="21024-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="21024-104">Par [Scott Addie](https://github.com/scottaddie) et [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="21024-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="21024-105">Une application à une page unique (SPA) est un type populaire d’application web en raison de sa riche expérience utilisateur inhérente.</span><span class="sxs-lookup"><span data-stu-id="21024-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="21024-106">Il peut être difficile d’intégrer des cadres SPA ou des bibliothèques du côté client, tels que [Angular](https://angular.io/) ou [React,](https://facebook.github.io/react/)avec des cadres côté serveur tels que ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21024-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="21024-107">JavaScript Services a été développé pour réduire les frictions dans le processus d’intégration.</span><span class="sxs-lookup"><span data-stu-id="21024-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="21024-108">Il permet un fonctionnement sans faille entre les différentes piles de technologie client et serveur.</span><span class="sxs-lookup"><span data-stu-id="21024-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="21024-109">Les caractéristiques décrites dans cet article sont obsolètes à partir de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="21024-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="21024-110">Un mécanisme d’intégration de cadres SPA plus simple est disponible dans le package [Microsoft.AspNetCore.SpaServices.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet.</span><span class="sxs-lookup"><span data-stu-id="21024-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="21024-111">Pour plus d’informations, voir [[Annonce] Obsoleting Microsoft.AspNetCore.SpaServices et Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span><span class="sxs-lookup"><span data-stu-id="21024-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="21024-112">Qu’est-ce que JavaScript Services</span><span class="sxs-lookup"><span data-stu-id="21024-112">What is JavaScript Services</span></span>

<span data-ttu-id="21024-113">JavaScript Services est une collection de technologies côté client pour ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="21024-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="21024-114">Son objectif est de positionner ASP.NET Core en tant que plate-forme préférée des développeurs côté serveur pour la construction des SPA.</span><span class="sxs-lookup"><span data-stu-id="21024-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="21024-115">JavaScript Services se compose de deux forfaits NuGet distincts :</span><span class="sxs-lookup"><span data-stu-id="21024-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="21024-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="21024-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="21024-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="21024-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="21024-118">Ces paquets sont utiles dans les scénarios suivants :</span><span class="sxs-lookup"><span data-stu-id="21024-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="21024-119">Exécuter JavaScript sur le serveur</span><span class="sxs-lookup"><span data-stu-id="21024-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="21024-120">Utiliser un cadre OU une bibliothèque SPA</span><span class="sxs-lookup"><span data-stu-id="21024-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="21024-121">Construire des actifs côté client avec Webpack</span><span class="sxs-lookup"><span data-stu-id="21024-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="21024-122">Une grande partie de l’accent dans cet article est placé sur l’utilisation du paquet SpaServices.</span><span class="sxs-lookup"><span data-stu-id="21024-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="21024-123">Qu’est-ce que SpaServices</span><span class="sxs-lookup"><span data-stu-id="21024-123">What is SpaServices</span></span>

<span data-ttu-id="21024-124">SpaServices a été créé pour positionner ASP.NET Core en tant que plate-forme préférée des développeurs côté serveur pour la construction de SPAs.</span><span class="sxs-lookup"><span data-stu-id="21024-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="21024-125">SpaServices n’est pas tenu de développer des SPA avec ASP.NET Core, et il ne verrouille pas les développeurs dans un cadre client particulier.</span><span class="sxs-lookup"><span data-stu-id="21024-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="21024-126">SpaServices fournit une infrastructure utile telle que :</span><span class="sxs-lookup"><span data-stu-id="21024-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="21024-127">Prerendering côté serveur</span><span class="sxs-lookup"><span data-stu-id="21024-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="21024-128">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="21024-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="21024-129">Remplacement du module chaud</span><span class="sxs-lookup"><span data-stu-id="21024-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="21024-130">Aide à l’itinéraire</span><span class="sxs-lookup"><span data-stu-id="21024-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="21024-131">Collectivement, ces composantes de l’infrastructure améliorent à la fois le flux de travail de développement et l’expérience de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="21024-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="21024-132">Les composants peuvent être adoptés individuellement.</span><span class="sxs-lookup"><span data-stu-id="21024-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="21024-133">Conditions préalables pour l’utilisation de SpaServices</span><span class="sxs-lookup"><span data-stu-id="21024-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="21024-134">Pour travailler avec SpaServices, installez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="21024-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="21024-135">[Node.js](https://nodejs.org/) (version 6 ou plus tard) avec npm</span><span class="sxs-lookup"><span data-stu-id="21024-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="21024-136">Pour vérifier que ces composants sont installés et peuvent être trouvés, exécutez ce qui suit à partir de la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="21024-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="21024-137">Si vous êtes déployé sur un site&mdash;Web Azure, aucune action n’est requise Node.js est installé et disponible dans les environnements du serveur.</span><span class="sxs-lookup"><span data-stu-id="21024-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="21024-138">Sur Windows à l’aide de Visual Studio 2017, le SDK est installé en sélectionnant la charge de travail **de développement multiplateforme .NET Core.**</span><span class="sxs-lookup"><span data-stu-id="21024-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="21024-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span><span class="sxs-lookup"><span data-stu-id="21024-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="21024-140">Prerendering côté serveur</span><span class="sxs-lookup"><span data-stu-id="21024-140">Server-side prerendering</span></span>

<span data-ttu-id="21024-141">Une application universelle (également connue sous le nom d’isomorphe) est une application JavaScript capable de s’exécuter à la fois sur le serveur et le client.</span><span class="sxs-lookup"><span data-stu-id="21024-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="21024-142">Les cadres angulaires, réactifs et autres cadres populaires constituent une plate-forme universelle pour ce style de développement d’applications.</span><span class="sxs-lookup"><span data-stu-id="21024-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="21024-143">L’idée est d’abord de rendre les composants-cadres sur le serveur via Node.js, puis de déléguer une nouvelle exécution au client.</span><span class="sxs-lookup"><span data-stu-id="21024-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="21024-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) fournis par SpaServices simplifient la mise en œuvre du prerendering côté serveur en invoquant les fonctions JavaScript sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="21024-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="21024-145">Prérequis de prerendering côté serveur</span><span class="sxs-lookup"><span data-stu-id="21024-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="21024-146">Installer le paquet [npm aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) :</span><span class="sxs-lookup"><span data-stu-id="21024-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="21024-147">Configuration de prerendering côté serveur</span><span class="sxs-lookup"><span data-stu-id="21024-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="21024-148">Les Tag Helpers sont rendus détectables via l’enregistrement de namespace dans le fichier *_ViewImports.cshtml* du projet :</span><span class="sxs-lookup"><span data-stu-id="21024-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="21024-149">Ces Tag Helpers résument les subtilités de communiquer directement avec les API de bas niveau en tirant parti d’une syntaxe HTML-like à l’intérieur de la vue Razor:</span><span class="sxs-lookup"><span data-stu-id="21024-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="21024-150">asp-prerender-module Tag Helper</span><span class="sxs-lookup"><span data-stu-id="21024-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="21024-151">L’aide `asp-prerender-module` Tag, utilisée dans l’exemple de code précédent, exécute *ClientApp/dist/main-server.js* sur le serveur via Node.js.</span><span class="sxs-lookup"><span data-stu-id="21024-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="21024-152">Pour plus de clarté, le fichier *main-server.js* est un artefact de la tâche de transpilation TypeScript-to-JavaScript dans le processus de construction [Webpack.](https://webpack.github.io/)</span><span class="sxs-lookup"><span data-stu-id="21024-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="21024-153">Webpack définit un alias `main-server`point d’entrée de ; et, traversée du graphique de dépendance pour ce pseudonyme commence sur le *fichier ClientApp/boot-server.ts* :</span><span class="sxs-lookup"><span data-stu-id="21024-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="21024-154">Dans l’exemple angulaire suivant, le fichier *ClientApp/boot-server.ts* utilise la fonction et `createServerRenderer` `RenderResult` le type du paquet npm pour configurer le `aspnet-prerendering` rendu serveur via Node.js.</span><span class="sxs-lookup"><span data-stu-id="21024-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="21024-155">La balisage HTML destinée au rendu côté serveur est passée à un appel de `Promise` fonction de résolution, qui est enveloppé dans un objet JavaScript fortement typé.</span><span class="sxs-lookup"><span data-stu-id="21024-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="21024-156">L’importance `Promise` de l’objet est qu’il fournit asynchronement la marque HTML à la page pour l’injection dans l’élément de placeholder du DOM.</span><span class="sxs-lookup"><span data-stu-id="21024-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="21024-157">asp-prerender-data Tag Helper</span><span class="sxs-lookup"><span data-stu-id="21024-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="21024-158">Lorsqu’il `asp-prerender-module` est couplé avec `asp-prerender-data` l’aide Tag, l’aide Tag peut être utilisé pour passer des informations contextuelles de la vue Razor à la côté serveur JavaScript.</span><span class="sxs-lookup"><span data-stu-id="21024-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="21024-159">Par exemple, la balisage suivante `main-server` transmet les données utilisateur au module :</span><span class="sxs-lookup"><span data-stu-id="21024-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="21024-160">L’argument reçu `UserName` est sérialisé à l’aide du `params.data` sérialisateur JSON intégré et est stocké dans l’objet.</span><span class="sxs-lookup"><span data-stu-id="21024-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="21024-161">Dans l’exemple angulaire suivant, les données sont utilisées `h1` pour construire un message d’accueil personnalisé dans un élément :</span><span class="sxs-lookup"><span data-stu-id="21024-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="21024-162">Les noms de propriété passés dans Tag Helpers sont représentés avec la notation **PascalCase.**</span><span class="sxs-lookup"><span data-stu-id="21024-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="21024-163">Comparez cela à JavaScript, où les mêmes noms de propriété sont représentés avec **camelCase**.</span><span class="sxs-lookup"><span data-stu-id="21024-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="21024-164">La configuration de sérialisation JSON par défaut est responsable de cette différence.</span><span class="sxs-lookup"><span data-stu-id="21024-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="21024-165">Pour développer l’exemple de code précédent, les données peuvent être `globals` transmises `resolve` du serveur à la vue en hydratant la propriété fournie à la fonction :</span><span class="sxs-lookup"><span data-stu-id="21024-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="21024-166">Le `postList` tableau défini `globals` à l’intérieur de `window` l’objet est fixé à l’objet global du navigateur.</span><span class="sxs-lookup"><span data-stu-id="21024-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="21024-167">Ce levage variable à la portée globale élimine la duplication de l’effort, en particulier en ce qui concerne le chargement des mêmes données une fois sur le serveur et à nouveau sur le client.</span><span class="sxs-lookup"><span data-stu-id="21024-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![variable globale postList attachée à l’objet de fenêtre](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="21024-169">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="21024-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="21024-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduit un flux de travail de développement rationalisé par lequel Webpack construit des ressources sur demande.</span><span class="sxs-lookup"><span data-stu-id="21024-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="21024-171">Le middleware compile et sert automatiquement les ressources côté client lorsqu’une page est rechargée dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="21024-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="21024-172">L’autre approche consiste à invoquer manuellement Webpack via le script de construction npm du projet lorsqu’une dépendance de tiers ou le code personnalisé change.</span><span class="sxs-lookup"><span data-stu-id="21024-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="21024-173">Un script de construction npm dans le fichier *package.json* est montré dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="21024-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="21024-174">Webpack Dev Middleware préalables</span><span class="sxs-lookup"><span data-stu-id="21024-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="21024-175">Installez le forfait [npm aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) :</span><span class="sxs-lookup"><span data-stu-id="21024-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="21024-176">Configuration Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="21024-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="21024-177">Webpack Dev Middleware est enregistré dans le pipeline de demande HTTP via `Configure` le code suivant dans la méthode du fichier *Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="21024-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="21024-178">La `UseWebpackDevMiddleware` méthode d’extension doit être appelée `UseStaticFiles` avant [d’enregistrer l’hébergement statique de fichiers](xref:fundamentals/static-files) via la méthode d’extension.</span><span class="sxs-lookup"><span data-stu-id="21024-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="21024-179">Pour des raisons de sécurité, enregistrez le middleware uniquement lorsque l’application s’exécute en mode de développement.</span><span class="sxs-lookup"><span data-stu-id="21024-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="21024-180">La propriété du `output.publicPath` fichier *webpack.config.js* indique au `dist` middleware de regarder le dossier pour les changements :</span><span class="sxs-lookup"><span data-stu-id="21024-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="21024-181">Remplacement du module chaud</span><span class="sxs-lookup"><span data-stu-id="21024-181">Hot Module Replacement</span></span>

<span data-ttu-id="21024-182">Pensez à la fonction [de remplacement](https://webpack.js.org/concepts/hot-module-replacement/) du module chaud (HMR) de Webpack comme une évolution de [Webpack Dev Middleware](#webpack-dev-middleware).</span><span class="sxs-lookup"><span data-stu-id="21024-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="21024-183">HMR introduit tous les mêmes avantages, mais il rationalise davantage le flux de travail de développement en mettant automatiquement à jour le contenu de la page après la compilation des modifications.</span><span class="sxs-lookup"><span data-stu-id="21024-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="21024-184">Ne confondez pas cela avec un rafraîchissement du navigateur, ce qui interfèrerait avec l’état actuel de mémoire et de débogage session de la SPA.</span><span class="sxs-lookup"><span data-stu-id="21024-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="21024-185">Il ya un lien en direct entre le service Webpack Dev Middleware et le navigateur, ce qui signifie que les modifications sont poussés sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="21024-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="21024-186">Conditions préalables de remplacement du module chaud</span><span class="sxs-lookup"><span data-stu-id="21024-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="21024-187">Installez le forfait [npm webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) :</span><span class="sxs-lookup"><span data-stu-id="21024-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="21024-188">Configuration de remplacement du module chaud</span><span class="sxs-lookup"><span data-stu-id="21024-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="21024-189">La composante HMR doit être enregistrée dans le `Configure` pipeline de demandes HTTP de MVC dans la méthode :</span><span class="sxs-lookup"><span data-stu-id="21024-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="21024-190">Comme c’était le cas avec `UseWebpackDevMiddleware` [Webpack Dev Middleware](#webpack-dev-middleware), la méthode d’extension doit être appelée avant la méthode d’extension. `UseStaticFiles`</span><span class="sxs-lookup"><span data-stu-id="21024-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="21024-191">Pour des raisons de sécurité, enregistrez le middleware uniquement lorsque l’application s’exécute en mode de développement.</span><span class="sxs-lookup"><span data-stu-id="21024-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="21024-192">Le fichier *webpack.config.js* `plugins` doit définir un tableau, même s’il est laissé vide :</span><span class="sxs-lookup"><span data-stu-id="21024-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="21024-193">Après le chargement de l’application dans le navigateur, l’onglet Console des outils de développeur fournit la confirmation de l’activation HMR :</span><span class="sxs-lookup"><span data-stu-id="21024-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Message connecté de remplacement de module chaud](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="21024-195">Aide à l’itinéraire</span><span class="sxs-lookup"><span data-stu-id="21024-195">Routing helpers</span></span>

<span data-ttu-id="21024-196">Dans la plupart des ASP.NET les APE core, le routage côté client est souvent souhaité en plus du routage côté serveur.</span><span class="sxs-lookup"><span data-stu-id="21024-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="21024-197">Les systèmes de routage SPA et MVC peuvent fonctionner indépendamment sans interférence.</span><span class="sxs-lookup"><span data-stu-id="21024-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="21024-198">Il ya, cependant, un cas bord posant des défis: l’identification de 404 réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="21024-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="21024-199">Considérez le scénario dans lequel `/some/page` une voie sans extension de est utilisé.</span><span class="sxs-lookup"><span data-stu-id="21024-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="21024-200">Supposons que la demande ne correspond pas à un itinéraire côté serveur, mais son modèle correspond à un itinéraire côté client.</span><span class="sxs-lookup"><span data-stu-id="21024-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="21024-201">Maintenant, envisagez `/images/user-512.png`une demande entrante pour , qui s’attend généralement à trouver un fichier d’image sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="21024-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="21024-202">Si cette trajectoire de ressources demandée ne correspond à aucun itinéraire côté serveur ou fichier statique, il est peu probable que l’application côté client serait gérer généralement le retour d’un&mdash;code d’état HTTP 404 est souhaité.</span><span class="sxs-lookup"><span data-stu-id="21024-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="21024-203">Les secours de routage des aides préalables</span><span class="sxs-lookup"><span data-stu-id="21024-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="21024-204">Installez le paquet npm de routage côté client.</span><span class="sxs-lookup"><span data-stu-id="21024-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="21024-205">En utilisant Angular comme exemple :</span><span class="sxs-lookup"><span data-stu-id="21024-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="21024-206">Configuration d’aide de routage</span><span class="sxs-lookup"><span data-stu-id="21024-206">Routing helpers configuration</span></span>

<span data-ttu-id="21024-207">Une méthode `MapSpaFallbackRoute` d’extension `Configure` nommée est utilisée dans la méthode :</span><span class="sxs-lookup"><span data-stu-id="21024-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="21024-208">Les itinéraires sont évalués dans l’ordre dans lequel ils sont configurés.</span><span class="sxs-lookup"><span data-stu-id="21024-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="21024-209">Par conséquent, `default` l’itinéraire dans l’exemple de code précédent est utilisé en premier pour l’appariement des motifs.</span><span class="sxs-lookup"><span data-stu-id="21024-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="21024-210">Créer un projet</span><span class="sxs-lookup"><span data-stu-id="21024-210">Create a new project</span></span>

<span data-ttu-id="21024-211">JavaScript Services fournit des modèles d’application préconfigurés.</span><span class="sxs-lookup"><span data-stu-id="21024-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="21024-212">SpaServices est utilisé dans ces modèles en conjonction avec différents cadres et bibliothèques tels que Angular, React, et Redux.</span><span class="sxs-lookup"><span data-stu-id="21024-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="21024-213">Ces modèles peuvent être installés via le CLI .NET Core en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="21024-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="21024-214">Une liste des modèles SPA disponibles est affichée :</span><span class="sxs-lookup"><span data-stu-id="21024-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="21024-215">Modèles</span><span class="sxs-lookup"><span data-stu-id="21024-215">Templates</span></span>                                 | <span data-ttu-id="21024-216">Nom court</span><span class="sxs-lookup"><span data-stu-id="21024-216">Short Name</span></span> | <span data-ttu-id="21024-217">Langage</span><span class="sxs-lookup"><span data-stu-id="21024-217">Language</span></span> | <span data-ttu-id="21024-218">Balises</span><span class="sxs-lookup"><span data-stu-id="21024-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="21024-219">MVC ASP.NET Core avec Angular</span><span class="sxs-lookup"><span data-stu-id="21024-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="21024-220">angular</span><span class="sxs-lookup"><span data-stu-id="21024-220">angular</span></span>    | <span data-ttu-id="21024-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="21024-221">[C#]</span></span>     | <span data-ttu-id="21024-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="21024-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="21024-223">MVC ASP.NET Core avec React.js</span><span class="sxs-lookup"><span data-stu-id="21024-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="21024-224">react</span><span class="sxs-lookup"><span data-stu-id="21024-224">react</span></span>      | <span data-ttu-id="21024-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="21024-225">[C#]</span></span>     | <span data-ttu-id="21024-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="21024-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="21024-227">MVC ASP.NET Core avec React.js et Redux</span><span class="sxs-lookup"><span data-stu-id="21024-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="21024-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="21024-228">reactredux</span></span> | <span data-ttu-id="21024-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="21024-229">[C#]</span></span>     | <span data-ttu-id="21024-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="21024-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="21024-231">Pour créer un nouveau projet à l’aide de l’un des modèles SPA, incluez le **nom court** du modèle dans la nouvelle commande [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="21024-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="21024-232">La commande suivante crée une application angulaire avec ASP.NET Core MVC configuré pour le côté serveur :</span><span class="sxs-lookup"><span data-stu-id="21024-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="21024-233">Définir le mode de configuration de temps d’exécution</span><span class="sxs-lookup"><span data-stu-id="21024-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="21024-234">Il existe deux modes de configuration de temps d’exécution primaires :</span><span class="sxs-lookup"><span data-stu-id="21024-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="21024-235">**Développement**:</span><span class="sxs-lookup"><span data-stu-id="21024-235">**Development**:</span></span>
  * <span data-ttu-id="21024-236">Inclut des cartes sources pour faciliter le débogage.</span><span class="sxs-lookup"><span data-stu-id="21024-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="21024-237">N’optimise pas le code côté client pour les performances.</span><span class="sxs-lookup"><span data-stu-id="21024-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="21024-238">**Production**:</span><span class="sxs-lookup"><span data-stu-id="21024-238">**Production**:</span></span>
  * <span data-ttu-id="21024-239">Exclut les cartes sources.</span><span class="sxs-lookup"><span data-stu-id="21024-239">Excludes source maps.</span></span>
  * <span data-ttu-id="21024-240">Optimise le code côté client par le regroupement et la minification.</span><span class="sxs-lookup"><span data-stu-id="21024-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="21024-241">ASP.NET Core utilise une variable `ASPNETCORE_ENVIRONMENT` d’environnement nommée pour stocker le mode de configuration.</span><span class="sxs-lookup"><span data-stu-id="21024-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="21024-242">Pour plus d’informations, voir [Définir l’environnement](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="21024-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="21024-243">Exécuter avec .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="21024-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="21024-244">Restaurer les paquets NuGet et npm requis en exécutant la commande suivante à la racine du projet :</span><span class="sxs-lookup"><span data-stu-id="21024-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="21024-245">Construire et exécuter l’application :</span><span class="sxs-lookup"><span data-stu-id="21024-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="21024-246">L’application commence sur localhost en fonction du [mode de configuration de temps d’exécution](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="21024-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="21024-247">Naviguer `http://localhost:5000` dans le navigateur affiche la page de destination.</span><span class="sxs-lookup"><span data-stu-id="21024-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="21024-248">Exécuter avec Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="21024-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="21024-249">Ouvrez le fichier *.csproj* généré par la nouvelle commande [dotnet.](/dotnet/core/tools/dotnet-new)</span><span class="sxs-lookup"><span data-stu-id="21024-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="21024-250">Les paquets NuGet et npm requis sont restaurés automatiquement à l’ouverture du projet.</span><span class="sxs-lookup"><span data-stu-id="21024-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="21024-251">Ce processus de restauration peut prendre jusqu’à quelques minutes, et l’application est prête à fonctionner quand elle se termine.</span><span class="sxs-lookup"><span data-stu-id="21024-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="21024-252">Cliquez sur le bouton `Ctrl + F5`d’exécution verte ou appuyez sur, et le navigateur s’ouvre sur la page de destination de l’application.</span><span class="sxs-lookup"><span data-stu-id="21024-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="21024-253">L’application fonctionne sur localhost en fonction du [mode de configuration de temps d’exécution](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="21024-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="21024-254">Test de l'application</span><span class="sxs-lookup"><span data-stu-id="21024-254">Test the app</span></span>

<span data-ttu-id="21024-255">Les modèles SpaServices sont préconfigurés pour exécuter des tests côté client à l’aide [de Karma](https://karma-runner.github.io/1.0/index.html) et [Jasmine](https://jasmine.github.io/).</span><span class="sxs-lookup"><span data-stu-id="21024-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="21024-256">Jasmine est un cadre de test unitaire populaire pour JavaScript, tandis que Karma est un coureur de test pour ces tests.</span><span class="sxs-lookup"><span data-stu-id="21024-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="21024-257">Karma est configuré pour travailler avec le [Webpack Dev Middleware](#webpack-dev-middleware) de telle sorte que le développeur n’est pas tenu de s’arrêter et d’exécuter le test chaque fois que des modifications sont apportées.</span><span class="sxs-lookup"><span data-stu-id="21024-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="21024-258">Qu’il s’agisse du code en cours d’exécution contre le boîtier de test ou le cas de test lui-même, le test s’exécute automatiquement.</span><span class="sxs-lookup"><span data-stu-id="21024-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="21024-259">À titre d’exemple, deux cas de test de `CounterComponent` jasmin sont déjà prévus pour le fichier *counter.component.spec.ts* :</span><span class="sxs-lookup"><span data-stu-id="21024-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="21024-260">Ouvrez l’invite de commande dans le répertoire *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="21024-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="21024-261">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="21024-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="21024-262">Le script lance le coureur de test Karma, qui lit les paramètres définis dans le fichier *karma.conf.js.*</span><span class="sxs-lookup"><span data-stu-id="21024-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="21024-263">Entre autres paramètres, le *karma.conf.js* identifie les fichiers `files` de test à exécuter via son tableau :</span><span class="sxs-lookup"><span data-stu-id="21024-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="21024-264">Publier l’application</span><span class="sxs-lookup"><span data-stu-id="21024-264">Publish the app</span></span>

<span data-ttu-id="21024-265">Voir [ce numéro GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/12474) pour plus d’informations sur la publication à Azure.</span><span class="sxs-lookup"><span data-stu-id="21024-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="21024-266">La combinaison des actifs générés du côté client et des artefacts ASP.NET Core publiés dans un ensemble prêt à être déployé peut être lourde.</span><span class="sxs-lookup"><span data-stu-id="21024-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="21024-267">Heureusement, SpaServices orchestre tout ce processus de publication avec `RunWebpack`une cible MSBuild personnalisée nommée :</span><span class="sxs-lookup"><span data-stu-id="21024-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="21024-268">La cible MSBuild a les responsabilités suivantes :</span><span class="sxs-lookup"><span data-stu-id="21024-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="21024-269">Restaurer les paquets npm.</span><span class="sxs-lookup"><span data-stu-id="21024-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="21024-270">Créer une construction de qualité de production des actifs tiers côté client.</span><span class="sxs-lookup"><span data-stu-id="21024-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="21024-271">Créez une construction de qualité de production des actifs personnalisés du côté client.</span><span class="sxs-lookup"><span data-stu-id="21024-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="21024-272">Copiez les actifs générés par Webpack au dossier de publication.</span><span class="sxs-lookup"><span data-stu-id="21024-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="21024-273">La cible MSBuild est invoquée lors de l’exécution :</span><span class="sxs-lookup"><span data-stu-id="21024-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="21024-274">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="21024-274">Additional resources</span></span>

* [<span data-ttu-id="21024-275">Docs angulaires</span><span class="sxs-lookup"><span data-stu-id="21024-275">Angular Docs</span></span>](https://angular.io/docs)
