---
title: RazorBibliothèques de classes des composants ASP.net Core
author: guardrex
description: Découvrez comment les composants peuvent être inclus dans des Blazor applications à partir d’une bibliothèque de composants externes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: b172059407f9a08dacc0fadd804864c7aee7fb90
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944498"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="47233-103">RazorBibliothèques de classes des composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="47233-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="47233-104">Par [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="47233-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="47233-105">Les composants peuvent être partagés dans une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) d’un projet à l’autre.</span><span class="sxs-lookup"><span data-stu-id="47233-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="47233-106">Vous pouvez inclure une \* Razor bibliothèque de classes de composants\* à partir de :</span><span class="sxs-lookup"><span data-stu-id="47233-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="47233-107">Un autre projet dans la solution.</span><span class="sxs-lookup"><span data-stu-id="47233-107">Another project in the solution.</span></span>
* <span data-ttu-id="47233-108">Package NuGet.</span><span class="sxs-lookup"><span data-stu-id="47233-108">A NuGet package.</span></span>
* <span data-ttu-id="47233-109">Bibliothèque .NET référencée.</span><span class="sxs-lookup"><span data-stu-id="47233-109">A referenced .NET library.</span></span>

<span data-ttu-id="47233-110">Tout comme les composants sont des types .NET standard, les composants fournis par un RCL sont des assemblys .NET normaux.</span><span class="sxs-lookup"><span data-stu-id="47233-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="47233-111">Créer un RCL</span><span class="sxs-lookup"><span data-stu-id="47233-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="47233-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="47233-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="47233-113">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="47233-113">Create a new project.</span></span>
1. <span data-ttu-id="47233-114">Sélectionnez \*\* Razor bibliothèque de classes\*\*.</span><span class="sxs-lookup"><span data-stu-id="47233-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="47233-115">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="47233-115">Select **Next**.</span></span>
1. <span data-ttu-id="47233-116">Dans la boîte de dialogue **créer une nouvelle Razor bibliothèque de classes** , sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="47233-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="47233-117">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="47233-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="47233-118">Les exemples de cette rubrique utilisent le nom du projet `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="47233-118">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="47233-119">Sélectionnez **Créer**.</span><span class="sxs-lookup"><span data-stu-id="47233-119">Select **Create**.</span></span>
1. <span data-ttu-id="47233-120">Ajouter RCL à une solution :</span><span class="sxs-lookup"><span data-stu-id="47233-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="47233-121">Cliquez avec le bouton droit sur la solution.</span><span class="sxs-lookup"><span data-stu-id="47233-121">Right-click the solution.</span></span> <span data-ttu-id="47233-122">Sélectionnez **Ajouter**un  >  **projet existant**.</span><span class="sxs-lookup"><span data-stu-id="47233-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="47233-123">Accédez au fichier projet de RCL.</span><span class="sxs-lookup"><span data-stu-id="47233-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="47233-124">Sélectionnez le fichier projet de RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="47233-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="47233-125">Ajoutez une référence à l’RCL à partir de l’application :</span><span class="sxs-lookup"><span data-stu-id="47233-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="47233-126">Cliquez avec le bouton droit sur le projet d’application.</span><span class="sxs-lookup"><span data-stu-id="47233-126">Right-click the app project.</span></span> <span data-ttu-id="47233-127">Sélectionnez **Ajouter**une  >  **référence**.</span><span class="sxs-lookup"><span data-stu-id="47233-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="47233-128">Sélectionnez le projet RCL.</span><span class="sxs-lookup"><span data-stu-id="47233-128">Select the RCL project.</span></span> <span data-ttu-id="47233-129">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="47233-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="47233-130">Si la case à cocher **pages et vues de support** est activée lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :</span><span class="sxs-lookup"><span data-stu-id="47233-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="47233-131">Ajoutez manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="47233-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="47233-132">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="47233-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="47233-133">Utilisez le modèle de \*\* Razor bibliothèque de classes\*\* ( `razorclasslib` ) avec la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="47233-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="47233-134">Dans l’exemple suivant, un RCL est créé nommé `MyComponentLib1` .</span><span class="sxs-lookup"><span data-stu-id="47233-134">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="47233-135">Le dossier qui contient `MyComponentLib1` est créé automatiquement lors de l’exécution de la commande :</span><span class="sxs-lookup"><span data-stu-id="47233-135">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="47233-136">Si le `-s|--support-pages-and-views` commutateur est utilisé lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :</span><span class="sxs-lookup"><span data-stu-id="47233-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="47233-137">Ajoutez manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="47233-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="47233-138">Pour ajouter la bibliothèque à un projet existant, utilisez la [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="47233-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="47233-139">Dans l’exemple suivant, le RCL est ajouté à l’application.</span><span class="sxs-lookup"><span data-stu-id="47233-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="47233-140">Exécutez la commande suivante à partir du dossier du projet de l’application avec le chemin d’accès à la bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="47233-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="47233-141">Consommer un composant de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="47233-141">Consume a library component</span></span>

<span data-ttu-id="47233-142">Pour utiliser des composants définis dans une bibliothèque dans un autre projet, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="47233-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="47233-143">Utilisez le nom de type complet avec l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="47233-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="47233-144">Utilisez Razor [`@using`](xref:mvc/views/razor#using) la directive de.</span><span class="sxs-lookup"><span data-stu-id="47233-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="47233-145">Des composants individuels peuvent être ajoutés par nom.</span><span class="sxs-lookup"><span data-stu-id="47233-145">Individual components can be added by name.</span></span>

<span data-ttu-id="47233-146">Dans les exemples suivants, `MyComponentLib1` est une bibliothèque de composants contenant un `SalesReport` composant.</span><span class="sxs-lookup"><span data-stu-id="47233-146">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="47233-147">Le `SalesReport` composant peut être référencé à l’aide de son nom de type complet avec l’espace de noms :</span><span class="sxs-lookup"><span data-stu-id="47233-147">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="47233-148">Le composant peut également être référencé si la bibliothèque est placée dans la portée avec une `@using` directive :</span><span class="sxs-lookup"><span data-stu-id="47233-148">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="47233-149">Incluez la `@using MyComponentLib1` directive dans le fichier de niveau supérieur `_Import.razor` pour mettre les composants de la bibliothèque à la disposition d’un projet entier.</span><span class="sxs-lookup"><span data-stu-id="47233-149">Include the `@using MyComponentLib1` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="47233-150">Ajoutez la directive à un `_Import.razor` fichier à tout niveau pour appliquer l’espace de noms à une seule page ou à un ensemble de pages dans un dossier.</span><span class="sxs-lookup"><span data-stu-id="47233-150">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="47233-151">Créer une Razor bibliothèque de classes de composants avec des ressources statiques</span><span class="sxs-lookup"><span data-stu-id="47233-151">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="47233-152">Un RCL peut inclure des ressources statiques.</span><span class="sxs-lookup"><span data-stu-id="47233-152">An RCL can include static assets.</span></span> <span data-ttu-id="47233-153">Les ressources statiques sont disponibles pour toutes les applications qui consomment la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="47233-153">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="47233-154">Pour plus d’informations, consultez <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="47233-154">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="47233-155">Générer, empaqueter et envoyer à NuGet</span><span class="sxs-lookup"><span data-stu-id="47233-155">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="47233-156">Étant donné que les bibliothèques de composants sont des bibliothèques .NET standard, leur empaquetage et leur envoi à NuGet ne sont pas différents de l’empaquetage et de l’expédition d’une bibliothèque à NuGet.</span><span class="sxs-lookup"><span data-stu-id="47233-156">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="47233-157">L’empaquetage est effectué à l’aide [`dotnet pack`](/dotnet/core/tools/dotnet-pack) de la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="47233-157">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="47233-158">Téléchargez le package dans NuGet à l’aide de la [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="47233-158">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="47233-159">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="47233-159">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="47233-160">Ajouter un fichier de configuration de l’éditeur de liens XML à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="47233-160">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
