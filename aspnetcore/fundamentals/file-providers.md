---
<span data-ttu-id="6251f-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-102">'Blazor'</span></span>
- <span data-ttu-id="6251f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-103">'Identity'</span></span>
- <span data-ttu-id="6251f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-105">'Razor'</span></span>
- <span data-ttu-id="6251f-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="6251f-107">Fournisseurs de fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6251f-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="6251f-108">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6251f-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6251f-109">ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers.</span><span class="sxs-lookup"><span data-stu-id="6251f-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="6251f-110">Les fournisseurs de fichiers sont utilisés dans l’infrastructure ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6251f-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="6251f-111">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="6251f-111">For example:</span></span>

* <span data-ttu-id="6251f-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>expose la racine de [contenu](xref:fundamentals/index#content-root) et la [racine Web](xref:fundamentals/index#web-root) de l’application en tant que `IFileProvider` types.</span><span class="sxs-lookup"><span data-stu-id="6251f-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="6251f-113">[L’intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files) utilise des fournisseurs de fichiers pour localiser les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="6251f-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="6251f-114">[Razor](xref:mvc/views/razor)utilise des fournisseurs de fichiers pour localiser des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="6251f-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="6251f-115">Les outils .NET Core utilisent des fournisseurs de fichiers et des modèles d’utilisation des caractères génériques pour spécifier les fichiers qui doivent être publiés.</span><span class="sxs-lookup"><span data-stu-id="6251f-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="6251f-116">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6251f-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="6251f-117">Interfaces de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="6251f-117">File Provider interfaces</span></span>

<span data-ttu-id="6251f-118">L’interface principale est <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="6251f-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="6251f-119">`IFileProvider` expose des méthodes pour :</span><span class="sxs-lookup"><span data-stu-id="6251f-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="6251f-120">Obtenir des informations sur le fichier (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="6251f-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="6251f-121">Obtenir des informations sur le répertoire (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="6251f-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="6251f-122">Configurer des notifications de modification (à l’aide un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="6251f-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="6251f-123">`IFileInfo` fournit des méthodes et propriétés d’utilisation des fichiers :</span><span class="sxs-lookup"><span data-stu-id="6251f-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="6251f-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en octets)</span><span class="sxs-lookup"><span data-stu-id="6251f-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="6251f-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>date</span><span class="sxs-lookup"><span data-stu-id="6251f-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="6251f-126">Vous pouvez lire à partir du fichier à l’aide de la <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> méthode.</span><span class="sxs-lookup"><span data-stu-id="6251f-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="6251f-127">L’exemple d’application *FileProviderSample* montre comment configurer un fournisseur de fichiers dans `Startup.ConfigureServices` pour une utilisation dans l’ensemble de l’application via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6251f-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="6251f-128">Implémentations de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="6251f-128">File Provider implementations</span></span>

<span data-ttu-id="6251f-129">Le tableau suivant répertorie les implémentations de `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="6251f-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="6251f-130">Implémentation</span><span class="sxs-lookup"><span data-stu-id="6251f-130">Implementation</span></span> | <span data-ttu-id="6251f-131">Description</span><span class="sxs-lookup"><span data-stu-id="6251f-131">Description</span></span> |
| ---
<span data-ttu-id="6251f-132">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-133">'Blazor'</span></span>
- <span data-ttu-id="6251f-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-134">'Identity'</span></span>
- <span data-ttu-id="6251f-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-136">'Razor'</span></span>
- <span data-ttu-id="6251f-137">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-138">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-139">'Blazor'</span></span>
- <span data-ttu-id="6251f-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-140">'Identity'</span></span>
- <span data-ttu-id="6251f-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-142">'Razor'</span></span>
- <span data-ttu-id="6251f-143">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-144">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-145">'Blazor'</span></span>
- <span data-ttu-id="6251f-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-146">'Identity'</span></span>
- <span data-ttu-id="6251f-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-148">'Razor'</span></span>
- <span data-ttu-id="6251f-149">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-150">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-151">'Blazor'</span></span>
- <span data-ttu-id="6251f-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-152">'Identity'</span></span>
- <span data-ttu-id="6251f-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-154">'Razor'</span></span>
- <span data-ttu-id="6251f-155">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-156">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-157">'Blazor'</span></span>
- <span data-ttu-id="6251f-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-158">'Identity'</span></span>
- <span data-ttu-id="6251f-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-160">'Razor'</span></span>
- <span data-ttu-id="6251f-161">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-161">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-162">------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-163">'Blazor'</span></span>
- <span data-ttu-id="6251f-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-164">'Identity'</span></span>
- <span data-ttu-id="6251f-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-166">'Razor'</span></span>
- <span data-ttu-id="6251f-167">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-168">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-169">'Blazor'</span></span>
- <span data-ttu-id="6251f-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-170">'Identity'</span></span>
- <span data-ttu-id="6251f-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-172">'Razor'</span></span>
- <span data-ttu-id="6251f-173">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-174">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-175">'Blazor'</span></span>
- <span data-ttu-id="6251f-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-176">'Identity'</span></span>
- <span data-ttu-id="6251f-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-178">'Razor'</span></span>
- <span data-ttu-id="6251f-179">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-179">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-180">------ | | [CompositeFileProvider](#compositefileprovider) | Utilisé pour fournir un accès combiné aux fichiers et aux répertoires à partir d’un ou de plusieurs autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6251f-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="6251f-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Utilisé pour accéder aux fichiers incorporés dans les assemblys.</span><span class="sxs-lookup"><span data-stu-id="6251f-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="6251f-182">| | [PhysicalFileProvider](#physicalfileprovider) | Utilisé pour accéder aux fichiers physiques du système.</span><span class="sxs-lookup"><span data-stu-id="6251f-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="6251f-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="6251f-183">PhysicalFileProvider</span></span>

<span data-ttu-id="6251f-184">La classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fournit un accès au système de fichiers physique.</span><span class="sxs-lookup"><span data-stu-id="6251f-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="6251f-185">`PhysicalFileProvider` utilise le type <xref:System.IO.File?displayProperty=fullName> (pour le fournisseur physique) et définissant comme portée tous les chemins d’un répertoire et de ses enfants.</span><span class="sxs-lookup"><span data-stu-id="6251f-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="6251f-186">Cette portée empêche l’accès au système de fichiers en dehors du répertoire spécifié et ses enfants.</span><span class="sxs-lookup"><span data-stu-id="6251f-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="6251f-187">Le scénario le plus courant pour créer et utiliser un `PhysicalFileProvider` consiste à demander un `IFileProvider` dans un constructeur par le biais de l’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6251f-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6251f-188">Lors de l’instanciation de ce fournisseur directement, un chemin d’accès absolu au répertoire est requis et sert de chemin de base pour toutes les requêtes effectuées à l’aide du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="6251f-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="6251f-189">Les modèles glob ne sont pas pris en charge dans le chemin d’accès du répertoire.</span><span class="sxs-lookup"><span data-stu-id="6251f-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="6251f-190">Le code suivant montre comment utiliser `PhysicalFileProvider` pour obtenir le contenu du répertoire et les informations sur le fichier :</span><span class="sxs-lookup"><span data-stu-id="6251f-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="6251f-191">Types dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="6251f-191">Types in the preceding example:</span></span>

* <span data-ttu-id="6251f-192">`provider` est une `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="6251f-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="6251f-193">`contents` est une `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="6251f-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="6251f-194">`fileInfo` est une `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="6251f-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="6251f-195">Le fournisseur de fichiers peut être utilisé pour itérer au sein du répertoire spécifié par `applicationRoot` ou pour appeler `GetFileInfo` afin d’obtenir des informations sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="6251f-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="6251f-196">Les modèles glob ne peuvent pas être passés à la `GetFileInfo` méthode.</span><span class="sxs-lookup"><span data-stu-id="6251f-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="6251f-197">Le fournisseur de fichiers n’a pas accès en dehors du répertoire `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="6251f-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="6251f-198">L’exemple d’application *FileProviderSample* crée le fournisseur dans la `Startup.ConfigureServices` méthode à l’aide de <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="6251f-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="6251f-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="6251f-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="6251f-200"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> est utilisé pour accéder à des fichiers incorporés dans des assemblys.</span><span class="sxs-lookup"><span data-stu-id="6251f-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="6251f-201">`ManifestEmbeddedFileProvider` utilise un manifeste compilé dans l’assembly pour reconstruire les chemins d’accès d’origine des fichiers intégrés.</span><span class="sxs-lookup"><span data-stu-id="6251f-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="6251f-202">Pour générer un manifeste des fichiers incorporés :</span><span class="sxs-lookup"><span data-stu-id="6251f-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="6251f-203">Ajoutez le package NuGet [Microsoft. extensions. FileProviders. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) à votre projet.</span><span class="sxs-lookup"><span data-stu-id="6251f-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="6251f-204">Attribuez à la propriété `<GenerateEmbeddedFilesManifest>` la valeur `true`.</span><span class="sxs-lookup"><span data-stu-id="6251f-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="6251f-205">Spécifiez les fichiers à incorporer [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="6251f-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="6251f-206">Utilisez les [modèles glob](#glob-patterns) pour spécifier un ou plusieurs fichiers à incorporer dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="6251f-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="6251f-207">L’exemple d’application *FileProviderSample* crée un `ManifestEmbeddedFileProvider` et passe l’assembly en cours d’exécution à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="6251f-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="6251f-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6251f-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="6251f-209">Des surcharges supplémentaires vous permettent de :</span><span class="sxs-lookup"><span data-stu-id="6251f-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="6251f-210">Spécifier un chemin de fichier relatif.</span><span class="sxs-lookup"><span data-stu-id="6251f-210">Specify a relative file path.</span></span>
* <span data-ttu-id="6251f-211">Définir la portée de fichiers sur une date de dernière modification.</span><span class="sxs-lookup"><span data-stu-id="6251f-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="6251f-212">Nommer la ressource incorporée contenant le manifeste de fichier incorporé.</span><span class="sxs-lookup"><span data-stu-id="6251f-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="6251f-213">Surcharge</span><span class="sxs-lookup"><span data-stu-id="6251f-213">Overload</span></span> | <span data-ttu-id="6251f-214">Description</span><span class="sxs-lookup"><span data-stu-id="6251f-214">Description</span></span> |
| ---
<span data-ttu-id="6251f-215">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-216">'Blazor'</span></span>
- <span data-ttu-id="6251f-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-217">'Identity'</span></span>
- <span data-ttu-id="6251f-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-219">'Razor'</span></span>
- <span data-ttu-id="6251f-220">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-221">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-222">'Blazor'</span></span>
- <span data-ttu-id="6251f-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-223">'Identity'</span></span>
- <span data-ttu-id="6251f-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-225">'Razor'</span></span>
- <span data-ttu-id="6251f-226">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-226">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-227">---- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-228">'Blazor'</span></span>
- <span data-ttu-id="6251f-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-229">'Identity'</span></span>
- <span data-ttu-id="6251f-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-231">'Razor'</span></span>
- <span data-ttu-id="6251f-232">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-233">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-234">'Blazor'</span></span>
- <span data-ttu-id="6251f-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-235">'Identity'</span></span>
- <span data-ttu-id="6251f-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-237">'Razor'</span></span>
- <span data-ttu-id="6251f-238">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-239">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-240">'Blazor'</span></span>
- <span data-ttu-id="6251f-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-241">'Identity'</span></span>
- <span data-ttu-id="6251f-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-243">'Razor'</span></span>
- <span data-ttu-id="6251f-244">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-244">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepte un `root` paramètre de chemin d’accès relatif facultatif.</span><span class="sxs-lookup"><span data-stu-id="6251f-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="6251f-246">Spécifiez `root` pour définir la portée des appels à <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> sur ces ressources sous le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="6251f-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="6251f-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepte un `root` paramètre de chemin d’accès relatif facultatif et un `lastModified` paramètre date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="6251f-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="6251f-248">La date `lastModified` définit la portée de la dernière date de modification pour les instances <xref:Microsoft.Extensions.FileProviders.IFileInfo> retournées par <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="6251f-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="6251f-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepte un `root` chemin d’accès, une `lastModified` date et des paramètres relatifs facultatifs `manifestName` .</span><span class="sxs-lookup"><span data-stu-id="6251f-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="6251f-250">`manifestName` représente le nom de la ressource incorporée contenant la manifeste.</span><span class="sxs-lookup"><span data-stu-id="6251f-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="6251f-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="6251f-251">CompositeFileProvider</span></span>

<span data-ttu-id="6251f-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combine des instances `IFileProvider`, en exposant une interface unique qui permet d’utiliser des fichiers de différents fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6251f-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="6251f-253">Quand vous créez `CompositeFileProvider`, vous passez une ou plusieurs instances `IFileProvider` à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="6251f-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="6251f-254">Dans l’exemple d’application *FileProviderSample* , un `PhysicalFileProvider` et un `ManifestEmbeddedFileProvider` fournissent des fichiers à un `CompositeFileProvider` inscrit dans le conteneur de services de l’application.</span><span class="sxs-lookup"><span data-stu-id="6251f-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="6251f-255">Le code suivant se trouve dans la méthode du projet `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6251f-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="6251f-256">Suivre les modifications apportées</span><span class="sxs-lookup"><span data-stu-id="6251f-256">Watch for changes</span></span>

<span data-ttu-id="6251f-257">La <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> méthode fournit un scénario pour regarder un ou plusieurs fichiers ou répertoires pour les modifications.</span><span class="sxs-lookup"><span data-stu-id="6251f-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="6251f-258">La méthode `Watch` :</span><span class="sxs-lookup"><span data-stu-id="6251f-258">The `Watch` method:</span></span>

* <span data-ttu-id="6251f-259">Accepte une chaîne de chemin d’accès de fichier, qui peut utiliser des [modèles glob](#glob-patterns) pour spécifier plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="6251f-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="6251f-260">Retourne un <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="6251f-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="6251f-261">Le jeton de modification qui en résulte expose les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="6251f-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="6251f-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Propriété qui peut être inspectée pour déterminer si une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="6251f-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="6251f-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Appelé lorsque des modifications sont détectées dans la chaîne de chemin d’accès spécifiée.</span><span class="sxs-lookup"><span data-stu-id="6251f-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="6251f-264">Chaque jeton de modification appelle uniquement son rappel associé en réponse à un changement unique.</span><span class="sxs-lookup"><span data-stu-id="6251f-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="6251f-265">Pour activer une surveillance constante, vous pouvez utiliser une <xref:System.Threading.Tasks.TaskCompletionSource`1> comme indiqué ci-dessous, ou recréer des instances `IChangeToken` en réponse aux changements.</span><span class="sxs-lookup"><span data-stu-id="6251f-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="6251f-266">L’exemple d’application *WatchConsole* écrit un message chaque fois qu’un fichier *. txt* dans le répertoire *Textfiles* est modifié :</span><span class="sxs-lookup"><span data-stu-id="6251f-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="6251f-267">Certains systèmes de fichiers, comme les conteneurs Docker et les partages réseau, peuvent ne pas envoyer de manière fiable les notifications de modifications.</span><span class="sxs-lookup"><span data-stu-id="6251f-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="6251f-268">Définissez la variable d’environnement `DOTNET_USE_POLLING_FILE_WATCHER` sur `1` ou `true` pour interroger le système de fichiers à la recherche de changements toutes les quatre secondes (non configurable).</span><span class="sxs-lookup"><span data-stu-id="6251f-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="6251f-269">Modèles Glob</span><span class="sxs-lookup"><span data-stu-id="6251f-269">Glob patterns</span></span>

<span data-ttu-id="6251f-270">Les chemins de système de fichiers utilisent des modèles à caractères génériques appelés *modèles Glob (ou d’utilisation des caractères génériques)*.</span><span class="sxs-lookup"><span data-stu-id="6251f-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="6251f-271">Spécifiez les groupes de fichiers avec ces modèles.</span><span class="sxs-lookup"><span data-stu-id="6251f-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="6251f-272">Les deux caractères génériques sont `*` et `**` :</span><span class="sxs-lookup"><span data-stu-id="6251f-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="6251f-273">Établit une correspondance avec n’importe quel élément au niveau de dossier actuel, avec n’importe quel nom de fichier ou avec n’importe quelle extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="6251f-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="6251f-274">Les correspondances sont terminées par des caractères `/` et `.` dans le chemin des fichiers.</span><span class="sxs-lookup"><span data-stu-id="6251f-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="6251f-275">Établit une correspondance avec n’importe quel élément sur plusieurs niveaux de répertoire.</span><span class="sxs-lookup"><span data-stu-id="6251f-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="6251f-276">Peut être utilisé pour établir une correspondance avec plusieurs fichiers dans une hiérarchie de répertoires de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="6251f-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="6251f-277">Le tableau suivant fournit des exemples courants de modèles glob.</span><span class="sxs-lookup"><span data-stu-id="6251f-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="6251f-278">Modèle</span><span class="sxs-lookup"><span data-stu-id="6251f-278">Pattern</span></span>  |<span data-ttu-id="6251f-279">Description</span><span class="sxs-lookup"><span data-stu-id="6251f-279">Description</span></span>  |
|---
<span data-ttu-id="6251f-280">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-281">'Blazor'</span></span>
- <span data-ttu-id="6251f-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-282">'Identity'</span></span>
- <span data-ttu-id="6251f-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-284">'Razor'</span></span>
- <span data-ttu-id="6251f-285">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-286">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-287">'Blazor'</span></span>
- <span data-ttu-id="6251f-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-288">'Identity'</span></span>
- <span data-ttu-id="6251f-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-290">'Razor'</span></span>
- <span data-ttu-id="6251f-291">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-291">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-292">-----|---
titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-293">'Blazor'</span></span>
- <span data-ttu-id="6251f-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-294">'Identity'</span></span>
- <span data-ttu-id="6251f-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-296">'Razor'</span></span>
- <span data-ttu-id="6251f-297">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-298">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-299">'Blazor'</span></span>
- <span data-ttu-id="6251f-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-300">'Identity'</span></span>
- <span data-ttu-id="6251f-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-302">'Razor'</span></span>
- <span data-ttu-id="6251f-303">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-303">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-304">-----|
|`directory/file.txt`| Correspond à un fichier spécifique dans un répertoire spécifique. | |`directory/*.txt`| Correspond à tous les fichiers avec l’extension *. txt* dans un répertoire spécifique. | |`directory/*/appsettings.json`| Met en correspondance tous les fichiers *appSettings. JSON* des répertoires exactement un niveau sous le dossier du *répertoire* . | |`directory/**/*.txt`| Correspond à tous les fichiers avec une extension *. txt* située n’importe où dans le dossier du *répertoire* . |</span><span class="sxs-lookup"><span data-stu-id="6251f-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6251f-305">ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers.</span><span class="sxs-lookup"><span data-stu-id="6251f-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="6251f-306">Des fournisseurs de fichiers sont utilisés dans l’infrastructure ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="6251f-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="6251f-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>expose la racine de [contenu](xref:fundamentals/index#content-root) et la [racine Web](xref:fundamentals/index#web-root) de l’application en tant que `IFileProvider` types.</span><span class="sxs-lookup"><span data-stu-id="6251f-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="6251f-308">[L’intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files) utilise des fournisseurs de fichiers pour localiser les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="6251f-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="6251f-309">[Razor](xref:mvc/views/razor)utilise des fournisseurs de fichiers pour localiser des pages et des vues.</span><span class="sxs-lookup"><span data-stu-id="6251f-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="6251f-310">Les outils .NET Core utilisent des fournisseurs de fichiers et des modèles d’utilisation des caractères génériques pour spécifier les fichiers qui doivent être publiés.</span><span class="sxs-lookup"><span data-stu-id="6251f-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="6251f-311">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6251f-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="6251f-312">Interfaces de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="6251f-312">File Provider interfaces</span></span>

<span data-ttu-id="6251f-313">L’interface principale est <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="6251f-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="6251f-314">`IFileProvider` expose des méthodes pour :</span><span class="sxs-lookup"><span data-stu-id="6251f-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="6251f-315">Obtenir des informations sur le fichier (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="6251f-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="6251f-316">Obtenir des informations sur le répertoire (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="6251f-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="6251f-317">Configurer des notifications de modification (à l’aide un <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="6251f-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="6251f-318">`IFileInfo` fournit des méthodes et propriétés d’utilisation des fichiers :</span><span class="sxs-lookup"><span data-stu-id="6251f-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="6251f-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en octets)</span><span class="sxs-lookup"><span data-stu-id="6251f-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="6251f-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>date</span><span class="sxs-lookup"><span data-stu-id="6251f-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="6251f-321">Vous pouvez lire à partir du fichier en utilisant la méthode [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).</span><span class="sxs-lookup"><span data-stu-id="6251f-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="6251f-322">L’exemple d’application montre comment configurer un fournisseur de fichiers dans `Startup.ConfigureServices` pour une utilisation dans toute l’application via [l’injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6251f-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="6251f-323">Implémentations de fournisseur de fichiers</span><span class="sxs-lookup"><span data-stu-id="6251f-323">File Provider implementations</span></span>

<span data-ttu-id="6251f-324">Trois implémentations de `IFileProvider` sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="6251f-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="6251f-325">Implémentation</span><span class="sxs-lookup"><span data-stu-id="6251f-325">Implementation</span></span> | <span data-ttu-id="6251f-326">Description</span><span class="sxs-lookup"><span data-stu-id="6251f-326">Description</span></span> |
| ---
<span data-ttu-id="6251f-327">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-328">'Blazor'</span></span>
- <span data-ttu-id="6251f-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-329">'Identity'</span></span>
- <span data-ttu-id="6251f-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-331">'Razor'</span></span>
- <span data-ttu-id="6251f-332">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-333">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-334">'Blazor'</span></span>
- <span data-ttu-id="6251f-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-335">'Identity'</span></span>
- <span data-ttu-id="6251f-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-337">'Razor'</span></span>
- <span data-ttu-id="6251f-338">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-339">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-340">'Blazor'</span></span>
- <span data-ttu-id="6251f-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-341">'Identity'</span></span>
- <span data-ttu-id="6251f-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-343">'Razor'</span></span>
- <span data-ttu-id="6251f-344">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-345">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-346">'Blazor'</span></span>
- <span data-ttu-id="6251f-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-347">'Identity'</span></span>
- <span data-ttu-id="6251f-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-349">'Razor'</span></span>
- <span data-ttu-id="6251f-350">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-351">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-352">'Blazor'</span></span>
- <span data-ttu-id="6251f-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-353">'Identity'</span></span>
- <span data-ttu-id="6251f-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-355">'Razor'</span></span>
- <span data-ttu-id="6251f-356">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-356">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-357">------- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-358">'Blazor'</span></span>
- <span data-ttu-id="6251f-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-359">'Identity'</span></span>
- <span data-ttu-id="6251f-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-361">'Razor'</span></span>
- <span data-ttu-id="6251f-362">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-363">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-364">'Blazor'</span></span>
- <span data-ttu-id="6251f-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-365">'Identity'</span></span>
- <span data-ttu-id="6251f-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-367">'Razor'</span></span>
- <span data-ttu-id="6251f-368">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-369">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-370">'Blazor'</span></span>
- <span data-ttu-id="6251f-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-371">'Identity'</span></span>
- <span data-ttu-id="6251f-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-373">'Razor'</span></span>
- <span data-ttu-id="6251f-374">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-374">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | Le fournisseur physique est utilisé pour accéder aux fichiers physiques du système.</span><span class="sxs-lookup"><span data-stu-id="6251f-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="6251f-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Le fournisseur incorporé du manifeste est utilisé pour accéder aux fichiers incorporés dans les assemblys.</span><span class="sxs-lookup"><span data-stu-id="6251f-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="6251f-377">| | [CompositeFileProvider](#compositefileprovider) | Le fournisseur composite est utilisé pour fournir un accès combiné aux fichiers et aux répertoires à partir d’un ou de plusieurs autres fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6251f-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="6251f-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="6251f-378">PhysicalFileProvider</span></span>

<span data-ttu-id="6251f-379">La classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fournit un accès au système de fichiers physique.</span><span class="sxs-lookup"><span data-stu-id="6251f-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="6251f-380">`PhysicalFileProvider` utilise le type <xref:System.IO.File?displayProperty=fullName> (pour le fournisseur physique) et définissant comme portée tous les chemins d’un répertoire et de ses enfants.</span><span class="sxs-lookup"><span data-stu-id="6251f-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="6251f-381">Cette portée empêche l’accès au système de fichiers en dehors du répertoire spécifié et ses enfants.</span><span class="sxs-lookup"><span data-stu-id="6251f-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="6251f-382">Le scénario le plus courant pour créer et utiliser un `PhysicalFileProvider` consiste à demander un `IFileProvider` dans un constructeur par le biais de l’[injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="6251f-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6251f-383">Lors de l’instanciation directe de ce fournisseur, un chemin de répertoire est obligatoire et sert de chemin de base pour toutes les demandes effectuées à l’aide du fournisseur.</span><span class="sxs-lookup"><span data-stu-id="6251f-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="6251f-384">Le code suivant montre comment créer un `PhysicalFileProvider` et l’utiliser pour obtenir le contenu du répertoire et les informations sur le fichier :</span><span class="sxs-lookup"><span data-stu-id="6251f-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="6251f-385">Types dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="6251f-385">Types in the preceding example:</span></span>

* <span data-ttu-id="6251f-386">`provider` est une `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="6251f-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="6251f-387">`contents` est une `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="6251f-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="6251f-388">`fileInfo` est une `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="6251f-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="6251f-389">Le fournisseur de fichiers peut être utilisé pour itérer au sein du répertoire spécifié par `applicationRoot` ou pour appeler `GetFileInfo` afin d’obtenir des informations sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="6251f-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="6251f-390">Le fournisseur de fichiers n’a pas accès en dehors du répertoire `applicationRoot`.</span><span class="sxs-lookup"><span data-stu-id="6251f-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="6251f-391">L’exemple d’application crée le fournisseur dans la classe `Startup.ConfigureServices` de l’application à l’aide de [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) :</span><span class="sxs-lookup"><span data-stu-id="6251f-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="6251f-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="6251f-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="6251f-393"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> est utilisé pour accéder à des fichiers incorporés dans des assemblys.</span><span class="sxs-lookup"><span data-stu-id="6251f-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="6251f-394">`ManifestEmbeddedFileProvider` utilise un manifeste compilé dans l’assembly pour reconstruire les chemins d’accès d’origine des fichiers intégrés.</span><span class="sxs-lookup"><span data-stu-id="6251f-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="6251f-395">Pour générer un manifeste des fichiers incorporés, définissez la propriété `<GenerateEmbeddedFilesManifest>` sur `true`.</span><span class="sxs-lookup"><span data-stu-id="6251f-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="6251f-396">Spécifiez les fichiers à incorporer avec [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="6251f-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="6251f-397">Utilisez les [modèles glob](#glob-patterns) pour spécifier un ou plusieurs fichiers à incorporer dans l’assembly.</span><span class="sxs-lookup"><span data-stu-id="6251f-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="6251f-398">L’exemple d’application crée un `ManifestEmbeddedFileProvider` et transmet l’assembly en cours d’exécution à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="6251f-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="6251f-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6251f-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="6251f-400">Des surcharges supplémentaires vous permettent de :</span><span class="sxs-lookup"><span data-stu-id="6251f-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="6251f-401">Spécifier un chemin de fichier relatif.</span><span class="sxs-lookup"><span data-stu-id="6251f-401">Specify a relative file path.</span></span>
* <span data-ttu-id="6251f-402">Définir la portée de fichiers sur une date de dernière modification.</span><span class="sxs-lookup"><span data-stu-id="6251f-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="6251f-403">Nommer la ressource incorporée contenant le manifeste de fichier incorporé.</span><span class="sxs-lookup"><span data-stu-id="6251f-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="6251f-404">Surcharge</span><span class="sxs-lookup"><span data-stu-id="6251f-404">Overload</span></span> | <span data-ttu-id="6251f-405">Description</span><span class="sxs-lookup"><span data-stu-id="6251f-405">Description</span></span> |
| ---
<span data-ttu-id="6251f-406">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-407">'Blazor'</span></span>
- <span data-ttu-id="6251f-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-408">'Identity'</span></span>
- <span data-ttu-id="6251f-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-410">'Razor'</span></span>
- <span data-ttu-id="6251f-411">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-412">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-413">'Blazor'</span></span>
- <span data-ttu-id="6251f-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-414">'Identity'</span></span>
- <span data-ttu-id="6251f-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-416">'Razor'</span></span>
- <span data-ttu-id="6251f-417">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-417">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-418">---- | titre de--- : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-419">'Blazor'</span></span>
- <span data-ttu-id="6251f-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-420">'Identity'</span></span>
- <span data-ttu-id="6251f-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-422">'Razor'</span></span>
- <span data-ttu-id="6251f-423">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-424">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-425">'Blazor'</span></span>
- <span data-ttu-id="6251f-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-426">'Identity'</span></span>
- <span data-ttu-id="6251f-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-428">'Razor'</span></span>
- <span data-ttu-id="6251f-429">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6251f-430">titre : Auteur : Description : monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6251f-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6251f-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6251f-431">'Blazor'</span></span>
- <span data-ttu-id="6251f-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6251f-432">'Identity'</span></span>
- <span data-ttu-id="6251f-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6251f-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="6251f-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6251f-434">'Razor'</span></span>
- <span data-ttu-id="6251f-435">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6251f-435">'SignalR' uid:</span></span> 

<span data-ttu-id="6251f-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepte un `root` paramètre de chemin d’accès relatif facultatif.</span><span class="sxs-lookup"><span data-stu-id="6251f-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="6251f-437">Spécifiez `root` pour définir la portée des appels à <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> sur ces ressources sous le chemin d’accès fourni.</span><span class="sxs-lookup"><span data-stu-id="6251f-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="6251f-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepte un `root` paramètre de chemin d’accès relatif facultatif et un `lastModified` paramètre date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="6251f-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="6251f-439">La date `lastModified` définit la portée de la dernière date de modification pour les instances <xref:Microsoft.Extensions.FileProviders.IFileInfo> retournées par <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="6251f-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="6251f-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepte un `root` chemin d’accès, une `lastModified` date et des paramètres relatifs facultatifs `manifestName` .</span><span class="sxs-lookup"><span data-stu-id="6251f-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="6251f-441">`manifestName` représente le nom de la ressource incorporée contenant la manifeste.</span><span class="sxs-lookup"><span data-stu-id="6251f-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="6251f-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="6251f-442">CompositeFileProvider</span></span>

<span data-ttu-id="6251f-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combine des instances `IFileProvider`, en exposant une interface unique qui permet d’utiliser des fichiers de différents fournisseurs.</span><span class="sxs-lookup"><span data-stu-id="6251f-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="6251f-444">Quand vous créez `CompositeFileProvider`, vous passez une ou plusieurs instances `IFileProvider` à son constructeur.</span><span class="sxs-lookup"><span data-stu-id="6251f-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="6251f-445">Dans l’exemple d’application, un `PhysicalFileProvider` et un `ManifestEmbeddedFileProvider` fournissent des fichiers à un `CompositeFileProvider` inscrit dans le conteneur de service de l’application :</span><span class="sxs-lookup"><span data-stu-id="6251f-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="6251f-446">Suivre les modifications apportées</span><span class="sxs-lookup"><span data-stu-id="6251f-446">Watch for changes</span></span>

<span data-ttu-id="6251f-447">La méthode [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) offre un moyen d’observer un ou plusieurs fichiers ou répertoires afin de détecter les changements.</span><span class="sxs-lookup"><span data-stu-id="6251f-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="6251f-448">`Watch` accepte une chaîne de chemin, qui peut utiliser des [modèles d’utilisation des caractères génériques](#glob-patterns) pour spécifier plusieurs fichiers.</span><span class="sxs-lookup"><span data-stu-id="6251f-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="6251f-449">`Watch`Retourne un <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="6251f-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="6251f-450">Le jeton de modification expose :</span><span class="sxs-lookup"><span data-stu-id="6251f-450">The change token exposes:</span></span>

* <span data-ttu-id="6251f-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Propriété qui peut être inspectée pour déterminer si une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="6251f-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="6251f-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Appelé lorsque des modifications sont détectées dans la chaîne de chemin d’accès spécifiée.</span><span class="sxs-lookup"><span data-stu-id="6251f-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="6251f-453">Chaque jeton de modification appelle uniquement son rappel associé en réponse à un changement unique.</span><span class="sxs-lookup"><span data-stu-id="6251f-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="6251f-454">Pour activer une surveillance constante, vous pouvez utiliser une <xref:System.Threading.Tasks.TaskCompletionSource`1> comme indiqué ci-dessous, ou recréer des instances `IChangeToken` en réponse aux changements.</span><span class="sxs-lookup"><span data-stu-id="6251f-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="6251f-455">Dans l’exemple d’application, l’application console *WatchConsole* est configurée pour afficher un message chaque fois qu’un fichier texte est modifié :</span><span class="sxs-lookup"><span data-stu-id="6251f-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="6251f-456">Certains systèmes de fichiers, comme les conteneurs Docker et les partages réseau, peuvent ne pas envoyer de manière fiable les notifications de modifications.</span><span class="sxs-lookup"><span data-stu-id="6251f-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="6251f-457">Définissez la variable d’environnement `DOTNET_USE_POLLING_FILE_WATCHER` sur `1` ou `true` pour interroger le système de fichiers à la recherche de changements toutes les quatre secondes (non configurable).</span><span class="sxs-lookup"><span data-stu-id="6251f-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="6251f-458">Modèles Glob</span><span class="sxs-lookup"><span data-stu-id="6251f-458">Glob patterns</span></span>

<span data-ttu-id="6251f-459">Les chemins de système de fichiers utilisent des modèles à caractères génériques appelés *modèles Glob (ou d’utilisation des caractères génériques)*.</span><span class="sxs-lookup"><span data-stu-id="6251f-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="6251f-460">Spécifiez les groupes de fichiers avec ces modèles.</span><span class="sxs-lookup"><span data-stu-id="6251f-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="6251f-461">Les deux caractères génériques sont `*` et `**` :</span><span class="sxs-lookup"><span data-stu-id="6251f-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="6251f-462">Établit une correspondance avec n’importe quel élément au niveau de dossier actuel, avec n’importe quel nom de fichier ou avec n’importe quelle extension de fichier.</span><span class="sxs-lookup"><span data-stu-id="6251f-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="6251f-463">Les correspondances sont terminées par des caractères `/` et `.` dans le chemin des fichiers.</span><span class="sxs-lookup"><span data-stu-id="6251f-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="6251f-464">Établit une correspondance avec n’importe quel élément sur plusieurs niveaux de répertoire.</span><span class="sxs-lookup"><span data-stu-id="6251f-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="6251f-465">Peut être utilisé pour établir une correspondance avec plusieurs fichiers dans une hiérarchie de répertoires de manière récursive.</span><span class="sxs-lookup"><span data-stu-id="6251f-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="6251f-466">**Exemples de modèles d’utilisation des caractères génériques**</span><span class="sxs-lookup"><span data-stu-id="6251f-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="6251f-467">Établit une correspondance avec un fichier spécifique dans un répertoire spécifique.</span><span class="sxs-lookup"><span data-stu-id="6251f-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="6251f-468">Établit une correspondance avec tous les fichiers ayant l’extension *.txt* dans un répertoire spécifique.</span><span class="sxs-lookup"><span data-stu-id="6251f-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="6251f-469">Établit une correspondance avec tous les fichiers `appsettings.json` dans les répertoires situés exactement un niveau en dessous du dossier *répertoire*.</span><span class="sxs-lookup"><span data-stu-id="6251f-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="6251f-470">Établit une correspondance avec tous les fichiers ayant l’extension *.txt* et se trouvant n’importe où sous le dossier *répertoire*.</span><span class="sxs-lookup"><span data-stu-id="6251f-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
