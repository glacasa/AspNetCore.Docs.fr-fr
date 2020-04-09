---
title: Zones dans ASP.NET Core
author: rick-anderson
description: Découvrez les zones, fonctionnalité d’ASP.NET MVC utilisée pour organiser des fonctionnalités connexes dans un groupe sous la forme d’un espace de noms distinct (pour le routage) et d’une structure de dossiers (pour les vues).
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625928"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="b7574-103">Zones dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7574-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="b7574-104">Par [Dhananjay Kumar](https://twitter.com/debug_mode) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7574-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7574-105">Les zones sont une fonctionnalité ASP.NET utilisée pour organiser les fonctionnalités connexes en un groupe en tant que distinct :</span><span class="sxs-lookup"><span data-stu-id="b7574-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="b7574-106">Namespace pour le routage.</span><span class="sxs-lookup"><span data-stu-id="b7574-106">Namespace for routing.</span></span>
* <span data-ttu-id="b7574-107">Structure de dossier pour les vues et les pages Razor.</span><span class="sxs-lookup"><span data-stu-id="b7574-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="b7574-108">L’utilisation de zones a pour effet de créer une hiérarchie pour les besoins du routage en ajoutant un autre paramètre de route, `area`, à `controller` et `action` ou une Razor Page `page`.</span><span class="sxs-lookup"><span data-stu-id="b7574-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="b7574-109">Les zones offrent un moyen de partitionner une application web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de Razor Pages, de contrôleurs, de vues et de modèles.</span><span class="sxs-lookup"><span data-stu-id="b7574-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="b7574-110">Une zone est en réalité une structure au sein d’une application.</span><span class="sxs-lookup"><span data-stu-id="b7574-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="b7574-111">Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers.</span><span class="sxs-lookup"><span data-stu-id="b7574-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="b7574-112">Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="b7574-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="b7574-113">Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau.</span><span class="sxs-lookup"><span data-stu-id="b7574-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="b7574-114">Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche.</span><span class="sxs-lookup"><span data-stu-id="b7574-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="b7574-115">Chacune de ces unités a sa propre zone pour accueillir les vues, les contrôleurs, les Razor Pages et les modèles.</span><span class="sxs-lookup"><span data-stu-id="b7574-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="b7574-116">Envisagez d’utiliser des zones dans un projet quand :</span><span class="sxs-lookup"><span data-stu-id="b7574-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="b7574-117">L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.</span><span class="sxs-lookup"><span data-stu-id="b7574-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="b7574-118">Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="b7574-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="b7574-119">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b7574-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b7574-120">L’exemple de code téléchargeable fournit une application de base pour tester les zones.</span><span class="sxs-lookup"><span data-stu-id="b7574-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="b7574-121">Si vous utilisez Razor Pages, consultez [Zones avec Razor Pages](#areas-with-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="b7574-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="b7574-122">Zones pour contrôleurs avec vues</span><span class="sxs-lookup"><span data-stu-id="b7574-122">Areas for controllers with views</span></span>

<span data-ttu-id="b7574-123">Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b7574-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="b7574-124">Une [structure de dossiers Zone](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="b7574-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="b7574-125">Contrôleurs avec [`[Area]`](#attribute) l’attribut d’associer le contrôleur à la zone :</span><span class="sxs-lookup"><span data-stu-id="b7574-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="b7574-126">[L’itinéraire de zone ajouté au démarrage](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="b7574-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="b7574-127">Structure de dossiers Zone</span><span class="sxs-lookup"><span data-stu-id="b7574-127">Area folder structure</span></span>

<span data-ttu-id="b7574-128">Imaginez une application qui contient deux groupes logiques, *Produits* et *Services*.</span><span class="sxs-lookup"><span data-stu-id="b7574-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="b7574-129">En utilisant des zones, la structure de dossiers se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="b7574-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="b7574-130">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="b7574-130">Project name</span></span>
  * <span data-ttu-id="b7574-131">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="b7574-131">Areas</span></span>
    * <span data-ttu-id="b7574-132">Products</span><span class="sxs-lookup"><span data-stu-id="b7574-132">Products</span></span>
      * <span data-ttu-id="b7574-133">Controllers</span><span class="sxs-lookup"><span data-stu-id="b7574-133">Controllers</span></span>
        * <span data-ttu-id="b7574-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b7574-134">HomeController.cs</span></span>
        * <span data-ttu-id="b7574-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="b7574-135">ManageController.cs</span></span>
      * <span data-ttu-id="b7574-136">Les vues</span><span class="sxs-lookup"><span data-stu-id="b7574-136">Views</span></span>
        * <span data-ttu-id="b7574-137">Accueil</span><span class="sxs-lookup"><span data-stu-id="b7574-137">Home</span></span>
          * <span data-ttu-id="b7574-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-138">Index.cshtml</span></span>
        * <span data-ttu-id="b7574-139">Gérer</span><span class="sxs-lookup"><span data-stu-id="b7574-139">Manage</span></span>
          * <span data-ttu-id="b7574-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-140">Index.cshtml</span></span>
          * <span data-ttu-id="b7574-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-141">About.cshtml</span></span>
    * <span data-ttu-id="b7574-142">Services</span><span class="sxs-lookup"><span data-stu-id="b7574-142">Services</span></span>
      * <span data-ttu-id="b7574-143">Controllers</span><span class="sxs-lookup"><span data-stu-id="b7574-143">Controllers</span></span>
        * <span data-ttu-id="b7574-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b7574-144">HomeController.cs</span></span>
      * <span data-ttu-id="b7574-145">Les vues</span><span class="sxs-lookup"><span data-stu-id="b7574-145">Views</span></span>
        * <span data-ttu-id="b7574-146">Accueil</span><span class="sxs-lookup"><span data-stu-id="b7574-146">Home</span></span>
          * <span data-ttu-id="b7574-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-147">Index.cshtml</span></span>

<span data-ttu-id="b7574-148">Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers.</span><span class="sxs-lookup"><span data-stu-id="b7574-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="b7574-149">La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="b7574-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="b7574-150">Associer le contrôleur à une zone</span><span class="sxs-lookup"><span data-stu-id="b7574-150">Associate the controller with an Area</span></span>

<span data-ttu-id="b7574-151">Les contrôleurs régionaux sont désignés avec l’attribut [ &lbrack;de zone&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="b7574-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="b7574-152">Ajouter une route de zone</span><span class="sxs-lookup"><span data-stu-id="b7574-152">Add Area route</span></span>

<span data-ttu-id="b7574-153">Les itinéraires de zone utilisent typiquement [le routage conventionnel](xref:mvc/controllers/routing#cr) plutôt que [l’acheminement d’attribut.](xref:mvc/controllers/routing#ar)</span><span class="sxs-lookup"><span data-stu-id="b7574-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="b7574-154">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="b7574-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="b7574-155">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="b7574-156">`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :</span><span class="sxs-lookup"><span data-stu-id="b7574-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="b7574-157">Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="b7574-158">Utilisation `{area:...}` `MapControllerRoute`avec :</span><span class="sxs-lookup"><span data-stu-id="b7574-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="b7574-159">Est le mécanisme le moins compliqué pour ajouter le routage dans les zones.</span><span class="sxs-lookup"><span data-stu-id="b7574-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="b7574-160">Correspond à tous `[Area("Area name")]` les contrôleurs avec l’attribut.</span><span class="sxs-lookup"><span data-stu-id="b7574-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="b7574-161">Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> pour créer deux routes de zone nommées :</span><span class="sxs-lookup"><span data-stu-id="b7574-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="b7574-162">Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="b7574-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="b7574-163">Génération de liens avec zones MVC</span><span class="sxs-lookup"><span data-stu-id="b7574-163">Link generation with MVC areas</span></span>

<span data-ttu-id="b7574-164">Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) illustre une génération de liens avec la zone spécifiée :</span><span class="sxs-lookup"><span data-stu-id="b7574-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="b7574-165">Le téléchargement de l’échantillon comprend une [vue partielle](xref:mvc/views/partial) qui contient :</span><span class="sxs-lookup"><span data-stu-id="b7574-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="b7574-166">Les liens précédents.</span><span class="sxs-lookup"><span data-stu-id="b7574-166">The preceding links.</span></span>
* <span data-ttu-id="b7574-167">Les liens similaires `area` au précédent sauf n’est pas spécifié.</span><span class="sxs-lookup"><span data-stu-id="b7574-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="b7574-168">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="b7574-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="b7574-169">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b7574-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="b7574-170">Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs [ambiantes](xref:mvc/controllers/routing#ambient).</span><span class="sxs-lookup"><span data-stu-id="b7574-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="b7574-171">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="b7574-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="b7574-172">Dans de nombreux cas pour l’application d’échantillon, l’utilisation des valeurs ambiantes génère des liens incorrects avec la balisage qui ne spécifie pas la zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="b7574-173">Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="b7574-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="b7574-174">Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="b7574-175">Pour partager une mise en page commune pour l’ensemble de l’application, gardez le *_ViewStart.cshtml* dans le [dossier de racine d’application](#arf).</span><span class="sxs-lookup"><span data-stu-id="b7574-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="b7574-176">Pour plus d'informations, consultez <xref:mvc/views/layout></span><span class="sxs-lookup"><span data-stu-id="b7574-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="b7574-177">Dossier de racine d’application</span><span class="sxs-lookup"><span data-stu-id="b7574-177">Application root folder</span></span>

<span data-ttu-id="b7574-178">Le dossier racine d’application est le dossier contenant *Startup.cs* dans l’application web créée avec les modèles ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7574-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="b7574-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="b7574-180">*/Vues/_ViewImports.cshtml*, pour MVC, et */Pages/_ViewImports.cshtml* pour Razor Pages, n’est pas importé aux vues dans les zones.</span><span class="sxs-lookup"><span data-stu-id="b7574-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="b7574-181">Utilisez l’une des approches suivantes pour donner une vue des importations à tous les points de vue :</span><span class="sxs-lookup"><span data-stu-id="b7574-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="b7574-182">Ajouter *_ViewImports.cshtml* au [dossier racine d’application.](#arf)</span><span class="sxs-lookup"><span data-stu-id="b7574-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="b7574-183">Un *_ViewImports.cshtml* dans le dossier de racine d’application s’appliquera à toutes les vues de l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="b7574-184">Copiez le fichier *_ViewImports.cshtml* au dossier de vue approprié sous les zones.</span><span class="sxs-lookup"><span data-stu-id="b7574-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="b7574-185">Le *fichier _ViewImports.cshtml* contient généralement [des importations d’aide tag,](xref:mvc/views/tag-helpers/intro) `@using`et `@inject` des déclarations.</span><span class="sxs-lookup"><span data-stu-id="b7574-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="b7574-186">Pour plus d’informations, voir [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="b7574-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="b7574-187">Changer le dossier de zone par défaut où sont stockées les vues</span><span class="sxs-lookup"><span data-stu-id="b7574-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="b7574-188">Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="b7574-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="b7574-189">Zones avec Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-189">Areas with Razor Pages</span></span>

<span data-ttu-id="b7574-190">Les zones avec `Areas/<area name>/Pages` des pages Razor nécessitent un dossier dans la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="b7574-191">La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) :</span><span class="sxs-lookup"><span data-stu-id="b7574-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="b7574-192">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="b7574-192">Project name</span></span>
  * <span data-ttu-id="b7574-193">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="b7574-193">Areas</span></span>
    * <span data-ttu-id="b7574-194">Products</span><span class="sxs-lookup"><span data-stu-id="b7574-194">Products</span></span>
      * <span data-ttu-id="b7574-195">Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-195">Pages</span></span>
        * <span data-ttu-id="b7574-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="b7574-196">_ViewImports</span></span>
        * <span data-ttu-id="b7574-197">À propos</span><span class="sxs-lookup"><span data-stu-id="b7574-197">About</span></span>
        * <span data-ttu-id="b7574-198">Index</span><span class="sxs-lookup"><span data-stu-id="b7574-198">Index</span></span>
    * <span data-ttu-id="b7574-199">Services</span><span class="sxs-lookup"><span data-stu-id="b7574-199">Services</span></span>
      * <span data-ttu-id="b7574-200">Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-200">Pages</span></span>
        * <span data-ttu-id="b7574-201">Gérer</span><span class="sxs-lookup"><span data-stu-id="b7574-201">Manage</span></span>
          * <span data-ttu-id="b7574-202">À propos</span><span class="sxs-lookup"><span data-stu-id="b7574-202">About</span></span>
          * <span data-ttu-id="b7574-203">Index</span><span class="sxs-lookup"><span data-stu-id="b7574-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="b7574-204">Génération de liens avec Razor Pages et des zones</span><span class="sxs-lookup"><span data-stu-id="b7574-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="b7574-205">Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :</span><span class="sxs-lookup"><span data-stu-id="b7574-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="b7574-206">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="b7574-207">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="b7574-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="b7574-208">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="b7574-209">Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes*.</span><span class="sxs-lookup"><span data-stu-id="b7574-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="b7574-210">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="b7574-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="b7574-211">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="b7574-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="b7574-212">Prenons l’exemple des liens générés à partir de l’extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="b7574-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="b7574-213">Pour le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b7574-213">For the preceding code:</span></span>

* <span data-ttu-id="b7574-214">Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`.</span><span class="sxs-lookup"><span data-stu-id="b7574-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="b7574-215">Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="b7574-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="b7574-216">Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.</span><span class="sxs-lookup"><span data-stu-id="b7574-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="b7574-217">Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="b7574-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="b7574-218">Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports</span><span class="sxs-lookup"><span data-stu-id="b7574-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="b7574-219">Un fichier *_ViewImports.cshtml* peut être ajouté à chaque dossier *Pages* pour importer l’espace de noms et des Tag Helpers à chaque page Razor dans le dossier.</span><span class="sxs-lookup"><span data-stu-id="b7574-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="b7574-220">Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b7574-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="b7574-221">Le balisage suivant montre la Razor Page */Services/Manage/About* :</span><span class="sxs-lookup"><span data-stu-id="b7574-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="b7574-222">Dans le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="b7574-222">In the preceding markup:</span></span>

* <span data-ttu-id="b7574-223">Le nom de domaine complet doit être utilisé pour spécifier le modèle (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="b7574-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="b7574-224">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="b7574-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="b7574-225">Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :</span><span class="sxs-lookup"><span data-stu-id="b7574-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="b7574-226">Le balisage suivant montre Razor Page */Products/About* :</span><span class="sxs-lookup"><span data-stu-id="b7574-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="b7574-227">Dans le fichier précédent, `@addTagHelper` l’espace nom et la directive sont importés au fichier par le fichier *Zones/Produits/Pages/_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b7574-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="b7574-228">Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="b7574-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="b7574-229">Disposition partagée pour les zones Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="b7574-230">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="b7574-231">Zones de publication</span><span class="sxs-lookup"><span data-stu-id="b7574-231">Publishing Areas</span></span>

<span data-ttu-id="b7574-232">Tous les fichiers \*.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="b7574-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b7574-233">Les zones sont une fonctionnalité d’ASP.NET qui servent à organiser les fonctionnalités associées dans un groupe sous la forme d’un espace de noms (pour le routage) et d’une structure de dossiers (pour les vues) distincts.</span><span class="sxs-lookup"><span data-stu-id="b7574-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="b7574-234">L’utilisation de zones a pour effet de créer une hiérarchie pour les besoins du routage en ajoutant un autre paramètre de route, `area`, à `controller` et `action` ou une Razor Page `page`.</span><span class="sxs-lookup"><span data-stu-id="b7574-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="b7574-235">Les zones offrent un moyen de partitionner une application web ASP.NET Core en groupes fonctionnels plus petits, chacun avec son propre ensemble de Razor Pages, de contrôleurs, de vues et de modèles.</span><span class="sxs-lookup"><span data-stu-id="b7574-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="b7574-236">Une zone est en réalité une structure au sein d’une application.</span><span class="sxs-lookup"><span data-stu-id="b7574-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="b7574-237">Dans un projet web ASP.NET Core, les composants logiques comme Pages, Controller et View sont conservés dans différents dossiers.</span><span class="sxs-lookup"><span data-stu-id="b7574-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="b7574-238">Le runtime ASP.NET Core utilise des conventions de nommage pour créer la relation entre ces composants.</span><span class="sxs-lookup"><span data-stu-id="b7574-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="b7574-239">Pour une application volumineuse, il peut être avantageux de partitionner l’application en différentes zones de fonctionnalités de premier niveau.</span><span class="sxs-lookup"><span data-stu-id="b7574-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="b7574-240">Par exemple, pour une application d’e-commerce à plusieurs entités, il peut s’agir de la caisse, de la facturation et de la recherche.</span><span class="sxs-lookup"><span data-stu-id="b7574-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="b7574-241">Chacune de ces unités a sa propre zone pour accueillir les vues, les contrôleurs, les Razor Pages et les modèles.</span><span class="sxs-lookup"><span data-stu-id="b7574-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="b7574-242">Envisagez d’utiliser des zones dans un projet quand :</span><span class="sxs-lookup"><span data-stu-id="b7574-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="b7574-243">L’application est constituée de plusieurs composants fonctionnels globaux qui peuvent être logiquement séparés.</span><span class="sxs-lookup"><span data-stu-id="b7574-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="b7574-244">Vous pouvez partitionner l’application de façon à pouvoir travailler indépendamment sur chaque zone fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="b7574-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="b7574-245">[Affichez ou téléchargez un exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b7574-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b7574-246">L’exemple de code téléchargeable fournit une application de base pour tester les zones.</span><span class="sxs-lookup"><span data-stu-id="b7574-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="b7574-247">Si vous utilisez Razor Pages, consultez [Zones avec Razor Pages](#areas-with-razor-pages) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="b7574-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="b7574-248">Zones pour contrôleurs avec vues</span><span class="sxs-lookup"><span data-stu-id="b7574-248">Areas for controllers with views</span></span>

<span data-ttu-id="b7574-249">Une application web ASP.NET Core type qui utilise des zones, des contrôleurs et des vues est constituée des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="b7574-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="b7574-250">Une [structure de dossiers Zone](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="b7574-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="b7574-251">Contrôleurs avec [`[Area]`](#attribute) l’attribut d’associer le contrôleur à la zone :</span><span class="sxs-lookup"><span data-stu-id="b7574-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="b7574-252">[L’itinéraire de zone ajouté au démarrage](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="b7574-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="b7574-253">Structure de dossiers Zone</span><span class="sxs-lookup"><span data-stu-id="b7574-253">Area folder structure</span></span>

<span data-ttu-id="b7574-254">Imaginez une application qui contient deux groupes logiques, *Produits* et *Services*.</span><span class="sxs-lookup"><span data-stu-id="b7574-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="b7574-255">En utilisant des zones, la structure de dossiers se présenterait comme suit :</span><span class="sxs-lookup"><span data-stu-id="b7574-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="b7574-256">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="b7574-256">Project name</span></span>
  * <span data-ttu-id="b7574-257">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="b7574-257">Areas</span></span>
    * <span data-ttu-id="b7574-258">Products</span><span class="sxs-lookup"><span data-stu-id="b7574-258">Products</span></span>
      * <span data-ttu-id="b7574-259">Controllers</span><span class="sxs-lookup"><span data-stu-id="b7574-259">Controllers</span></span>
        * <span data-ttu-id="b7574-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b7574-260">HomeController.cs</span></span>
        * <span data-ttu-id="b7574-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="b7574-261">ManageController.cs</span></span>
      * <span data-ttu-id="b7574-262">Les vues</span><span class="sxs-lookup"><span data-stu-id="b7574-262">Views</span></span>
        * <span data-ttu-id="b7574-263">Accueil</span><span class="sxs-lookup"><span data-stu-id="b7574-263">Home</span></span>
          * <span data-ttu-id="b7574-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-264">Index.cshtml</span></span>
        * <span data-ttu-id="b7574-265">Gérer</span><span class="sxs-lookup"><span data-stu-id="b7574-265">Manage</span></span>
          * <span data-ttu-id="b7574-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-266">Index.cshtml</span></span>
          * <span data-ttu-id="b7574-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-267">About.cshtml</span></span>
    * <span data-ttu-id="b7574-268">Services</span><span class="sxs-lookup"><span data-stu-id="b7574-268">Services</span></span>
      * <span data-ttu-id="b7574-269">Controllers</span><span class="sxs-lookup"><span data-stu-id="b7574-269">Controllers</span></span>
        * <span data-ttu-id="b7574-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="b7574-270">HomeController.cs</span></span>
      * <span data-ttu-id="b7574-271">Les vues</span><span class="sxs-lookup"><span data-stu-id="b7574-271">Views</span></span>
        * <span data-ttu-id="b7574-272">Accueil</span><span class="sxs-lookup"><span data-stu-id="b7574-272">Home</span></span>
          * <span data-ttu-id="b7574-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-273">Index.cshtml</span></span>

<span data-ttu-id="b7574-274">Si la disposition précédente est classique dans les cas où des zones sont utilisées, seuls les fichiers de vues sont nécessaires pour utiliser cette structure de dossiers.</span><span class="sxs-lookup"><span data-stu-id="b7574-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="b7574-275">La détection de vues recherche un fichier de vue de zone correspondant dans l’ordre suivant :</span><span class="sxs-lookup"><span data-stu-id="b7574-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="b7574-276">Associer le contrôleur à une zone</span><span class="sxs-lookup"><span data-stu-id="b7574-276">Associate the controller with an Area</span></span>

<span data-ttu-id="b7574-277">Les contrôleurs régionaux sont désignés avec l’attribut [ &lbrack;de zone&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :</span><span class="sxs-lookup"><span data-stu-id="b7574-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="b7574-278">Ajouter une route de zone</span><span class="sxs-lookup"><span data-stu-id="b7574-278">Add Area route</span></span>

<span data-ttu-id="b7574-279">Les routes de zones utilisent généralement un routage conventionnel plutôt qu’un routage par attributs.</span><span class="sxs-lookup"><span data-stu-id="b7574-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="b7574-280">Le routage conventionnel est dépendant de l’ordre.</span><span class="sxs-lookup"><span data-stu-id="b7574-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="b7574-281">En général, les routes avec des zones doivent être placées plus haut dans la table de routage, car elles sont plus spécifiques que les routes sans zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="b7574-282">`{area:...}` peut être utilisé comme jeton dans les modèles de route si l’espace d’url est uniforme dans toutes les zones :</span><span class="sxs-lookup"><span data-stu-id="b7574-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="b7574-283">Dans le code précédent, `exists` applique une contrainte qui impose à la route de correspondre à une zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="b7574-284">Pour ajouter un routage à des zones, le mécanisme le moins compliqué consiste à utiliser `{area:...}`.</span><span class="sxs-lookup"><span data-stu-id="b7574-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="b7574-285">Le code suivant utilise <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> pour créer deux routes de zone nommées :</span><span class="sxs-lookup"><span data-stu-id="b7574-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="b7574-286">Si vous utilisez `MapAreaRoute` avec ASP.NET Core 2.2, prenez connaissance de [ce problème sur GitHub](https://github.com/dotnet/AspNetCore/issues/7772).</span><span class="sxs-lookup"><span data-stu-id="b7574-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="b7574-287">Pour plus d’informations, consultez [Routage de zones](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="b7574-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="b7574-288">Génération de liens avec zones MVC</span><span class="sxs-lookup"><span data-stu-id="b7574-288">Link generation with MVC areas</span></span>

<span data-ttu-id="b7574-289">Le code suivant tiré de l’[exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) illustre une génération de liens avec la zone spécifiée :</span><span class="sxs-lookup"><span data-stu-id="b7574-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="b7574-290">Les liens générés par le code précédent sont valides où que ce soit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="b7574-291">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="b7574-292">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="b7574-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="b7574-293">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone et le même contrôleur.</span><span class="sxs-lookup"><span data-stu-id="b7574-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="b7574-294">Quand la zone ou le contrôleur n’est pas spécifié, le routage dépend des valeurs *ambiantes*.</span><span class="sxs-lookup"><span data-stu-id="b7574-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="b7574-295">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="b7574-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="b7574-296">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="b7574-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="b7574-297">Pour plus d’informations, consultez [Routage vers des actions de contrôleur](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="b7574-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="b7574-298">Disposition partagée pour les zones en utilisant le fichier _ViewStart.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="b7574-299">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="b7574-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="b7574-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="b7574-301">Dans son emplacement standard, */Views/_ViewImports.cshtml* ne s’applique pas aux zones.</span><span class="sxs-lookup"><span data-stu-id="b7574-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="b7574-302">Pour utiliser [Tag Helpers](xref:mvc/views/tag-helpers/intro)les aides `@using`Tag `@inject` courantes , , ou dans votre région, assurez-vous *qu’un fichier _ViewImports.cshtml* approprié [s’applique à vos vues de secteur](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="b7574-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="b7574-303">Si vous souhaitez obtenir le même comportement dans toutes vos vues, déplacez */Views/_ViewImports.cshtml* vers la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="b7574-304">Changer le dossier de zone par défaut où sont stockées les vues</span><span class="sxs-lookup"><span data-stu-id="b7574-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="b7574-305">Le code suivant remplace le dossier de zone par défaut `"Areas"` par `"MyAreas"` :</span><span class="sxs-lookup"><span data-stu-id="b7574-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="b7574-306">Zones avec Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-306">Areas with Razor Pages</span></span>

<span data-ttu-id="b7574-307">Les zones avec `Areas/<area name>/Pages` des pages Razor nécessitent un dossier dans la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="b7574-308">La structure de dossiers suivante est utilisée avec [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) :</span><span class="sxs-lookup"><span data-stu-id="b7574-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="b7574-309">Nom du projet</span><span class="sxs-lookup"><span data-stu-id="b7574-309">Project name</span></span>
  * <span data-ttu-id="b7574-310">Zones (Areas)</span><span class="sxs-lookup"><span data-stu-id="b7574-310">Areas</span></span>
    * <span data-ttu-id="b7574-311">Products</span><span class="sxs-lookup"><span data-stu-id="b7574-311">Products</span></span>
      * <span data-ttu-id="b7574-312">Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-312">Pages</span></span>
        * <span data-ttu-id="b7574-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="b7574-313">_ViewImports</span></span>
        * <span data-ttu-id="b7574-314">À propos</span><span class="sxs-lookup"><span data-stu-id="b7574-314">About</span></span>
        * <span data-ttu-id="b7574-315">Index</span><span class="sxs-lookup"><span data-stu-id="b7574-315">Index</span></span>
    * <span data-ttu-id="b7574-316">Services</span><span class="sxs-lookup"><span data-stu-id="b7574-316">Services</span></span>
      * <span data-ttu-id="b7574-317">Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-317">Pages</span></span>
        * <span data-ttu-id="b7574-318">Gérer</span><span class="sxs-lookup"><span data-stu-id="b7574-318">Manage</span></span>
          * <span data-ttu-id="b7574-319">À propos</span><span class="sxs-lookup"><span data-stu-id="b7574-319">About</span></span>
          * <span data-ttu-id="b7574-320">Index</span><span class="sxs-lookup"><span data-stu-id="b7574-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="b7574-321">Génération de liens avec Razor Pages et des zones</span><span class="sxs-lookup"><span data-stu-id="b7574-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="b7574-322">Le code suivant tiré de [l’exemple de code téléchargeable](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) illustre une génération de liens avec la zone spécifiée (par exemple, `asp-area="Products"`) :</span><span class="sxs-lookup"><span data-stu-id="b7574-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="b7574-323">Les liens générés par le code précédent sont valides où que ce soit dans l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="b7574-324">L’exemple de code téléchargeable comprend une [vue partielle](xref:mvc/views/partial) qui contient les liens précédents et les mêmes liens sans spécification de la zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="b7574-325">La vue partielle étant référencée dans le [fichier de disposition](xref:mvc/views/layout), chaque page de l’application affiche les liens générés.</span><span class="sxs-lookup"><span data-stu-id="b7574-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="b7574-326">Les liens générés sans spécification de la zone sont valides uniquement quand ils sont référencés dans une page contenue dans la même zone.</span><span class="sxs-lookup"><span data-stu-id="b7574-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="b7574-327">Quand la zone n’est pas spécifiée, le routage dépend des valeurs *ambiantes*.</span><span class="sxs-lookup"><span data-stu-id="b7574-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="b7574-328">Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens.</span><span class="sxs-lookup"><span data-stu-id="b7574-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="b7574-329">Dans de nombreux cas, pour l’exemple d’application, l’utilisation des valeurs ambiantes génère des liens incorrects.</span><span class="sxs-lookup"><span data-stu-id="b7574-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="b7574-330">Prenons l’exemple des liens générés à partir de l’extrait de code suivant :</span><span class="sxs-lookup"><span data-stu-id="b7574-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="b7574-331">Pour le code précédent :</span><span class="sxs-lookup"><span data-stu-id="b7574-331">For the preceding code:</span></span>

* <span data-ttu-id="b7574-332">Le lien généré à partir de `<a asp-page="/Manage/About">` est correct uniquement lorsque la dernière requête concernait une page dans la zone `Services`.</span><span class="sxs-lookup"><span data-stu-id="b7574-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="b7574-333">Par exemple, `/Services/Manage/`, `/Services/Manage/Index` ou `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="b7574-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="b7574-334">Le lien généré à partir de `<a asp-page="/About">` est correct uniquement lorsque la dernière requête concernait une page dans `/Home`.</span><span class="sxs-lookup"><span data-stu-id="b7574-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="b7574-335">Le code est issu du [téléchargement de l’exemple](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="b7574-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="b7574-336">Importer l’espace de noms et les Tag Helpers avec le fichier _ViewImports</span><span class="sxs-lookup"><span data-stu-id="b7574-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="b7574-337">Un fichier *_ViewImports.cshtml* peut être ajouté à chaque dossier *Pages* pour importer l’espace de noms et des Tag Helpers à chaque page Razor dans le dossier.</span><span class="sxs-lookup"><span data-stu-id="b7574-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="b7574-338">Considérez la zone *Services* de l’exemple de code, qui ne contient pas de fichier *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b7574-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="b7574-339">Le balisage suivant montre la Razor Page */Services/Manage/About* :</span><span class="sxs-lookup"><span data-stu-id="b7574-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="b7574-340">Dans le balisage précédent :</span><span class="sxs-lookup"><span data-stu-id="b7574-340">In the preceding markup:</span></span>

* <span data-ttu-id="b7574-341">Le nom de domaine complet doit être utilisé pour spécifier le modèle (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="b7574-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="b7574-342">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="b7574-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="b7574-343">Dans l’exemple de téléchargement, la zone Products contient le fichier *_ViewImports.cshtml* suivant :</span><span class="sxs-lookup"><span data-stu-id="b7574-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="b7574-344">Le balisage suivant montre Razor Page */Products/About* :</span><span class="sxs-lookup"><span data-stu-id="b7574-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="b7574-345">Dans le fichier précédent, `@addTagHelper` l’espace nom et la directive sont importés au fichier par le fichier *Zones/Produits/Pages/_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b7574-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="b7574-346">Pour plus d’informations, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) et [Importation de directives partagées](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="b7574-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="b7574-347">Disposition partagée pour les zones Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b7574-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="b7574-348">Pour partager une disposition commune pour l’ensemble de l’application, déplacez *_ViewStart.cshtml* dans le dossier racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="b7574-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="b7574-349">Zones de publication</span><span class="sxs-lookup"><span data-stu-id="b7574-349">Publishing Areas</span></span>

<span data-ttu-id="b7574-350">Tous les fichiers \*.cshtml et autres fichiers dans le répertoire *wwwroot* sont publiés en sortie quand `<Project Sdk="Microsoft.NET.Sdk.Web">` est inclus dans le fichier \*.csproj.</span><span class="sxs-lookup"><span data-stu-id="b7574-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
