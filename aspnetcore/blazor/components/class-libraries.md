---
title: RazorBibliothèques de classes des composants ASP.net Core
author: guardrex
description: Découvrez comment les composants peuvent être inclus dans des Blazor applications à partir d’une bibliothèque de composants externes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 0bace66e0aab41cf31e18fe9f86dbf9bbcf59447
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014696"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="8deed-103">RazorBibliothèques de classes des composants ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="8deed-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="8deed-104">Par [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="8deed-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="8deed-105">Les composants peuvent être partagés dans une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) d’un projet à l’autre.</span><span class="sxs-lookup"><span data-stu-id="8deed-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="8deed-106">Vous pouvez inclure une \* Razor bibliothèque de classes de composants\* à partir de :</span><span class="sxs-lookup"><span data-stu-id="8deed-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="8deed-107">Un autre projet dans la solution.</span><span class="sxs-lookup"><span data-stu-id="8deed-107">Another project in the solution.</span></span>
* <span data-ttu-id="8deed-108">Package NuGet.</span><span class="sxs-lookup"><span data-stu-id="8deed-108">A NuGet package.</span></span>
* <span data-ttu-id="8deed-109">Bibliothèque .NET référencée.</span><span class="sxs-lookup"><span data-stu-id="8deed-109">A referenced .NET library.</span></span>

<span data-ttu-id="8deed-110">Tout comme les composants sont des types .NET standard, les composants fournis par un RCL sont des assemblys .NET normaux.</span><span class="sxs-lookup"><span data-stu-id="8deed-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="8deed-111">Créer un RCL</span><span class="sxs-lookup"><span data-stu-id="8deed-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8deed-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8deed-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8deed-113">Créez un projet.</span><span class="sxs-lookup"><span data-stu-id="8deed-113">Create a new project.</span></span>
1. <span data-ttu-id="8deed-114">Sélectionnez \*\* Razor bibliothèque de classes\*\*.</span><span class="sxs-lookup"><span data-stu-id="8deed-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="8deed-115">Sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="8deed-115">Select **Next**.</span></span>
1. <span data-ttu-id="8deed-116">Dans la boîte de dialogue **créer une nouvelle Razor bibliothèque de classes** , sélectionnez **créer**.</span><span class="sxs-lookup"><span data-stu-id="8deed-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="8deed-117">Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut.</span><span class="sxs-lookup"><span data-stu-id="8deed-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8deed-118">Les exemples de cette rubrique utilisent le nom du projet `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="8deed-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="8deed-119">Sélectionnez **Create** (Créer).</span><span class="sxs-lookup"><span data-stu-id="8deed-119">Select **Create**.</span></span>
1. <span data-ttu-id="8deed-120">Ajouter RCL à une solution :</span><span class="sxs-lookup"><span data-stu-id="8deed-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="8deed-121">Cliquez avec le bouton droit sur la solution.</span><span class="sxs-lookup"><span data-stu-id="8deed-121">Right-click the solution.</span></span> <span data-ttu-id="8deed-122">Sélectionnez **Ajouter**un  >  **projet existant**.</span><span class="sxs-lookup"><span data-stu-id="8deed-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="8deed-123">Accédez au fichier projet de RCL.</span><span class="sxs-lookup"><span data-stu-id="8deed-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="8deed-124">Sélectionnez le fichier projet de RCL ( `.csproj` ).</span><span class="sxs-lookup"><span data-stu-id="8deed-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="8deed-125">Ajoutez une référence à l’RCL à partir de l’application :</span><span class="sxs-lookup"><span data-stu-id="8deed-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="8deed-126">Cliquez avec le bouton droit sur le projet d’application.</span><span class="sxs-lookup"><span data-stu-id="8deed-126">Right-click the app project.</span></span> <span data-ttu-id="8deed-127">Sélectionnez **Ajouter**une  >  **référence**.</span><span class="sxs-lookup"><span data-stu-id="8deed-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="8deed-128">Sélectionnez le projet RCL.</span><span class="sxs-lookup"><span data-stu-id="8deed-128">Select the RCL project.</span></span> <span data-ttu-id="8deed-129">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="8deed-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="8deed-130">Si la case à cocher **pages et vues de support** est activée lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :</span><span class="sxs-lookup"><span data-stu-id="8deed-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="8deed-131">Ajoutez manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="8deed-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8deed-132">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="8deed-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="8deed-133">Utilisez le modèle de \*\* Razor bibliothèque de classes\*\* ( `razorclasslib` ) avec la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="8deed-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="8deed-134">Dans l’exemple suivant, un RCL est créé nommé `ComponentLibrary` .</span><span class="sxs-lookup"><span data-stu-id="8deed-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="8deed-135">Le dossier qui contient `ComponentLibrary` est créé automatiquement lors de l’exécution de la commande :</span><span class="sxs-lookup"><span data-stu-id="8deed-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="8deed-136">Si le `-s|--support-pages-and-views` commutateur est utilisé lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :</span><span class="sxs-lookup"><span data-stu-id="8deed-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="8deed-137">Ajoutez manuellement le fichier à la racine du projet généré.</span><span class="sxs-lookup"><span data-stu-id="8deed-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="8deed-138">Pour ajouter la bibliothèque à un projet existant, utilisez la [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="8deed-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="8deed-139">Dans l’exemple suivant, le RCL est ajouté à l’application.</span><span class="sxs-lookup"><span data-stu-id="8deed-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="8deed-140">Exécutez la commande suivante à partir du dossier du projet de l’application avec le chemin d’accès à la bibliothèque :</span><span class="sxs-lookup"><span data-stu-id="8deed-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="8deed-141">Consommer un composant de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="8deed-141">Consume a library component</span></span>

<span data-ttu-id="8deed-142">Pour utiliser des composants définis dans une bibliothèque dans un autre projet, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="8deed-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="8deed-143">Utilisez le nom de type complet avec l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="8deed-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="8deed-144">Utilisez Razor [`@using`](xref:mvc/views/razor#using) la directive de.</span><span class="sxs-lookup"><span data-stu-id="8deed-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="8deed-145">Des composants individuels peuvent être ajoutés par nom.</span><span class="sxs-lookup"><span data-stu-id="8deed-145">Individual components can be added by name.</span></span>

<span data-ttu-id="8deed-146">Dans les exemples suivants, `ComponentLibrary` est une bibliothèque de composants contenant le `Component1` composant ( `Component1.razor` ).</span><span class="sxs-lookup"><span data-stu-id="8deed-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="8deed-147">Le `Component1` composant est un exemple de composant automatiquement ajouté par le modèle de projet RCL lors de la création de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="8deed-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="8deed-148">Référencez le `Component1` composant à l’aide de son espace de noms :</span><span class="sxs-lookup"><span data-stu-id="8deed-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="8deed-149">Vous pouvez également placer la bibliothèque dans la portée avec une [`@using`](xref:mvc/views/razor#using) directive et utiliser le composant sans son espace de noms :</span><span class="sxs-lookup"><span data-stu-id="8deed-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="8deed-150">Si vous le souhaitez, incluez la `@using ComponentLibrary` directive dans le fichier de niveau supérieur `_Import.razor` pour mettre les composants de la bibliothèque à la disposition d’un projet entier.</span><span class="sxs-lookup"><span data-stu-id="8deed-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="8deed-151">Ajoutez la directive à un `_Import.razor` fichier à tout niveau pour appliquer l’espace de noms à un composant unique ou à un ensemble de composants dans un dossier.</span><span class="sxs-lookup"><span data-stu-id="8deed-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="8deed-152">Pour fournir `Component1` `my-component` la classe CSS du composant, liez-le à la feuille de style de la bibliothèque à l’aide du [ `Link` composant](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) de l’infrastructure dans `Component1.razor` :</span><span class="sxs-lookup"><span data-stu-id="8deed-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="8deed-153">Pour fournir la feuille de style à l’ensemble de l’application, vous pouvez également créer un lien vers la feuille de style de la bibliothèque dans le fichier `wwwroot/index.html` () ou le fichier de l’application Blazor WebAssembly `Pages/_Host.cshtml` ( Blazor Server ) :</span><span class="sxs-lookup"><span data-stu-id="8deed-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="8deed-154">Lorsque le `Link` composant est utilisé dans un composant enfant, la ressource liée est également disponible pour tout autre composant enfant du composant parent, à condition que l’enfant avec le `Link` composant soit rendu.</span><span class="sxs-lookup"><span data-stu-id="8deed-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="8deed-155">La distinction entre l’utilisation du `Link` composant dans un composant enfant et la mise en place d’une `<link>` balise HTML dans `wwwroot/index.html` ou `Pages/_Host.cshtml` est que la balise HTML rendue d’un composant de l’infrastructure :</span><span class="sxs-lookup"><span data-stu-id="8deed-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="8deed-156">Peut être modifié par l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="8deed-156">Can be modified by application state.</span></span> <span data-ttu-id="8deed-157">Une `<link>` balise HTML codée en dur ne peut pas être modifiée par l’état de l’application.</span><span class="sxs-lookup"><span data-stu-id="8deed-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="8deed-158">Est supprimé du code HTML `<head>` lorsque le composant parent n’est plus rendu.</span><span class="sxs-lookup"><span data-stu-id="8deed-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="8deed-159">Pour fournir `Component1` la `my-component` classe CSS de, liez-la à la feuille de style de la bibliothèque dans le fichier `wwwroot/index.html` () ou le fichier de l’application Blazor WebAssembly `Pages/_Host.cshtml` ( Blazor Server ) :</span><span class="sxs-lookup"><span data-stu-id="8deed-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="8deed-160">Créer une Razor bibliothèque de classes de composants avec des ressources statiques</span><span class="sxs-lookup"><span data-stu-id="8deed-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="8deed-161">Un RCL peut inclure des ressources statiques.</span><span class="sxs-lookup"><span data-stu-id="8deed-161">An RCL can include static assets.</span></span> <span data-ttu-id="8deed-162">Les ressources statiques sont disponibles pour toutes les applications qui consomment la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="8deed-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="8deed-163">Pour plus d'informations, consultez <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="8deed-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="8deed-164">Fournir des composants et des ressources statiques à plusieurs applications hébergées Blazor</span><span class="sxs-lookup"><span data-stu-id="8deed-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="8deed-165">Pour plus d'informations, consultez <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="8deed-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="8deed-166">Générer, empaqueter et envoyer à NuGet</span><span class="sxs-lookup"><span data-stu-id="8deed-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="8deed-167">Étant donné que les bibliothèques de composants sont des bibliothèques .NET standard, leur empaquetage et leur envoi à NuGet ne sont pas différents de l’empaquetage et de l’expédition d’une bibliothèque à NuGet.</span><span class="sxs-lookup"><span data-stu-id="8deed-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="8deed-168">L’empaquetage est effectué à l’aide [`dotnet pack`](/dotnet/core/tools/dotnet-pack) de la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="8deed-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="8deed-169">Téléchargez le package dans NuGet à l’aide de la [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) commande dans un interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="8deed-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8deed-170">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="8deed-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="8deed-171">Ajouter un fichier de configuration de l’éditeur de liens XML à une bibliothèque</span><span class="sxs-lookup"><span data-stu-id="8deed-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
