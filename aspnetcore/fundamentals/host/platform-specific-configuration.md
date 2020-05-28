---
<span data-ttu-id="de9dc-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="de9dc-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="de9dc-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="de9dc-102">'Blazor'</span></span>
- <span data-ttu-id="de9dc-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="de9dc-103">'Identity'</span></span>
- <span data-ttu-id="de9dc-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="de9dc-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="de9dc-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="de9dc-105">'Razor'</span></span>
- <span data-ttu-id="de9dc-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="de9dc-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="de9dc-107">Utiliser des assemblys de démarrage d’hébergement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de9dc-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="de9dc-108">Par [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="de9dc-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="de9dc-109">Une <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implémentation (hébergement de démarrage) ajoute des améliorations à une application au démarrage à partir d’un assembly externe.</span><span class="sxs-lookup"><span data-stu-id="de9dc-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="de9dc-110">Par exemple, une bibliothèque externe peut utiliser une implémentation d’hébergement au démarrage pour fournir des fournisseurs ou services de configuration supplémentaires à une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="de9dc-111">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de9dc-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="de9dc-112">Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="de9dc-112">HostingStartup attribute</span></span>

<span data-ttu-id="de9dc-113">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indique la présence d’un assembly d’hébergement au démarrage à activer au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="de9dc-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="de9dc-114">L’assembly d’entrée ou l’assembly contenant la classe `Startup` est automatiquement analysé pour détecter l’attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-115">La liste des assemblys où les attributs `HostingStartup` doivent être recherchés est chargée au moment de l’exécution à partir de la configuration spécifiée dans [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="de9dc-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="de9dc-116">La liste des assemblys à exclure de cette détection est chargée de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="de9dc-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="de9dc-117">Dans l’exemple suivant, l’espace de noms de l’assembly d’hébergement au démarrage est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="de9dc-118">La classe contenant le code d’hébergement au démarrage est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="de9dc-119">L’attribut `HostingStartup` se trouve généralement dans le fichier de classe d’implémentation `IHostingStartup` de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="de9dc-120">Découvrir les assemblys d’hébergement au démarrage chargés</span><span class="sxs-lookup"><span data-stu-id="de9dc-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="de9dc-121">Pour détecter les assemblys d’hébergement au démarrage chargés, activez la journalisation et analysez les journaux de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="de9dc-122">Les erreurs qui se produisent durant le chargement des assemblys sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="de9dc-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="de9dc-123">Les assemblys d’hébergement au démarrage chargés sont journalisés au niveau Débogage, et toutes les erreurs sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="de9dc-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="de9dc-124">Désactiver le chargement automatique des assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="de9dc-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="de9dc-125">Pour désactiver le chargement automatique des assemblys d’hébergement au démarrage, choisissez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="de9dc-126">Pour bloquer le chargement de tous les assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sur `true` ou `1` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="de9dc-127">Empêcher l’hébergement des paramètres de configuration de l’hôte de démarrage :</span><span class="sxs-lookup"><span data-stu-id="de9dc-127">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="de9dc-128">La variable d’environnement `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="de9dc-129">Pour bloquer le chargement de certains assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sous la forme d’une liste délimitée par des points-virgules contenant les assemblys d’hébergement au démarrage à exclure au moment du démarrage :</span><span class="sxs-lookup"><span data-stu-id="de9dc-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="de9dc-130">Paramètre de configuration d’hôte d’exclusion des assemblys de démarrage d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="de9dc-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="de9dc-131">La variable d’environnement `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="de9dc-132">Si le paramètre de configuration d’hôte et la variable d’environnement sont définis tous les deux, c’est le paramètre d’hôte qui détermine le comportement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="de9dc-133">La désactivation des assemblys d’hébergement au démarrage à l’aide du paramètre d’hôte ou de la variable d’environnement désactive l’assembly globalement et peut donc désactiver plusieurs caractéristiques d’une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="de9dc-134">Project</span><span class="sxs-lookup"><span data-stu-id="de9dc-134">Project</span></span>

<span data-ttu-id="de9dc-135">Créez un hébergement au démarrage avec un des types de projet suivants :</span><span class="sxs-lookup"><span data-stu-id="de9dc-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="de9dc-136">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="de9dc-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="de9dc-137">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="de9dc-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="de9dc-138">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="de9dc-138">Class library</span></span>

<span data-ttu-id="de9dc-139">Une amélioration de l’hébergement au démarrage peut être fournie dans une bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="de9dc-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="de9dc-140">La bibliothèque contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="de9dc-141">L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) comprend une Razor application pages, *HostingStartupApp*et une bibliothèque de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="de9dc-142">La bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-142">The class library:</span></span>

* <span data-ttu-id="de9dc-143">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="de9dc-144">`ServiceKeyInjection`Ajoute une paire de chaînes de service à la configuration de l’application à l’aide du fournisseur de configuration en mémoire ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="de9dc-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="de9dc-145">Inclut un attribut `HostingStartup` qui identifie l’espace de noms et la classe d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="de9dc-146">La `ServiceKeyInjection` méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="de9dc-147">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="de9dc-148">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage de la bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="de9dc-149">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="de9dc-150">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclut également un projet de package NuGet qui fournit un hébergement au démarrage distinct, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="de9dc-151">Le package a les mêmes caractéristiques que la bibliothèque de classes décrite précédemment.</span><span class="sxs-lookup"><span data-stu-id="de9dc-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="de9dc-152">Le package :</span><span class="sxs-lookup"><span data-stu-id="de9dc-152">The package:</span></span>

* <span data-ttu-id="de9dc-153">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="de9dc-154">`ServiceKeyInjection` ajoute une paire de chaînes de service à la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="de9dc-155">Inclut un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="de9dc-156">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="de9dc-157">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage du package :</span><span class="sxs-lookup"><span data-stu-id="de9dc-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="de9dc-158">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="de9dc-159">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="de9dc-159">Console app without an entry point</span></span>

<span data-ttu-id="de9dc-160">*Cette approche s’applique aux applications .NET Core, et non à .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="de9dc-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="de9dc-161">Une amélioration d’hébergement au démarrage dynamique qui ne nécessite pas de référence au moment de la compilation pour l’activation peut être fournie dans une application console sans point d’entrée qui contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-162">La publication de l’application console génère un assembly d’hébergement au démarrage qui peut être utilisé à partir du magasin de runtime.</span><span class="sxs-lookup"><span data-stu-id="de9dc-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="de9dc-163">Une application console sans point d’entrée est utilisée dans ce processus, car :</span><span class="sxs-lookup"><span data-stu-id="de9dc-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="de9dc-164">Un fichier de dépendances est nécessaire pour utiliser l’hébergement au démarrage dans l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="de9dc-165">Un fichier de dépendances est une ressource d’application exécutable qui est générée par la publication d’une application, et non d’une bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="de9dc-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="de9dc-166">Une bibliothèque ne peut pas être ajoutée directement au [magasin de packages de runtime](/dotnet/core/deploying/runtime-store), qui nécessite un projet exécutable ciblant le runtime partagé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="de9dc-167">Lors de la création d’un hébergement au démarrage dynamique :</span><span class="sxs-lookup"><span data-stu-id="de9dc-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="de9dc-168">Un assembly d’hébergement au démarrage est créé à partir de l’application console sans point d’entrée qui :</span><span class="sxs-lookup"><span data-stu-id="de9dc-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="de9dc-169">Inclut une classe qui contient l’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="de9dc-170">Inclut un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pour identifier la classe d’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="de9dc-171">L’application console est publiée pour obtenir les dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="de9dc-172">La publication de l’application console entraîne la suppression des dépendances inutilisées dans le fichier de dépendances.</span><span class="sxs-lookup"><span data-stu-id="de9dc-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="de9dc-173">Le fichier de dépendances est modifié pour définir l’emplacement d’exécution de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="de9dc-174">L’assembly d’hébergement au démarrage et le fichier de dépendances associé sont placés dans le magasin de packages de runtime.</span><span class="sxs-lookup"><span data-stu-id="de9dc-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="de9dc-175">Pour permettre leur détection, l’assembly d’hébergement au démarrage et le fichier de dépendances associé sont référencés dans une paire de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="de9dc-176">L’application console référence le package [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="de9dc-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="de9dc-177">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifie une classe en tant qu’implémentation de `IHostingStartup` pour le chargement et l’exécution lors de la génération du <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="de9dc-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="de9dc-178">Dans l’exemple suivant, l’espace de noms est `StartupEnhancement`, et la classe est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="de9dc-179">Une classe implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="de9dc-180">La méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="de9dc-181">`IHostingStartup.Configure` dans l’assembly d’hébergement au démarrage est appelé par le runtime avant `Startup.Configure` dans le code utilisateur, ce qui permet au code utilisateur de remplacer la configuration fournie par l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="de9dc-182">Durant la génération d’un projet `IHostingStartup`, le fichier de dépendances (*. deps.json*) définit le dossier *bin* comme emplacement du `runtime` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="de9dc-183">Seule une partie du fichier est affichée.</span><span class="sxs-lookup"><span data-stu-id="de9dc-183">Only part of the file is shown.</span></span> <span data-ttu-id="de9dc-184">Le nom de l’assembly dans l’exemple est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="de9dc-185">Configuration fournie par le démarrage d’hébergement</span><span class="sxs-lookup"><span data-stu-id="de9dc-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="de9dc-186">Il existe deux approches de gestion de la configuration selon si vous souhaitez que la configuration du démarrage d’hébergement ait la priorité ou que la configuration d’application ait la priorité :</span><span class="sxs-lookup"><span data-stu-id="de9dc-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="de9dc-187">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pour charger la configuration après l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="de9dc-188">Avec cette approche, la configuration de démarrage d’hébergement est prioritaire sur la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="de9dc-189">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pour charger la configuration avant l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="de9dc-190">Avec cette approche, les valeurs de la configuration d’application sont prioritaires sur celles fournies par le démarrage d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="de9dc-191">Spécifier l’assembly d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="de9dc-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="de9dc-192">Pour l’hébergement au démarrage fourni par une bibliothèque de classes ou une application console, spécifiez le nom de l’assembly d’hébergement au démarrage dans la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="de9dc-193">Cette variable est spécifiée sous la forme d’une liste d’assemblys délimitée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="de9dc-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="de9dc-194">L’analyse de détection de l’attribut `HostingStartup` porte uniquement sur les assemblys d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-195">Dans l’exemple d’application (*HostingStartupApp*), pour permettre la détection des hébergements au démarrage décrits précédemment, la variable d’environnement est définie à la valeur suivante :</span><span class="sxs-lookup"><span data-stu-id="de9dc-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="de9dc-196">Un assembly de démarrage d’hébergement peut également être défini à l’aide du paramètre de configuration d’hôte assemblys de démarrage d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="de9dc-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="de9dc-197">Lorsque plusieurs Assemblies de démarrage d’hébergement sont présents, leurs <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> méthodes sont exécutées dans l’ordre dans lequel les assemblys sont répertoriés.</span><span class="sxs-lookup"><span data-stu-id="de9dc-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="de9dc-198">Activation</span><span class="sxs-lookup"><span data-stu-id="de9dc-198">Activation</span></span>

<span data-ttu-id="de9dc-199">Les options d’activation de l’hébergement au démarrage sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="de9dc-200">[Magasin du runtime](#runtime-store): l’activation ne nécessite pas de référence au moment de la compilation pour l’activation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="de9dc-201">L’exemple d’application place les fichiers de l’assembly d’hébergement au démarrage et de ses dépendances dans le dossier *deployment* pour faciliter le déploiement de l’hébergement au démarrage dans un environnement multimachine.</span><span class="sxs-lookup"><span data-stu-id="de9dc-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="de9dc-202">Le dossier *deployment* inclut également un script PowerShell qui crée ou modifie des variables d’environnement sur le système de déploiement pour activer l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="de9dc-203">Référence au moment de la compilation requise pour l’activation</span><span class="sxs-lookup"><span data-stu-id="de9dc-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="de9dc-204">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="de9dc-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="de9dc-205">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="de9dc-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="de9dc-206">Magasin de runtime</span><span class="sxs-lookup"><span data-stu-id="de9dc-206">Runtime store</span></span>

<span data-ttu-id="de9dc-207">L’implémentation d’hébergement au démarrage est placée dans le [magasin de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="de9dc-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="de9dc-208">L’application améliorée ne nécessite pas de référence à l’assembly au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="de9dc-209">Une fois l’hébergement au démarrage créé, un magasin de runtime est généré à l’aide du fichier manifeste de projet et de la commande [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="de9dc-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="de9dc-210">Dans l’exemple d’application (projet *RuntimeStore*), la commande suivante est utilisée :</span><span class="sxs-lookup"><span data-stu-id="de9dc-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="de9dc-211">Pour que le runtime découvre le magasin de runtime, l’emplacement du magasin de runtime est ajouté à la variable d’environnement `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="de9dc-212">**Modifier et placer le fichier de dépendances de l’hébergement au démarrage**</span><span class="sxs-lookup"><span data-stu-id="de9dc-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="de9dc-213">Pour activer l’amélioration sans référence de package à l’amélioration, spécifiez les dépendances supplémentaires pour le runtime avec `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="de9dc-214">`additionalDeps` vous permet de :</span><span class="sxs-lookup"><span data-stu-id="de9dc-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="de9dc-215">Étendre le graphe de la bibliothèque de l’application en fournissant un ensemble de fichiers *.deps.json* supplémentaires à fusionner avec le fichier *.deps.json* de l’application au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="de9dc-216">Rendre l’assembly d’hébergement au démarrage détectable et chargeable.</span><span class="sxs-lookup"><span data-stu-id="de9dc-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="de9dc-217">L’approche recommandée pour la génération du fichier de dépendances supplémentaire est la suivante :</span><span class="sxs-lookup"><span data-stu-id="de9dc-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="de9dc-218">Exécutez `dotnet publish` sur le fichier manifeste du magasin de runtime référencé dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="de9dc-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="de9dc-219">Supprimez la référence de manifeste des bibliothèques et la `runtime` section du fichier *. DEPS. JSON* résultant.</span><span class="sxs-lookup"><span data-stu-id="de9dc-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="de9dc-220">Dans l’exemple de projet, la propriété `store.manifest/1.0.0` est supprimée de la section `targets` et de la section `libraries` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="de9dc-221">Placez le fichier *.deps.json* à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="de9dc-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="de9dc-222">`{ADDITIONAL DEPENDENCIES PATH}`: Emplacement ajouté à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="de9dc-223">`{SHARED FRAMEWORK NAME}`: Framework partagé requis pour ce fichier de dépendances supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="de9dc-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="de9dc-224">`{SHARED FRAMEWORK VERSION}`: Version minimale du Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="de9dc-225">`{ENHANCEMENT ASSEMBLY NAME}`: Le nom de l’assembly de l’amélioration.</span><span class="sxs-lookup"><span data-stu-id="de9dc-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="de9dc-226">Dans l’exemple d’application (projet *RuntimeStore*), le fichier de dépendances supplémentaire est placé à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="de9dc-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="de9dc-227">Pour que le runtime découvre l’emplacement du magasin de runtime, l’emplacement du fichier de dépendances supplémentaire est ajouté à la variable d’environnement `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="de9dc-228">Dans l’exemple d’application (projet *RuntimeStore*), la génération du magasin de runtime et du fichier de dépendances supplémentaire s’effectue à l’aide d’un script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="de9dc-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="de9dc-229">Pour obtenir des exemples montrant comment définir des variables d’environnement pour différents systèmes d’exploitation, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="de9dc-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="de9dc-230">**Déploiement**</span><span class="sxs-lookup"><span data-stu-id="de9dc-230">**Deployment**</span></span>

<span data-ttu-id="de9dc-231">Pour faciliter le déploiement d’un hébergement au démarrage dans un environnement multimachine, l’exemple d’application crée un dossier *deployment* dans la sortie publiée qui contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="de9dc-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="de9dc-232">Le magasin de runtime d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="de9dc-233">Le fichier des dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="de9dc-234">Un script PowerShell qui crée ou modifie les variables `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` et `DOTNET_ADDITIONAL_DEPS` pour prendre en charge l’activation de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="de9dc-235">Exécutez ce script à partir d’une invite de commandes PowerShell d’administration sur le système de déploiement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="de9dc-236">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="de9dc-236">NuGet package</span></span>

<span data-ttu-id="de9dc-237">Une amélioration de l’hébergement au démarrage peut être fournie dans un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="de9dc-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="de9dc-238">Le package a un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-239">Les types d’hébergement au démarrage fournis par le package sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="de9dc-240">Le fichier projet de l’application améliorée crée une référence de package à l’hébergement au démarrage dans le fichier projet de l’application (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="de9dc-241">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="de9dc-242">Cette approche s’applique à un package d’assembly d’hébergement au démarrage qui a été publié sur [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="de9dc-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="de9dc-243">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="de9dc-244">Pour plus d’informations sur les packages NuGet et le magasin de runtime, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="de9dc-245">Création d’un Package NuGet avec les outils multiplateformes</span><span class="sxs-lookup"><span data-stu-id="de9dc-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="de9dc-246">Publication de packages</span><span class="sxs-lookup"><span data-stu-id="de9dc-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="de9dc-247">Magasin de packages de runtime</span><span class="sxs-lookup"><span data-stu-id="de9dc-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="de9dc-248">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="de9dc-248">Project bin folder</span></span>

<span data-ttu-id="de9dc-249">Une amélioration de l’hébergement au démarrage peut être fournie par un assembly déployé à partir du dossier *bin* dans l’application améliorée.</span><span class="sxs-lookup"><span data-stu-id="de9dc-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="de9dc-250">Les types d’hébergement au démarrage fournis par l’assembly sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="de9dc-251">Le fichier projet de l’application améliorée crée une référence d’assembly à l’hébergement au démarrage (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="de9dc-252">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="de9dc-253">Cette approche s’applique lorsque le scénario de déploiement appelle dans le but d’effectuer une référence au moment de la compilation à l’assembly d’hébergement au démarrage (fichier *.dll*) et en déplaçant l’assembly vers :</span><span class="sxs-lookup"><span data-stu-id="de9dc-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="de9dc-254">Le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-254">The consuming project.</span></span>
  * <span data-ttu-id="de9dc-255">Un emplacement accessible par le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="de9dc-256">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="de9dc-257">Lors du ciblage de .NET Framework, l’assembly peut être chargé dans le contexte de charge par défaut, qui, sur .NET Framework, signifie que l’assembly se trouve à l’un des emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="de9dc-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="de9dc-258">Chemin de la base de l’application : dossier *bin* où se trouve l’exécutable de l’application (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="de9dc-259">Global assembly cache (GAC) : le GAC stocke les assemblys partagés par plusieurs applications .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="de9dc-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="de9dc-260">Pour plus d’informations, consultez [Comment : installer un assembly dans le global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) dans la documentation de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="de9dc-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="de9dc-261">Exemple de code</span><span class="sxs-lookup"><span data-stu-id="de9dc-261">Sample code</span></span>

<span data-ttu-id="de9dc-262">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Comment télécharger un exemple](xref:index#how-to-download-a-sample)) montre des scénarios d’implémentation de l’hébergement au démarrage :</span><span class="sxs-lookup"><span data-stu-id="de9dc-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="de9dc-263">Deux assemblys d’hébergement au démarrage (bibliothèques de classes) définissent chacun une paire clé-valeur de configuration en mémoire :</span><span class="sxs-lookup"><span data-stu-id="de9dc-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="de9dc-264">Package NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="de9dc-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="de9dc-265">Bibliothèque de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="de9dc-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="de9dc-266">Un hébergement au démarrage est activé à partir d’un assembly déployé depuis le magasin de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="de9dc-267">L’assembly ajoute deux middleware (intergiciels) à l’application au démarrage, qui fournissent des informations de diagnostic :</span><span class="sxs-lookup"><span data-stu-id="de9dc-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="de9dc-268">Services inscrits</span><span class="sxs-lookup"><span data-stu-id="de9dc-268">Registered services</span></span>
  * <span data-ttu-id="de9dc-269">Adresse (schéma, hôte, chemin de base, chemin, chaîne de requête)</span><span class="sxs-lookup"><span data-stu-id="de9dc-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="de9dc-270">Connexion (adresse IP distante, port distant, adresse IP locale, port local, certificat client)</span><span class="sxs-lookup"><span data-stu-id="de9dc-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="de9dc-271">En-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="de9dc-271">Request headers</span></span>
  * <span data-ttu-id="de9dc-272">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="de9dc-272">Environment variables</span></span>

<span data-ttu-id="de9dc-273">Pour exécuter l’exemple :</span><span class="sxs-lookup"><span data-stu-id="de9dc-273">To run the sample:</span></span>

<span data-ttu-id="de9dc-274">**Activation à partir d’un package NuGet**</span><span class="sxs-lookup"><span data-stu-id="de9dc-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="de9dc-275">Compilez le package *HostingStartupPackage* à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="de9dc-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="de9dc-276">Ajoutez le nom de l’assembly du package *HostingStartupPackage* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="de9dc-277">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-277">Compile and run the app.</span></span> <span data-ttu-id="de9dc-278">Une référence de package est présente dans l’application améliorée (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="de9dc-279">Un `<PropertyGroup>` dans le fichier projet de l’application spécifie la sortie du projet de package (*../HostingStartupPackage/bin/Debug*) comme source de package.</span><span class="sxs-lookup"><span data-stu-id="de9dc-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="de9dc-280">Cela permet à l’application d’utiliser le package sans télécharger le package sur [NuGet.org](https://www.nuget.org/). Pour plus d’informations, consultez les notes dans le fichier projet de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="de9dc-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="de9dc-281">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` du package.</span><span class="sxs-lookup"><span data-stu-id="de9dc-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="de9dc-282">Si vous modifiez le projet *HostingStartupPackage* et le recompilez, effacez les caches locaux du package NuGet pour vous assurer que *HostingStartupApp* reçoit le package mis à jour et pas un package périmé du cache local.</span><span class="sxs-lookup"><span data-stu-id="de9dc-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="de9dc-283">Pour effacer les caches NuGet locaux, exécutez la commande [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) suivante :</span><span class="sxs-lookup"><span data-stu-id="de9dc-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="de9dc-284">**Activation à partir d’une bibliothèque de classes**</span><span class="sxs-lookup"><span data-stu-id="de9dc-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="de9dc-285">Compilez la bibliothèque de classes *HostingStartupLibrary* à l’aide de la commande [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="de9dc-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="de9dc-286">Ajoutez le nom de l’assembly de la bibliothèque de classes *HostingStartupLibrary* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="de9dc-287">À partir du dossier *bin*, déployez l’assembly de la bibliothèque de classes dans l’application en copiant le fichier *HostingStartupLibrary.dll* du résultat de la compilation de la bibliothèque de classes dans le dossier *bin/Debug* de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="de9dc-288">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-288">Compile and run the app.</span></span> <span data-ttu-id="de9dc-289">Un `<ItemGroup>` dans le fichier projet de l’application fait référence à l’assembly de la bibliothèque de classes (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (une référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="de9dc-290">Pour plus d’informations, consultez les remarques dans le fichier projet de l’application HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="de9dc-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="de9dc-291">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="de9dc-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="de9dc-292">**Activation à partir d’un assembly déployé à partir du magasin de runtime**</span><span class="sxs-lookup"><span data-stu-id="de9dc-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="de9dc-293">Le projet *StartupDiagnostics* utilise [PowerShell](/powershell/scripting/powershell-scripting) pour modifier le fichier *StartupDiagnostics.deps.json* associé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="de9dc-294">PowerShell est installé par défaut sur Windows à compter de Windows 7 SP1 et de Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="de9dc-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="de9dc-295">Pour obtenir PowerShell sur d’autres plateformes, consultez [installation de différentes versions de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="de9dc-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="de9dc-296">Exécutez le script *build.ps1* du dossier *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="de9dc-297">Le script :</span><span class="sxs-lookup"><span data-stu-id="de9dc-297">The script:</span></span>
   * <span data-ttu-id="de9dc-298">Génère le `StartupDiagnostics` package dans le dossier *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="de9dc-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="de9dc-299">Génère le magasin de runtime de `StartupDiagnostics` dans le dossier *store*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="de9dc-300">La commande `dotnet store` du script utilise l’ [identificateur de runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` pour un hébergement au démarrage déployé sur Windows.</span><span class="sxs-lookup"><span data-stu-id="de9dc-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="de9dc-301">Si vous fournissez l’hébergement au démarrage pour un autre runtime, spécifiez l’identificateur du runtime à la ligne 37 du script.</span><span class="sxs-lookup"><span data-stu-id="de9dc-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="de9dc-302">Le magasin du runtime pour `StartupDiagnostics` serait déplacé ultérieurement vers la Banque d’exécution du système ou de l’utilisateur sur l’ordinateur sur lequel l’assembly sera consommé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="de9dc-303">L’emplacement d’installation du magasin d’exécution de l’utilisateur pour l' `StartupDiagnostics` assembly est *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="de9dc-304">Génère le `additionalDeps` pour `StartupDiagnostics` dans le dossier *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="de9dc-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="de9dc-305">Les dépendances supplémentaires seraient ensuite déplacées vers les dépendances supplémentaires du système ou de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="de9dc-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="de9dc-306">L' `StartupDiagnostics` emplacement d’installation des dépendances supplémentaires de l’utilisateur est *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. Netcore. app/3.0.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="de9dc-307">Place le fichier *deploy.ps1* dans le dossier *deployment*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="de9dc-308">Exécutez le script *deploy.ps1* du dossier *Deployment*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="de9dc-309">Le script ajoute :</span><span class="sxs-lookup"><span data-stu-id="de9dc-309">The script appends:</span></span>
   * <span data-ttu-id="de9dc-310">`StartupDiagnostics` à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="de9dc-311">Chemin d’accès des dépendances de démarrage d’hébergement (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="de9dc-312">Chemin d’accès du magasin du Runtime (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_SHARED_STORE` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="de9dc-313">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-313">Run the sample app.</span></span>
1. <span data-ttu-id="de9dc-314">Demandez le point de terminaison `/services` pour voir les services inscrits de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="de9dc-315">Demandez le point de terminaison `/diag` pour voir les informations de diagnostic.</span><span class="sxs-lookup"><span data-stu-id="de9dc-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="de9dc-316">Une <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implémentation (hébergement de démarrage) ajoute des améliorations à une application au démarrage à partir d’un assembly externe.</span><span class="sxs-lookup"><span data-stu-id="de9dc-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="de9dc-317">Par exemple, une bibliothèque externe peut utiliser une implémentation d’hébergement au démarrage pour fournir des fournisseurs ou services de configuration supplémentaires à une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="de9dc-318">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de9dc-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="de9dc-319">Attribut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="de9dc-319">HostingStartup attribute</span></span>

<span data-ttu-id="de9dc-320">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indique la présence d’un assembly d’hébergement au démarrage à activer au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="de9dc-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="de9dc-321">L’assembly d’entrée ou l’assembly contenant la classe `Startup` est automatiquement analysé pour détecter l’attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-322">La liste des assemblys où les attributs `HostingStartup` doivent être recherchés est chargée au moment de l’exécution à partir de la configuration spécifiée dans [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="de9dc-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="de9dc-323">La liste des assemblys à exclure de cette détection est chargée de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="de9dc-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="de9dc-324">Pour plus d’informations, consultez [Hôte web : Assemblys d’hébergement au démarrage](xref:fundamentals/host/web-host#hosting-startup-assemblies) et [Hôte web : Assemblys exclus de l’hébergement au démarrage](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="de9dc-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="de9dc-325">Dans l’exemple suivant, l’espace de noms de l’assembly d’hébergement au démarrage est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="de9dc-326">La classe contenant le code d’hébergement au démarrage est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="de9dc-327">L’attribut `HostingStartup` se trouve généralement dans le fichier de classe d’implémentation `IHostingStartup` de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="de9dc-328">Découvrir les assemblys d’hébergement au démarrage chargés</span><span class="sxs-lookup"><span data-stu-id="de9dc-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="de9dc-329">Pour détecter les assemblys d’hébergement au démarrage chargés, activez la journalisation et analysez les journaux de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="de9dc-330">Les erreurs qui se produisent durant le chargement des assemblys sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="de9dc-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="de9dc-331">Les assemblys d’hébergement au démarrage chargés sont journalisés au niveau Débogage, et toutes les erreurs sont journalisées.</span><span class="sxs-lookup"><span data-stu-id="de9dc-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="de9dc-332">Désactiver le chargement automatique des assemblys d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="de9dc-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="de9dc-333">Pour désactiver le chargement automatique des assemblys d’hébergement au démarrage, choisissez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="de9dc-334">Pour bloquer le chargement de tous les assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sur `true` ou `1` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="de9dc-335">Le paramètre de configuration d’hôte [PreventHostingStartup](xref:fundamentals/host/web-host#prevent-hosting-startup).</span><span class="sxs-lookup"><span data-stu-id="de9dc-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="de9dc-336">La variable d’environnement `ASPNETCORE_PREVENTHOSTINGSTARTUP`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="de9dc-337">Pour bloquer le chargement de certains assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sous la forme d’une liste délimitée par des points-virgules contenant les assemblys d’hébergement au démarrage à exclure au moment du démarrage :</span><span class="sxs-lookup"><span data-stu-id="de9dc-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="de9dc-338">Le paramètre de configuration d’hôte [HostingStartupExcludeAssemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="de9dc-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="de9dc-339">La variable d’environnement `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="de9dc-340">Si le paramètre de configuration d’hôte et la variable d’environnement sont définis tous les deux, c’est le paramètre d’hôte qui détermine le comportement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="de9dc-341">La désactivation des assemblys d’hébergement au démarrage à l’aide du paramètre d’hôte ou de la variable d’environnement désactive l’assembly globalement et peut donc désactiver plusieurs caractéristiques d’une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="de9dc-342">Project</span><span class="sxs-lookup"><span data-stu-id="de9dc-342">Project</span></span>

<span data-ttu-id="de9dc-343">Créez un hébergement au démarrage avec un des types de projet suivants :</span><span class="sxs-lookup"><span data-stu-id="de9dc-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="de9dc-344">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="de9dc-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="de9dc-345">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="de9dc-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="de9dc-346">Bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="de9dc-346">Class library</span></span>

<span data-ttu-id="de9dc-347">Une amélioration de l’hébergement au démarrage peut être fournie dans une bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="de9dc-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="de9dc-348">La bibliothèque contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="de9dc-349">L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) comprend une Razor application pages, *HostingStartupApp*et une bibliothèque de classes, *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="de9dc-350">La bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-350">The class library:</span></span>

* <span data-ttu-id="de9dc-351">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="de9dc-352">`ServiceKeyInjection`Ajoute une paire de chaînes de service à la configuration de l’application à l’aide du fournisseur de configuration en mémoire ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="de9dc-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="de9dc-353">Inclut un attribut `HostingStartup` qui identifie l’espace de noms et la classe d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="de9dc-354">La `ServiceKeyInjection` méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="de9dc-355">*HostingStartupLibrary/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="de9dc-356">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage de la bibliothèque de classes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="de9dc-357">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="de9dc-358">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclut également un projet de package NuGet qui fournit un hébergement au démarrage distinct, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="de9dc-359">Le package a les mêmes caractéristiques que la bibliothèque de classes décrite précédemment.</span><span class="sxs-lookup"><span data-stu-id="de9dc-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="de9dc-360">Le package :</span><span class="sxs-lookup"><span data-stu-id="de9dc-360">The package:</span></span>

* <span data-ttu-id="de9dc-361">Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="de9dc-362">`ServiceKeyInjection` ajoute une paire de chaînes de service à la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="de9dc-363">Inclut un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="de9dc-364">*HostingStartupPackage/ServiceKeyInjection.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="de9dc-365">La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage du package :</span><span class="sxs-lookup"><span data-stu-id="de9dc-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="de9dc-366">*HostingStartupApp/Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="de9dc-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="de9dc-367">Application console sans point d’entrée</span><span class="sxs-lookup"><span data-stu-id="de9dc-367">Console app without an entry point</span></span>

<span data-ttu-id="de9dc-368">*Cette approche s’applique aux applications .NET Core, et non à .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="de9dc-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="de9dc-369">Une amélioration d’hébergement au démarrage dynamique qui ne nécessite pas de référence au moment de la compilation pour l’activation peut être fournie dans une application console sans point d’entrée qui contient un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-370">La publication de l’application console génère un assembly d’hébergement au démarrage qui peut être utilisé à partir du magasin de runtime.</span><span class="sxs-lookup"><span data-stu-id="de9dc-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="de9dc-371">Une application console sans point d’entrée est utilisée dans ce processus, car :</span><span class="sxs-lookup"><span data-stu-id="de9dc-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="de9dc-372">Un fichier de dépendances est nécessaire pour utiliser l’hébergement au démarrage dans l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="de9dc-373">Un fichier de dépendances est une ressource d’application exécutable qui est générée par la publication d’une application, et non d’une bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="de9dc-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="de9dc-374">Une bibliothèque ne peut pas être ajoutée directement au [magasin de packages de runtime](/dotnet/core/deploying/runtime-store), qui nécessite un projet exécutable ciblant le runtime partagé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="de9dc-375">Lors de la création d’un hébergement au démarrage dynamique :</span><span class="sxs-lookup"><span data-stu-id="de9dc-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="de9dc-376">Un assembly d’hébergement au démarrage est créé à partir de l’application console sans point d’entrée qui :</span><span class="sxs-lookup"><span data-stu-id="de9dc-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="de9dc-377">Inclut une classe qui contient l’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="de9dc-378">Inclut un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pour identifier la classe d’implémentation `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="de9dc-379">L’application console est publiée pour obtenir les dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="de9dc-380">La publication de l’application console entraîne la suppression des dépendances inutilisées dans le fichier de dépendances.</span><span class="sxs-lookup"><span data-stu-id="de9dc-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="de9dc-381">Le fichier de dépendances est modifié pour définir l’emplacement d’exécution de l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="de9dc-382">L’assembly d’hébergement au démarrage et le fichier de dépendances associé sont placés dans le magasin de packages de runtime.</span><span class="sxs-lookup"><span data-stu-id="de9dc-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="de9dc-383">Pour permettre leur détection, l’assembly d’hébergement au démarrage et le fichier de dépendances associé sont référencés dans une paire de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="de9dc-384">L’application console référence le package [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="de9dc-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="de9dc-385">Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifie une classe en tant qu’implémentation de `IHostingStartup` pour le chargement et l’exécution lors de la génération du <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="de9dc-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="de9dc-386">Dans l’exemple suivant, l’espace de noms est `StartupEnhancement`, et la classe est `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="de9dc-387">Une classe implémente `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="de9dc-388">La méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="de9dc-389">`IHostingStartup.Configure` dans l’assembly d’hébergement au démarrage est appelé par le runtime avant `Startup.Configure` dans le code utilisateur, ce qui permet au code utilisateur de remplacer la configuration fournie par l’assembly d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="de9dc-390">Durant la génération d’un projet `IHostingStartup`, le fichier de dépendances (*. deps.json*) définit le dossier *bin* comme emplacement du `runtime` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="de9dc-391">Seule une partie du fichier est affichée.</span><span class="sxs-lookup"><span data-stu-id="de9dc-391">Only part of the file is shown.</span></span> <span data-ttu-id="de9dc-392">Le nom de l’assembly dans l’exemple est `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="de9dc-393">Configuration fournie par le démarrage d’hébergement</span><span class="sxs-lookup"><span data-stu-id="de9dc-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="de9dc-394">Il existe deux approches de gestion de la configuration selon si vous souhaitez que la configuration du démarrage d’hébergement ait la priorité ou que la configuration d’application ait la priorité :</span><span class="sxs-lookup"><span data-stu-id="de9dc-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="de9dc-395">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pour charger la configuration après l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="de9dc-396">Avec cette approche, la configuration de démarrage d’hébergement est prioritaire sur la configuration d’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="de9dc-397">Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pour charger la configuration avant l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="de9dc-398">Avec cette approche, les valeurs de la configuration d’application sont prioritaires sur celles fournies par le démarrage d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="de9dc-399">Spécifier l’assembly d’hébergement au démarrage</span><span class="sxs-lookup"><span data-stu-id="de9dc-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="de9dc-400">Pour l’hébergement au démarrage fourni par une bibliothèque de classes ou une application console, spécifiez le nom de l’assembly d’hébergement au démarrage dans la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="de9dc-401">Cette variable est spécifiée sous la forme d’une liste d’assemblys délimitée par des points-virgules.</span><span class="sxs-lookup"><span data-stu-id="de9dc-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="de9dc-402">L’analyse de détection de l’attribut `HostingStartup` porte uniquement sur les assemblys d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-403">Dans l’exemple d’application (*HostingStartupApp*), pour permettre la détection des hébergements au démarrage décrits précédemment, la variable d’environnement est définie à la valeur suivante :</span><span class="sxs-lookup"><span data-stu-id="de9dc-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="de9dc-404">Un assembly d’hébergement au démarrage peut également être défini à l’aide du paramètre de configuration d’hôte [HostingStartupAssemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies).</span><span class="sxs-lookup"><span data-stu-id="de9dc-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="de9dc-405">Lorsque plusieurs Assemblies de démarrage d’hébergement sont présents, leurs <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> méthodes sont exécutées dans l’ordre dans lequel les assemblys sont répertoriés.</span><span class="sxs-lookup"><span data-stu-id="de9dc-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="de9dc-406">Activation</span><span class="sxs-lookup"><span data-stu-id="de9dc-406">Activation</span></span>

<span data-ttu-id="de9dc-407">Les options d’activation de l’hébergement au démarrage sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="de9dc-408">[Magasin du runtime](#runtime-store): l’activation ne nécessite pas de référence au moment de la compilation pour l’activation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="de9dc-409">L’exemple d’application place les fichiers de l’assembly d’hébergement au démarrage et de ses dépendances dans le dossier *deployment* pour faciliter le déploiement de l’hébergement au démarrage dans un environnement multimachine.</span><span class="sxs-lookup"><span data-stu-id="de9dc-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="de9dc-410">Le dossier *deployment* inclut également un script PowerShell qui crée ou modifie des variables d’environnement sur le système de déploiement pour activer l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="de9dc-411">Référence au moment de la compilation requise pour l’activation</span><span class="sxs-lookup"><span data-stu-id="de9dc-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="de9dc-412">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="de9dc-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="de9dc-413">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="de9dc-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="de9dc-414">Magasin de runtime</span><span class="sxs-lookup"><span data-stu-id="de9dc-414">Runtime store</span></span>

<span data-ttu-id="de9dc-415">L’implémentation d’hébergement au démarrage est placée dans le [magasin de runtime](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="de9dc-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="de9dc-416">L’application améliorée ne nécessite pas de référence à l’assembly au moment de la compilation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="de9dc-417">Une fois l’hébergement au démarrage créé, un magasin de runtime est généré à l’aide du fichier manifeste de projet et de la commande [dotnet store](/dotnet/core/tools/dotnet-store).</span><span class="sxs-lookup"><span data-stu-id="de9dc-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="de9dc-418">Dans l’exemple d’application (projet *RuntimeStore*), la commande suivante est utilisée :</span><span class="sxs-lookup"><span data-stu-id="de9dc-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="de9dc-419">Pour que le runtime découvre le magasin de runtime, l’emplacement du magasin de runtime est ajouté à la variable d’environnement `DOTNET_SHARED_STORE`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="de9dc-420">**Modifier et placer le fichier de dépendances de l’hébergement au démarrage**</span><span class="sxs-lookup"><span data-stu-id="de9dc-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="de9dc-421">Pour activer l’amélioration sans référence de package à l’amélioration, spécifiez les dépendances supplémentaires pour le runtime avec `additionalDeps`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="de9dc-422">`additionalDeps` vous permet de :</span><span class="sxs-lookup"><span data-stu-id="de9dc-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="de9dc-423">Étendre le graphe de la bibliothèque de l’application en fournissant un ensemble de fichiers *.deps.json* supplémentaires à fusionner avec le fichier *.deps.json* de l’application au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="de9dc-424">Rendre l’assembly d’hébergement au démarrage détectable et chargeable.</span><span class="sxs-lookup"><span data-stu-id="de9dc-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="de9dc-425">L’approche recommandée pour la génération du fichier de dépendances supplémentaire est la suivante :</span><span class="sxs-lookup"><span data-stu-id="de9dc-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="de9dc-426">Exécutez `dotnet publish` sur le fichier manifeste du magasin de runtime référencé dans la section précédente.</span><span class="sxs-lookup"><span data-stu-id="de9dc-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="de9dc-427">Supprimez la référence de manifeste des bibliothèques et la `runtime` section du fichier *. DEPS. JSON* résultant.</span><span class="sxs-lookup"><span data-stu-id="de9dc-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="de9dc-428">Dans l’exemple de projet, la propriété `store.manifest/1.0.0` est supprimée de la section `targets` et de la section `libraries` :</span><span class="sxs-lookup"><span data-stu-id="de9dc-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="de9dc-429">Placez le fichier *.deps.json* à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="de9dc-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="de9dc-430">`{ADDITIONAL DEPENDENCIES PATH}`: Emplacement ajouté à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="de9dc-431">`{SHARED FRAMEWORK NAME}`: Framework partagé requis pour ce fichier de dépendances supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="de9dc-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="de9dc-432">`{SHARED FRAMEWORK VERSION}`: Version minimale du Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="de9dc-433">`{ENHANCEMENT ASSEMBLY NAME}`: Le nom de l’assembly de l’amélioration.</span><span class="sxs-lookup"><span data-stu-id="de9dc-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="de9dc-434">Dans l’exemple d’application (projet *RuntimeStore*), le fichier de dépendances supplémentaire est placé à l’emplacement suivant :</span><span class="sxs-lookup"><span data-stu-id="de9dc-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="de9dc-435">Pour que le runtime découvre l’emplacement du magasin de runtime, l’emplacement du fichier de dépendances supplémentaire est ajouté à la variable d’environnement `DOTNET_ADDITIONAL_DEPS`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="de9dc-436">Dans l’exemple d’application (projet *RuntimeStore*), la génération du magasin de runtime et du fichier de dépendances supplémentaire s’effectue à l’aide d’un script [PowerShell](/powershell/scripting/powershell-scripting).</span><span class="sxs-lookup"><span data-stu-id="de9dc-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="de9dc-437">Pour obtenir des exemples montrant comment définir des variables d’environnement pour différents systèmes d’exploitation, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="de9dc-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="de9dc-438">**Déploiement**</span><span class="sxs-lookup"><span data-stu-id="de9dc-438">**Deployment**</span></span>

<span data-ttu-id="de9dc-439">Pour faciliter le déploiement d’un hébergement au démarrage dans un environnement multimachine, l’exemple d’application crée un dossier *deployment* dans la sortie publiée qui contient les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="de9dc-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="de9dc-440">Le magasin de runtime d’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="de9dc-441">Le fichier des dépendances de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="de9dc-442">Un script PowerShell qui crée ou modifie les variables `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` et `DOTNET_ADDITIONAL_DEPS` pour prendre en charge l’activation de l’hébergement au démarrage.</span><span class="sxs-lookup"><span data-stu-id="de9dc-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="de9dc-443">Exécutez ce script à partir d’une invite de commandes PowerShell d’administration sur le système de déploiement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="de9dc-444">Package NuGet</span><span class="sxs-lookup"><span data-stu-id="de9dc-444">NuGet package</span></span>

<span data-ttu-id="de9dc-445">Une amélioration de l’hébergement au démarrage peut être fournie dans un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="de9dc-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="de9dc-446">Le package a un attribut `HostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="de9dc-447">Les types d’hébergement au démarrage fournis par le package sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="de9dc-448">Le fichier projet de l’application améliorée crée une référence de package à l’hébergement au démarrage dans le fichier projet de l’application (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="de9dc-449">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="de9dc-450">Cette approche s’applique à un package d’assembly d’hébergement au démarrage qui a été publié sur [nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="de9dc-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="de9dc-451">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="de9dc-452">Pour plus d’informations sur les packages NuGet et le magasin de runtime, consultez les rubriques suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="de9dc-453">Création d’un Package NuGet avec les outils multiplateformes</span><span class="sxs-lookup"><span data-stu-id="de9dc-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="de9dc-454">Publication de packages</span><span class="sxs-lookup"><span data-stu-id="de9dc-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="de9dc-455">Magasin de packages de runtime</span><span class="sxs-lookup"><span data-stu-id="de9dc-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="de9dc-456">Dossier bin du projet</span><span class="sxs-lookup"><span data-stu-id="de9dc-456">Project bin folder</span></span>

<span data-ttu-id="de9dc-457">Une amélioration de l’hébergement au démarrage peut être fournie par un assembly déployé à partir du dossier *bin* dans l’application améliorée.</span><span class="sxs-lookup"><span data-stu-id="de9dc-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="de9dc-458">Les types d’hébergement au démarrage fournis par l’assembly sont mis à la disposition de l’application au moyen de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="de9dc-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="de9dc-459">Le fichier projet de l’application améliorée crée une référence d’assembly à l’hébergement au démarrage (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="de9dc-460">Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="de9dc-461">Cette approche s’applique lorsque le scénario de déploiement appelle dans le but d’effectuer une référence au moment de la compilation à l’assembly d’hébergement au démarrage (fichier *.dll*) et en déplaçant l’assembly vers :</span><span class="sxs-lookup"><span data-stu-id="de9dc-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="de9dc-462">Le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-462">The consuming project.</span></span>
  * <span data-ttu-id="de9dc-463">Un emplacement accessible par le projet de consommation.</span><span class="sxs-lookup"><span data-stu-id="de9dc-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="de9dc-464">Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="de9dc-465">Lors du ciblage de .NET Framework, l’assembly peut être chargé dans le contexte de charge par défaut, qui, sur .NET Framework, signifie que l’assembly se trouve à l’un des emplacements suivants :</span><span class="sxs-lookup"><span data-stu-id="de9dc-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="de9dc-466">Chemin de la base de l’application : dossier *bin* où se trouve l’exécutable de l’application (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="de9dc-467">Global assembly cache (GAC) : le GAC stocke les assemblys partagés par plusieurs applications .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="de9dc-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="de9dc-468">Pour plus d’informations, consultez [Comment : installer un assembly dans le global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) dans la documentation de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="de9dc-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="de9dc-469">Exemple de code</span><span class="sxs-lookup"><span data-stu-id="de9dc-469">Sample code</span></span>

<span data-ttu-id="de9dc-470">[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Comment télécharger un exemple](xref:index#how-to-download-a-sample)) montre des scénarios d’implémentation de l’hébergement au démarrage :</span><span class="sxs-lookup"><span data-stu-id="de9dc-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="de9dc-471">Deux assemblys d’hébergement au démarrage (bibliothèques de classes) définissent chacun une paire clé-valeur de configuration en mémoire :</span><span class="sxs-lookup"><span data-stu-id="de9dc-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="de9dc-472">Package NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="de9dc-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="de9dc-473">Bibliothèque de classes (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="de9dc-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="de9dc-474">Un hébergement au démarrage est activé à partir d’un assembly déployé depuis le magasin de runtime (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="de9dc-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="de9dc-475">L’assembly ajoute deux middleware (intergiciels) à l’application au démarrage, qui fournissent des informations de diagnostic :</span><span class="sxs-lookup"><span data-stu-id="de9dc-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="de9dc-476">Services inscrits</span><span class="sxs-lookup"><span data-stu-id="de9dc-476">Registered services</span></span>
  * <span data-ttu-id="de9dc-477">Adresse (schéma, hôte, chemin de base, chemin, chaîne de requête)</span><span class="sxs-lookup"><span data-stu-id="de9dc-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="de9dc-478">Connexion (adresse IP distante, port distant, adresse IP locale, port local, certificat client)</span><span class="sxs-lookup"><span data-stu-id="de9dc-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="de9dc-479">En-têtes de requête</span><span class="sxs-lookup"><span data-stu-id="de9dc-479">Request headers</span></span>
  * <span data-ttu-id="de9dc-480">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="de9dc-480">Environment variables</span></span>

<span data-ttu-id="de9dc-481">Pour exécuter l’exemple :</span><span class="sxs-lookup"><span data-stu-id="de9dc-481">To run the sample:</span></span>

<span data-ttu-id="de9dc-482">**Activation à partir d’un package NuGet**</span><span class="sxs-lookup"><span data-stu-id="de9dc-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="de9dc-483">Compilez le package *HostingStartupPackage* à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack).</span><span class="sxs-lookup"><span data-stu-id="de9dc-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="de9dc-484">Ajoutez le nom de l’assembly du package *HostingStartupPackage* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="de9dc-485">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-485">Compile and run the app.</span></span> <span data-ttu-id="de9dc-486">Une référence de package est présente dans l’application améliorée (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="de9dc-487">Un `<PropertyGroup>` dans le fichier projet de l’application spécifie la sortie du projet de package (*../HostingStartupPackage/bin/Debug*) comme source de package.</span><span class="sxs-lookup"><span data-stu-id="de9dc-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="de9dc-488">Cela permet à l’application d’utiliser le package sans télécharger le package sur [NuGet.org](https://www.nuget.org/). Pour plus d’informations, consultez les notes dans le fichier projet de HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="de9dc-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="de9dc-489">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` du package.</span><span class="sxs-lookup"><span data-stu-id="de9dc-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="de9dc-490">Si vous modifiez le projet *HostingStartupPackage* et le recompilez, effacez les caches locaux du package NuGet pour vous assurer que *HostingStartupApp* reçoit le package mis à jour et pas un package périmé du cache local.</span><span class="sxs-lookup"><span data-stu-id="de9dc-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="de9dc-491">Pour effacer les caches NuGet locaux, exécutez la commande [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) suivante :</span><span class="sxs-lookup"><span data-stu-id="de9dc-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="de9dc-492">**Activation à partir d’une bibliothèque de classes**</span><span class="sxs-lookup"><span data-stu-id="de9dc-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="de9dc-493">Compilez la bibliothèque de classes *HostingStartupLibrary* à l’aide de la commande [dotnet build](/dotnet/core/tools/dotnet-build).</span><span class="sxs-lookup"><span data-stu-id="de9dc-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="de9dc-494">Ajoutez le nom de l’assembly de la bibliothèque de classes *HostingStartupLibrary* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="de9dc-495">À partir du dossier *bin*, déployez l’assembly de la bibliothèque de classes dans l’application en copiant le fichier *HostingStartupLibrary.dll* du résultat de la compilation de la bibliothèque de classes dans le dossier *bin/Debug* de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="de9dc-496">Compilez et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-496">Compile and run the app.</span></span> <span data-ttu-id="de9dc-497">Dans le fichier projet de l’application, un `<ItemGroup>` référence l’assembly de la bibliothèque de classes (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (référence au moment de la compilation).</span><span class="sxs-lookup"><span data-stu-id="de9dc-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="de9dc-498">Pour plus d’informations, consultez les remarques dans le fichier projet de l’application HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="de9dc-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="de9dc-499">Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` de la bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="de9dc-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="de9dc-500">**Activation à partir d’un assembly déployé à partir du magasin de runtime**</span><span class="sxs-lookup"><span data-stu-id="de9dc-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="de9dc-501">Le projet *StartupDiagnostics* utilise [PowerShell](/powershell/scripting/powershell-scripting) pour modifier le fichier *StartupDiagnostics.deps.json* associé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="de9dc-502">PowerShell est installé par défaut sur Windows à compter de Windows 7 SP1 et de Windows Server 2008 R2 SP1.</span><span class="sxs-lookup"><span data-stu-id="de9dc-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="de9dc-503">Pour obtenir PowerShell sur d’autres plateformes, consultez [installation de différentes versions de PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="de9dc-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="de9dc-504">Exécutez le script *build.ps1* du dossier *RuntimeStore*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="de9dc-505">Le script :</span><span class="sxs-lookup"><span data-stu-id="de9dc-505">The script:</span></span>
   * <span data-ttu-id="de9dc-506">Génère le `StartupDiagnostics` package dans le dossier *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="de9dc-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="de9dc-507">Génère le magasin de runtime de `StartupDiagnostics` dans le dossier *store*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="de9dc-508">La commande `dotnet store` du script utilise l’ [identificateur de runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` pour un hébergement au démarrage déployé sur Windows.</span><span class="sxs-lookup"><span data-stu-id="de9dc-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="de9dc-509">Si vous fournissez l’hébergement au démarrage pour un autre runtime, spécifiez l’identificateur du runtime à la ligne 37 du script.</span><span class="sxs-lookup"><span data-stu-id="de9dc-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="de9dc-510">Le magasin du runtime pour `StartupDiagnostics` serait déplacé ultérieurement vers la Banque d’exécution du système ou de l’utilisateur sur l’ordinateur sur lequel l’assembly sera consommé.</span><span class="sxs-lookup"><span data-stu-id="de9dc-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="de9dc-511">L’emplacement d’installation du magasin d’exécution de l’utilisateur pour l' `StartupDiagnostics` assembly est *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="de9dc-512">Génère le `additionalDeps` pour `StartupDiagnostics` dans le dossier *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="de9dc-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="de9dc-513">Les dépendances supplémentaires seraient ensuite déplacées vers les dépendances supplémentaires du système ou de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="de9dc-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="de9dc-514">L' `StartupDiagnostics` emplacement d’installation des dépendances supplémentaires de l’utilisateur est *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. Netcore. app/2.2.0/StartupDiagnostics. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="de9dc-515">Place le fichier *deploy.ps1* dans le dossier *deployment*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="de9dc-516">Exécutez le script *deploy.ps1* du dossier *Deployment*.</span><span class="sxs-lookup"><span data-stu-id="de9dc-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="de9dc-517">Le script ajoute :</span><span class="sxs-lookup"><span data-stu-id="de9dc-517">The script appends:</span></span>
   * <span data-ttu-id="de9dc-518">`StartupDiagnostics` à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.</span><span class="sxs-lookup"><span data-stu-id="de9dc-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="de9dc-519">Chemin d’accès des dépendances de démarrage d’hébergement (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="de9dc-520">Chemin d’accès du magasin du Runtime (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_SHARED_STORE` variable d’environnement.</span><span class="sxs-lookup"><span data-stu-id="de9dc-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="de9dc-521">Exécutez l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-521">Run the sample app.</span></span>
1. <span data-ttu-id="de9dc-522">Demandez le point de terminaison `/services` pour voir les services inscrits de l’application.</span><span class="sxs-lookup"><span data-stu-id="de9dc-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="de9dc-523">Demandez le point de terminaison `/diag` pour voir les informations de diagnostic.</span><span class="sxs-lookup"><span data-stu-id="de9dc-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
