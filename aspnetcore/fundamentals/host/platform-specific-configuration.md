---
title: Utiliser des assemblys de démarrage d’hébergement dans ASP.NET Core
author: rick-anderson
description: Découvrez comment améliorer une application ASP.NET Core à partir d’un assembly externe à l’aide d’une implémentation IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: fadd93cf28603653e20ed6c7dceadcabf0dfb9a5
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627518"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="5c3a9-103">Utiliser des assemblys de démarrage d’hébergement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c3a9-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="5c3a9-104">Par [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c3a9-105">Une <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implémentation (hébergement de démarrage) ajoute des améliorations à une application au démarrage à partir d’un assembly externe.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="5c3a9-106">Par exemple, une bibliothèque externe peut utiliser une implémentation d’hébergement au démarrage pour fournir des fournisseurs ou services de configuration supplémentaires à une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="5c3a9-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c3a9-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="5c3a9-108">Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="5c3a9-108">HostingStartup attribute</span></span>

<span data-ttu-id="5c3a9-109">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indique la présence d’un assembly d’hébergement au démarrage à activer au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="5c3a9-110">L’assembly d’entrée ou l’assembly contenant la classe `Startup` est automatiquement analysé pour détecter l’attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-111">La liste des assemblys où les attributs `HostingStartup` doivent être recherchés est chargée au moment de l’exécution à partir de la configuration spécifiée dans [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="5c3a9-112">La liste des assemblys à exclure de cette détection est chargée de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="5c3a9-113">Dans l’exemple suivant, l’espace de noms de l’assembly d’hébergement au démarrage est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="5c3a9-114">La classe contenant le code d’hébergement au démarrage est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5c3a9-115">L’attribut `HostingStartup` se trouve généralement dans le fichier de classe d’implémentation `IHostingStartup` de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="5c3a9-116">Découvrir les assemblys d’hébergement au démarrage chargés</span><span class="sxs-lookup"><span data-stu-id="5c3a9-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="5c3a9-117">Pour détecter les assemblys d’hébergement au démarrage chargés, activez la journalisation et analysez les journaux de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="5c3a9-118">Les erreurs qui se produisent durant le chargement des assemblys sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="5c3a9-119">Les assemblys d’hébergement au démarrage chargés sont journalisés au niveau Débogage, et toutes les erreurs sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="5c3a9-120">Désactiver le chargement automatique des assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="5c3a9-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="5c3a9-121">Pour désactiver le chargement automatique des assemblys d’hébergement au démarrage, choisissez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="5c3a9-122">Pour bloquer le chargement de tous les assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sur `true` ou `1` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="5c3a9-123">Empêcher l’hébergement des paramètres de configuration de l’hôte de démarrage :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="5c3a9-124">La variable d’environnement `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="5c3a9-125">Pour bloquer le chargement de certains assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sous la forme d’une liste délimitée par des points-virgules contenant les assemblys d’hébergement au démarrage à exclure au moment du démarrage :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="5c3a9-126">Paramètre de configuration d’hôte d’exclusion des assemblys de démarrage d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="5c3a9-127">La variable d’environnement `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="5c3a9-128">Si le paramètre de configuration d’hôte et la variable d’environnement sont définis tous les deux, c’est le paramètre d’hôte qui détermine le comportement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="5c3a9-129">La désactivation des assemblys d’hébergement au démarrage à l’aide du paramètre d’hôte ou de la variable d’environnement désactive l’assembly globalement et peut donc désactiver plusieurs caractéristiques d’une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="5c3a9-130">Project</span><span class="sxs-lookup"><span data-stu-id="5c3a9-130">Project</span></span>

<span data-ttu-id="5c3a9-131">Créez un hébergement au démarrage avec un des types de projet suivants :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="5c3a9-132">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="5c3a9-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="5c3a9-133">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="5c3a9-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="5c3a9-134">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="5c3a9-134">Class library</span></span>

<span data-ttu-id="5c3a9-135">Une amélioration de l’hébergement au démarrage peut être fournie dans une bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="5c3a9-136">La bibliothèque contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5c3a9-137">L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) comprend une Razor application pages, *HostingStartupApp*et une bibliothèque de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="5c3a9-138">La bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-138">The class library:</span></span>

* <span data-ttu-id="5c3a9-139">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5c3a9-140">`ServiceKeyInjection` Ajoute une paire de chaînes de service à la configuration de l’application à l’aide du fournisseur de configuration en mémoire ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="5c3a9-141">Inclut un attribut `HostingStartup` qui identifie l’espace de noms et la classe d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="5c3a9-142">La `ServiceKeyInjection` méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="5c3a9-143">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5c3a9-144">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage de la bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="5c3a9-145">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="5c3a9-146">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclut également un projet de package NuGet qui fournit un hébergement au démarrage distinct, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="5c3a9-147">Le package a les mêmes caractéristiques que la bibliothèque de classes décrite précédemment.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="5c3a9-148">Le package :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-148">The package:</span></span>

* <span data-ttu-id="5c3a9-149">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5c3a9-150">`ServiceKeyInjection` ajoute une paire de chaînes de service à la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="5c3a9-151">Inclut un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5c3a9-152">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5c3a9-153">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage du package :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="5c3a9-154">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="5c3a9-155">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="5c3a9-155">Console app without an entry point</span></span>

<span data-ttu-id="5c3a9-156">*Cette approche s’applique aux applications .NET Core, et non à .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="5c3a9-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="5c3a9-157">Une amélioration d’hébergement au démarrage dynamique qui ne nécessite pas de référence au moment de la compilation pour l’activation peut être fournie dans une application console sans point d’entrée qui contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-158">La publication de l’application console génère un assembly d’hébergement au démarrage qui peut être utilisé à partir du magasin de runtime.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="5c3a9-159">Une application console sans point d’entrée est utilisée dans ce processus, car :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="5c3a9-160">Un fichier de dépendances est nécessaire pour utiliser l’hébergement au démarrage dans l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="5c3a9-161">Un fichier de dépendances est une ressource d’application exécutable qui est générée par la publication d’une application, et non d’une bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="5c3a9-162">Une bibliothèque ne peut pas être ajoutée directement au [magasin de packages de runtime](/dotnet/core/deploying/runtime-store), qui nécessite un projet exécutable ciblant le runtime partagé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="5c3a9-163">Lors de la création d’un hébergement au démarrage dynamique :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="5c3a9-164">Un assembly d’hébergement au démarrage est créé à partir de l’application console sans point d’entrée qui :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="5c3a9-165">Inclut une classe qui contient l’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="5c3a9-166">Inclut un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pour identifier la classe d’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="5c3a9-167">L’application console est publiée pour obtenir les dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="5c3a9-168">La publication de l’application console entraîne la suppression des dépendances inutilisées dans le fichier de dépendances.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="5c3a9-169">Le fichier de dépendances est modifié pour définir l’emplacement d’exécution de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="5c3a9-170">L’assembly d’hébergement au démarrage et le fichier de dépendances associé sont placés dans le magasin de packages de runtime.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="5c3a9-171">Pour permettre leur détection, l’assembly d’hébergement au démarrage et le fichier de dépendances associé sont référencés dans une paire de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="5c3a9-172">L’application console référence le package [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="5c3a9-173">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifie une classe en tant qu’implémentation de `IHostingStartup` pour le chargement et l’exécution lors de la génération du <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="5c3a9-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="5c3a9-174">Dans l’exemple suivant, l’espace de noms est `StartupEnhancement`, et la classe est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5c3a9-175">Une classe implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="5c3a9-176">La méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="5c3a9-177">`IHostingStartup.Configure` dans l’assembly d’hébergement au démarrage est appelé par le runtime avant `Startup.Configure` dans le code utilisateur, ce qui permet au code utilisateur de remplacer la configuration fournie par l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="5c3a9-178">Durant la génération d’un projet `IHostingStartup`, le fichier de dépendances (*. deps.json*) définit le dossier *bin* comme emplacement du `runtime` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="5c3a9-179">Seule une partie du fichier est affichée.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-179">Only part of the file is shown.</span></span> <span data-ttu-id="5c3a9-180">Le nom de l’assembly dans l’exemple est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="5c3a9-181">Configuration fournie par le démarrage d’hébergement</span><span class="sxs-lookup"><span data-stu-id="5c3a9-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="5c3a9-182">Il existe deux approches de gestion de la configuration selon si vous souhaitez que la configuration du démarrage d’hébergement ait la priorité ou que la configuration d’application ait la priorité :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="5c3a9-183">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pour charger la configuration après l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5c3a9-184">Avec cette approche, la configuration de démarrage d’hébergement est prioritaire sur la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="5c3a9-185">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pour charger la configuration avant l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5c3a9-186">Avec cette approche, les valeurs de la configuration d’application sont prioritaires sur celles fournies par le démarrage d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="5c3a9-187">Spécifier l’assembly d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="5c3a9-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="5c3a9-188">Pour l’hébergement au démarrage fourni par une bibliothèque de classes ou une application console, spécifiez le nom de l’assembly d’hébergement au démarrage dans la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="5c3a9-189">Cette variable est spécifiée sous la forme d’une liste d’assemblys délimitée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="5c3a9-190">L’analyse de détection de l’attribut `HostingStartup` porte uniquement sur les assemblys d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-191">Dans l’exemple d’application (*HostingStartupApp*), pour permettre la détection des hébergements au démarrage décrits précédemment, la variable d’environnement est définie à la valeur suivante :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="5c3a9-192">Un assembly de démarrage d’hébergement peut également être défini à l’aide du paramètre de configuration d’hôte assemblys de démarrage d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="5c3a9-193">Lorsque plusieurs Assemblies de démarrage d’hébergement sont présents, leurs <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> méthodes sont exécutées dans l’ordre dans lequel les assemblys sont répertoriés.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="5c3a9-194">Activation</span><span class="sxs-lookup"><span data-stu-id="5c3a9-194">Activation</span></span>

<span data-ttu-id="5c3a9-195">Les options d’activation de l’hébergement au démarrage sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="5c3a9-196">[Magasin du runtime](#runtime-store): l’activation ne nécessite pas de référence au moment de la compilation pour l’activation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-196">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="5c3a9-197">L’exemple d’application place les fichiers de l’assembly d’hébergement au démarrage et de ses dépendances dans le dossier *deployment* pour faciliter le déploiement de l’hébergement au démarrage dans un environnement multimachine.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="5c3a9-198">Le dossier *deployment* inclut également un script PowerShell qui crée ou modifie des variables d’environnement sur le système de déploiement pour activer l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="5c3a9-199">Référence au moment de la compilation requise pour l’activation</span><span class="sxs-lookup"><span data-stu-id="5c3a9-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="5c3a9-200">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="5c3a9-201">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="5c3a9-202">Magasin de runtime</span><span class="sxs-lookup"><span data-stu-id="5c3a9-202">Runtime store</span></span>

<span data-ttu-id="5c3a9-203">L’implémentation d’hébergement au démarrage est placée dans le [magasin de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="5c3a9-204">L’application améliorée ne nécessite pas de référence à l’assembly au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="5c3a9-205">Une fois l’hébergement au démarrage créé, un magasin de runtime est généré à l’aide du fichier manifeste de projet et de la commande [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="5c3a9-206">Dans l’exemple d’application (projet *RuntimeStore*), la commande suivante est utilisée :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="5c3a9-207">Pour que le runtime découvre le magasin de runtime, l’emplacement du magasin de runtime est ajouté à la variable d’environnement `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="5c3a9-208">**Modifier et placer le fichier de dépendances de l’hébergement au démarrage**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="5c3a9-209">Pour activer l’amélioration sans référence de package à l’amélioration, spécifiez les dépendances supplémentaires pour le runtime avec `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="5c3a9-210">`additionalDeps` vous permet de :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="5c3a9-211">Étendre le graphe de la bibliothèque de l’application en fournissant un ensemble de fichiers *.deps.json* supplémentaires à fusionner avec le fichier *.deps.json* de l’application au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="5c3a9-212">Rendre l’assembly d’hébergement au démarrage détectable et chargeable.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="5c3a9-213">L’approche recommandée pour la génération du fichier de dépendances supplémentaire est la suivante :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="5c3a9-214">Exécutez `dotnet publish` sur le fichier manifeste du magasin de runtime référencé dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="5c3a9-215">Supprimez la référence de manifeste des bibliothèques et la `runtime` section du *.deps.jsrésultant sur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="5c3a9-216">Dans l’exemple de projet, la propriété `store.manifest/1.0.0` est supprimée de la section `targets` et de la section `libraries` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="5c3a9-217">Placez le fichier *.deps.json* à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="5c3a9-218">`{ADDITIONAL DEPENDENCIES PATH}`: Emplacement ajouté à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-218">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="5c3a9-219">`{SHARED FRAMEWORK NAME}`: Framework partagé requis pour ce fichier de dépendances supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-219">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="5c3a9-220">`{SHARED FRAMEWORK VERSION}`: Version minimale du Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-220">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="5c3a9-221">`{ENHANCEMENT ASSEMBLY NAME}`: Le nom de l’assembly de l’amélioration.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-221">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="5c3a9-222">Dans l’exemple d’application (projet *RuntimeStore*), le fichier de dépendances supplémentaire est placé à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="5c3a9-223">Pour que le runtime découvre l’emplacement du magasin de runtime, l’emplacement du fichier de dépendances supplémentaire est ajouté à la variable d’environnement `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="5c3a9-224">Dans l’exemple d’application (projet *RuntimeStore*), la génération du magasin de runtime et du fichier de dépendances supplémentaire s’effectue à l’aide d’un script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="5c3a9-225">Pour obtenir des exemples montrant comment définir des variables d’environnement pour différents systèmes d’exploitation, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5c3a9-226">**Déploiement**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-226">**Deployment**</span></span>

<span data-ttu-id="5c3a9-227">Pour faciliter le déploiement d’un hébergement au démarrage dans un environnement multimachine, l’exemple d’application crée un dossier *deployment* dans la sortie publiée qui contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="5c3a9-228">Le magasin de runtime d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="5c3a9-229">Le fichier des dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="5c3a9-230">Un script PowerShell qui crée ou modifie les variables `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` et `DOTNET_ADDITIONAL_DEPS` pour prendre en charge l’activation de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="5c3a9-231">Exécutez ce script à partir d’une invite de commandes PowerShell d’administration sur le système de déploiement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="5c3a9-232">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-232">NuGet package</span></span>

<span data-ttu-id="5c3a9-233">Une amélioration de l’hébergement au démarrage peut être fournie dans un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="5c3a9-234">Le package a un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-235">Les types d’hébergement au démarrage fournis par le package sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="5c3a9-236">Le fichier projet de l’application améliorée crée une référence de package à l’hébergement au démarrage dans le fichier projet de l’application (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-237">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5c3a9-238">Cette approche s’applique à un package d’assembly d’hébergement au démarrage qui a été publié sur [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="5c3a9-239">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="5c3a9-240">Pour plus d’informations sur les packages NuGet et le magasin de runtime, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="5c3a9-241">Création d’un Package NuGet avec les outils multiplateformes</span><span class="sxs-lookup"><span data-stu-id="5c3a9-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="5c3a9-242">Publication de packages</span><span class="sxs-lookup"><span data-stu-id="5c3a9-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="5c3a9-243">Magasin de packages de runtime</span><span class="sxs-lookup"><span data-stu-id="5c3a9-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="5c3a9-244">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-244">Project bin folder</span></span>

<span data-ttu-id="5c3a9-245">Une amélioration de l’hébergement au démarrage peut être fournie par un assembly déployé à partir du dossier *bin* dans l’application améliorée.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="5c3a9-246">Les types d’hébergement au démarrage fournis par l’assembly sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="5c3a9-247">Le fichier projet de l’application améliorée crée une référence d’assembly à l’hébergement au démarrage (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-248">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5c3a9-249">Cette approche s’applique lorsque le scénario de déploiement appelle dans le but d’effectuer une référence au moment de la compilation à l’assembly d’hébergement au démarrage (fichier *.dll*) et en déplaçant l’assembly vers :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="5c3a9-250">Le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-250">The consuming project.</span></span>
  * <span data-ttu-id="5c3a9-251">Un emplacement accessible par le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="5c3a9-252">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="5c3a9-253">Lors du ciblage de .NET Framework, l’assembly peut être chargé dans le contexte de charge par défaut, qui, sur .NET Framework, signifie que l’assembly se trouve à l’un des emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="5c3a9-254">Chemin de la base de l’application : dossier *bin* où se trouve l’exécutable de l’application (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-254">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="5c3a9-255">Global assembly cache (GAC) : le GAC stocke les assemblys partagés par plusieurs applications .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-255">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="5c3a9-256">Pour plus d’informations, consultez [Comment : installer un assembly dans le global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) dans la documentation de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="5c3a9-257">Exemple de code</span><span class="sxs-lookup"><span data-stu-id="5c3a9-257">Sample code</span></span>

<span data-ttu-id="5c3a9-258">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Comment télécharger un exemple](xref:index#how-to-download-a-sample)) montre des scénarios d’implémentation de l’hébergement au démarrage :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="5c3a9-259">Deux assemblys d’hébergement au démarrage (bibliothèques de classes) définissent chacun une paire clé-valeur de configuration en mémoire :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="5c3a9-260">Package NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="5c3a9-261">Bibliothèque de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="5c3a9-262">Un hébergement au démarrage est activé à partir d’un assembly déployé depuis le magasin de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="5c3a9-263">L’assembly ajoute deux middleware (intergiciels) à l’application au démarrage, qui fournissent des informations de diagnostic :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="5c3a9-264">Services inscrits</span><span class="sxs-lookup"><span data-stu-id="5c3a9-264">Registered services</span></span>
  * <span data-ttu-id="5c3a9-265">Adresse (schéma, hôte, chemin de base, chemin, chaîne de requête)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="5c3a9-266">Connexion (adresse IP distante, port distant, adresse IP locale, port local, certificat client)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="5c3a9-267">En-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="5c3a9-267">Request headers</span></span>
  * <span data-ttu-id="5c3a9-268">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="5c3a9-268">Environment variables</span></span>

<span data-ttu-id="5c3a9-269">Pour exécuter l’exemple :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-269">To run the sample:</span></span>

<span data-ttu-id="5c3a9-270">**Activation à partir d’un package NuGet**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="5c3a9-271">Compilez le package *HostingStartupPackage* à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="5c3a9-272">Ajoutez le nom de l’assembly du package *HostingStartupPackage* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5c3a9-273">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-273">Compile and run the app.</span></span> <span data-ttu-id="5c3a9-274">Une référence de package est présente dans l’application améliorée (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-275">Un `<PropertyGroup>` dans le fichier projet de l’application spécifie la sortie du projet de package (*../HostingStartupPackage/bin/Debug*) comme source de package.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="5c3a9-276">Cela permet à l’application d’utiliser le package sans télécharger le package sur [NuGet.org](https://www.nuget.org/). Pour plus d’informations, consultez les notes dans le fichier projet de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5c3a9-277">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` du package.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5c3a9-278">Si vous modifiez le projet *HostingStartupPackage* et le recompilez, effacez les caches locaux du package NuGet pour vous assurer que *HostingStartupApp* reçoit le package mis à jour et pas un package périmé du cache local.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="5c3a9-279">Pour effacer les caches NuGet locaux, exécutez la commande [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) suivante :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="5c3a9-280">**Activation à partir d’une bibliothèque de classes**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="5c3a9-281">Compilez la bibliothèque de classes *HostingStartupLibrary* à l’aide de la commande [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="5c3a9-282">Ajoutez le nom de l’assembly de la bibliothèque de classes *HostingStartupLibrary* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5c3a9-283">À partir du dossier *bin*, déployez l’assembly de la bibliothèque de classes dans l’application en copiant le fichier *HostingStartupLibrary.dll* du résultat de la compilation de la bibliothèque de classes dans le dossier *bin/Debug* de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="5c3a9-284">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-284">Compile and run the app.</span></span> <span data-ttu-id="5c3a9-285">Un `<ItemGroup>` dans le fichier projet de l’application fait référence à l’assembly de la bibliothèque de classes (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (une référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-286">Pour plus d’informations, consultez les remarques dans le fichier projet de l’application HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="5c3a9-287">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5c3a9-288">**Activation à partir d’un assembly déployé à partir du magasin de runtime**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="5c3a9-289">Le projet *StartupDiagnostics* utilise [PowerShell](/powershell/scripting/powershell-scripting) pour modifier le fichier *StartupDiagnostics.deps.json* associé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="5c3a9-290">PowerShell est installé par défaut sur Windows à compter de Windows 7 SP1 et de Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="5c3a9-291">Pour obtenir PowerShell sur d’autres plateformes, consultez [installation de différentes versions de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="5c3a9-292">Exécutez le script *build.ps1* du dossier *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="5c3a9-293">Le script :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-293">The script:</span></span>
   * <span data-ttu-id="5c3a9-294">Génère le `StartupDiagnostics` package dans le dossier *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="5c3a9-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="5c3a9-295">Génère le magasin de runtime de `StartupDiagnostics` dans le dossier *store*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="5c3a9-296">La commande `dotnet store` du script utilise l’ [identificateur de runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` pour un hébergement au démarrage déployé sur Windows.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="5c3a9-297">Si vous fournissez l’hébergement au démarrage pour un autre runtime, spécifiez l’identificateur du runtime à la ligne 37 du script.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="5c3a9-298">Le magasin du runtime pour `StartupDiagnostics` serait déplacé ultérieurement vers la Banque d’exécution du système ou de l’utilisateur sur l’ordinateur sur lequel l’assembly sera consommé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="5c3a9-299">L’emplacement d’installation du magasin d’exécution de l’utilisateur pour l' `StartupDiagnostics` assembly est *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="5c3a9-300">Génère le `additionalDeps` pour `StartupDiagnostics` dans le dossier *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="5c3a9-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="5c3a9-301">Les dépendances supplémentaires seraient ensuite déplacées vers les dépendances supplémentaires du système ou de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="5c3a9-302">L' `StartupDiagnostics` emplacement d’installation des dépendances supplémentaires de l’utilisateur est *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. Netcore. app/3.0.0/StartupDiagnostics.deps.jssur*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="5c3a9-303">Place le fichier *deploy.ps1* dans le dossier *deployment*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="5c3a9-304">Exécutez le script *deploy.ps1* du dossier *Deployment*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="5c3a9-305">Le script ajoute :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-305">The script appends:</span></span>
   * <span data-ttu-id="5c3a9-306">`StartupDiagnostics` à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="5c3a9-307">Chemin d’accès des dépendances de démarrage d’hébergement (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="5c3a9-308">Chemin d’accès du magasin du Runtime (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_SHARED_STORE` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="5c3a9-309">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-309">Run the sample app.</span></span>
1. <span data-ttu-id="5c3a9-310">Demandez le point de terminaison `/services` pour voir les services inscrits de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="5c3a9-311">Demandez le point de terminaison `/diag` pour voir les informations de diagnostic.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c3a9-312">Une <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implémentation (hébergement de démarrage) ajoute des améliorations à une application au démarrage à partir d’un assembly externe.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="5c3a9-313">Par exemple, une bibliothèque externe peut utiliser une implémentation d’hébergement au démarrage pour fournir des fournisseurs ou services de configuration supplémentaires à une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="5c3a9-314">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c3a9-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="5c3a9-315">Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="5c3a9-315">HostingStartup attribute</span></span>

<span data-ttu-id="5c3a9-316">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indique la présence d’un assembly d’hébergement au démarrage à activer au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="5c3a9-317">L’assembly d’entrée ou l’assembly contenant la classe `Startup` est automatiquement analysé pour détecter l’attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-318">La liste des assemblys où les attributs `HostingStartup` doivent être recherchés est chargée au moment de l’exécution à partir de la configuration spécifiée dans [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="5c3a9-319">La liste des assemblys à exclure de cette détection est chargée de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="5c3a9-320">Pour plus d’informations, consultez [Hôte web : Assemblys d’hébergement au démarrage](xref:fundamentals/host/web-host#hosting-startup-assemblies) et [Hôte web : Assemblys exclus de l’hébergement au démarrage](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="5c3a9-321">Dans l’exemple suivant, l’espace de noms de l’assembly d’hébergement au démarrage est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="5c3a9-322">La classe contenant le code d’hébergement au démarrage est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5c3a9-323">L’attribut `HostingStartup` se trouve généralement dans le fichier de classe d’implémentation `IHostingStartup` de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="5c3a9-324">Découvrir les assemblys d’hébergement au démarrage chargés</span><span class="sxs-lookup"><span data-stu-id="5c3a9-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="5c3a9-325">Pour détecter les assemblys d’hébergement au démarrage chargés, activez la journalisation et analysez les journaux de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="5c3a9-326">Les erreurs qui se produisent durant le chargement des assemblys sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="5c3a9-327">Les assemblys d’hébergement au démarrage chargés sont journalisés au niveau Débogage, et toutes les erreurs sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="5c3a9-328">Désactiver le chargement automatique des assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="5c3a9-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="5c3a9-329">Pour désactiver le chargement automatique des assemblys d’hébergement au démarrage, choisissez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="5c3a9-330">Pour bloquer le chargement de tous les assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sur `true` ou `1` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="5c3a9-331">Le paramètre de configuration d’hôte [PreventHostingStartup](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="5c3a9-332">La variable d’environnement `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="5c3a9-333">Pour bloquer le chargement de certains assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sous la forme d’une liste délimitée par des points-virgules contenant les assemblys d’hébergement au démarrage à exclure au moment du démarrage :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="5c3a9-334">Le paramètre de configuration d’hôte [HostingStartupExcludeAssemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="5c3a9-335">La variable d’environnement `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="5c3a9-336">Si le paramètre de configuration d’hôte et la variable d’environnement sont définis tous les deux, c’est le paramètre d’hôte qui détermine le comportement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="5c3a9-337">La désactivation des assemblys d’hébergement au démarrage à l’aide du paramètre d’hôte ou de la variable d’environnement désactive l’assembly globalement et peut donc désactiver plusieurs caractéristiques d’une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="5c3a9-338">Project</span><span class="sxs-lookup"><span data-stu-id="5c3a9-338">Project</span></span>

<span data-ttu-id="5c3a9-339">Créez un hébergement au démarrage avec un des types de projet suivants :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="5c3a9-340">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="5c3a9-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="5c3a9-341">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="5c3a9-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="5c3a9-342">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="5c3a9-342">Class library</span></span>

<span data-ttu-id="5c3a9-343">Une amélioration de l’hébergement au démarrage peut être fournie dans une bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="5c3a9-344">La bibliothèque contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5c3a9-345">L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) comprend une Razor application pages, *HostingStartupApp*et une bibliothèque de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="5c3a9-346">La bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-346">The class library:</span></span>

* <span data-ttu-id="5c3a9-347">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5c3a9-348">`ServiceKeyInjection` Ajoute une paire de chaînes de service à la configuration de l’application à l’aide du fournisseur de configuration en mémoire ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="5c3a9-349">Inclut un attribut `HostingStartup` qui identifie l’espace de noms et la classe d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="5c3a9-350">La `ServiceKeyInjection` méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="5c3a9-351">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5c3a9-352">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage de la bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="5c3a9-353">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="5c3a9-354">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclut également un projet de package NuGet qui fournit un hébergement au démarrage distinct, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="5c3a9-355">Le package a les mêmes caractéristiques que la bibliothèque de classes décrite précédemment.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="5c3a9-356">Le package :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-356">The package:</span></span>

* <span data-ttu-id="5c3a9-357">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="5c3a9-358">`ServiceKeyInjection` ajoute une paire de chaînes de service à la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="5c3a9-359">Inclut un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="5c3a9-360">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="5c3a9-361">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage du package :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="5c3a9-362">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="5c3a9-363">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="5c3a9-363">Console app without an entry point</span></span>

<span data-ttu-id="5c3a9-364">*Cette approche s’applique aux applications .NET Core, et non à .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="5c3a9-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="5c3a9-365">Une amélioration d’hébergement au démarrage dynamique qui ne nécessite pas de référence au moment de la compilation pour l’activation peut être fournie dans une application console sans point d’entrée qui contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-366">La publication de l’application console génère un assembly d’hébergement au démarrage qui peut être utilisé à partir du magasin de runtime.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="5c3a9-367">Une application console sans point d’entrée est utilisée dans ce processus, car :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="5c3a9-368">Un fichier de dépendances est nécessaire pour utiliser l’hébergement au démarrage dans l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="5c3a9-369">Un fichier de dépendances est une ressource d’application exécutable qui est générée par la publication d’une application, et non d’une bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="5c3a9-370">Une bibliothèque ne peut pas être ajoutée directement au [magasin de packages de runtime](/dotnet/core/deploying/runtime-store), qui nécessite un projet exécutable ciblant le runtime partagé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="5c3a9-371">Lors de la création d’un hébergement au démarrage dynamique :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="5c3a9-372">Un assembly d’hébergement au démarrage est créé à partir de l’application console sans point d’entrée qui :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="5c3a9-373">Inclut une classe qui contient l’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="5c3a9-374">Inclut un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pour identifier la classe d’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="5c3a9-375">L’application console est publiée pour obtenir les dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="5c3a9-376">La publication de l’application console entraîne la suppression des dépendances inutilisées dans le fichier de dépendances.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="5c3a9-377">Le fichier de dépendances est modifié pour définir l’emplacement d’exécution de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="5c3a9-378">L’assembly d’hébergement au démarrage et le fichier de dépendances associé sont placés dans le magasin de packages de runtime.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="5c3a9-379">Pour permettre leur détection, l’assembly d’hébergement au démarrage et le fichier de dépendances associé sont référencés dans une paire de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="5c3a9-380">L’application console référence le package [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="5c3a9-381">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifie une classe en tant qu’implémentation de `IHostingStartup` pour le chargement et l’exécution lors de la génération du <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="5c3a9-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="5c3a9-382">Dans l’exemple suivant, l’espace de noms est `StartupEnhancement`, et la classe est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="5c3a9-383">Une classe implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="5c3a9-384">La méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="5c3a9-385">`IHostingStartup.Configure` dans l’assembly d’hébergement au démarrage est appelé par le runtime avant `Startup.Configure` dans le code utilisateur, ce qui permet au code utilisateur de remplacer la configuration fournie par l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="5c3a9-386">Durant la génération d’un projet `IHostingStartup`, le fichier de dépendances (*. deps.json*) définit le dossier *bin* comme emplacement du `runtime` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="5c3a9-387">Seule une partie du fichier est affichée.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-387">Only part of the file is shown.</span></span> <span data-ttu-id="5c3a9-388">Le nom de l’assembly dans l’exemple est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="5c3a9-389">Configuration fournie par le démarrage d’hébergement</span><span class="sxs-lookup"><span data-stu-id="5c3a9-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="5c3a9-390">Il existe deux approches de gestion de la configuration selon si vous souhaitez que la configuration du démarrage d’hébergement ait la priorité ou que la configuration d’application ait la priorité :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="5c3a9-391">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pour charger la configuration après l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5c3a9-392">Avec cette approche, la configuration de démarrage d’hébergement est prioritaire sur la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="5c3a9-393">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pour charger la configuration avant l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="5c3a9-394">Avec cette approche, les valeurs de la configuration d’application sont prioritaires sur celles fournies par le démarrage d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="5c3a9-395">Spécifier l’assembly d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="5c3a9-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="5c3a9-396">Pour l’hébergement au démarrage fourni par une bibliothèque de classes ou une application console, spécifiez le nom de l’assembly d’hébergement au démarrage dans la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="5c3a9-397">Cette variable est spécifiée sous la forme d’une liste d’assemblys délimitée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="5c3a9-398">L’analyse de détection de l’attribut `HostingStartup` porte uniquement sur les assemblys d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-399">Dans l’exemple d’application (*HostingStartupApp*), pour permettre la détection des hébergements au démarrage décrits précédemment, la variable d’environnement est définie à la valeur suivante :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="5c3a9-400">Un assembly d’hébergement au démarrage peut également être défini à l’aide du paramètre de configuration d’hôte [HostingStartupAssemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="5c3a9-401">Lorsque plusieurs Assemblies de démarrage d’hébergement sont présents, leurs <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> méthodes sont exécutées dans l’ordre dans lequel les assemblys sont répertoriés.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="5c3a9-402">Activation</span><span class="sxs-lookup"><span data-stu-id="5c3a9-402">Activation</span></span>

<span data-ttu-id="5c3a9-403">Les options d’activation de l’hébergement au démarrage sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="5c3a9-404">[Magasin du runtime](#runtime-store): l’activation ne nécessite pas de référence au moment de la compilation pour l’activation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-404">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="5c3a9-405">L’exemple d’application place les fichiers de l’assembly d’hébergement au démarrage et de ses dépendances dans le dossier *deployment* pour faciliter le déploiement de l’hébergement au démarrage dans un environnement multimachine.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="5c3a9-406">Le dossier *deployment* inclut également un script PowerShell qui crée ou modifie des variables d’environnement sur le système de déploiement pour activer l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="5c3a9-407">Référence au moment de la compilation requise pour l’activation</span><span class="sxs-lookup"><span data-stu-id="5c3a9-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="5c3a9-408">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="5c3a9-409">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="5c3a9-410">Magasin de runtime</span><span class="sxs-lookup"><span data-stu-id="5c3a9-410">Runtime store</span></span>

<span data-ttu-id="5c3a9-411">L’implémentation d’hébergement au démarrage est placée dans le [magasin de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="5c3a9-412">L’application améliorée ne nécessite pas de référence à l’assembly au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="5c3a9-413">Une fois l’hébergement au démarrage créé, un magasin de runtime est généré à l’aide du fichier manifeste de projet et de la commande [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="5c3a9-414">Dans l’exemple d’application (projet *RuntimeStore*), la commande suivante est utilisée :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="5c3a9-415">Pour que le runtime découvre le magasin de runtime, l’emplacement du magasin de runtime est ajouté à la variable d’environnement `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="5c3a9-416">**Modifier et placer le fichier de dépendances de l’hébergement au démarrage**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="5c3a9-417">Pour activer l’amélioration sans référence de package à l’amélioration, spécifiez les dépendances supplémentaires pour le runtime avec `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="5c3a9-418">`additionalDeps` vous permet de :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="5c3a9-419">Étendre le graphe de la bibliothèque de l’application en fournissant un ensemble de fichiers *.deps.json* supplémentaires à fusionner avec le fichier *.deps.json* de l’application au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="5c3a9-420">Rendre l’assembly d’hébergement au démarrage détectable et chargeable.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="5c3a9-421">L’approche recommandée pour la génération du fichier de dépendances supplémentaire est la suivante :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="5c3a9-422">Exécutez `dotnet publish` sur le fichier manifeste du magasin de runtime référencé dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="5c3a9-423">Supprimez la référence de manifeste des bibliothèques et la `runtime` section du *.deps.jsrésultant sur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="5c3a9-424">Dans l’exemple de projet, la propriété `store.manifest/1.0.0` est supprimée de la section `targets` et de la section `libraries` :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="5c3a9-425">Placez le fichier *.deps.json* à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="5c3a9-426">`{ADDITIONAL DEPENDENCIES PATH}`: Emplacement ajouté à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-426">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="5c3a9-427">`{SHARED FRAMEWORK NAME}`: Framework partagé requis pour ce fichier de dépendances supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-427">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="5c3a9-428">`{SHARED FRAMEWORK VERSION}`: Version minimale du Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-428">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="5c3a9-429">`{ENHANCEMENT ASSEMBLY NAME}`: Le nom de l’assembly de l’amélioration.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-429">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="5c3a9-430">Dans l’exemple d’application (projet *RuntimeStore*), le fichier de dépendances supplémentaire est placé à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="5c3a9-431">Pour que le runtime découvre l’emplacement du magasin de runtime, l’emplacement du fichier de dépendances supplémentaire est ajouté à la variable d’environnement `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="5c3a9-432">Dans l’exemple d’application (projet *RuntimeStore*), la génération du magasin de runtime et du fichier de dépendances supplémentaire s’effectue à l’aide d’un script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="5c3a9-433">Pour obtenir des exemples montrant comment définir des variables d’environnement pour différents systèmes d’exploitation, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5c3a9-434">**Déploiement**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-434">**Deployment**</span></span>

<span data-ttu-id="5c3a9-435">Pour faciliter le déploiement d’un hébergement au démarrage dans un environnement multimachine, l’exemple d’application crée un dossier *deployment* dans la sortie publiée qui contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="5c3a9-436">Le magasin de runtime d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="5c3a9-437">Le fichier des dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="5c3a9-438">Un script PowerShell qui crée ou modifie les variables `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` et `DOTNET_ADDITIONAL_DEPS` pour prendre en charge l’activation de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="5c3a9-439">Exécutez ce script à partir d’une invite de commandes PowerShell d’administration sur le système de déploiement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="5c3a9-440">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-440">NuGet package</span></span>

<span data-ttu-id="5c3a9-441">Une amélioration de l’hébergement au démarrage peut être fournie dans un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="5c3a9-442">Le package a un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="5c3a9-443">Les types d’hébergement au démarrage fournis par le package sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="5c3a9-444">Le fichier projet de l’application améliorée crée une référence de package à l’hébergement au démarrage dans le fichier projet de l’application (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-445">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5c3a9-446">Cette approche s’applique à un package d’assembly d’hébergement au démarrage qui a été publié sur [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="5c3a9-447">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="5c3a9-448">Pour plus d’informations sur les packages NuGet et le magasin de runtime, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="5c3a9-449">Création d’un Package NuGet avec les outils multiplateformes</span><span class="sxs-lookup"><span data-stu-id="5c3a9-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="5c3a9-450">Publication de packages</span><span class="sxs-lookup"><span data-stu-id="5c3a9-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="5c3a9-451">Magasin de packages de runtime</span><span class="sxs-lookup"><span data-stu-id="5c3a9-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="5c3a9-452">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="5c3a9-452">Project bin folder</span></span>

<span data-ttu-id="5c3a9-453">Une amélioration de l’hébergement au démarrage peut être fournie par un assembly déployé à partir du dossier *bin* dans l’application améliorée.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="5c3a9-454">Les types d’hébergement au démarrage fournis par l’assembly sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="5c3a9-455">Le fichier projet de l’application améliorée crée une référence d’assembly à l’hébergement au démarrage (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-456">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="5c3a9-457">Cette approche s’applique lorsque le scénario de déploiement appelle dans le but d’effectuer une référence au moment de la compilation à l’assembly d’hébergement au démarrage (fichier *.dll*) et en déplaçant l’assembly vers :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="5c3a9-458">Le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-458">The consuming project.</span></span>
  * <span data-ttu-id="5c3a9-459">Un emplacement accessible par le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="5c3a9-460">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="5c3a9-461">Lors du ciblage de .NET Framework, l’assembly peut être chargé dans le contexte de charge par défaut, qui, sur .NET Framework, signifie que l’assembly se trouve à l’un des emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="5c3a9-462">Chemin de la base de l’application : dossier *bin* où se trouve l’exécutable de l’application (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-462">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="5c3a9-463">Global assembly cache (GAC) : le GAC stocke les assemblys partagés par plusieurs applications .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-463">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="5c3a9-464">Pour plus d’informations, consultez [Comment : installer un assembly dans le global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) dans la documentation de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="5c3a9-465">Exemple de code</span><span class="sxs-lookup"><span data-stu-id="5c3a9-465">Sample code</span></span>

<span data-ttu-id="5c3a9-466">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Comment télécharger un exemple](xref:index#how-to-download-a-sample)) montre des scénarios d’implémentation de l’hébergement au démarrage :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="5c3a9-467">Deux assemblys d’hébergement au démarrage (bibliothèques de classes) définissent chacun une paire clé-valeur de configuration en mémoire :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="5c3a9-468">Package NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="5c3a9-469">Bibliothèque de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="5c3a9-470">Un hébergement au démarrage est activé à partir d’un assembly déployé depuis le magasin de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="5c3a9-471">L’assembly ajoute deux middleware (intergiciels) à l’application au démarrage, qui fournissent des informations de diagnostic :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="5c3a9-472">Services inscrits</span><span class="sxs-lookup"><span data-stu-id="5c3a9-472">Registered services</span></span>
  * <span data-ttu-id="5c3a9-473">Adresse (schéma, hôte, chemin de base, chemin, chaîne de requête)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="5c3a9-474">Connexion (adresse IP distante, port distant, adresse IP locale, port local, certificat client)</span><span class="sxs-lookup"><span data-stu-id="5c3a9-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="5c3a9-475">En-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="5c3a9-475">Request headers</span></span>
  * <span data-ttu-id="5c3a9-476">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="5c3a9-476">Environment variables</span></span>

<span data-ttu-id="5c3a9-477">Pour exécuter l’exemple :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-477">To run the sample:</span></span>

<span data-ttu-id="5c3a9-478">**Activation à partir d’un package NuGet**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="5c3a9-479">Compilez le package *HostingStartupPackage* à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="5c3a9-480">Ajoutez le nom de l’assembly du package *HostingStartupPackage* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5c3a9-481">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-481">Compile and run the app.</span></span> <span data-ttu-id="5c3a9-482">Une référence de package est présente dans l’application améliorée (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-483">Un `<PropertyGroup>` dans le fichier projet de l’application spécifie la sortie du projet de package (*../HostingStartupPackage/bin/Debug*) comme source de package.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="5c3a9-484">Cela permet à l’application d’utiliser le package sans télécharger le package sur [NuGet.org](https://www.nuget.org/). Pour plus d’informations, consultez les notes dans le fichier projet de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="5c3a9-485">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` du package.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5c3a9-486">Si vous modifiez le projet *HostingStartupPackage* et le recompilez, effacez les caches locaux du package NuGet pour vous assurer que *HostingStartupApp* reçoit le package mis à jour et pas un package périmé du cache local.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="5c3a9-487">Pour effacer les caches NuGet locaux, exécutez la commande [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) suivante :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="5c3a9-488">**Activation à partir d’une bibliothèque de classes**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="5c3a9-489">Compilez la bibliothèque de classes *HostingStartupLibrary* à l’aide de la commande [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="5c3a9-490">Ajoutez le nom de l’assembly de la bibliothèque de classes *HostingStartupLibrary* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="5c3a9-491">À partir du dossier *bin*, déployez l’assembly de la bibliothèque de classes dans l’application en copiant le fichier *HostingStartupLibrary.dll* du résultat de la compilation de la bibliothèque de classes dans le dossier *bin/Debug* de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="5c3a9-492">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-492">Compile and run the app.</span></span> <span data-ttu-id="5c3a9-493">Dans le fichier projet de l’application, un `<ItemGroup>` référence l’assembly de la bibliothèque de classes (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="5c3a9-494">Pour plus d’informations, consultez les remarques dans le fichier projet de l’application HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="5c3a9-495">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="5c3a9-496">**Activation à partir d’un assembly déployé à partir du magasin de runtime**</span><span class="sxs-lookup"><span data-stu-id="5c3a9-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="5c3a9-497">Le projet *StartupDiagnostics* utilise [PowerShell](/powershell/scripting/powershell-scripting) pour modifier le fichier *StartupDiagnostics.deps.json* associé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="5c3a9-498">PowerShell est installé par défaut sur Windows à compter de Windows 7 SP1 et de Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="5c3a9-499">Pour obtenir PowerShell sur d’autres plateformes, consultez [installation de différentes versions de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="5c3a9-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="5c3a9-500">Exécutez le script *build.ps1* du dossier *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="5c3a9-501">Le script :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-501">The script:</span></span>
   * <span data-ttu-id="5c3a9-502">Génère le `StartupDiagnostics` package dans le dossier *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="5c3a9-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="5c3a9-503">Génère le magasin de runtime de `StartupDiagnostics` dans le dossier *store*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="5c3a9-504">La commande `dotnet store` du script utilise l’ [identificateur de runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` pour un hébergement au démarrage déployé sur Windows.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="5c3a9-505">Si vous fournissez l’hébergement au démarrage pour un autre runtime, spécifiez l’identificateur du runtime à la ligne 37 du script.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="5c3a9-506">Le magasin du runtime pour `StartupDiagnostics` serait déplacé ultérieurement vers la Banque d’exécution du système ou de l’utilisateur sur l’ordinateur sur lequel l’assembly sera consommé.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="5c3a9-507">L’emplacement d’installation du magasin d’exécution de l’utilisateur pour l' `StartupDiagnostics` assembly est *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="5c3a9-508">Génère le `additionalDeps` pour `StartupDiagnostics` dans le dossier *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="5c3a9-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="5c3a9-509">Les dépendances supplémentaires seraient ensuite déplacées vers les dépendances supplémentaires du système ou de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="5c3a9-510">L' `StartupDiagnostics` emplacement d’installation des dépendances supplémentaires de l’utilisateur est *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. Netcore. app/2.2.0/StartupDiagnostics.deps.jssur*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="5c3a9-511">Place le fichier *deploy.ps1* dans le dossier *deployment*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="5c3a9-512">Exécutez le script *deploy.ps1* du dossier *Deployment*.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="5c3a9-513">Le script ajoute :</span><span class="sxs-lookup"><span data-stu-id="5c3a9-513">The script appends:</span></span>
   * <span data-ttu-id="5c3a9-514">`StartupDiagnostics` à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="5c3a9-515">Chemin d’accès des dépendances de démarrage d’hébergement (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="5c3a9-516">Chemin d’accès du magasin du Runtime (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_SHARED_STORE` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="5c3a9-517">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-517">Run the sample app.</span></span>
1. <span data-ttu-id="5c3a9-518">Demandez le point de terminaison `/services` pour voir les services inscrits de l’application.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="5c3a9-519">Demandez le point de terminaison `/diag` pour voir les informations de diagnostic.</span><span class="sxs-lookup"><span data-stu-id="5c3a9-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
