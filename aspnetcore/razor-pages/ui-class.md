---
title: Interface utilisateur Razor réutilisable dans les bibliothèques de classes avec ASP.NET Core
author: Rick-Anderson
description: Explique comment créer une interface utilisateur Réutilisable Razor en utilisant des vues partielles dans une bibliothèque de classe à ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667565"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="76d4a-103">Créez une interface utilisateur réutilisable à l’aide du projet de bibliothèque de la classe Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="76d4a-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="76d4a-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="76d4a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="76d4a-105">Les vues de rasoir, les pages, les contrôleurs, les modèles de page, [les composants Razor,](xref:blazor/class-libraries) [les composants de vue,](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une bibliothèque de classe Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="76d4a-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="76d4a-106">La RCL peut être empaquetée et réutilisée.</span><span class="sxs-lookup"><span data-stu-id="76d4a-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="76d4a-107">Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient.</span><span class="sxs-lookup"><span data-stu-id="76d4a-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="76d4a-108">Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="76d4a-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="76d4a-109">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76d4a-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="76d4a-110">Créer une bibliothèque de classes contenant l’interface utilisateur Razor</span><span class="sxs-lookup"><span data-stu-id="76d4a-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76d4a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76d4a-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="76d4a-112">De Visual Studio **sélectionnez Créez un nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="76d4a-113">Sélectionnez **Razor Class Library** > **Next**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="76d4a-114">Nommez la bibliothèque (par exemple, "RazorClassLib"), > **Créer**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="76d4a-115">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="76d4a-116">Sélectionnez **les pages et les vues de support** si vous avez besoin de prendre en charge les vues.</span><span class="sxs-lookup"><span data-stu-id="76d4a-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="76d4a-117">Par défaut, seules les pages Razor sont prises en charge.</span><span class="sxs-lookup"><span data-stu-id="76d4a-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="76d4a-118">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="76d4a-118">Select **Create**.</span></span>

<span data-ttu-id="76d4a-119">Le modèle de bibliothèque de classe Razor (RCL) par défaut au développement de composants Razor par défaut.</span><span class="sxs-lookup"><span data-stu-id="76d4a-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="76d4a-120">**L’option Des pages et des vues** de support prend en charge les pages et les vues.</span><span class="sxs-lookup"><span data-stu-id="76d4a-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76d4a-121">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="76d4a-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76d4a-122">À partir de la ligne de commande, exécutez `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="76d4a-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="76d4a-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="76d4a-124">Le modèle de bibliothèque de classe Razor (RCL) par défaut au développement de composants Razor par défaut.</span><span class="sxs-lookup"><span data-stu-id="76d4a-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="76d4a-125">Passez `--support-pages-and-views` l’option`dotnet new razorclasslib --support-pages-and-views`() pour fournir un soutien pour les pages et les vues.</span><span class="sxs-lookup"><span data-stu-id="76d4a-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="76d4a-126">Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="76d4a-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="76d4a-127">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="76d4a-128">Ajoutez des fichiers Razor à la RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="76d4a-129">Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *Zones.*</span><span class="sxs-lookup"><span data-stu-id="76d4a-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="76d4a-130">Voir [la mise en page RCL Pages](#rcl-pages-layout) `~/Pages` pour `~/Areas/Pages`créer un RCL qui expose le contenu dans plutôt que .</span><span class="sxs-lookup"><span data-stu-id="76d4a-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="76d4a-131">Contenu de référence RCL</span><span class="sxs-lookup"><span data-stu-id="76d4a-131">Reference RCL content</span></span>

<span data-ttu-id="76d4a-132">La RCL peut être référencée par :</span><span class="sxs-lookup"><span data-stu-id="76d4a-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="76d4a-133">Un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="76d4a-133">NuGet package.</span></span> <span data-ttu-id="76d4a-134">Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="76d4a-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="76d4a-135">Un fichier *{NomProjet}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="76d4a-136">Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="76d4a-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="76d4a-137">Substituer des vues, des vues partielles et des pages</span><span class="sxs-lookup"><span data-stu-id="76d4a-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="76d4a-138">Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="76d4a-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="76d4a-139">Par exemple, ajoutez *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* à WebApp1, et Page1 dans le WebApp1 aura préséance sur Page1 dans le RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="76d4a-140">Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.</span><span class="sxs-lookup"><span data-stu-id="76d4a-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="76d4a-141">Copiez la vue partielle *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* dans *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="76d4a-142">Mettez à jour le balisage pour indiquer le nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="76d4a-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="76d4a-143">Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.</span><span class="sxs-lookup"><span data-stu-id="76d4a-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="76d4a-144">Mise en page de RCL Pages</span><span class="sxs-lookup"><span data-stu-id="76d4a-144">RCL Pages layout</span></span>

<span data-ttu-id="76d4a-145">Pour référencer le contenu RCL comme s’il faisait partie du dossier *Pages* de l’application Web, créez le projet RCL avec la structure de fichier suivante :</span><span class="sxs-lookup"><span data-stu-id="76d4a-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="76d4a-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="76d4a-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="76d4a-147">*RazorUIClassLib/Pages/Partagé*</span><span class="sxs-lookup"><span data-stu-id="76d4a-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="76d4a-148">Supposons *RazorUIClassLib/Pages/Shared* contient deux fichiers partiels: *_Header.cshtml* et *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="76d4a-149">Les `<partial>` balises pourraient être ajoutées au fichier *_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="76d4a-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="76d4a-150">Créer un RCL avec des actifs statiques</span><span class="sxs-lookup"><span data-stu-id="76d4a-150">Create an RCL with static assets</span></span>

<span data-ttu-id="76d4a-151">Un RCL peut nécessiter des actifs statiques complémentaires qui peuvent être référencés par le RCL ou l’application consommatrice du RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="76d4a-152">ASP.NET Core permet de créer des CCR qui incluent des actifs statiques qui sont disponibles pour une application consommatrice.</span><span class="sxs-lookup"><span data-stu-id="76d4a-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="76d4a-153">Pour inclure les actifs complémentaires dans le cadre d’un RCL, créez un dossier *wwwroot* dans la bibliothèque de classe et incluez tous les fichiers requis dans ce dossier.</span><span class="sxs-lookup"><span data-stu-id="76d4a-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="76d4a-154">Lors de l’emballage d’un RCL, tous les actifs compagnons dans le dossier *wwwroot* sont automatiquement inclus dans le paquet.</span><span class="sxs-lookup"><span data-stu-id="76d4a-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="76d4a-155">Exclure les actifs statiques</span><span class="sxs-lookup"><span data-stu-id="76d4a-155">Exclude static assets</span></span>

<span data-ttu-id="76d4a-156">Pour exclure les actifs statiques, `$(DefaultItemExcludes)` ajoutez le chemin d’exclusion souhaité au groupe immobilier dans le dossier du projet.</span><span class="sxs-lookup"><span data-stu-id="76d4a-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="76d4a-157">Entrées séparées avec un`;`point-virgule ( ).</span><span class="sxs-lookup"><span data-stu-id="76d4a-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="76d4a-158">Dans l’exemple suivant, la feuille de style *lib.css* dans le dossier *wwwroot* n’est pas considérée comme un actif statique et n’est pas incluse dans le RCL publié :</span><span class="sxs-lookup"><span data-stu-id="76d4a-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="76d4a-159">Intégration de typescript</span><span class="sxs-lookup"><span data-stu-id="76d4a-159">Typescript integration</span></span>

<span data-ttu-id="76d4a-160">Pour inclure les fichiers TypeScript dans un RCL :</span><span class="sxs-lookup"><span data-stu-id="76d4a-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="76d4a-161">Placez les fichiers TypeScript (*.ts*) à l’extérieur du dossier *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="76d4a-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="76d4a-162">Par exemple, placez les fichiers dans un dossier *Client.*</span><span class="sxs-lookup"><span data-stu-id="76d4a-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="76d4a-163">Configurez la sortie de build TypeScript pour le dossier *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="76d4a-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="76d4a-164">Réglez la `TypescriptOutDir` `PropertyGroup` propriété à l’intérieur d’un dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="76d4a-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="76d4a-165">Inclure la cible TypeScript comme `ResolveCurrentProjectStaticWebAssets` une dépendance de la `PropertyGroup` cible en ajoutant la cible suivante à l’intérieur d’un dans le fichier du projet :</span><span class="sxs-lookup"><span data-stu-id="76d4a-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="76d4a-166">Consommer du contenu d’un RCL référencé</span><span class="sxs-lookup"><span data-stu-id="76d4a-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="76d4a-167">Les fichiers inclus dans le dossier *wwwroot* du RCL sont exposés soit à `_content/{LIBRARY NAME}/`la RCL ou à l’application consommatrice sous le préfixe .</span><span class="sxs-lookup"><span data-stu-id="76d4a-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="76d4a-168">Par exemple, une bibliothèque nommée *Razor.Class.Lib* donne `_content/Razor.Class.Lib/`un chemin vers le contenu statique à .</span><span class="sxs-lookup"><span data-stu-id="76d4a-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="76d4a-169">Lorsque vous produisez un paquet NuGet et le nom d’assemblage n’est pas le même que l’ID du paquet, utilisez l’ID du paquet pour `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="76d4a-170">L’application de consommation fait référence `<script>` `<style>`aux `<img>`actifs statiques fournis par la bibliothèque avec , , , et d’autres balises HTML.</span><span class="sxs-lookup"><span data-stu-id="76d4a-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="76d4a-171">L’application consommatrice doit avoir [la prise en charge statique de fichiers](xref:fundamentals/static-files) activée dans `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="76d4a-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="76d4a-172">Lors de l’exécution de`dotnet run`l’application de consommation à partir de la sortie de construction ( ), les actifs web statiques sont activés par défaut dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="76d4a-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="76d4a-173">Pour soutenir les actifs dans d’autres `UseStaticWebAssets` environnements lorsqu’il s’exécute à partir de la production de construction, faites appel au constructeur hôte dans *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="76d4a-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="76d4a-174">L’appel `UseStaticWebAssets` n’est pas nécessaire lors`dotnet publish`de l’exécution d’une application à partir de la sortie publiée ( ).</span><span class="sxs-lookup"><span data-stu-id="76d4a-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="76d4a-175">Flux de développement multi-projets</span><span class="sxs-lookup"><span data-stu-id="76d4a-175">Multi-project development flow</span></span>

<span data-ttu-id="76d4a-176">Lorsque l’application consommatrice s’exécute :</span><span class="sxs-lookup"><span data-stu-id="76d4a-176">When the consuming app runs:</span></span>

* <span data-ttu-id="76d4a-177">Les actifs de la RCL restent dans leurs dossiers d’origine.</span><span class="sxs-lookup"><span data-stu-id="76d4a-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="76d4a-178">Les actifs ne sont pas transférés à l’application de consommation.</span><span class="sxs-lookup"><span data-stu-id="76d4a-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="76d4a-179">Tout changement dans le dossier *wwwroot* de la RCL se reflète dans l’application de consommation après la reconstruction du RCL et sans reconstruire l’application consommatrice.</span><span class="sxs-lookup"><span data-stu-id="76d4a-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="76d4a-180">Lorsque le RCL est construit, un manifeste est produit qui décrit les emplacements statiques d’actifs Web.</span><span class="sxs-lookup"><span data-stu-id="76d4a-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="76d4a-181">L’application de consommation lit le manifeste au moment de l’exécution pour consommer les actifs de projets et de paquets référencés.</span><span class="sxs-lookup"><span data-stu-id="76d4a-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="76d4a-182">Lorsqu’un nouvel actif est ajouté à un RCL, le RCL doit être reconstruit pour mettre à jour son manifeste avant qu’une application consommatrice puisse accéder au nouvel actif.</span><span class="sxs-lookup"><span data-stu-id="76d4a-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="76d4a-183">Publish</span><span class="sxs-lookup"><span data-stu-id="76d4a-183">Publish</span></span>

<span data-ttu-id="76d4a-184">Lorsque l’application est publiée, les actifs complémentaires de tous les projets et forfaits `_content/{LIBRARY NAME}/`référencés sont copiés dans le dossier *wwwroot* de l’application publiée en dessous .</span><span class="sxs-lookup"><span data-stu-id="76d4a-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="76d4a-185">Les vues de rasoir, les pages, les contrôleurs, les modèles de page, [les composants Razor,](xref:blazor/class-libraries) [les composants de vue,](xref:mvc/views/view-components)et les modèles de données peuvent être intégrés dans une bibliothèque de classe Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="76d4a-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="76d4a-186">La RCL peut être empaquetée et réutilisée.</span><span class="sxs-lookup"><span data-stu-id="76d4a-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="76d4a-187">Les applications peuvent inclure la RCL et remplacer les vues et les pages qu’elle contient.</span><span class="sxs-lookup"><span data-stu-id="76d4a-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="76d4a-188">Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="76d4a-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="76d4a-189">[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([comment télécharger](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="76d4a-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="76d4a-190">Créer une bibliothèque de classes contenant l’interface utilisateur Razor</span><span class="sxs-lookup"><span data-stu-id="76d4a-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76d4a-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76d4a-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="76d4a-192">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="76d4a-193">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="76d4a-194">Nommez la bibliothèque (par exemple, « RazorClassLib ») > **OK**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="76d4a-195">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="76d4a-196">Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="76d4a-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="76d4a-197">Sélectionnez **Razor Class Library** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="76d4a-198">Un RCL a le dossier de projet suivant :</span><span class="sxs-lookup"><span data-stu-id="76d4a-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="76d4a-199">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="76d4a-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76d4a-200">À partir de la ligne de commande, exécutez `dotnet new razorclasslib`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="76d4a-201">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="76d4a-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="76d4a-202">Pour plus d’informations, consultez [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="76d4a-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="76d4a-203">Pour éviter une collision de nom de fichier avec la bibliothèque de vues générée, vérifiez que le nom de la bibliothèque ne se termine pas par `.Views`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="76d4a-204">Ajoutez des fichiers Razor à la RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="76d4a-205">Les modèles ASP.NET Core supposent que le contenu RCL se trouve dans le dossier *Zones.*</span><span class="sxs-lookup"><span data-stu-id="76d4a-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="76d4a-206">Voir [la mise en page RCL Pages](#rcl-pages-layout) `~/Pages` pour `~/Areas/Pages`créer un RCL qui expose le contenu dans plutôt que .</span><span class="sxs-lookup"><span data-stu-id="76d4a-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="76d4a-207">Contenu de référence RCL</span><span class="sxs-lookup"><span data-stu-id="76d4a-207">Reference RCL content</span></span>

<span data-ttu-id="76d4a-208">La RCL peut être référencée par :</span><span class="sxs-lookup"><span data-stu-id="76d4a-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="76d4a-209">Un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="76d4a-209">NuGet package.</span></span> <span data-ttu-id="76d4a-210">Consultez [Création de packages NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) et [Créer et publier un package NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="76d4a-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="76d4a-211">Un fichier *{NomProjet}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="76d4a-212">Consultez [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="76d4a-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="76d4a-213">Procédure pas à pas : Créer un projet RCL et utiliser à partir d’un projet Razor Pages</span><span class="sxs-lookup"><span data-stu-id="76d4a-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="76d4a-214">Vous pouvez télécharger et tester le [projet complet](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) au lieu de le créer de toutes pièces.</span><span class="sxs-lookup"><span data-stu-id="76d4a-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="76d4a-215">L’exemple proposé sous forme de téléchargement contient du code et des liens supplémentaires qui facilitent le test du projet.</span><span class="sxs-lookup"><span data-stu-id="76d4a-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="76d4a-216">Si vous souhaitez commenter le mode d’obtention des exemples (téléchargement ou création au moyen d’instructions détaillées), entrez vos commentaires dans [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/6098).</span><span class="sxs-lookup"><span data-stu-id="76d4a-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="76d4a-217">Tester l’application téléchargée</span><span class="sxs-lookup"><span data-stu-id="76d4a-217">Test the download app</span></span>

<span data-ttu-id="76d4a-218">Si vous n’avez pas téléchargé l’application complète et que vous souhaitez créer le projet pas à pas, passez à la [section suivante](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="76d4a-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76d4a-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76d4a-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="76d4a-220">Ouvrez le fichier *.sln* dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="76d4a-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="76d4a-221">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="76d4a-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76d4a-222">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="76d4a-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76d4a-223">À partir d’une invite de commandes dans le répertoire *cli*, générez la RCL et l’application web.</span><span class="sxs-lookup"><span data-stu-id="76d4a-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="76d4a-224">Passez au répertoire *WebApp1* et exécutez l’application :</span><span class="sxs-lookup"><span data-stu-id="76d4a-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="76d4a-225">Suivez les instructions contenues dans [Test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="76d4a-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="76d4a-226">Créer un RCL</span><span class="sxs-lookup"><span data-stu-id="76d4a-226">Create an RCL</span></span>

<span data-ttu-id="76d4a-227">Dans cette section, un RCL est créé.</span><span class="sxs-lookup"><span data-stu-id="76d4a-227">In this section, an RCL is created.</span></span> <span data-ttu-id="76d4a-228">Des fichiers Razor sont ajoutés à la RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76d4a-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76d4a-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="76d4a-230">Créez le projet RCL :</span><span class="sxs-lookup"><span data-stu-id="76d4a-230">Create the RCL project:</span></span>

* <span data-ttu-id="76d4a-231">Dans Visual Studio, dans le menu **Fichier**, sélectionnez **Nouveau** > **Projet**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="76d4a-232">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="76d4a-233">Nommez l’application **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="76d4a-234">Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="76d4a-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="76d4a-235">Sélectionnez **Razor Class Library** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="76d4a-236">Ajoutez un fichier de vue partielle Razor nommé *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76d4a-237">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="76d4a-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76d4a-238">À partir de la ligne de commande, exécutez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="76d4a-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="76d4a-239">Les commandes précédentes :</span><span class="sxs-lookup"><span data-stu-id="76d4a-239">The preceding commands:</span></span>

* <span data-ttu-id="76d4a-240">Crée `RazorUIClassLib` le RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="76d4a-241">Créent une page _Message Razor et l’ajoutent à la RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="76d4a-242">Le paramètre `-np` crée la page sans `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="76d4a-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="76d4a-243">Crée un fichier [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) et l’ajoute au RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="76d4a-244">Le fichier *_ViewStart.cshtml* est nécessaire pour utiliser la mise en page du projet Razor Pages (qui est ajouté dans la section suivante).</span><span class="sxs-lookup"><span data-stu-id="76d4a-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="76d4a-245">Ajouter des fichiers et des dossiers Razor au projet</span><span class="sxs-lookup"><span data-stu-id="76d4a-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="76d4a-246">Remplacez le balisage dans *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="76d4a-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="76d4a-247">Remplacez le balisage dans *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="76d4a-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="76d4a-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` est nécessaire pour utiliser la vue partielle (`<partial name="_Message" />`).</span><span class="sxs-lookup"><span data-stu-id="76d4a-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="76d4a-249">Au lieu d’inclure la directive `@addTagHelper`, vous pouvez ajouter un fichier *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="76d4a-250">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="76d4a-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="76d4a-251">Pour plus d’informations sur *_ViewImports.cshtml*, voir [Directives partagées d’importation](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="76d4a-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="76d4a-252">Générez la bibliothèque de classes pour vérifier l’absence d’erreurs de compilateur :</span><span class="sxs-lookup"><span data-stu-id="76d4a-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="76d4a-253">La sortie de build contient *RazorUIClassLib.dll* et *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="76d4a-254">*RazorUIClassLib.Views.dll* contient le contenu Razor compilé.</span><span class="sxs-lookup"><span data-stu-id="76d4a-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="76d4a-255">Utiliser la bibliothèque de l’interface utilisateur Razor à partir d’un projet Razor Pages</span><span class="sxs-lookup"><span data-stu-id="76d4a-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76d4a-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76d4a-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="76d4a-257">Créez l’application web Razor Pages :</span><span class="sxs-lookup"><span data-stu-id="76d4a-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="76d4a-258">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur la solution > **Ajouter** >  **Nouveau projet**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="76d4a-259">Sélectionnez **Application web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="76d4a-260">Nommez l’application **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="76d4a-261">Vérifiez que **ASP.NET Core 2.1** ou ultérieur est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="76d4a-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="76d4a-262">Sélectionnez **Application web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="76d4a-263">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Définir comme projet de démarrage**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="76d4a-264">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Dépendances de build** > **Dépendances du projet**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="76d4a-265">Cochez **RazorUIClassLib** comme dépendance de **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="76d4a-266">Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur **WebApp1**, puis sélectionnez **Ajouter** > **Référence**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="76d4a-267">Dans la boîte de dialogue **Gestionnaire de références**, cochez **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="76d4a-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="76d4a-268">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="76d4a-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76d4a-269">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="76d4a-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76d4a-270">Créez une application Web Razor Pages et un fichier de solution contenant l’application Razor Pages et le RCL :</span><span class="sxs-lookup"><span data-stu-id="76d4a-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="76d4a-271">Générez et exécutez l’application web :</span><span class="sxs-lookup"><span data-stu-id="76d4a-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="76d4a-272">Tester WebApp1</span><span class="sxs-lookup"><span data-stu-id="76d4a-272">Test WebApp1</span></span>

<span data-ttu-id="76d4a-273">Naviguez `/MyFeature/Page1` pour vérifier que la bibliothèque de classe Razor UI est utilisée.</span><span class="sxs-lookup"><span data-stu-id="76d4a-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="76d4a-274">Substituer des vues, des vues partielles et des pages</span><span class="sxs-lookup"><span data-stu-id="76d4a-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="76d4a-275">Quand une vue, une vue partielle ou une page Razor est présente dans l’application web et la RCL, le balisage Razor (fichier *.cshtml*) dans l’application web est prioritaire.</span><span class="sxs-lookup"><span data-stu-id="76d4a-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="76d4a-276">Par exemple, ajoutez *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* à WebApp1, et Page1 dans le WebApp1 aura préséance sur Page1 dans le RCL.</span><span class="sxs-lookup"><span data-stu-id="76d4a-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="76d4a-277">Dans l’exemple proposé sous forme de téléchargement, renommez *WebApp1/Areas/MyFeature2* en *WebApp1/Areas/MyFeature* pour tester la priorité.</span><span class="sxs-lookup"><span data-stu-id="76d4a-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="76d4a-278">Copiez la vue partielle *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* dans *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="76d4a-279">Mettez à jour le balisage pour indiquer le nouvel emplacement.</span><span class="sxs-lookup"><span data-stu-id="76d4a-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="76d4a-280">Générez et exécutez l’application pour vérifier que la version de la vue partielle de l’application est utilisée.</span><span class="sxs-lookup"><span data-stu-id="76d4a-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="76d4a-281">Mise en page de RCL Pages</span><span class="sxs-lookup"><span data-stu-id="76d4a-281">RCL Pages layout</span></span>

<span data-ttu-id="76d4a-282">Pour référencer le contenu RCL comme s’il faisait partie du dossier *Pages* de l’application Web, créez le projet RCL avec la structure de fichier suivante :</span><span class="sxs-lookup"><span data-stu-id="76d4a-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="76d4a-283">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="76d4a-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="76d4a-284">*RazorUIClassLib/Pages/Partagé*</span><span class="sxs-lookup"><span data-stu-id="76d4a-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="76d4a-285">Supposons *RazorUIClassLib/Pages/Shared* contient deux fichiers partiels: *_Header.cshtml* et *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="76d4a-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="76d4a-286">Les `<partial>` balises pourraient être ajoutées au fichier *_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="76d4a-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="76d4a-287">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="76d4a-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
