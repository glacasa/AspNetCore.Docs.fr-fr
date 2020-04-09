---
title: ASP.NET Core Razor components classe bibliothèques
author: guardrex
description: Découvrez comment les composants Blazor peuvent être inclus dans les applications d’une bibliothèque de composants externes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218764"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="78f06-103">ASP.NET Core Razor components classe bibliothèques</span><span class="sxs-lookup"><span data-stu-id="78f06-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="78f06-104">Par [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="78f06-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="78f06-105">Les composants peuvent être partagés dans une [bibliothèque de classe Razor (RCL)](xref:razor-pages/ui-class) dans tous les projets.</span><span class="sxs-lookup"><span data-stu-id="78f06-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="78f06-106">Une *bibliothèque de classe de composants Razor* peut être incluse à partir de :</span><span class="sxs-lookup"><span data-stu-id="78f06-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="78f06-107">Un autre projet dans la solution.</span><span class="sxs-lookup"><span data-stu-id="78f06-107">Another project in the solution.</span></span>
* <span data-ttu-id="78f06-108">Un paquet NuGet.</span><span class="sxs-lookup"><span data-stu-id="78f06-108">A NuGet package.</span></span>
* <span data-ttu-id="78f06-109">Une bibliothèque .NET référencée.</span><span class="sxs-lookup"><span data-stu-id="78f06-109">A referenced .NET library.</span></span>

<span data-ttu-id="78f06-110">Tout comme les composants sont des types réguliers .NET, les composants fournis par un RCL sont des assemblages .NET normaux.</span><span class="sxs-lookup"><span data-stu-id="78f06-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="78f06-111">Créer un RCL</span><span class="sxs-lookup"><span data-stu-id="78f06-111">Create an RCL</span></span>

<span data-ttu-id="78f06-112">Suivez les conseils <xref:blazor/get-started> dans l’article pour configurer votre environnement pour Blazor.</span><span class="sxs-lookup"><span data-stu-id="78f06-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78f06-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78f06-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="78f06-114">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="78f06-114">Create a new project.</span></span>
1. <span data-ttu-id="78f06-115">Sélectionnez **Razor Class Library**.</span><span class="sxs-lookup"><span data-stu-id="78f06-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="78f06-116">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="78f06-116">Select **Next**.</span></span>
1. <span data-ttu-id="78f06-117">Dans le Créer un nouveau dialogue **de bibliothèque de classe Razor,** sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="78f06-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="78f06-118">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="78f06-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="78f06-119">Les exemples dans ce `MyComponentLib1`sujet utilisent le nom du projet .</span><span class="sxs-lookup"><span data-stu-id="78f06-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="78f06-120">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="78f06-120">Select **Create**.</span></span>
1. <span data-ttu-id="78f06-121">Ajouter le RCL à une solution :</span><span class="sxs-lookup"><span data-stu-id="78f06-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="78f06-122">Cliquez à droite sur la solution.</span><span class="sxs-lookup"><span data-stu-id="78f06-122">Right-click the solution.</span></span> <span data-ttu-id="78f06-123">Sélectionnez **Ajouter** > **le projet existant**.</span><span class="sxs-lookup"><span data-stu-id="78f06-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="78f06-124">Naviguez vers le dossier de projet du RCL.</span><span class="sxs-lookup"><span data-stu-id="78f06-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="78f06-125">Sélectionnez le fichier de projet de la RCL (*.csproj*).</span><span class="sxs-lookup"><span data-stu-id="78f06-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="78f06-126">Ajoutez une référence le RCL de l’application :</span><span class="sxs-lookup"><span data-stu-id="78f06-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="78f06-127">Cliquez à droite sur le projet d’application.</span><span class="sxs-lookup"><span data-stu-id="78f06-127">Right-click the app project.</span></span> <span data-ttu-id="78f06-128">Sélectionnez **Ajouter la** > **référence**.</span><span class="sxs-lookup"><span data-stu-id="78f06-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="78f06-129">Sélectionnez le projet RCL.</span><span class="sxs-lookup"><span data-stu-id="78f06-129">Select the RCL project.</span></span> <span data-ttu-id="78f06-130">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="78f06-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="78f06-131">Si les **pages de support et** la case de cocher vues sont sélectionnées lors de la génération du RCL à partir du modèle, ajoutez également un fichier *_Imports.razor* à la racine du projet généré avec le contenu suivant pour permettre l’auteur du composant Razor :</span><span class="sxs-lookup"><span data-stu-id="78f06-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="78f06-132">Ajouter manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="78f06-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="78f06-133">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="78f06-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="78f06-134">Utilisez le modèle Razor`razorclasslib`Class **Library** ( ) avec la nouvelle commande [dotnet](/dotnet/core/tools/dotnet-new) dans une coque de commande.</span><span class="sxs-lookup"><span data-stu-id="78f06-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="78f06-135">Dans l’exemple suivant, un `MyComponentLib1`RCL est créé nommé .</span><span class="sxs-lookup"><span data-stu-id="78f06-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="78f06-136">Le dossier qui `MyComponentLib1` tient est créé automatiquement lorsque la commande est exécutée :</span><span class="sxs-lookup"><span data-stu-id="78f06-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="78f06-137">Si `-s|--support-pages-and-views` le commutateur est utilisé lors de la génération de la RCL à partir du modèle, puis aussi ajouter un fichier *_Imports.razor* à la racine du projet généré avec le contenu suivant pour permettre l’auteur du composant Razor:</span><span class="sxs-lookup"><span data-stu-id="78f06-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="78f06-138">Ajouter manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="78f06-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="78f06-139">Pour ajouter la bibliothèque à un projet existant, utilisez le [dotnet ajouter](/dotnet/core/tools/dotnet-add-reference) la commande de référence dans une coque de commande.</span><span class="sxs-lookup"><span data-stu-id="78f06-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="78f06-140">Dans l’exemple suivant, le RCL est ajouté à l’application.</span><span class="sxs-lookup"><span data-stu-id="78f06-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="78f06-141">Exécutez la commande suivante à partir du dossier de projet de l’application avec le chemin vers la bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="78f06-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="78f06-142">Consommer un composant de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="78f06-142">Consume a library component</span></span>

<span data-ttu-id="78f06-143">Afin de consommer des composants définis dans une bibliothèque dans un autre projet, utilisez l’une ou l’autre des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="78f06-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="78f06-144">Utilisez le nom de type complet avec l’espace nom.</span><span class="sxs-lookup"><span data-stu-id="78f06-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="78f06-145">Utilisez la directive de [ \@Razor.](xref:mvc/views/razor#using)</span><span class="sxs-lookup"><span data-stu-id="78f06-145">Use Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="78f06-146">Les composants individuels peuvent être ajoutés par leur nom.</span><span class="sxs-lookup"><span data-stu-id="78f06-146">Individual components can be added by name.</span></span>

<span data-ttu-id="78f06-147">Dans les exemples suivants, `MyComponentLib1` est `SalesReport` une bibliothèque de composants contenant un composant.</span><span class="sxs-lookup"><span data-stu-id="78f06-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="78f06-148">Le `SalesReport` composant peut être référencé à l’aide de son nom de type complet avec namespace:</span><span class="sxs-lookup"><span data-stu-id="78f06-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="78f06-149">Le composant peut également être référencé si `@using` la bibliothèque est mise en œuvre avec une directive :</span><span class="sxs-lookup"><span data-stu-id="78f06-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="78f06-150">Inclure `@using MyComponentLib1` la directive dans le fichier *_Import.razor* de haut niveau pour mettre les composants de la bibliothèque à la disposition de l’ensemble d’un projet.</span><span class="sxs-lookup"><span data-stu-id="78f06-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="78f06-151">Ajoutez la directive à un fichier *_Import.razor* à n’importe quel niveau pour appliquer l’espace de nom à une seule page ou un ensemble de pages dans un dossier.</span><span class="sxs-lookup"><span data-stu-id="78f06-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="78f06-152">Créer une bibliothèque de classe de composants Razor avec des actifs statiques</span><span class="sxs-lookup"><span data-stu-id="78f06-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="78f06-153">Un RCL peut inclure des actifs statiques.</span><span class="sxs-lookup"><span data-stu-id="78f06-153">An RCL can include static assets.</span></span> <span data-ttu-id="78f06-154">Les actifs statiques sont disponibles pour toute application qui consomme la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="78f06-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="78f06-155">Pour plus d’informations, consultez <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="78f06-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="78f06-156">Construire, emballer et expédier à NuGet</span><span class="sxs-lookup"><span data-stu-id="78f06-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="78f06-157">Parce que les bibliothèques de composants sont des bibliothèques .NET standard, les emballages et les expédier à NuGet n’est pas différent de l’emballage et l’expédition d’une bibliothèque à NuGet.</span><span class="sxs-lookup"><span data-stu-id="78f06-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="78f06-158">L’emballage est effectué à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack) dans une coque de commande:</span><span class="sxs-lookup"><span data-stu-id="78f06-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="78f06-159">Téléchargez le paquet sur NuGet à l’aide de la commande [de poussée de nuget de pointnet](/dotnet/core/tools/dotnet-nuget-push) dans une coque de commande.</span><span class="sxs-lookup"><span data-stu-id="78f06-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78f06-160">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="78f06-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="78f06-161">Ajouter un fichier de configuration de liaison XML à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="78f06-161">Add an XML linker configuration file to a library</span></span>](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
