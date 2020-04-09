---
title: Fournisseurs de fichiers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751106"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="100d4-103">Fournisseurs de fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="100d4-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="100d4-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="100d4-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="100d4-105">ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers.</span><span class="sxs-lookup"><span data-stu-id="100d4-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="100d4-106">Les fournisseurs de fichiers sont utilisés dans l’ensemble du cadre ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="100d4-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="100d4-107">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="100d4-107">For example:</span></span>

* <span data-ttu-id="100d4-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>expose la racine de [contenu](xref:fundamentals/index#content-root) et `IFileProvider` la racine [web](xref:fundamentals/index#web-root) de l’application en tant que types.</span><span class="sxs-lookup"><span data-stu-id="100d4-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="100d4-109">[L’intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files) utilise des fournisseurs de fichiers pour localiser les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="100d4-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="100d4-110">[Razor](xref:mvc/views/razor) utilise des fournisseurs de fichiers pour localiser des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="100d4-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="100d4-111">Les outils .NET Core utilisent des fournisseurs de fichiers et des modèles d’utilisation des caractères génériques pour spécifier les fichiers qui doivent être publiés.</span><span class="sxs-lookup"><span data-stu-id="100d4-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="100d4-112">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="100d4-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="100d4-113">Interfaces de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="100d4-113">File Provider interfaces</span></span>

<span data-ttu-id="100d4-114">L’interface principale est <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="100d4-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="100d4-115">`IFileProvider` expose des méthodes pour :</span><span class="sxs-lookup"><span data-stu-id="100d4-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="100d4-116">Obtenir des informations sur le fichier (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="100d4-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="100d4-117">Obtenir des informations sur le répertoire (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="100d4-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="100d4-118">Configurer des notifications de modification (à l’aide un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="100d4-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="100d4-119">`IFileInfo` fournit des méthodes et propriétés d’utilisation des fichiers :</span><span class="sxs-lookup"><span data-stu-id="100d4-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="100d4-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en octets)</span><span class="sxs-lookup"><span data-stu-id="100d4-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="100d4-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>date</span><span class="sxs-lookup"><span data-stu-id="100d4-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="100d4-122">Vous pouvez lire à <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> partir du fichier en utilisant la méthode.</span><span class="sxs-lookup"><span data-stu-id="100d4-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="100d4-123">*L’application d’échantillons FileProviderSample* montre comment `Startup.ConfigureServices` configurer un fournisseur de fichiers pour une utilisation dans toute l’application via injection [de dépendance](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="100d4-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="100d4-124">Implémentations de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="100d4-124">File Provider implementations</span></span>

<span data-ttu-id="100d4-125">Le tableau suivant répertorie les implémentations de `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="100d4-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="100d4-126">Implémentation</span><span class="sxs-lookup"><span data-stu-id="100d4-126">Implementation</span></span> | <span data-ttu-id="100d4-127">Description</span><span class="sxs-lookup"><span data-stu-id="100d4-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="100d4-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="100d4-129">Utilisé pour fournir un accès combiné aux fichiers et annuaires d’un ou de plusieurs autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="100d4-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="100d4-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="100d4-131">Utilisé pour accéder aux fichiers intégrés dans les assemblages.</span><span class="sxs-lookup"><span data-stu-id="100d4-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="100d4-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="100d4-133">Utilisé pour accéder aux fichiers physiques du système.</span><span class="sxs-lookup"><span data-stu-id="100d4-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="100d4-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-134">PhysicalFileProvider</span></span>

<span data-ttu-id="100d4-135">La classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fournit un accès au système de fichiers physique.</span><span class="sxs-lookup"><span data-stu-id="100d4-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="100d4-136">`PhysicalFileProvider` utilise le type <xref:System.IO.File?displayProperty=fullName> (pour le fournisseur physique) et définissant comme portée tous les chemins d’un répertoire et de ses enfants.</span><span class="sxs-lookup"><span data-stu-id="100d4-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="100d4-137">Cette portée empêche l’accès au système de fichiers en dehors du répertoire spécifié et ses enfants.</span><span class="sxs-lookup"><span data-stu-id="100d4-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="100d4-138">Le scénario le plus courant pour créer et utiliser un `PhysicalFileProvider` consiste à demander un `IFileProvider` dans un constructeur par le biais de l’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="100d4-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="100d4-139">Lors de l’instantanéise de ce fournisseur directement, un chemin d’annuaire absolu est nécessaire et sert de voie de base pour toutes les demandes faites à l’aide du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="100d4-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="100d4-140">Les motifs glob ne sont pas pris en charge dans le chemin du répertoire.</span><span class="sxs-lookup"><span data-stu-id="100d4-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="100d4-141">Le code suivant montre `PhysicalFileProvider` comment utiliser pour obtenir le contenu de l’annuaire et les informations de fichier:</span><span class="sxs-lookup"><span data-stu-id="100d4-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="100d4-142">Types dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="100d4-142">Types in the preceding example:</span></span>

* <span data-ttu-id="100d4-143">`provider` est une `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="100d4-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="100d4-144">`contents` est une `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="100d4-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="100d4-145">`fileInfo` est une `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="100d4-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="100d4-146">Le fournisseur de fichiers peut être utilisé pour itérer au sein du répertoire spécifié par `applicationRoot` ou pour appeler `GetFileInfo` afin d’obtenir des informations sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="100d4-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="100d4-147">Les motifs glob ne `GetFileInfo` peuvent pas être transmis à la méthode.</span><span class="sxs-lookup"><span data-stu-id="100d4-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="100d4-148">Le fournisseur de fichiers n’a pas accès en dehors du répertoire `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="100d4-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="100d4-149">*L’application d’échantillon FileProviderSample* crée le fournisseur dans la `Startup.ConfigureServices` méthode en utilisant <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="100d4-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="100d4-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="100d4-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> est utilisé pour accéder à des fichiers incorporés dans des assemblys.</span><span class="sxs-lookup"><span data-stu-id="100d4-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="100d4-152">`ManifestEmbeddedFileProvider` utilise un manifeste compilé dans l’assembly pour reconstruire les chemins d’accès d’origine des fichiers intégrés.</span><span class="sxs-lookup"><span data-stu-id="100d4-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="100d4-153">Pour générer un manifeste des fichiers intégrés :</span><span class="sxs-lookup"><span data-stu-id="100d4-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="100d4-154">Ajoutez le package [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet à votre projet.</span><span class="sxs-lookup"><span data-stu-id="100d4-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="100d4-155">Attribuez à la propriété `<GenerateEmbeddedFilesManifest>` la valeur `true`.</span><span class="sxs-lookup"><span data-stu-id="100d4-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="100d4-156">Spécifiez les fichiers à intégrer à [ \<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="100d4-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="100d4-157">Utilisez les [modèles glob](#glob-patterns) pour spécifier un ou plusieurs fichiers à incorporer dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="100d4-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="100d4-158">*L’application d’échantillon FileProviderSample* crée un `ManifestEmbeddedFileProvider` assemblage et passe l’assemblage actuellement exécutant à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="100d4-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="100d4-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="100d4-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="100d4-160">Des surcharges supplémentaires vous permettent de :</span><span class="sxs-lookup"><span data-stu-id="100d4-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="100d4-161">Spécifier un chemin de fichier relatif.</span><span class="sxs-lookup"><span data-stu-id="100d4-161">Specify a relative file path.</span></span>
* <span data-ttu-id="100d4-162">Définir la portée de fichiers sur une date de dernière modification.</span><span class="sxs-lookup"><span data-stu-id="100d4-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="100d4-163">Nommer la ressource incorporée contenant le manifeste de fichier incorporé.</span><span class="sxs-lookup"><span data-stu-id="100d4-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="100d4-164">Surcharge</span><span class="sxs-lookup"><span data-stu-id="100d4-164">Overload</span></span> | <span data-ttu-id="100d4-165">Description</span><span class="sxs-lookup"><span data-stu-id="100d4-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="100d4-166">Accepte un paramètre de chemin d'accès relatif `root` facultatif.</span><span class="sxs-lookup"><span data-stu-id="100d4-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="100d4-167">Spécifiez `root` pour définir la portée des appels à <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> sur ces ressources sous le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="100d4-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="100d4-168">Accepte un paramètre de chemin relatif `root` facultatif et un paramètre de date `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="100d4-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="100d4-169">La date `lastModified` définit la portée de la dernière date de modification pour les instances <xref:Microsoft.Extensions.FileProviders.IFileInfo> retournées par <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="100d4-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="100d4-170">Accepte un chemin d'accès relatif `root` facultatif, une date `lastModified` et des paramètres `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="100d4-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="100d4-171">`manifestName` représente le nom de la ressource incorporée contenant la manifeste.</span><span class="sxs-lookup"><span data-stu-id="100d4-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="100d4-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-172">CompositeFileProvider</span></span>

<span data-ttu-id="100d4-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combine des instances `IFileProvider`, en exposant une interface unique qui permet d’utiliser des fichiers de différents fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="100d4-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="100d4-174">Quand vous créez `CompositeFileProvider`, vous passez une ou plusieurs instances `IFileProvider` à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="100d4-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="100d4-175">Dans *l’application d’échantillon FileProviderSample,* a `PhysicalFileProvider` et un `ManifestEmbeddedFileProvider` fichier de fournir à un `CompositeFileProvider` enregistrement dans le conteneur de service de l’application.</span><span class="sxs-lookup"><span data-stu-id="100d4-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="100d4-176">Le code suivant se trouve `Startup.ConfigureServices` dans la méthode du projet :</span><span class="sxs-lookup"><span data-stu-id="100d4-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="100d4-177">Suivre les modifications apportées</span><span class="sxs-lookup"><span data-stu-id="100d4-177">Watch for changes</span></span>

<span data-ttu-id="100d4-178">La <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> méthode fournit un scénario pour regarder un ou plusieurs fichiers ou répertoires pour les changements.</span><span class="sxs-lookup"><span data-stu-id="100d4-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="100d4-179">La méthode `Watch` :</span><span class="sxs-lookup"><span data-stu-id="100d4-179">The `Watch` method:</span></span>

* <span data-ttu-id="100d4-180">Accepte une chaîne de trajectoire de fichier, qui peut utiliser [des modèles de glob](#glob-patterns) pour spécifier plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="100d4-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="100d4-181">Retourne un <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="100d4-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="100d4-182">Le jeton de changement qui en résulte expose :</span><span class="sxs-lookup"><span data-stu-id="100d4-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="100d4-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Une propriété qui peut être inspectée pour déterminer si un changement s’est produit.</span><span class="sxs-lookup"><span data-stu-id="100d4-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="100d4-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Appelé lorsque des modifications sont détectées à la chaîne de chemin spécifiée.</span><span class="sxs-lookup"><span data-stu-id="100d4-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="100d4-185">Chaque jeton de modification appelle uniquement son rappel associé en réponse à un changement unique.</span><span class="sxs-lookup"><span data-stu-id="100d4-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="100d4-186">Pour activer une surveillance constante, vous pouvez utiliser une <xref:System.Threading.Tasks.TaskCompletionSource`1> comme indiqué ci-dessous, ou recréer des instances `IChangeToken` en réponse aux changements.</span><span class="sxs-lookup"><span data-stu-id="100d4-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="100d4-187">*L’application d’échantillon WatchConsole* écrit un message chaque fois qu’un fichier *.txt* dans le répertoire *TextFiles* est modifié :</span><span class="sxs-lookup"><span data-stu-id="100d4-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="100d4-188">Certains systèmes de fichiers, comme les conteneurs Docker et les partages réseau, peuvent ne pas envoyer de manière fiable les notifications de modifications.</span><span class="sxs-lookup"><span data-stu-id="100d4-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="100d4-189">Définissez la variable d’environnement `DOTNET_USE_POLLING_FILE_WATCHER` sur `1` ou `true` pour interroger le système de fichiers à la recherche de changements toutes les quatre secondes (non configurable).</span><span class="sxs-lookup"><span data-stu-id="100d4-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="100d4-190">Modèles Glob</span><span class="sxs-lookup"><span data-stu-id="100d4-190">Glob patterns</span></span>

<span data-ttu-id="100d4-191">Les chemins de système de fichiers utilisent des modèles à caractères génériques appelés *modèles Glob (ou d’utilisation des caractères génériques)*.</span><span class="sxs-lookup"><span data-stu-id="100d4-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="100d4-192">Spécifiez les groupes de fichiers avec ces modèles.</span><span class="sxs-lookup"><span data-stu-id="100d4-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="100d4-193">Les deux caractères génériques sont `*` et `**` :</span><span class="sxs-lookup"><span data-stu-id="100d4-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="100d4-194">Établit une correspondance avec n’importe quel élément au niveau de dossier actuel, avec n’importe quel nom de fichier ou avec n’importe quelle extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="100d4-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="100d4-195">Les correspondances sont terminées par des caractères `/` et `.` dans le chemin des fichiers.</span><span class="sxs-lookup"><span data-stu-id="100d4-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="100d4-196">Établit une correspondance avec n’importe quel élément sur plusieurs niveaux de répertoire.</span><span class="sxs-lookup"><span data-stu-id="100d4-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="100d4-197">Peut être utilisé pour établir une correspondance avec plusieurs fichiers dans une hiérarchie de répertoires de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="100d4-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="100d4-198">Le tableau suivant fournit des exemples communs de modèles de glob.</span><span class="sxs-lookup"><span data-stu-id="100d4-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="100d4-199">Modèle</span><span class="sxs-lookup"><span data-stu-id="100d4-199">Pattern</span></span>  |<span data-ttu-id="100d4-200">Description</span><span class="sxs-lookup"><span data-stu-id="100d4-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="100d4-201">Établit une correspondance avec un fichier spécifique dans un répertoire spécifique.</span><span class="sxs-lookup"><span data-stu-id="100d4-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="100d4-202">Établit une correspondance avec tous les fichiers ayant l’extension *.txt* dans un répertoire spécifique.</span><span class="sxs-lookup"><span data-stu-id="100d4-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="100d4-203">Correspond à tous les fichiers *appsettings.json* dans les répertoires exactement un niveau en dessous du dossier *d’annuaire.*</span><span class="sxs-lookup"><span data-stu-id="100d4-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="100d4-204">Correspond à tous les fichiers avec une extension *.txt* trouvé n’importe où sous le dossier *d’annuaire.*</span><span class="sxs-lookup"><span data-stu-id="100d4-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="100d4-205">ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers.</span><span class="sxs-lookup"><span data-stu-id="100d4-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="100d4-206">Des fournisseurs de fichiers sont utilisés dans l’infrastructure ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="100d4-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="100d4-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>expose la racine de [contenu](xref:fundamentals/index#content-root) et `IFileProvider` la racine [web](xref:fundamentals/index#web-root) de l’application en tant que types.</span><span class="sxs-lookup"><span data-stu-id="100d4-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="100d4-208">[L’intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files) utilise des fournisseurs de fichiers pour localiser les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="100d4-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="100d4-209">[Razor](xref:mvc/views/razor) utilise des fournisseurs de fichiers pour localiser des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="100d4-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="100d4-210">Les outils .NET Core utilisent des fournisseurs de fichiers et des modèles d’utilisation des caractères génériques pour spécifier les fichiers qui doivent être publiés.</span><span class="sxs-lookup"><span data-stu-id="100d4-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="100d4-211">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="100d4-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="100d4-212">Interfaces de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="100d4-212">File Provider interfaces</span></span>

<span data-ttu-id="100d4-213">L’interface principale est <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="100d4-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="100d4-214">`IFileProvider` expose des méthodes pour :</span><span class="sxs-lookup"><span data-stu-id="100d4-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="100d4-215">Obtenir des informations sur le fichier (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="100d4-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="100d4-216">Obtenir des informations sur le répertoire (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="100d4-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="100d4-217">Configurer des notifications de modification (à l’aide un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="100d4-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="100d4-218">`IFileInfo` fournit des méthodes et propriétés d’utilisation des fichiers :</span><span class="sxs-lookup"><span data-stu-id="100d4-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="100d4-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en octets)</span><span class="sxs-lookup"><span data-stu-id="100d4-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="100d4-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>date</span><span class="sxs-lookup"><span data-stu-id="100d4-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="100d4-221">Vous pouvez lire à partir du fichier en utilisant la méthode [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="100d4-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="100d4-222">L’exemple d’application montre comment configurer un fournisseur de fichiers dans `Startup.ConfigureServices` pour une utilisation dans toute l’application via [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="100d4-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="100d4-223">Implémentations de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="100d4-223">File Provider implementations</span></span>

<span data-ttu-id="100d4-224">Trois implémentations de `IFileProvider` sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="100d4-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="100d4-225">Implémentation</span><span class="sxs-lookup"><span data-stu-id="100d4-225">Implementation</span></span> | <span data-ttu-id="100d4-226">Description</span><span class="sxs-lookup"><span data-stu-id="100d4-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="100d4-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="100d4-228">Le fournisseur physique est utilisé pour accéder aux fichiers physiques du système.</span><span class="sxs-lookup"><span data-stu-id="100d4-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="100d4-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="100d4-230">Le fournisseur incorporé de manifeste est utilisé pour accéder à des fichiers incorporés dans des assemblys.</span><span class="sxs-lookup"><span data-stu-id="100d4-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="100d4-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="100d4-232">Le fournisseur composite est utilisé pour fournir un accès combiné à des fichiers et à des répertoires à partir d’un ou de plusieurs fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="100d4-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="100d4-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-233">PhysicalFileProvider</span></span>

<span data-ttu-id="100d4-234">La classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fournit un accès au système de fichiers physique.</span><span class="sxs-lookup"><span data-stu-id="100d4-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="100d4-235">`PhysicalFileProvider` utilise le type <xref:System.IO.File?displayProperty=fullName> (pour le fournisseur physique) et définissant comme portée tous les chemins d’un répertoire et de ses enfants.</span><span class="sxs-lookup"><span data-stu-id="100d4-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="100d4-236">Cette portée empêche l’accès au système de fichiers en dehors du répertoire spécifié et ses enfants.</span><span class="sxs-lookup"><span data-stu-id="100d4-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="100d4-237">Le scénario le plus courant pour créer et utiliser un `PhysicalFileProvider` consiste à demander un `IFileProvider` dans un constructeur par le biais de l’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="100d4-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="100d4-238">Lors de l’instanciation directe de ce fournisseur, un chemin de répertoire est obligatoire et sert de chemin de base pour toutes les demandes effectuées à l’aide du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="100d4-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="100d4-239">Le code suivant montre comment créer un `PhysicalFileProvider` et l’utiliser pour obtenir le contenu du répertoire et les informations sur le fichier :</span><span class="sxs-lookup"><span data-stu-id="100d4-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="100d4-240">Types dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="100d4-240">Types in the preceding example:</span></span>

* <span data-ttu-id="100d4-241">`provider` est une `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="100d4-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="100d4-242">`contents` est une `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="100d4-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="100d4-243">`fileInfo` est une `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="100d4-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="100d4-244">Le fournisseur de fichiers peut être utilisé pour itérer au sein du répertoire spécifié par `applicationRoot` ou pour appeler `GetFileInfo` afin d’obtenir des informations sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="100d4-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="100d4-245">Le fournisseur de fichiers n’a pas accès en dehors du répertoire `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="100d4-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="100d4-246">L’exemple d’application crée le fournisseur dans la classe `Startup.ConfigureServices` de l’application à l’aide de [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) :</span><span class="sxs-lookup"><span data-stu-id="100d4-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="100d4-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="100d4-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> est utilisé pour accéder à des fichiers incorporés dans des assemblys.</span><span class="sxs-lookup"><span data-stu-id="100d4-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="100d4-249">`ManifestEmbeddedFileProvider` utilise un manifeste compilé dans l’assembly pour reconstruire les chemins d’accès d’origine des fichiers intégrés.</span><span class="sxs-lookup"><span data-stu-id="100d4-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="100d4-250">Pour générer un manifeste des fichiers incorporés, définissez la propriété `<GenerateEmbeddedFilesManifest>` sur `true`.</span><span class="sxs-lookup"><span data-stu-id="100d4-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="100d4-251">Spécifier les fichiers à intégrer avec [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="100d4-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="100d4-252">Utilisez les [modèles glob](#glob-patterns) pour spécifier un ou plusieurs fichiers à incorporer dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="100d4-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="100d4-253">L’exemple d’application crée un `ManifestEmbeddedFileProvider` et transmet l’assembly en cours d’exécution à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="100d4-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="100d4-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="100d4-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="100d4-255">Des surcharges supplémentaires vous permettent de :</span><span class="sxs-lookup"><span data-stu-id="100d4-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="100d4-256">Spécifier un chemin de fichier relatif.</span><span class="sxs-lookup"><span data-stu-id="100d4-256">Specify a relative file path.</span></span>
* <span data-ttu-id="100d4-257">Définir la portée de fichiers sur une date de dernière modification.</span><span class="sxs-lookup"><span data-stu-id="100d4-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="100d4-258">Nommer la ressource incorporée contenant le manifeste de fichier incorporé.</span><span class="sxs-lookup"><span data-stu-id="100d4-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="100d4-259">Surcharge</span><span class="sxs-lookup"><span data-stu-id="100d4-259">Overload</span></span> | <span data-ttu-id="100d4-260">Description</span><span class="sxs-lookup"><span data-stu-id="100d4-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="100d4-261">Accepte un paramètre de chemin d'accès relatif `root` facultatif.</span><span class="sxs-lookup"><span data-stu-id="100d4-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="100d4-262">Spécifiez `root` pour définir la portée des appels à <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> sur ces ressources sous le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="100d4-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="100d4-263">Accepte un paramètre de chemin relatif `root` facultatif et un paramètre de date `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="100d4-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="100d4-264">La date `lastModified` définit la portée de la dernière date de modification pour les instances <xref:Microsoft.Extensions.FileProviders.IFileInfo> retournées par <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="100d4-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="100d4-265">Accepte un chemin d'accès relatif `root` facultatif, une date `lastModified` et des paramètres `manifestName`.</span><span class="sxs-lookup"><span data-stu-id="100d4-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="100d4-266">`manifestName` représente le nom de la ressource incorporée contenant la manifeste.</span><span class="sxs-lookup"><span data-stu-id="100d4-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="100d4-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="100d4-267">CompositeFileProvider</span></span>

<span data-ttu-id="100d4-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combine des instances `IFileProvider`, en exposant une interface unique qui permet d’utiliser des fichiers de différents fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="100d4-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="100d4-269">Quand vous créez `CompositeFileProvider`, vous passez une ou plusieurs instances `IFileProvider` à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="100d4-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="100d4-270">Dans l’exemple d’application, un `PhysicalFileProvider` et un `ManifestEmbeddedFileProvider` fournissent des fichiers à un `CompositeFileProvider` inscrit dans le conteneur de service de l’application :</span><span class="sxs-lookup"><span data-stu-id="100d4-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="100d4-271">Suivre les modifications apportées</span><span class="sxs-lookup"><span data-stu-id="100d4-271">Watch for changes</span></span>

<span data-ttu-id="100d4-272">La méthode [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) offre un moyen d’observer un ou plusieurs fichiers ou répertoires afin de détecter les changements.</span><span class="sxs-lookup"><span data-stu-id="100d4-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="100d4-273">`Watch` accepte une chaîne de chemin, qui peut utiliser des [modèles d’utilisation des caractères génériques](#glob-patterns) pour spécifier plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="100d4-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="100d4-274">`Watch`Retourne un <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="100d4-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="100d4-275">Le jeton de modification expose :</span><span class="sxs-lookup"><span data-stu-id="100d4-275">The change token exposes:</span></span>

* <span data-ttu-id="100d4-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Une propriété qui peut être inspectée pour déterminer si un changement s’est produit.</span><span class="sxs-lookup"><span data-stu-id="100d4-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="100d4-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Appelé lorsque des modifications sont détectées à la chaîne de chemin spécifiée.</span><span class="sxs-lookup"><span data-stu-id="100d4-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="100d4-278">Chaque jeton de modification appelle uniquement son rappel associé en réponse à un changement unique.</span><span class="sxs-lookup"><span data-stu-id="100d4-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="100d4-279">Pour activer une surveillance constante, vous pouvez utiliser une <xref:System.Threading.Tasks.TaskCompletionSource`1> comme indiqué ci-dessous, ou recréer des instances `IChangeToken` en réponse aux changements.</span><span class="sxs-lookup"><span data-stu-id="100d4-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="100d4-280">Dans l’exemple d’application, l’application console *WatchConsole* est configurée pour afficher un message chaque fois qu’un fichier texte est modifié :</span><span class="sxs-lookup"><span data-stu-id="100d4-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="100d4-281">Certains systèmes de fichiers, comme les conteneurs Docker et les partages réseau, peuvent ne pas envoyer de manière fiable les notifications de modifications.</span><span class="sxs-lookup"><span data-stu-id="100d4-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="100d4-282">Définissez la variable d’environnement `DOTNET_USE_POLLING_FILE_WATCHER` sur `1` ou `true` pour interroger le système de fichiers à la recherche de changements toutes les quatre secondes (non configurable).</span><span class="sxs-lookup"><span data-stu-id="100d4-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="100d4-283">Modèles Glob</span><span class="sxs-lookup"><span data-stu-id="100d4-283">Glob patterns</span></span>

<span data-ttu-id="100d4-284">Les chemins de système de fichiers utilisent des modèles à caractères génériques appelés *modèles Glob (ou d’utilisation des caractères génériques)*.</span><span class="sxs-lookup"><span data-stu-id="100d4-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="100d4-285">Spécifiez les groupes de fichiers avec ces modèles.</span><span class="sxs-lookup"><span data-stu-id="100d4-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="100d4-286">Les deux caractères génériques sont `*` et `**` :</span><span class="sxs-lookup"><span data-stu-id="100d4-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="100d4-287">Établit une correspondance avec n’importe quel élément au niveau de dossier actuel, avec n’importe quel nom de fichier ou avec n’importe quelle extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="100d4-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="100d4-288">Les correspondances sont terminées par des caractères `/` et `.` dans le chemin des fichiers.</span><span class="sxs-lookup"><span data-stu-id="100d4-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="100d4-289">Établit une correspondance avec n’importe quel élément sur plusieurs niveaux de répertoire.</span><span class="sxs-lookup"><span data-stu-id="100d4-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="100d4-290">Peut être utilisé pour établir une correspondance avec plusieurs fichiers dans une hiérarchie de répertoires de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="100d4-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="100d4-291">**Exemples de modèles d’utilisation des caractères génériques**</span><span class="sxs-lookup"><span data-stu-id="100d4-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="100d4-292">Établit une correspondance avec un fichier spécifique dans un répertoire spécifique.</span><span class="sxs-lookup"><span data-stu-id="100d4-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="100d4-293">Établit une correspondance avec tous les fichiers ayant l’extension *.txt* dans un répertoire spécifique.</span><span class="sxs-lookup"><span data-stu-id="100d4-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="100d4-294">Établit une correspondance avec tous les fichiers `appsettings.json` dans les répertoires situés exactement un niveau en dessous du dossier *répertoire*.</span><span class="sxs-lookup"><span data-stu-id="100d4-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="100d4-295">Établit une correspondance avec tous les fichiers ayant l’extension *.txt* et se trouvant n’importe où sous le dossier *répertoire*.</span><span class="sxs-lookup"><span data-stu-id="100d4-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
