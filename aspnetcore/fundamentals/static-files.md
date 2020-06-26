---
title: Fichiers statiques dans ASP.NET Core
author: rick-anderson
description: Découvrez comment délivrer et sécuriser des fichiers statiques, et comment configurer le comportement d’un intergiciel (middleware) hébergeant des fichiers statiques dans une application web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 33b4d43471932ed5e9e328163672028b00e6b5b8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399476"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="9dec3-103">Fichiers statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dec3-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="9dec3-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9dec3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9dec3-105">Les fichiers statiques, comme les fichiers HTML, CSS, images et JavaScript, sont des ressources qu’une application ASP.NET Core délivre directement aux clients.</span><span class="sxs-lookup"><span data-stu-id="9dec3-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="9dec3-106">Une configuration est nécessaire pour pouvoir délivrer ces fichiers.</span><span class="sxs-lookup"><span data-stu-id="9dec3-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="9dec3-107">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9dec3-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="9dec3-108">Délivrer des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="9dec3-108">Serve static files</span></span>

<span data-ttu-id="9dec3-109">Les fichiers statiques sont stockés dans le répertoire [racine Web](xref:fundamentals/index#web-root) du projet.</span><span class="sxs-lookup"><span data-stu-id="9dec3-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="9dec3-110">Le répertoire par défaut est *{content root}/wwwroot*, mais il peut être modifié à l’aide de la méthode [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) .</span><span class="sxs-lookup"><span data-stu-id="9dec3-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="9dec3-111">Pour plus d’informations, consultez [Racine du contenu](xref:fundamentals/index#content-root) et [Racine web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="9dec3-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="9dec3-112">L’hôte web de l’application doit être informé du répertoire racine du contenu.</span><span class="sxs-lookup"><span data-stu-id="9dec3-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="9dec3-113">La méthode `WebHost.CreateDefaultBuilder` définit le répertoire actif comme racine du contenu :</span><span class="sxs-lookup"><span data-stu-id="9dec3-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9dec3-114">Définissez le répertoire actif comme racine du contenu en appelant [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) à l’intérieur de `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="9dec3-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="9dec3-115">Les fichiers statiques sont accessibles via un chemin d’accès relatif à la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="9dec3-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="9dec3-116">Par exemple, le modèle de projet **Application web** contient plusieurs dossiers dans le dossier *wwwroot* :</span><span class="sxs-lookup"><span data-stu-id="9dec3-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="9dec3-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="9dec3-117">**wwwroot**</span></span>
  * <span data-ttu-id="9dec3-118">**format**</span><span class="sxs-lookup"><span data-stu-id="9dec3-118">**css**</span></span>
  * <span data-ttu-id="9dec3-119">**images**</span><span class="sxs-lookup"><span data-stu-id="9dec3-119">**images**</span></span>
  * <span data-ttu-id="9dec3-120">**js**</span><span class="sxs-lookup"><span data-stu-id="9dec3-120">**js**</span></span>

<span data-ttu-id="9dec3-121">Le format d’URI permettant d’accéder à un fichier dans le sous-dossier *images* est \*http:// \<server_address> /images/ \<image_file_name> \*.</span><span class="sxs-lookup"><span data-stu-id="9dec3-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="9dec3-122">Par exemple : *http://localhost:9189/images/banner3.svg* .</span><span class="sxs-lookup"><span data-stu-id="9dec3-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9dec3-123">Si vous ciblez .NET Framework, ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.</span><span class="sxs-lookup"><span data-stu-id="9dec3-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="9dec3-124">Si vous ciblez .NET Core, le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) comprend ce package.</span><span class="sxs-lookup"><span data-stu-id="9dec3-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9dec3-125">Si vous ciblez .NET Framework, ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.</span><span class="sxs-lookup"><span data-stu-id="9dec3-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="9dec3-126">Si vous ciblez .NET Core, le métapackage [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) inclut ce package.</span><span class="sxs-lookup"><span data-stu-id="9dec3-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9dec3-127">Ajoutez le package [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) au projet.</span><span class="sxs-lookup"><span data-stu-id="9dec3-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="9dec3-128">Configurez le [middleware](xref:fundamentals/middleware/index) qui permet de délivrer des fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="9dec3-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="9dec3-129">Délivrer des fichiers dans la racine web</span><span class="sxs-lookup"><span data-stu-id="9dec3-129">Serve files inside of web root</span></span>

<span data-ttu-id="9dec3-130">Appelez la méthode [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9dec3-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="9dec3-131">La `UseStaticFiles` surcharge de méthode sans paramètre marque les fichiers dans la [racine Web](xref:fundamentals/index#web-root) comme étant reservables.</span><span class="sxs-lookup"><span data-stu-id="9dec3-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="9dec3-132">Le balisage suivant référence *wwwroot/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="9dec3-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="9dec3-133">Dans le code précédent, le caractère tilde `~/` pointe vers la [racine Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="9dec3-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="9dec3-134">Délivrer des fichiers en dehors de la racine web</span><span class="sxs-lookup"><span data-stu-id="9dec3-134">Serve files outside of web root</span></span>

<span data-ttu-id="9dec3-135">Prenons l’exemple d’une hiérarchie de répertoires dans laquelle les fichiers statiques à prendre en charge se trouvent en dehors de la [racine Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="9dec3-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="9dec3-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="9dec3-136">**wwwroot**</span></span>
  * <span data-ttu-id="9dec3-137">**format**</span><span class="sxs-lookup"><span data-stu-id="9dec3-137">**css**</span></span>
  * <span data-ttu-id="9dec3-138">**images**</span><span class="sxs-lookup"><span data-stu-id="9dec3-138">**images**</span></span>
  * <span data-ttu-id="9dec3-139">**js**</span><span class="sxs-lookup"><span data-stu-id="9dec3-139">**js**</span></span>
* <span data-ttu-id="9dec3-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="9dec3-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="9dec3-141">**images**</span><span class="sxs-lookup"><span data-stu-id="9dec3-141">**images**</span></span>
    * <span data-ttu-id="9dec3-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="9dec3-142">*banner1.svg*</span></span>

<span data-ttu-id="9dec3-143">Vous permettez à une requête d’accéder au fichier *banner1.svg* en configurant le middleware (intergiciel) des fichiers statiques comme suit :</span><span class="sxs-lookup"><span data-stu-id="9dec3-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="9dec3-144">Dans le code précédent, la hiérarchie de répertoires *MyStaticFiles* est exposée publiquement via le segment d’URI *StaticFiles*.</span><span class="sxs-lookup"><span data-stu-id="9dec3-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="9dec3-145">Une demande à *http:// \<server_address> /StaticFiles/images/banner1.svg* sert le fichier *banner1. svg* .</span><span class="sxs-lookup"><span data-stu-id="9dec3-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="9dec3-146">Le balisage suivant référence *MyStaticFiles/images/banner1.svg* :</span><span class="sxs-lookup"><span data-stu-id="9dec3-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="9dec3-147">Définir des en-têtes de réponse HTTP</span><span class="sxs-lookup"><span data-stu-id="9dec3-147">Set HTTP response headers</span></span>

<span data-ttu-id="9dec3-148">Un objet [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) peut être utilisé pour définir des en-têtes de réponse HTTP.</span><span class="sxs-lookup"><span data-stu-id="9dec3-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="9dec3-149">En plus de configurer le service de fichiers statiques à partir de la [racine Web](xref:fundamentals/index#web-root), le code suivant définit l' `Cache-Control` en-tête :</span><span class="sxs-lookup"><span data-stu-id="9dec3-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="9dec3-150">La méthode [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) se trouve dans le package [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="9dec3-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="9dec3-151">Les fichiers peuvent être mis en cache publiquement pendant 10 minutes (600 secondes) dans l’environnement de développement :</span><span class="sxs-lookup"><span data-stu-id="9dec3-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![En-têtes de réponse montrant que l’en-tête Cache-Control a été ajouté](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="9dec3-153">Autorisations des fichiers statiques</span><span class="sxs-lookup"><span data-stu-id="9dec3-153">Static file authorization</span></span>

<span data-ttu-id="9dec3-154">Le middleware de fichiers statiques ne fournit pas de vérification des autorisations.</span><span class="sxs-lookup"><span data-stu-id="9dec3-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="9dec3-155">Tous les fichiers qu’il délivre, notamment ceux sous *wwwroot*, sont accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="9dec3-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="9dec3-156">Pour délivrer des fichiers en fonction d’une autorisation :</span><span class="sxs-lookup"><span data-stu-id="9dec3-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="9dec3-157">Stockez-les en dehors de *wwwroot* et de tout répertoire accessible au middleware de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="9dec3-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="9dec3-158">Délivrez-les via une méthode d’action à laquelle une autorisation est appliquée.</span><span class="sxs-lookup"><span data-stu-id="9dec3-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="9dec3-159">Retournez un objet [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) :</span><span class="sxs-lookup"><span data-stu-id="9dec3-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="9dec3-160">Activer l’exploration des répertoires</span><span class="sxs-lookup"><span data-stu-id="9dec3-160">Enable directory browsing</span></span>

<span data-ttu-id="9dec3-161">L’exploration des répertoires permet aux utilisateurs de votre application web de voir une liste des répertoires et des fichiers dans un répertoire spécifié.</span><span class="sxs-lookup"><span data-stu-id="9dec3-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="9dec3-162">L’exploration des répertoires est désactivée par défaut pour des raisons de sécurité (consultez [Considérations](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="9dec3-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="9dec3-163">Activez l’exploration des répertoires en appelant la méthode [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9dec3-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="9dec3-164">Ajoutez les services nécessaires en appelant la méthode [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) depuis `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9dec3-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="9dec3-165">Le code précédent autorise l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages*, avec des liens vers chaque fichier et dossier :</span><span class="sxs-lookup"><span data-stu-id="9dec3-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![exploration des répertoires](static-files/_static/dir-browse.png)

<span data-ttu-id="9dec3-167">Consultez [Considérations](#considerations) sur les risques de sécurité lors de l’activation de l’exploration.</span><span class="sxs-lookup"><span data-stu-id="9dec3-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="9dec3-168">Notez les deux appels de `UseStaticFiles` dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="9dec3-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="9dec3-169">Le premier appel permet de délivrer des fichiers statiques dans le dossier *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="9dec3-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="9dec3-170">Le deuxième appel active l’exploration des répertoires du dossier *wwwroot/images* à l’aide de l’URL *http:// \<server_address> /myImages*:</span><span class="sxs-lookup"><span data-stu-id="9dec3-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="9dec3-171">Délivrer un document par défaut</span><span class="sxs-lookup"><span data-stu-id="9dec3-171">Serve a default document</span></span>

<span data-ttu-id="9dec3-172">La définition d’une page d’accueil par défaut donne aux visiteurs un point de départ logique lors de la visite de votre site.</span><span class="sxs-lookup"><span data-stu-id="9dec3-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="9dec3-173">Pour délivrer une page par défaut sans que l’utilisateur qualifie complètement l’URI, appelez la méthode [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) depuis `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="9dec3-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="9dec3-174">`UseDefaultFiles` doit être appelé avant `UseStaticFiles` pour délivrer le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="9dec3-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="9dec3-175">`UseDefaultFiles` est un module de réécriture d’URL qui ne délivre pas réellement le fichier.</span><span class="sxs-lookup"><span data-stu-id="9dec3-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="9dec3-176">Activez le middleware de fichiers statiques via `UseStaticFiles` pour délivrer le fichier.</span><span class="sxs-lookup"><span data-stu-id="9dec3-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="9dec3-177">Avec `UseDefaultFiles`, les requêtes sur un dossier recherchent :</span><span class="sxs-lookup"><span data-stu-id="9dec3-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="9dec3-178">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="9dec3-178">*default.htm*</span></span>
* <span data-ttu-id="9dec3-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="9dec3-179">*default.html*</span></span>
* <span data-ttu-id="9dec3-180">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="9dec3-180">*index.htm*</span></span>
* <span data-ttu-id="9dec3-181">*index.html*</span><span class="sxs-lookup"><span data-stu-id="9dec3-181">*index.html*</span></span>

<span data-ttu-id="9dec3-182">Le premier fichier trouvé dans la liste est délivré comme si la requête était l’URI qualifié complet.</span><span class="sxs-lookup"><span data-stu-id="9dec3-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="9dec3-183">L’URL du navigateur continue de refléter l’URI demandé.</span><span class="sxs-lookup"><span data-stu-id="9dec3-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="9dec3-184">Le code suivant change le nom de fichier par défaut en *mydefault.html* :</span><span class="sxs-lookup"><span data-stu-id="9dec3-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="9dec3-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="9dec3-185">UseFileServer</span></span>

<span data-ttu-id="9dec3-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>combine les fonctionnalités de `UseStaticFiles` , `UseDefaultFiles` et éventuellement `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="9dec3-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="9dec3-187">Le code suivant active la possibilité de délivrer des fichiers statiques et le fichier par défaut.</span><span class="sxs-lookup"><span data-stu-id="9dec3-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="9dec3-188">L’exploration des répertoires n’est pas activée.</span><span class="sxs-lookup"><span data-stu-id="9dec3-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="9dec3-189">Le code suivant s’appuie sur la surcharge sans paramètre en activant l’exploration des répertoires :</span><span class="sxs-lookup"><span data-stu-id="9dec3-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="9dec3-190">Considérez la hiérarchie de répertoires suivante :</span><span class="sxs-lookup"><span data-stu-id="9dec3-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="9dec3-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="9dec3-191">**wwwroot**</span></span>
  * <span data-ttu-id="9dec3-192">**format**</span><span class="sxs-lookup"><span data-stu-id="9dec3-192">**css**</span></span>
  * <span data-ttu-id="9dec3-193">**images**</span><span class="sxs-lookup"><span data-stu-id="9dec3-193">**images**</span></span>
  * <span data-ttu-id="9dec3-194">**js**</span><span class="sxs-lookup"><span data-stu-id="9dec3-194">**js**</span></span>
* <span data-ttu-id="9dec3-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="9dec3-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="9dec3-196">**images**</span><span class="sxs-lookup"><span data-stu-id="9dec3-196">**images**</span></span>
    * <span data-ttu-id="9dec3-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="9dec3-197">*banner1.svg*</span></span>
  * <span data-ttu-id="9dec3-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="9dec3-198">*default.html*</span></span>

<span data-ttu-id="9dec3-199">Le code suivant active les fichiers statiques, les fichiers par défaut et l’exploration des répertoires de `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="9dec3-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="9dec3-200">`AddDirectoryBrowser` doit être appelé quand la valeur de la propriété `EnableDirectoryBrowsing` est `true` :</span><span class="sxs-lookup"><span data-stu-id="9dec3-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="9dec3-201">En utilisant la hiérarchie de fichiers et le code précédent, les URL sont résolues comme suit :</span><span class="sxs-lookup"><span data-stu-id="9dec3-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="9dec3-202">URI</span><span class="sxs-lookup"><span data-stu-id="9dec3-202">URI</span></span>            |                             <span data-ttu-id="9dec3-203">response</span><span class="sxs-lookup"><span data-stu-id="9dec3-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="9dec3-204">*http:// \<server_address> /StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="9dec3-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="9dec3-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="9dec3-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="9dec3-206">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="9dec3-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="9dec3-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="9dec3-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="9dec3-208">Si aucun fichier nommé par défaut n’existe dans le répertoire *MyStaticFiles* , *http:// \<server_address> /StaticFiles* retourne la liste des répertoires avec des liens interactives :</span><span class="sxs-lookup"><span data-stu-id="9dec3-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Liste des fichiers statiques](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="9dec3-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> et <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> effectuent une redirection côté client à partir de `http://{SERVER ADDRESS}/StaticFiles` (sans barre oblique) vers `http://{SERVER ADDRESS}/StaticFiles/` (avec barre oblique).</span><span class="sxs-lookup"><span data-stu-id="9dec3-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="9dec3-211">Les URL relatives du répertoire *StaticFiles* ne sont pas valides sans barre oblique de fin.</span><span class="sxs-lookup"><span data-stu-id="9dec3-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="9dec3-212">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="9dec3-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="9dec3-213">La classe [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) contient une propriété `Mappings` agissant comme un mappage des extensions de fichiers à des types de contenu MIME.</span><span class="sxs-lookup"><span data-stu-id="9dec3-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="9dec3-214">Dans l’exemple suivant, plusieurs extensions de fichiers sont inscrites avec des types MIME connus.</span><span class="sxs-lookup"><span data-stu-id="9dec3-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="9dec3-215">L’extension *.rtf* est remplacée et *.mp4* est supprimée.</span><span class="sxs-lookup"><span data-stu-id="9dec3-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="9dec3-216">Consultez [Types de contenu MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="9dec3-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="9dec3-217">Types de contenu non standard</span><span class="sxs-lookup"><span data-stu-id="9dec3-217">Non-standard content types</span></span>

<span data-ttu-id="9dec3-218">Static File Middleware comprend près de 400 types connus de contenu de fichier.</span><span class="sxs-lookup"><span data-stu-id="9dec3-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="9dec3-219">Si l’utilisateur demande un fichier d’un type inconnu, Static File Middleware transmet la requête à l’intergiciel (middleware) suivant dans le pipeline.</span><span class="sxs-lookup"><span data-stu-id="9dec3-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="9dec3-220">Si aucun intergiciel ne gère la requête, une réponse *404 introuvable* est retournée.</span><span class="sxs-lookup"><span data-stu-id="9dec3-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="9dec3-221">Si l’exploration des répertoires est activée, un lien vers le fichier est affiché dans la liste de répertoires.</span><span class="sxs-lookup"><span data-stu-id="9dec3-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="9dec3-222">Le code suivant permet de délivrer des types inconnus et rend le fichier inconnu en tant qu’image :</span><span class="sxs-lookup"><span data-stu-id="9dec3-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="9dec3-223">Avec le code précédent, une requête pour un fichier avec un type de contenu inconnu est retournée en tant qu’image.</span><span class="sxs-lookup"><span data-stu-id="9dec3-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="9dec3-224">L’activation de [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) présente un risque de sécurité.</span><span class="sxs-lookup"><span data-stu-id="9dec3-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="9dec3-225">Il est désactivé par défaut et son utilisation est déconseillée.</span><span class="sxs-lookup"><span data-stu-id="9dec3-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="9dec3-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) fournit une alternative plus sûre pour délivrer des fichiers avec des extensions non standard.</span><span class="sxs-lookup"><span data-stu-id="9dec3-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="9dec3-227">Servir des fichiers à partir de plusieurs emplacements</span><span class="sxs-lookup"><span data-stu-id="9dec3-227">Serve files from multiple locations</span></span>

<span data-ttu-id="9dec3-228">`UseStaticFiles``UseFileServer`la valeur par défaut est le fournisseur de fichiers qui pointe vers *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="9dec3-228">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="9dec3-229">Vous pouvez fournir des instances supplémentaires de `UseStaticFiles` et `UseFileServer` avec d’autres fournisseurs de fichiers pour servir des fichiers à partir d’autres emplacements.</span><span class="sxs-lookup"><span data-stu-id="9dec3-229">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="9dec3-230">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="9dec3-230">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="9dec3-231">Considérations</span><span class="sxs-lookup"><span data-stu-id="9dec3-231">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="9dec3-232">`UseDirectoryBrowser` et `UseStaticFiles` peuvent entraîner une fuite de secrets.</span><span class="sxs-lookup"><span data-stu-id="9dec3-232">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="9dec3-233">La désactivation de l’exploration de répertoires est fortement recommandée en production.</span><span class="sxs-lookup"><span data-stu-id="9dec3-233">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="9dec3-234">Examinez attentivement les répertoires qui sont activés via `UseStaticFiles` ou `UseDirectoryBrowser`.</span><span class="sxs-lookup"><span data-stu-id="9dec3-234">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="9dec3-235">L’ensemble du répertoire et de ses sous-répertoires deviennent accessibles publiquement.</span><span class="sxs-lookup"><span data-stu-id="9dec3-235">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="9dec3-236">Stocker les fichiers pouvant être affectés au public dans un répertoire dédié, par exemple \* \<content_root> /wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="9dec3-236">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="9dec3-237">Séparez ces fichiers des vues MVC, des Razor pages (2. x uniquement), des fichiers de configuration, etc.</span><span class="sxs-lookup"><span data-stu-id="9dec3-237">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="9dec3-238">Les URL pour le contenu exposé avec `UseDirectoryBrowser` et `UseStaticFiles` sont soumises aux restrictions de respect de la casse et de caractères du système de fichiers sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="9dec3-238">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="9dec3-239">Par exemple, Windows ne respecte pas la casse, mais macOS et Linux la respectent.</span><span class="sxs-lookup"><span data-stu-id="9dec3-239">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="9dec3-240">Les applications ASP.NET Core hébergées dans IIS utilisent le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pour transférer toutes les requêtes à l’application, notamment les requêtes de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="9dec3-240">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="9dec3-241">Le gestionnaire de fichiers statiques d’IIS n’est pas utilisé.</span><span class="sxs-lookup"><span data-stu-id="9dec3-241">The IIS static file handler isn't used.</span></span> <span data-ttu-id="9dec3-242">Il ne lui est pas permis de traiter les requêtes avant qu’elles soient gérées par le module.</span><span class="sxs-lookup"><span data-stu-id="9dec3-242">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="9dec3-243">Effectuez les étapes suivantes dans le Gestionnaire des services Internet (IIS) pour supprimer le gestionnaire de fichiers statiques d’IIS au niveau du serveur ou du site web :</span><span class="sxs-lookup"><span data-stu-id="9dec3-243">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="9dec3-244">Accédez à la fonctionnalité **Modules**.</span><span class="sxs-lookup"><span data-stu-id="9dec3-244">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="9dec3-245">Sélectionnez **StaticFileModule** dans la liste.</span><span class="sxs-lookup"><span data-stu-id="9dec3-245">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="9dec3-246">Cliquez sur **Supprimer** dans l’encadré **Actions**.</span><span class="sxs-lookup"><span data-stu-id="9dec3-246">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="9dec3-247">Si le gestionnaire de fichiers statiques d’IIS est activé **et** que le module ASP.NET Core est incorrectement configuré, les fichiers statiques peuvent être délivrés.</span><span class="sxs-lookup"><span data-stu-id="9dec3-247">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="9dec3-248">Cela se produit par exemple si le fichier *web.config* n’est pas déployé.</span><span class="sxs-lookup"><span data-stu-id="9dec3-248">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="9dec3-249">Placez les fichiers de code (y compris les fichiers *. cs* et *. cshtml*) en dehors de la [racine Web](xref:fundamentals/index#web-root)du projet d’application.</span><span class="sxs-lookup"><span data-stu-id="9dec3-249">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="9dec3-250">Par conséquent, une séparation logique est créée entre le contenu côté client et le code basé sur le serveur de l’application.</span><span class="sxs-lookup"><span data-stu-id="9dec3-250">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="9dec3-251">Ceci empêche la fuite de code côté serveur.</span><span class="sxs-lookup"><span data-stu-id="9dec3-251">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9dec3-252">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9dec3-252">Additional resources</span></span>

* [<span data-ttu-id="9dec3-253">Middleware</span><span class="sxs-lookup"><span data-stu-id="9dec3-253">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="9dec3-254">Introduction à ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9dec3-254">Introduction to ASP.NET Core</span></span>](xref:index)
