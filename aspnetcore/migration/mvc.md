---
title: Migrer ASP.NET MVC vers ASP.NET Core MVC
author: ardalis
description: Découvrez comment commencer à migrer un projet ASP.NET MVC vers ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777044"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="e4153-103">Migrer ASP.NET MVC vers ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="e4153-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="e4153-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)et [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="e4153-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="e4153-105">Cet article explique comment prendre en main la migration d’un projet ASP.NET MVC vers [ASP.net Core MVC](../mvc/overview.md).</span><span class="sxs-lookup"><span data-stu-id="e4153-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="e4153-106">Dans le processus, il met en évidence un grand nombre des éléments qui ont été modifiés par rapport à ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="e4153-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="e4153-107">La migration à partir de ASP.NET MVC est un processus en plusieurs étapes et cet article couvre l’installation initiale, les contrôleurs de base et les vues, le contenu statique et les dépendances côté client.</span><span class="sxs-lookup"><span data-stu-id="e4153-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="e4153-108">D’autres articles traitent de la migration du code de configuration et d’identité trouvé dans de nombreux projets MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e4153-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="e4153-109">Les numéros de version dans les exemples ne sont peut-être pas à jour.</span><span class="sxs-lookup"><span data-stu-id="e4153-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="e4153-110">Vous devrez peut-être mettre à jour vos projets en conséquence.</span><span class="sxs-lookup"><span data-stu-id="e4153-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="e4153-111">Créer le projet MVC ASP.NET de démarrage</span><span class="sxs-lookup"><span data-stu-id="e4153-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="e4153-112">Pour illustrer la mise à niveau, nous allons commencer par créer une application ASP.NET MVC.</span><span class="sxs-lookup"><span data-stu-id="e4153-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="e4153-113">Créez-le avec le nom *application Web 1* afin que l’espace de noms corresponde au projet ASP.net Core que nous créons à l’étape suivante.</span><span class="sxs-lookup"><span data-stu-id="e4153-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Boîte de dialogue Nouveau projet Visual Studio](mvc/_static/new-project.png)

![Boîte de dialogue nouvelle application Web : modèle de projet MVC sélectionné dans le panneau modèles ASP.NET](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="e4153-116">*Facultatif :* Remplacez le nom de la solution *application Web 1* par *Mvc5*.</span><span class="sxs-lookup"><span data-stu-id="e4153-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="e4153-117">Visual Studio affiche le nouveau nom de la solution (*Mvc5*), ce qui vous permet d’indiquer plus facilement ce projet à partir du projet suivant.</span><span class="sxs-lookup"><span data-stu-id="e4153-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="e4153-118">Créer le projet ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4153-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="e4153-119">Créez une application Web ASP.NET Core *vide* portant le même nom que le projet précédent (*application Web 1*) de sorte que les espaces de noms des deux projets correspondent.</span><span class="sxs-lookup"><span data-stu-id="e4153-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="e4153-120">Le fait d’avoir le même espace de noms facilite la copie du code entre les deux projets.</span><span class="sxs-lookup"><span data-stu-id="e4153-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="e4153-121">Vous devrez créer ce projet dans un répertoire différent de celui du projet précédent pour utiliser le même nom.</span><span class="sxs-lookup"><span data-stu-id="e4153-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![Boîte de dialogue Nouveau projet](mvc/_static/new_core.png)

![Boîte de dialogue nouvelle application Web ASP.NET : modèle de projet vide sélectionné dans ASP.NET Core panneau modèles](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="e4153-124">*Facultatif :* Créez une nouvelle ASP.NET Core application à l’aide du modèle de projet d' *application Web* .</span><span class="sxs-lookup"><span data-stu-id="e4153-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="e4153-125">Nommez le projet *application Web 1*, puis sélectionnez l’option d’authentification des **comptes d’utilisateur individuels**.</span><span class="sxs-lookup"><span data-stu-id="e4153-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="e4153-126">Renommez cette application en *FullAspNetCore*.</span><span class="sxs-lookup"><span data-stu-id="e4153-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="e4153-127">La création de ce projet vous fait gagner du temps lors de la conversion.</span><span class="sxs-lookup"><span data-stu-id="e4153-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="e4153-128">Vous pouvez consulter le code généré par le modèle pour afficher le résultat final ou copier le code dans le projet de conversion.</span><span class="sxs-lookup"><span data-stu-id="e4153-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="e4153-129">Cela est également utile lorsque vous vous rendez bloqué sur une étape de conversion pour comparer avec le projet généré par modèle.</span><span class="sxs-lookup"><span data-stu-id="e4153-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="e4153-130">Configurer le site pour utiliser MVC</span><span class="sxs-lookup"><span data-stu-id="e4153-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="e4153-131">Quand vous ciblez .NET Core, le [AspNetCore Microsoft.. app](xref:fundamentals/metapackage-app) est référencé par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4153-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="e4153-132">Ce package contient des packages couramment utilisés par les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="e4153-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="e4153-133">Si vous ciblez .NET Framework, les références de package doivent être listées individuellement dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="e4153-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="e4153-134">Quand vous ciblez .NET Core, le [AspNetCore Microsoft.. All](xref:fundamentals/metapackage) est référencé par défaut.</span><span class="sxs-lookup"><span data-stu-id="e4153-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="e4153-135">Ce package contient les packages les plus couramment utilisés par les applications MVC.</span><span class="sxs-lookup"><span data-stu-id="e4153-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="e4153-136">Si vous ciblez .NET Framework, les références de package doivent être listées individuellement dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="e4153-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="e4153-137">Quand vous ciblez .NET Core ou .NET Framework, les packages couramment utilisés par les applications MVC sont répertoriés individuellement dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="e4153-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="e4153-138">`Microsoft.AspNetCore.Mvc`est l’infrastructure MVC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4153-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="e4153-139">`Microsoft.AspNetCore.StaticFiles`est le gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="e4153-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="e4153-140">Le runtime ASP.NET Core est modulaire, et vous devez explicitement choisir de servir des fichiers statiques (voir [fichiers statiques](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="e4153-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="e4153-141">Ouvrez le fichier *Startup.cs* et modifiez le code pour qu’il corresponde à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="e4153-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="e4153-142">La `UseStaticFiles` méthode d’extension ajoute le gestionnaire de fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="e4153-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="e4153-143">Comme mentionné précédemment, le runtime ASP.NET est modulaire, et vous devez explicitement choisir de traiter les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="e4153-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="e4153-144">La `UseMvc` méthode d’extension ajoute le routage.</span><span class="sxs-lookup"><span data-stu-id="e4153-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="e4153-145">Pour plus d’informations, consultez démarrage et [routage](xref:fundamentals/routing)de l' [application](xref:fundamentals/startup) .</span><span class="sxs-lookup"><span data-stu-id="e4153-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="e4153-146">Ajouter un contrôleur et une vue</span><span class="sxs-lookup"><span data-stu-id="e4153-146">Add a controller and view</span></span>

<span data-ttu-id="e4153-147">Dans cette section, vous allez ajouter un contrôleur et une vue minimaux pour servir d’espaces réservés pour le contrôleur MVC ASP.NET et les vues que vous allez migrer dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="e4153-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="e4153-148">Ajoutez un dossier *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="e4153-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="e4153-149">Ajoutez une **classe de contrôleur** nommée *HomeController.cs* au dossier *Controllers* .</span><span class="sxs-lookup"><span data-stu-id="e4153-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="e4153-151">Ajoutez un dossier *views* .</span><span class="sxs-lookup"><span data-stu-id="e4153-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="e4153-152">Ajoutez un dossier *views/de départ* .</span><span class="sxs-lookup"><span data-stu-id="e4153-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="e4153-153">Ajoutez un \*\* Razor affichage\*\* nommé *index. cshtml* au dossier *views/de départ* .</span><span class="sxs-lookup"><span data-stu-id="e4153-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](mvc/_static/view.png)

<span data-ttu-id="e4153-155">La structure du projet est indiquée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="e4153-155">The project structure is shown below:</span></span>

![Explorateur de solutions de l’indication des fichiers et des dossiers de application Web 1](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="e4153-157">Remplacez le contenu du fichier *views/page d’index. cshtml* par le suivant :</span><span class="sxs-lookup"><span data-stu-id="e4153-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="e4153-158">Exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="e4153-158">Run the app.</span></span>

![Application Web ouverte dans Microsoft Edge](mvc/_static/hello-world.png)

<span data-ttu-id="e4153-160">Pour plus d’informations, consultez [contrôleurs](xref:mvc/controllers/actions) et [vues](xref:mvc/views/overview) .</span><span class="sxs-lookup"><span data-stu-id="e4153-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="e4153-161">Maintenant que nous avons un projet de ASP.NET Core de travail minimal, nous pouvons commencer à migrer les fonctionnalités à partir du projet MVC ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="e4153-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="e4153-162">Nous devons déplacer les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="e4153-162">We need to move the following:</span></span>

* <span data-ttu-id="e4153-163">contenu côté client (CSS, polices et scripts)</span><span class="sxs-lookup"><span data-stu-id="e4153-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="e4153-164">controllers</span><span class="sxs-lookup"><span data-stu-id="e4153-164">controllers</span></span>

* <span data-ttu-id="e4153-165">views</span><span class="sxs-lookup"><span data-stu-id="e4153-165">views</span></span>

* <span data-ttu-id="e4153-166">modèles</span><span class="sxs-lookup"><span data-stu-id="e4153-166">models</span></span>

* <span data-ttu-id="e4153-167">regroupement</span><span class="sxs-lookup"><span data-stu-id="e4153-167">bundling</span></span>

* <span data-ttu-id="e4153-168">filtres</span><span class="sxs-lookup"><span data-stu-id="e4153-168">filters</span></span>

* <span data-ttu-id="e4153-169">Connectez-vous, Identity (cette opération est effectuée dans le didacticiel suivant).</span><span class="sxs-lookup"><span data-stu-id="e4153-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="e4153-170">Contrôleurs et vues</span><span class="sxs-lookup"><span data-stu-id="e4153-170">Controllers and views</span></span>

* <span data-ttu-id="e4153-171">Copiez chacune des méthodes du MVC `HomeController` ASP.net vers le nouveau. `HomeController`</span><span class="sxs-lookup"><span data-stu-id="e4153-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="e4153-172">Notez que dans ASP.NET MVC, le type de retour de la méthode d’action de contrôleur du modèle intégré est [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); dans ASP.NET Core MVC, les méthodes d’action `IActionResult` retournent à la place.</span><span class="sxs-lookup"><span data-stu-id="e4153-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="e4153-173">`ActionResult`implémente `IActionResult`, il n’est donc pas nécessaire de modifier le type de retour de vos méthodes d’action.</span><span class="sxs-lookup"><span data-stu-id="e4153-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="e4153-174">Copiez les fichiers de vue *about. cshtml*, *contact. cshtml*et *index. cshtml* Razor du projet MVC ASP.net dans le projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e4153-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="e4153-175">Exécutez l’application ASP.NET Core et testez chaque méthode.</span><span class="sxs-lookup"><span data-stu-id="e4153-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="e4153-176">Nous n’avons pas encore migré le fichier de disposition ou les styles, de sorte que les vues rendues contiennent uniquement le contenu des fichiers de vue.</span><span class="sxs-lookup"><span data-stu-id="e4153-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="e4153-177">Vous n’aurez pas de liens générés par le `About` fichier `Contact` de disposition pour les vues et, vous devrez donc les appeler à partir du navigateur (remplacez **4492** par le numéro de port utilisé dans votre projet).</span><span class="sxs-lookup"><span data-stu-id="e4153-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Page de contact](mvc/_static/contact-page.png)

<span data-ttu-id="e4153-179">Notez l’absence de styles et d’éléments de menu.</span><span class="sxs-lookup"><span data-stu-id="e4153-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="e4153-180">Nous le corrigerons dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="e4153-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="e4153-181">Contenu statique</span><span class="sxs-lookup"><span data-stu-id="e4153-181">Static content</span></span>

<span data-ttu-id="e4153-182">Dans les versions précédentes de ASP.NET MVC, le contenu statique était hébergé à partir de la racine du projet Web et a été mélangé aux fichiers côté serveur.</span><span class="sxs-lookup"><span data-stu-id="e4153-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="e4153-183">Dans ASP.NET Core, le contenu statique est hébergé dans le dossier *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="e4153-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="e4153-184">Vous pouvez copier le contenu statique de votre ancienne application ASP.NET MVC dans le dossier *wwwroot* de votre projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e4153-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="e4153-185">Dans cet exemple de conversion :</span><span class="sxs-lookup"><span data-stu-id="e4153-185">In this sample conversion:</span></span>

* <span data-ttu-id="e4153-186">Copiez le fichier *favicon. ico* de l’ancien projet MVC dans le dossier *wwwroot* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e4153-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="e4153-187">L’ancien projet MVC ASP.NET utilise [bootstrap](https://getbootstrap.com/) pour son style et stocke les fichiers de démarrage dans les dossiers *content* et *scripts* .</span><span class="sxs-lookup"><span data-stu-id="e4153-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="e4153-188">Le modèle, qui a généré l’ancien projet MVC ASP.NET, référence le bootstrap dans le fichier de disposition (*Views/Shared/_Layout. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="e4153-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="e4153-189">Vous pouvez copier les fichiers *bootstrap. js* et *bootstrap. CSS* à partir du projet MVC ASP.net dans le dossier *wwwroot* dans le nouveau projet.</span><span class="sxs-lookup"><span data-stu-id="e4153-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="e4153-190">Au lieu de cela, nous ajouterons la prise en charge des démarrages (et d’autres bibliothèques côté client) à l’aide de CDN dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="e4153-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="e4153-191">Migrer le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="e4153-191">Migrate the layout file</span></span>

* <span data-ttu-id="e4153-192">Copiez le fichier *_ViewStart. cshtml* du dossier *views* de l’ancien projet MVC ASP.net dans le dossier *views* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e4153-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="e4153-193">Le fichier *_ViewStart. cshtml* n’a pas été modifié dans ASP.net Core Mvc.</span><span class="sxs-lookup"><span data-stu-id="e4153-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="e4153-194">Créez un dossier *Views/Shared* .</span><span class="sxs-lookup"><span data-stu-id="e4153-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="e4153-195">*Facultatif :* Copiez *_ViewImports. cshtml* du dossier *views* du projet MVC *FullAspNetCore* dans le dossier *views* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e4153-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="e4153-196">Supprimez toute déclaration d’espace de noms dans le fichier *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="e4153-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e4153-197">Le fichier *_ViewImports. cshtml* fournit des espaces de noms pour tous les fichiers de vue et insère les [balises](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="e4153-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="e4153-198">Les tag helpers sont utilisés dans le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="e4153-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="e4153-199">Le fichier *_ViewImports. cshtml* est nouveau pour ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e4153-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="e4153-200">Copiez le fichier *_Layout. cshtml* à partir de l’ancien dossier *Views/Shared* du projet MVC ASP.net dans le dossier *Views/Shared* du projet ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e4153-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="e4153-201">Ouvrez *_Layout fichier. cshtml* et apportez les modifications suivantes (le code complet est indiqué ci-dessous) :</span><span class="sxs-lookup"><span data-stu-id="e4153-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="e4153-202">Remplacez `@Styles.Render("~/Content/css")` par un `<link>` élément pour charger *bootstrap. CSS* (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="e4153-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="e4153-203">Supprimez `@Scripts.Render("~/bundles/modernizr")`.</span><span class="sxs-lookup"><span data-stu-id="e4153-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="e4153-204">Commentez la `@Html.Partial("_LoginPartial")` ligne (entourez la `@*...*@`ligne de).</span><span class="sxs-lookup"><span data-stu-id="e4153-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="e4153-205">Pour plus d’informations [, consultez migrer l’authentification et Identity vers ASP.net Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="e4153-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="e4153-206">Remplacez `@Scripts.Render("~/bundles/jquery")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="e4153-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="e4153-207">Remplacez `@Scripts.Render("~/bundles/bootstrap")` par un `<script>` élément (voir ci-dessous).</span><span class="sxs-lookup"><span data-stu-id="e4153-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="e4153-208">Balisage de remplacement pour l’inclusion CSS de démarrage :</span><span class="sxs-lookup"><span data-stu-id="e4153-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="e4153-209">Balisage de remplacement pour l’inclusion de jQuery et du code d’amorçage JavaScript :</span><span class="sxs-lookup"><span data-stu-id="e4153-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="e4153-210">Le fichier *_Layout. cshtml* mis à jour est affiché ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="e4153-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="e4153-211">Affichez le site dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="e4153-211">View the site in the browser.</span></span> <span data-ttu-id="e4153-212">Elle doit maintenant se charger correctement, avec les styles attendus en place.</span><span class="sxs-lookup"><span data-stu-id="e4153-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="e4153-213">*Facultatif :* Vous souhaiterez peut-être essayer d’utiliser le nouveau fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="e4153-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="e4153-214">Pour ce projet, vous pouvez copier le fichier de disposition à partir du projet *FullAspNetCore* .</span><span class="sxs-lookup"><span data-stu-id="e4153-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="e4153-215">Le nouveau fichier de disposition utilise [tag Helper](xref:mvc/views/tag-helpers/intro) et présente d’autres améliorations.</span><span class="sxs-lookup"><span data-stu-id="e4153-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="e4153-216">Configurer le regroupement et la minimisation</span><span class="sxs-lookup"><span data-stu-id="e4153-216">Configure bundling and minification</span></span>

<span data-ttu-id="e4153-217">Pour plus d’informations sur la configuration du regroupement et de la minimisation, consultez [regroupement et minimisation](../client-side/bundling-and-minification.md).</span><span class="sxs-lookup"><span data-stu-id="e4153-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="e4153-218">Résoudre les erreurs HTTP 500</span><span class="sxs-lookup"><span data-stu-id="e4153-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="e4153-219">De nombreux problèmes peuvent entraîner un message d’erreur HTTP 500 qui ne contient aucune information sur la source du problème.</span><span class="sxs-lookup"><span data-stu-id="e4153-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="e4153-220">Par exemple, si le fichier *views/_ViewImports. cshtml* contient un espace de noms qui n’existe pas dans votre projet, vous obtiendrez une erreur HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="e4153-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="e4153-221">Par défaut, dans ASP.NET Core Apps, `UseDeveloperExceptionPage` l’extension est ajoutée au `IApplicationBuilder` et exécutée lorsque la configuration est en cours de *développement*.</span><span class="sxs-lookup"><span data-stu-id="e4153-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="e4153-222">Ce code est détaillé dans le code suivant :</span><span class="sxs-lookup"><span data-stu-id="e4153-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="e4153-223">ASP.NET Core convertit les exceptions non gérées dans une application Web en réponses d’erreur HTTP 500.</span><span class="sxs-lookup"><span data-stu-id="e4153-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="e4153-224">Normalement, les détails de l’erreur ne sont pas inclus dans ces réponses pour empêcher la divulgation d’informations potentiellement sensibles sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="e4153-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="e4153-225">Pour plus d’informations, consultez **utilisation de la page d’exception de développeur** dans gérer les [Erreurs](../fundamentals/error-handling.md) .</span><span class="sxs-lookup"><span data-stu-id="e4153-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4153-226">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e4153-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
