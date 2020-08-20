---
title: Notions de base d’ASP.NET Core
author: rick-anderson
description: Découvrez les concepts de base permettant de créer des applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/index
ms.openlocfilehash: 8fa44f567906ecf36a9bbaa5076cd36c75c10781
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634876"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="ca1ca-103">Notions de base d’ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca1ca-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca1ca-104">Cet article fournit une vue d’ensemble des rubriques clés pour comprendre comment développer des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="ca1ca-105">Classe Startup</span><span class="sxs-lookup"><span data-stu-id="ca1ca-105">The Startup class</span></span>

<span data-ttu-id="ca1ca-106">La classe `Startup` est l’endroit où :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="ca1ca-107">Les services nécessaires à l’application sont configurés.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="ca1ca-108">Le pipeline de traitement des demandes de l’application est défini comme une série de composants d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="ca1ca-109">Voici un exemple de classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="ca1ca-110">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="ca1ca-111">Injection de dépendances (services)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-111">Dependency injection (services)</span></span>

<span data-ttu-id="ca1ca-112">ASP.NET Core intègre une infrastructure d’injection de dépendances (DI) intégrée qui rend les services configurés disponibles dans toute l’application.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="ca1ca-113">Par exemple, un composant de journalisation est un service.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="ca1ca-114">Le code pour configurer (ou *enregistrer*) des services est ajouté à la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ca1ca-115">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="ca1ca-116">Les services sont généralement résolus à partir de DI à l’aide de l’injection de constructeur.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="ca1ca-117">Avec l’injection de constructeur, une classe déclare un paramètre de constructeur du type requis ou d’une interface.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="ca1ca-118">L’infrastructure DI fournit une instance de ce service au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="ca1ca-119">L’exemple suivant utilise l’injection de constructeur pour résoudre un `RazorPagesMovieContext` à partir de di :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="ca1ca-120">Si le conteneur inversion de contrôle (IoC) intégré ne répond pas à tous les besoins d’une application, un conteneur IoC tiers peut être utilisé à la place.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="ca1ca-121">Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="ca1ca-122">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-122">Middleware</span></span>

<span data-ttu-id="ca1ca-123">Le pipeline de traitement des requêtes est composé comme une série de composants d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="ca1ca-124">Chaque composant effectue des opérations sur un `HttpContext` et appelle l’intergiciel suivant dans le pipeline ou met fin à la requête.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="ca1ca-125">Par Convention, un composant middleware est ajouté au pipeline en appelant une `Use...` méthode d’extension dans la `Startup.Configure` méthode.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="ca1ca-126">Par exemple, pour activer le rendu des fichiers statiques, appelez `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="ca1ca-127">L’exemple suivant configure un pipeline de traitement des demandes :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="ca1ca-128">ASP.NET Core comprend un ensemble complet d’intergiciels (middleware) intégrés.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="ca1ca-129">Vous pouvez également écrire des composants d’intergiciel (middleware) personnalisés.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="ca1ca-130">Pour plus d'informations, consultez <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="ca1ca-131">Hôte</span><span class="sxs-lookup"><span data-stu-id="ca1ca-131">Host</span></span>

<span data-ttu-id="ca1ca-132">Au démarrage, une application ASP.NET Core crée un *hôte*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="ca1ca-133">L’hôte encapsule toutes les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="ca1ca-134">Une implémentation serveur HTTP</span><span class="sxs-lookup"><span data-stu-id="ca1ca-134">An HTTP server implementation</span></span>
* <span data-ttu-id="ca1ca-135">Composants d’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-135">Middleware components</span></span>
* <span data-ttu-id="ca1ca-136">Journalisation</span><span class="sxs-lookup"><span data-stu-id="ca1ca-136">Logging</span></span>
* <span data-ttu-id="ca1ca-137">Services d’injection de dépendance (DI)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="ca1ca-138">Configuration</span><span class="sxs-lookup"><span data-stu-id="ca1ca-138">Configuration</span></span>

<span data-ttu-id="ca1ca-139">Il existe deux hôtes différents :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-139">There are two different hosts:</span></span> 

* <span data-ttu-id="ca1ca-140">Hôte générique .NET</span><span class="sxs-lookup"><span data-stu-id="ca1ca-140">.NET Generic Host</span></span>
* <span data-ttu-id="ca1ca-141">Hôte web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ca1ca-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="ca1ca-142">L’hôte générique .NET est recommandé.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="ca1ca-143">L’hôte Web ASP.NET Core est disponible uniquement à des fins de compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="ca1ca-144">L’exemple suivant crée un hôte générique .NET :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="ca1ca-145">Les `CreateDefaultBuilder` `ConfigureWebHostDefaults` méthodes et configurent un hôte avec un ensemble d’options par défaut, telles que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="ca1ca-146">Utilisez [Kestrel](#servers) en tant que serveur web et activez l’intégration IIS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="ca1ca-147">Chargez la configuration à partir de *appsettings.json*, *appsettings.{Environment Name}.json*, des variables d’environnement, des arguments de ligne de commande et d’autres sources de configuration.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="ca1ca-148">Envoyez la sortie de journalisation aux fournisseurs Console et Debug.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="ca1ca-149">Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="ca1ca-150">Scénarios non basés sur le web</span><span class="sxs-lookup"><span data-stu-id="ca1ca-150">Non-web scenarios</span></span>

<span data-ttu-id="ca1ca-151">L’hôte générique permet à d’autres types d’application d’utiliser des extensions de framework composites, par exemple la journalisation, l’injection de dépendance, la configuration et la gestion de la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="ca1ca-152">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host> et <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="ca1ca-153">Serveurs</span><span class="sxs-lookup"><span data-stu-id="ca1ca-153">Servers</span></span>

<span data-ttu-id="ca1ca-154">Une application ASP.NET Core utilise une implémentation de serveur HTTP pour écouter les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="ca1ca-155">Les surfaces de serveur envoient des requêtes à l’application comme un ensemble de [fonctionnalités de requête](xref:fundamentals/request-features) composées dans un `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="ca1ca-156">Windows</span><span class="sxs-lookup"><span data-stu-id="ca1ca-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="ca1ca-157">ASP.NET Core fournit les implémentations de serveur suivantes :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="ca1ca-158">*Kestrel* est un serveur web multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="ca1ca-159">Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="ca1ca-160">Dans ASP.NET Core 2.0 ou versions ultérieures, Kestrel peut être exécuté en tant que serveur de périphérie public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="ca1ca-161">Le *serveur http IIS* est un serveur pour Windows qui utilise IIS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="ca1ca-162">Avec ce serveur, l’application ASP.NET Core et IIS s’exécutent dans le même processus.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="ca1ca-163">*HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="ca1ca-164">macOS</span><span class="sxs-lookup"><span data-stu-id="ca1ca-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="ca1ca-165">ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="ca1ca-166">Dans ASP.NET Core 2,0 ou version ultérieure, Kestrel peut s’exécuter en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="ca1ca-167">Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="ca1ca-168">Linux</span><span class="sxs-lookup"><span data-stu-id="ca1ca-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="ca1ca-169">ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="ca1ca-170">Dans ASP.NET Core 2,0 ou version ultérieure, Kestrel peut s’exécuter en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="ca1ca-171">Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="ca1ca-172">Pour plus d'informations, consultez <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="ca1ca-173">Configuration</span><span class="sxs-lookup"><span data-stu-id="ca1ca-173">Configuration</span></span>

<span data-ttu-id="ca1ca-174">ASP.NET Core fournit une infrastructure de configuration qui obtient des paramètres en tant que paires nom-valeur à partir d’un ensemble ordonné de fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="ca1ca-175">Les fournisseurs de configuration intégrés sont disponibles pour diverses sources, telles que les fichiers *. JSON* , les fichiers *. xml* , les variables d’environnement et les arguments de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="ca1ca-176">Écrivez des fournisseurs de configuration personnalisés pour prendre en charge d’autres sources.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="ca1ca-177">Par [défaut](xref:fundamentals/configuration/index#default), les applications ASP.net Core sont configurées pour lire à partir d'appsettings.js, de variables \* d'\* environnement, de la ligne de commande, etc.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="ca1ca-178">Lorsque la configuration de l’application est chargée, les valeurs des variables d’environnement remplacent les valeurs de *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="ca1ca-179">La méthode recommandée pour lire les valeurs de configuration associées utilise le [modèle d’options](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="ca1ca-180">Pour plus d’informations, consultez [lier des données de configuration hiérarchiques à l’aide du modèle options](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="ca1ca-181">Pour la gestion des données de configuration confidentielles telles que les mots de passe, ASP.NET Core fournit le [Gestionnaire de secret](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="ca1ca-182">Pour les secrets de production, nous vous recommandons [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="ca1ca-183">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="ca1ca-184">Environnements</span><span class="sxs-lookup"><span data-stu-id="ca1ca-184">Environments</span></span>

<span data-ttu-id="ca1ca-185">Les environnements d’exécution, tels que `Development` , `Staging` et `Production` , sont une notion de première classe dans ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="ca1ca-186">Spécifiez l’environnement dans lequel une application s’exécute en définissant la `ASPNETCORE_ENVIRONMENT` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="ca1ca-187">ASP.NET Core lit la variable d’environnement au démarrage de l’application et stocke la valeur dans une implémentation `IWebHostEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="ca1ca-188">Cette implémentation est disponible n’importe où dans une application via l’injection de dépendances (DI).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="ca1ca-189">L’exemple suivant configure l’application pour fournir des informations détaillées sur l’erreur lors de son exécution dans l' `Development` environnement :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="ca1ca-190">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="ca1ca-191">Journalisation</span><span class="sxs-lookup"><span data-stu-id="ca1ca-191">Logging</span></span>

<span data-ttu-id="ca1ca-192">ASP.NET Core prend en charge une API de journalisation qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="ca1ca-193">Les fournisseurs disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-193">Available providers include:</span></span>

* <span data-ttu-id="ca1ca-194">Console</span><span class="sxs-lookup"><span data-stu-id="ca1ca-194">Console</span></span>
* <span data-ttu-id="ca1ca-195">Débogage</span><span class="sxs-lookup"><span data-stu-id="ca1ca-195">Debug</span></span>
* <span data-ttu-id="ca1ca-196">Suivi des événements sur Windows</span><span class="sxs-lookup"><span data-stu-id="ca1ca-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="ca1ca-197">Journal des événements Windows</span><span class="sxs-lookup"><span data-stu-id="ca1ca-197">Windows Event Log</span></span>
* <span data-ttu-id="ca1ca-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="ca1ca-198">TraceSource</span></span>
* <span data-ttu-id="ca1ca-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ca1ca-199">Azure App Service</span></span>
* <span data-ttu-id="ca1ca-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="ca1ca-200">Azure Application Insights</span></span>

<span data-ttu-id="ca1ca-201">Pour créer des journaux, résolvez un <xref:Microsoft.Extensions.Logging.ILogger%601> service à partir d’une injection de dépendances et des méthodes de journalisation des appels telles que <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> .</span><span class="sxs-lookup"><span data-stu-id="ca1ca-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="ca1ca-202">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="ca1ca-203">Les méthodes de journalisation, telles que `LogInformation` prennent en charge un nombre quelconque de champs.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="ca1ca-204">Ces champs sont couramment utilisés pour construire un message `string` , mais certains fournisseurs de journalisation les envoient à un magasin de données en tant que champs distincts.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="ca1ca-205">Cette fonctionnalité permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="ca1ca-206">Pour plus d'informations, consultez <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="ca1ca-207">Routage</span><span class="sxs-lookup"><span data-stu-id="ca1ca-207">Routing</span></span>

<span data-ttu-id="ca1ca-208">Un *itinéraire* est un modèle d’URL qui est mappé à un gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="ca1ca-209">Le gestionnaire est généralement une Razor page, une méthode d’action dans un contrôleur MVC ou un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="ca1ca-210">Le routage ASP.NET Core vous permet de contrôler les URL utilisées par votre application.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="ca1ca-211">Pour plus d'informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="ca1ca-212">Gestion des erreurs</span><span class="sxs-lookup"><span data-stu-id="ca1ca-212">Error handling</span></span>

<span data-ttu-id="ca1ca-213">ASP.NET Core offre des fonctionnalités intégrées pour gérer des erreurs, telles que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="ca1ca-214">Une page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="ca1ca-214">A developer exception page</span></span>
* <span data-ttu-id="ca1ca-215">Pages d’erreur personnalisées</span><span class="sxs-lookup"><span data-stu-id="ca1ca-215">Custom error pages</span></span>
* <span data-ttu-id="ca1ca-216">Pages de codes d’état statique</span><span class="sxs-lookup"><span data-stu-id="ca1ca-216">Static status code pages</span></span>
* <span data-ttu-id="ca1ca-217">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="ca1ca-217">Startup exception handling</span></span>

<span data-ttu-id="ca1ca-218">Pour plus d'informations, consultez <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="ca1ca-219">Effectuer des requêtes HTTP</span><span class="sxs-lookup"><span data-stu-id="ca1ca-219">Make HTTP requests</span></span>

<span data-ttu-id="ca1ca-220">Une implémentation de `IHttpClientFactory` est disponible pour la création d’instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="ca1ca-221">La fabrique :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-221">The factory:</span></span>

* <span data-ttu-id="ca1ca-222">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ca1ca-223">Par exemple, inscrivez et configurez un client *GitHub* pour accéder à github.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="ca1ca-224">Inscrire et configurer un client par défaut à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="ca1ca-225">Prend en charge l’inscription et le chaînage de plusieurs gestionnaires de délégation pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ca1ca-226">Ce modèle est similaire au pipeline de l’intergiciel (middleware) entrant de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="ca1ca-227">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux pour les requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="ca1ca-228">S’intègre à *Polly*, une bibliothèque tierce populaire pour la gestion des erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="ca1ca-229">Gère le regroupement et la durée de vie des instances sous-jacentes `HttpClientHandler` afin d’éviter les problèmes DNS courants qui se produisent lors de la gestion `HttpClient` manuelle des durées de vie.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="ca1ca-230">Ajoute une expérience de journalisation configurable via <xref:Microsoft.Extensions.Logging.ILogger> pour toutes les requêtes envoyées via les clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ca1ca-231">Pour plus d'informations, consultez <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="ca1ca-232">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="ca1ca-232">Content root</span></span>

<span data-ttu-id="ca1ca-233">La racine du contenu est le chemin de base pour :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-233">The content root is the base path for:</span></span>

* <span data-ttu-id="ca1ca-234">Exécutable qui héberge l’application (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="ca1ca-235">Assemblys compilés qui composent l’application (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="ca1ca-236">Fichiers de contenu utilisés par l’application, tels que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="ca1ca-237">Razor fichiers (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="ca1ca-238">Fichiers de configuration (*. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="ca1ca-239">Fichiers de données (*. db*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-239">Data files (*.db*)</span></span>
* <span data-ttu-id="ca1ca-240">[Racine Web](#web-root), généralement le dossier *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="ca1ca-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="ca1ca-241">Pendant le développement, la racine de contenu est par défaut le répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="ca1ca-242">Ce répertoire est également le chemin d’accès de base pour les fichiers de contenu de l’application et la [racine Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="ca1ca-243">Spécifiez une autre racine de contenu en définissant son chemin d’accès lors de [la génération de l’hôte](#host).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="ca1ca-244">Pour plus d’informations, consultez [Racine de contenu](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="ca1ca-245">Racine web</span><span class="sxs-lookup"><span data-stu-id="ca1ca-245">Web root</span></span>

<span data-ttu-id="ca1ca-246">La racine Web est le chemin de base pour les fichiers de ressources statiques publics, tels que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="ca1ca-247">Feuilles de style (*. CSS*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="ca1ca-248">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="ca1ca-249">Images (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="ca1ca-250">Par défaut, les fichiers statiques sont pris en charge uniquement à partir du répertoire racine Web et de ses sous-répertoires.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="ca1ca-251">Le chemin d’accès racine Web par défaut est *{racine du contenu}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="ca1ca-252">Spécifiez une autre racine Web en définissant son chemin d’accès lors de [la génération de l’hôte](#host).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="ca1ca-253">Pour plus d’informations, consultez [Racine web](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="ca1ca-254">Empêcher la publication de fichiers dans *wwwroot* avec l' [ \<Content> élément de projet](/visualstudio/msbuild/common-msbuild-project-items#content) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="ca1ca-255">L’exemple suivant empêche la publication de contenu dans *wwwroot/local* et dans ses sous-répertoires :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="ca1ca-256">Dans Razor les fichiers *. cshtml* , les barres obliques `~/` inverses () pointent vers la racine Web.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="ca1ca-257">Un chemin d’accès commençant par `~/` est désigné sous le terme de « *chemin d’accès virtuel*».</span><span class="sxs-lookup"><span data-stu-id="ca1ca-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="ca1ca-258">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca1ca-259">Cet article est une vue d’ensemble des sujets clés pour comprendre comment développer des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="ca1ca-260">Classe Startup</span><span class="sxs-lookup"><span data-stu-id="ca1ca-260">The Startup class</span></span>

<span data-ttu-id="ca1ca-261">La classe `Startup` est l’endroit où :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="ca1ca-262">Les services nécessaires à l’application sont configurés.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="ca1ca-263">Le pipeline de traitement des requêtes est défini.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="ca1ca-264">Les *services* sont des composants utilisés par l’application.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="ca1ca-265">Par exemple, un composant de journalisation est un service.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-265">For example, a logging component is a service.</span></span> <span data-ttu-id="ca1ca-266">Le code pour configurer (ou *enregistrer*) des services est ajouté à la méthode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="ca1ca-267">Le pipeline de traitement des demandes est composé d’une série de composants d' *intergiciel (middleware* ).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="ca1ca-268">Par exemple, un intergiciel (middleware) peut gérer les demandes de fichiers statiques ou rediriger les requêtes HTTP vers HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="ca1ca-269">Chaque intergiciel (middleware) effectue des opérations asynchrones sur `HttpContext`, puis appelle l’intergiciel (middleware) suivant dans le pipeline ou met fin à la requête.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="ca1ca-270">Le code pour configurer le pipeline de traitement des requêtes est ajouté à la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="ca1ca-271">Voici un exemple de classe `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="ca1ca-272">Pour plus d'informations, consultez <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="ca1ca-273">Injection de dépendances (services)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-273">Dependency injection (services)</span></span>

<span data-ttu-id="ca1ca-274">ASP.NET Core offre une infrastructure d’injection de dépendances (DI) intégrée qui rend disponibles les services configurés aux classes d’une application.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="ca1ca-275">Pour obtenir une instance d’un service dans une classe, vous pouvez créer un constructeur avec un paramètre du type requis.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="ca1ca-276">Le paramètre peut être le type de service ou une interface.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="ca1ca-277">Le système d’injection de dépendances fournit le service lors de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="ca1ca-278">Voici une classe qui utilise l’injection de dépendances pour obtenir un objet de contexte Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="ca1ca-279">La ligne en surbrillance est un exemple d’injection de constructeur :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="ca1ca-280">Si l’injection de dépendances est intégrée, elle est conçue pour vous permettre d’incorporer un conteneur d’inversion de contrôle (IoC) tiers si vous préférez.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="ca1ca-281">Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="ca1ca-282">Intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-282">Middleware</span></span>

<span data-ttu-id="ca1ca-283">Le pipeline de traitement des requêtes est composé comme une série de composants d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="ca1ca-284">Chaque composant effectue des opérations asynchrones sur `HttpContext`, puis appelle l’intergiciel (middleware) suivant dans le pipeline ou met fin à la requête.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="ca1ca-285">Par convention, un composant d’intergiciel (middleware) est ajouté au pipeline en appelant sa méthode d’extension `Use...` dans la méthode `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="ca1ca-286">Par exemple, pour activer le rendu des fichiers statiques, appelez `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="ca1ca-287">Le code en surbrillance dans l’exemple suivant configure le pipeline de traitement des requêtes :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="ca1ca-288">ASP.NET Core inclut un ensemble complet de middlewares intégrés, et vous pouvez écrire un middleware personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="ca1ca-289">Pour plus d'informations, consultez <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="ca1ca-290">Hôte</span><span class="sxs-lookup"><span data-stu-id="ca1ca-290">Host</span></span>

<span data-ttu-id="ca1ca-291">Une application ASP.NET Core génère un *hôte* au démarrage.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="ca1ca-292">L’hôte est un objet qui encapsule toutes les ressources de l’application, telles que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="ca1ca-293">Une implémentation serveur HTTP</span><span class="sxs-lookup"><span data-stu-id="ca1ca-293">An HTTP server implementation</span></span>
* <span data-ttu-id="ca1ca-294">Composants d’intergiciel (middleware)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-294">Middleware components</span></span>
* <span data-ttu-id="ca1ca-295">Journalisation</span><span class="sxs-lookup"><span data-stu-id="ca1ca-295">Logging</span></span>
* <span data-ttu-id="ca1ca-296">DI</span><span class="sxs-lookup"><span data-stu-id="ca1ca-296">DI</span></span>
* <span data-ttu-id="ca1ca-297">Configuration</span><span class="sxs-lookup"><span data-stu-id="ca1ca-297">Configuration</span></span>

<span data-ttu-id="ca1ca-298">La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="ca1ca-299">Deux hôtes sont disponibles : l’hôte web et l’hôte générique.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="ca1ca-300">En ASP.NET Core 2.x, l’hôte générique est destiné uniquement aux scénarios non basés sur le web.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="ca1ca-301">Le code permettant de créer un hôte se trouve dans `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="ca1ca-302">La méthode `CreateDefaultBuilder` permet de configurer un hôte avec les options fréquemment utilisées, notamment :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="ca1ca-303">Utilisez [Kestrel](#servers) en tant que serveur web et activez l’intégration IIS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="ca1ca-304">Chargez la configuration à partir de *appsettings.json*, *appsettings.{Environment Name}.json*, des variables d’environnement, des arguments de ligne de commande et d’autres sources de configuration.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="ca1ca-305">Envoyez la sortie de journalisation aux fournisseurs Console et Debug.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="ca1ca-306">Pour plus d'informations, consultez <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="ca1ca-307">Scénarios non basés sur le web</span><span class="sxs-lookup"><span data-stu-id="ca1ca-307">Non-web scenarios</span></span>

<span data-ttu-id="ca1ca-308">L’hôte générique permet à d’autres types d’application d’utiliser des extensions de framework composites, par exemple la journalisation, l’injection de dépendance, la configuration et la gestion de la durée de vie de l’application.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="ca1ca-309">Pour plus d’informations, consultez <xref:fundamentals/host/generic-host> et <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="ca1ca-310">Serveurs</span><span class="sxs-lookup"><span data-stu-id="ca1ca-310">Servers</span></span>

<span data-ttu-id="ca1ca-311">Une application ASP.NET Core utilise une implémentation de serveur HTTP pour écouter les requêtes HTTP.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="ca1ca-312">Les surfaces de serveur envoient des requêtes à l’application comme un ensemble de [fonctionnalités de requête](xref:fundamentals/request-features) composées dans un `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="ca1ca-313">Windows</span><span class="sxs-lookup"><span data-stu-id="ca1ca-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="ca1ca-314">ASP.NET Core fournit les implémentations de serveur suivantes :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="ca1ca-315">*Kestrel* est un serveur web multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="ca1ca-316">Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="ca1ca-317">Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="ca1ca-318">*Le serveur HTTP IIS* est un serveur pour Windows qui utilise IIS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="ca1ca-319">Avec ce serveur, l’application ASP.NET Core et IIS s’exécutent dans le même processus.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="ca1ca-320">*HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="ca1ca-321">macOS</span><span class="sxs-lookup"><span data-stu-id="ca1ca-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="ca1ca-322">ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="ca1ca-323">Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="ca1ca-324">Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="ca1ca-325">Linux</span><span class="sxs-lookup"><span data-stu-id="ca1ca-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="ca1ca-326">ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="ca1ca-327">Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="ca1ca-328">Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="ca1ca-329">Windows</span><span class="sxs-lookup"><span data-stu-id="ca1ca-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="ca1ca-330">ASP.NET Core fournit les implémentations de serveur suivantes :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="ca1ca-331">*Kestrel* est un serveur web multiplateforme.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="ca1ca-332">Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="ca1ca-333">Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="ca1ca-334">*HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="ca1ca-335">macOS</span><span class="sxs-lookup"><span data-stu-id="ca1ca-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="ca1ca-336">ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="ca1ca-337">Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="ca1ca-338">Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="ca1ca-339">Linux</span><span class="sxs-lookup"><span data-stu-id="ca1ca-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="ca1ca-340">ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="ca1ca-341">Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="ca1ca-342">Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca1ca-343">Pour plus d'informations, consultez <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="ca1ca-344">Configuration</span><span class="sxs-lookup"><span data-stu-id="ca1ca-344">Configuration</span></span>

<span data-ttu-id="ca1ca-345">ASP.NET Core fournit une infrastructure de configuration qui obtient des paramètres en tant que paires nom-valeur à partir d’un ensemble ordonné de fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="ca1ca-346">Il existe des fournisseurs de configuration intégrés pour une grande variété de sources, tels que des fichiers *.json*, *.xml*, des variables d’environnement et des arguments de ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="ca1ca-347">Vous pouvez également écrire des fournisseurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="ca1ca-348">Par exemple, vous pouvez spécifier que la configuration provient de *appsettings.json* et de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="ca1ca-349">Lorsque la valeur de *ConnectionString* est demandée, l’infrastructure recherche d’abord dans le fichier *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="ca1ca-350">Si la valeur est trouvée dans ce fichier, mais également dans une variable d’environnement, la valeur de la variable d’environnement est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="ca1ca-351">Pour gérer des données de configuration confidentielles telles que les mots de passe, ASP.NET Core fournit un [outil Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ca1ca-352">Pour les secrets de production, nous vous recommandons [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="ca1ca-353">Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="ca1ca-354">Options</span><span class="sxs-lookup"><span data-stu-id="ca1ca-354">Options</span></span>

<span data-ttu-id="ca1ca-355">Lorsque cela est possible, ASP.NET Core suit le *modèle d’options* pour stocker et récupérer des valeurs de configuration.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="ca1ca-356">Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="ca1ca-357">Par exemple, le code suivant définit des options WebSockets :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="ca1ca-358">Pour plus d'informations, consultez <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="ca1ca-359">Environnements</span><span class="sxs-lookup"><span data-stu-id="ca1ca-359">Environments</span></span>

<span data-ttu-id="ca1ca-360">Les environnements d’exécution, tels que *Développement*, *Mise en lots* et *Production*, sont une notion de premier plan dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="ca1ca-361">Vous pouvez spécifier l’environnement d’exécution d’une application en définissant la variable d’environnement `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="ca1ca-362">ASP.NET Core lit la variable d’environnement au démarrage de l’application et stocke la valeur dans une implémentation `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="ca1ca-363">L’objet d’environnement est disponible partout dans l’application par le biais de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="ca1ca-364">L’exemple de code suivant de la classe `Startup` configure l’application pour fournir des informations d’erreur détaillées uniquement lorsqu’elle s’exécute en développement :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="ca1ca-365">Pour plus d'informations, consultez <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="ca1ca-366">Journalisation</span><span class="sxs-lookup"><span data-stu-id="ca1ca-366">Logging</span></span>

<span data-ttu-id="ca1ca-367">ASP.NET Core prend en charge une API de journalisation qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="ca1ca-368">Les fournisseurs disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-368">Available providers include the following:</span></span>

* <span data-ttu-id="ca1ca-369">Console</span><span class="sxs-lookup"><span data-stu-id="ca1ca-369">Console</span></span>
* <span data-ttu-id="ca1ca-370">Débogage</span><span class="sxs-lookup"><span data-stu-id="ca1ca-370">Debug</span></span>
* <span data-ttu-id="ca1ca-371">Suivi des événements sur Windows</span><span class="sxs-lookup"><span data-stu-id="ca1ca-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="ca1ca-372">Journal des événements Windows</span><span class="sxs-lookup"><span data-stu-id="ca1ca-372">Windows Event Log</span></span>
* <span data-ttu-id="ca1ca-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="ca1ca-373">TraceSource</span></span>
* <span data-ttu-id="ca1ca-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ca1ca-374">Azure App Service</span></span>
* <span data-ttu-id="ca1ca-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="ca1ca-375">Azure Application Insights</span></span>

<span data-ttu-id="ca1ca-376">Écrivez des journaux à partir de n’importe quel emplacement dans le code d’une application en obtenant un objet `ILogger` à partir de l’injection de dépendances et en appelant les méthodes de journal.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="ca1ca-377">Voici un exemple de code qui utilise un objet `ILogger`, avec l’injection de constructeur et les appels de méthode de journalisation mis en surbrillance.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="ca1ca-378">L’interface `ILogger` vous permet de passer un certain nombre de champs au fournisseur de journalisation.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="ca1ca-379">Les champs sont couramment utilisés pour construire une chaîne de message, mais le fournisseur peut également les envoyer en tant que champs distincts dans un magasin de données.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="ca1ca-380">Cette fonctionnalité permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="ca1ca-381">Pour plus d'informations, consultez <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="ca1ca-382">Routage</span><span class="sxs-lookup"><span data-stu-id="ca1ca-382">Routing</span></span>

<span data-ttu-id="ca1ca-383">Un *itinéraire* est un modèle d’URL qui est mappé à un gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="ca1ca-384">Le gestionnaire est généralement une Razor page, une méthode d’action dans un contrôleur MVC ou un intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="ca1ca-385">Le routage ASP.NET Core vous permet de contrôler les URL utilisées par votre application.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="ca1ca-386">Pour plus d'informations, consultez <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="ca1ca-387">Gestion des erreurs</span><span class="sxs-lookup"><span data-stu-id="ca1ca-387">Error handling</span></span>

<span data-ttu-id="ca1ca-388">ASP.NET Core offre des fonctionnalités intégrées pour gérer des erreurs, telles que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="ca1ca-389">Une page d’exceptions du développeur</span><span class="sxs-lookup"><span data-stu-id="ca1ca-389">A developer exception page</span></span>
* <span data-ttu-id="ca1ca-390">Pages d’erreur personnalisées</span><span class="sxs-lookup"><span data-stu-id="ca1ca-390">Custom error pages</span></span>
* <span data-ttu-id="ca1ca-391">Pages de codes d’état statique</span><span class="sxs-lookup"><span data-stu-id="ca1ca-391">Static status code pages</span></span>
* <span data-ttu-id="ca1ca-392">Gestion des exceptions de démarrage</span><span class="sxs-lookup"><span data-stu-id="ca1ca-392">Startup exception handling</span></span>

<span data-ttu-id="ca1ca-393">Pour plus d'informations, consultez <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="ca1ca-394">Effectuer des requêtes HTTP</span><span class="sxs-lookup"><span data-stu-id="ca1ca-394">Make HTTP requests</span></span>

<span data-ttu-id="ca1ca-395">Une implémentation de `IHttpClientFactory` est disponible pour la création d’instances `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="ca1ca-396">La fabrique :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-396">The factory:</span></span>

* <span data-ttu-id="ca1ca-397">Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="ca1ca-398">Par exemple, un client *GitHub* peut être inscrit et configuré pour accéder à github.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="ca1ca-399">Un client par défaut peut être inscrit à d’autres fins.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="ca1ca-400">Prend en charge l’inscription et le chaînage de plusieurs gestionnaires de délégation pour créer un pipeline de middlewares pour les requêtes sortantes.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="ca1ca-401">Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="ca1ca-402">Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="ca1ca-403">S’intègre à *Polly*, une bibliothèque tierce populaire pour la gestion des erreurs temporaires.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="ca1ca-404">Gère le regroupement et la durée de vie des instances de `HttpClientHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="ca1ca-405">Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="ca1ca-406">Pour plus d'informations, consultez <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="ca1ca-407">Racine de contenu</span><span class="sxs-lookup"><span data-stu-id="ca1ca-407">Content root</span></span>

<span data-ttu-id="ca1ca-408">La racine du contenu est le chemin d’accès de base à :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="ca1ca-409">Exécutable hébergeant l’application (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="ca1ca-410">Assemblys compilés qui composent l’application (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="ca1ca-411">Fichiers de contenu sans code utilisés par l’application, tels que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="ca1ca-412">Razor fichiers (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="ca1ca-413">Fichiers de configuration (*. JSON*, *. xml*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="ca1ca-414">Fichiers de données (*. db*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-414">Data files (*.db*)</span></span>
* <span data-ttu-id="ca1ca-415">[Racine Web](#web-root), en général le dossier *wwwroot* publié.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="ca1ca-416">Lors du développement :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-416">During development:</span></span>

* <span data-ttu-id="ca1ca-417">La racine du contenu est par défaut le répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="ca1ca-418">Le répertoire racine du projet est utilisé pour créer le :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="ca1ca-419">Chemin d’accès aux fichiers de contenu sans code de l’application dans le répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="ca1ca-420">[Racine Web](#web-root), généralement le dossier *wwwroot* dans le répertoire racine du projet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="ca1ca-421">Vous pouvez spécifier un autre chemin d’accès racine [de contenu lors de la génération de l’hôte](#host).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="ca1ca-422">Pour plus d'informations, consultez <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="ca1ca-423">Racine web</span><span class="sxs-lookup"><span data-stu-id="ca1ca-423">Web root</span></span>

<span data-ttu-id="ca1ca-424">La racine Web est le chemin de base des fichiers de ressources statiques, non-code et publics, tels que :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="ca1ca-425">Feuilles de style (*. CSS*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="ca1ca-426">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="ca1ca-427">Images (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="ca1ca-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="ca1ca-428">Les fichiers statiques sont pris en charge par défaut uniquement à partir du répertoire racine Web (et des sous-répertoires).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="ca1ca-429">Le chemin d’accès racine Web a comme valeur par défaut *{content root}/wwwroot*, mais une autre racine Web peut être spécifiée lors de [la génération de l’hôte](#host).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="ca1ca-430">Pour plus d’informations, consultez [Racine web](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="ca1ca-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="ca1ca-431">Empêcher la publication de fichiers dans *wwwroot* avec l' [ \<Content> élément de projet](/visualstudio/msbuild/common-msbuild-project-items#content) dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="ca1ca-432">L’exemple suivant empêche la publication de contenu dans le répertoire et les sous-répertoires *wwwroot/local* :</span><span class="sxs-lookup"><span data-stu-id="ca1ca-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="ca1ca-433">Dans les Razor fichiers (*. cshtml*), le tilde-slash ( `~/` ) pointe vers la racine Web.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="ca1ca-434">Un chemin d’accès commençant par `~/` est désigné sous le terme de « *chemin d’accès virtuel*».</span><span class="sxs-lookup"><span data-stu-id="ca1ca-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="ca1ca-435">Pour plus d'informations, consultez <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="ca1ca-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
