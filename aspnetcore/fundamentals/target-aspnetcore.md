---
title: Utiliser ASP.NET API de base dans une bibliothèque de classe
author: scottaddie
description: Apprenez à utiliser ASP.NET API de base dans une bibliothèque de classe.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- Blazor
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: 5374d7eec4334223a4bba7ee26cb6e2f208ed20b
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977195"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="57478-103">Utiliser ASP.NET API de base dans une bibliothèque de classe</span><span class="sxs-lookup"><span data-stu-id="57478-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="57478-104">Par [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="57478-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="57478-105">Ce document fournit des conseils pour l’utilisation des API de base ASP.NET dans une bibliothèque de classe.</span><span class="sxs-lookup"><span data-stu-id="57478-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="57478-106">Pour tous les autres conseils de bibliothèque, voir [les conseils de bibliothèque Open-source](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="57478-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="57478-107">Déterminer quelles ASP.NET versions Core à prendre en charge</span><span class="sxs-lookup"><span data-stu-id="57478-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="57478-108">ASP.NET Core adhère à la [politique de soutien de base .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="57478-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="57478-109">Consultez la politique de support pour déterminer quelles ASP.NET versions Core à prendre en charge dans une bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="57478-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="57478-110">Une bibliothèque doit :</span><span class="sxs-lookup"><span data-stu-id="57478-110">A library should:</span></span>

* <span data-ttu-id="57478-111">Faites un effort pour prendre en charge toutes les versions ASP.NET Core classées comme *support à long terme* (LTS).</span><span class="sxs-lookup"><span data-stu-id="57478-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="57478-112">Ne vous sentez pas obligé de prendre en charge ASP.NET versions Core classées *en fin de vie* (EOL).</span><span class="sxs-lookup"><span data-stu-id="57478-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="57478-113">Au fur et à mesure que les versions préliminaires de ASP.NET Core sont disponibles, des modifications de rupture sont affichées dans le référentiel [GitHub aspnet/Announcements.](https://github.com/aspnet/Announcements/issues)</span><span class="sxs-lookup"><span data-stu-id="57478-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="57478-114">Les tests de compatibilité des bibliothèques peuvent être effectués au fur et à mesure que des fonctionnalités-cadres sont en cours d’élaboration.</span><span class="sxs-lookup"><span data-stu-id="57478-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="57478-115">Utiliser le cadre partagé ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57478-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="57478-116">Avec la sortie de .NET Core 3.0, de nombreux assemblages ASP.NET Core ne sont plus publiés sur NuGet sous forme de paquets.</span><span class="sxs-lookup"><span data-stu-id="57478-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="57478-117">Au lieu de cela, `Microsoft.AspNetCore.App` les assemblages sont inclus dans le cadre partagé, qui est installé avec le .NET Core SDK et les installateurs de temps d’exécution.</span><span class="sxs-lookup"><span data-stu-id="57478-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="57478-118">Pour une liste de paquets qui ne sont plus publiés, voir [Supprimer les références de paquets obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="57478-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="57478-119">À partir de .NET Core 3.0, les projets utilisant le `Microsoft.NET.Sdk.Web` MSBuild SDK renvoie implicitement le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="57478-120">Les projets utilisant le `Microsoft.NET.Sdk` SDK ou `Microsoft.NET.Sdk.Razor` SDK doivent faire référence ASP.NET Core pour utiliser ASP.NET API de base dans le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="57478-121">Pour faire référence ASP.NET Core, `<FrameworkReference>` ajoutez l’élément suivant à votre dossier de projet :</span><span class="sxs-lookup"><span data-stu-id="57478-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="57478-122">Référencement ASP.NET Core de cette manière n’est pris en charge que pour les projets ciblant .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="57478-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="57478-123">Inclure l’extéabilité blazor</span><span class="sxs-lookup"><span data-stu-id="57478-123">Include Blazor extensibility</span></span>

<span data-ttu-id="57478-124">Blazor prend en charge les modèles [d’hébergement](xref:blazor/hosting-models)WebAssembly (WASM) et Server .</span><span class="sxs-lookup"><span data-stu-id="57478-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="57478-125">À moins qu’il n’y ait une raison spécifique de ne pas le faire, une bibliothèque [de composants Razor](xref:blazor/components) devrait prendre en charge les deux modèles d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="57478-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components) library should support both hosting models.</span></span> <span data-ttu-id="57478-126">Une bibliothèque de composants Razor doit utiliser le [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="57478-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="57478-127">Prendre en charge les deux modèles d’hébergement</span><span class="sxs-lookup"><span data-stu-id="57478-127">Support both hosting models</span></span>

<span data-ttu-id="57478-128">Pour prendre en charge la consommation de composants Razor provenant des projets [Blazor Server](xref:blazor/hosting-models#blazor-server) et [Blazor WASM,](xref:blazor/hosting-models#blazor-webassembly) utilisez les instructions suivantes pour votre éditeur.</span><span class="sxs-lookup"><span data-stu-id="57478-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57478-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57478-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57478-130">Utilisez le modèle de projet **Razor Class Library.**</span><span class="sxs-lookup"><span data-stu-id="57478-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="57478-131">Les pages de support et la case **ci-contre vues** du modèle doivent être désélectionnés.</span><span class="sxs-lookup"><span data-stu-id="57478-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57478-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57478-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57478-133">Exécuter la commande suivante dans le terminal intégré :</span><span class="sxs-lookup"><span data-stu-id="57478-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57478-134">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57478-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57478-135">Utilisez le modèle de projet **Razor Class Library.**</span><span class="sxs-lookup"><span data-stu-id="57478-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="57478-136">Le projet généré à partir du modèle fait les choses suivantes :</span><span class="sxs-lookup"><span data-stu-id="57478-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="57478-137">Cibles .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="57478-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="57478-138">Affecte la valeur `RazorLangVersion` à la propriété `3.0`.</span><span class="sxs-lookup"><span data-stu-id="57478-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="57478-139">`3.0`est la valeur par défaut pour .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="57478-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="57478-140">Ajoute les références de paquets suivantes :</span><span class="sxs-lookup"><span data-stu-id="57478-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="57478-141">Microsoft.AspNetCore.Composants</span><span class="sxs-lookup"><span data-stu-id="57478-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="57478-142">Microsoft.AspNetCore.Components.Web (en anglais seulement)</span><span class="sxs-lookup"><span data-stu-id="57478-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="57478-143">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="57478-144">Prendre en charge un modèle d’hébergement spécifique</span><span class="sxs-lookup"><span data-stu-id="57478-144">Support a specific hosting model</span></span>

<span data-ttu-id="57478-145">Il est beaucoup moins fréquent de prendre en charge un seul modèle d’hébergement Blazor.</span><span class="sxs-lookup"><span data-stu-id="57478-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="57478-146">À titre d’exemple, pour soutenir la consommation de composants Razor des projets [Blazor Server](xref:blazor/hosting-models#blazor-server) seulement :</span><span class="sxs-lookup"><span data-stu-id="57478-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="57478-147">Cible .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="57478-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="57478-148">Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="57478-149">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="57478-150">Pour plus d’informations sur les bibliothèques contenant des composants Razor, voir [ASP.NET core Razor composants classe bibliothèques](xref:blazor/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="57478-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="57478-151">Inclure l’extéabilité de MVC</span><span class="sxs-lookup"><span data-stu-id="57478-151">Include MVC extensibility</span></span>

<span data-ttu-id="57478-152">Cette section présente les recommandations à l’égard des bibliothèques qui comprennent :</span><span class="sxs-lookup"><span data-stu-id="57478-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="57478-153">Vues de rasoir ou pages de rasoir</span><span class="sxs-lookup"><span data-stu-id="57478-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="57478-154">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="57478-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="57478-155">Composants de vues</span><span class="sxs-lookup"><span data-stu-id="57478-155">View components</span></span>](#view-components)

<span data-ttu-id="57478-156">Cette section ne traite pas de multi-ciblage pour prendre en charge plusieurs versions de MVC.</span><span class="sxs-lookup"><span data-stu-id="57478-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="57478-157">Pour obtenir des conseils sur le support de plusieurs versions ASP.NET Core, voir [Support plusieurs versions ASP.NET Core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="57478-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="57478-158">Vues de rasoir ou pages de rasoir</span><span class="sxs-lookup"><span data-stu-id="57478-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="57478-159">Un projet qui comprend [des vues Razor](xref:mvc/views/overview) ou Des pages [Razor](xref:razor-pages/index) doit utiliser le [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="57478-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="57478-160">Si le projet cible .NET Core 3.x, il nécessite :</span><span class="sxs-lookup"><span data-stu-id="57478-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="57478-161">Une `AddRazorSupportForMvc` propriété MSBuild `true`réglé à .</span><span class="sxs-lookup"><span data-stu-id="57478-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="57478-162">Un `<FrameworkReference>` élément pour le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="57478-163">Le modèle de projet **Razor Class Library** répond aux exigences précédentes pour les projets ciblant .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="57478-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="57478-164">Utilisez les instructions suivantes pour votre éditeur.</span><span class="sxs-lookup"><span data-stu-id="57478-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57478-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57478-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57478-166">Utilisez le modèle de projet **Razor Class Library.**</span><span class="sxs-lookup"><span data-stu-id="57478-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="57478-167">Les pages **de support et** la case ci-pour-vue du modèle doivent être sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="57478-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="57478-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="57478-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="57478-169">Exécuter la commande suivante dans le terminal intégré :</span><span class="sxs-lookup"><span data-stu-id="57478-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="57478-170">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="57478-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="57478-171">Aucun modèle de projet de support pour le moment.</span><span class="sxs-lookup"><span data-stu-id="57478-171">No project template support at this time.</span></span>

---

<span data-ttu-id="57478-172">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="57478-173">Si le projet cible .NET Standard à la place, une référence de paquet [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) est nécessaire.</span><span class="sxs-lookup"><span data-stu-id="57478-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="57478-174">Le `Microsoft.AspNetCore.Mvc` paquet est passé dans le cadre commun dans ASP.NET Core 3.0 et n’est donc plus publié.</span><span class="sxs-lookup"><span data-stu-id="57478-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="57478-175">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="57478-176">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="57478-176">Tag Helpers</span></span>

<span data-ttu-id="57478-177">Un projet qui comprend Tag `Microsoft.NET.Sdk` [Helpers](xref:mvc/views/tag-helpers/intro) devrait utiliser le SDK.</span><span class="sxs-lookup"><span data-stu-id="57478-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="57478-178">Si vous ciblez .NET Core 3.x, ajoutez un `<FrameworkReference>` élément pour le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="57478-179">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="57478-180">Si vous ciblez .NET Standard (pour prendre en charge les versions plus tôt que ASP.NET Core 3.x), ajoutez une référence de paquet à [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span><span class="sxs-lookup"><span data-stu-id="57478-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="57478-181">Le `Microsoft.AspNetCore.Mvc.Razor` paquet est entré dans le cadre commun et n’est donc plus publié.</span><span class="sxs-lookup"><span data-stu-id="57478-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="57478-182">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="57478-183">Composants de vue</span><span class="sxs-lookup"><span data-stu-id="57478-183">View components</span></span>

<span data-ttu-id="57478-184">Un projet qui comprend des `Microsoft.NET.Sdk` composants [View](xref:mvc/views/view-components) devrait utiliser le SDK.</span><span class="sxs-lookup"><span data-stu-id="57478-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="57478-185">Si vous ciblez .NET Core 3.x, ajoutez un `<FrameworkReference>` élément pour le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="57478-186">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="57478-187">Si vous ciblez .NET Standard (pour prendre en charge les versions plus tôt que ASP.NET Core 3.x), ajoutez une référence de paquet à [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="57478-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="57478-188">Le `Microsoft.AspNetCore.Mvc.ViewFeatures` paquet est entré dans le cadre commun et n’est donc plus publié.</span><span class="sxs-lookup"><span data-stu-id="57478-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="57478-189">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="57478-190">Prendre en charge plusieurs versions ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57478-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="57478-191">Multi-ciblage est nécessaire pour l’auteur d’une bibliothèque qui prend en charge plusieurs variantes de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57478-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="57478-192">Considérez un scénario dans lequel une bibliothèque Tag Helpers doit prendre en charge les variantes suivantes ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="57478-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="57478-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="57478-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="57478-194">ASP.NET Cœur 2.x ciblage .NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="57478-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="57478-195">ASP.NET Cœur 3.x ciblage .NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="57478-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="57478-196">Le dossier de projet suivant `TargetFrameworks` prend en charge ces variantes via la propriété :</span><span class="sxs-lookup"><span data-stu-id="57478-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="57478-197">Avec le fichier de projet précédent :</span><span class="sxs-lookup"><span data-stu-id="57478-197">With the preceding project file:</span></span>

* <span data-ttu-id="57478-198">Le `Markdig` forfait est ajouté pour tous les consommateurs.</span><span class="sxs-lookup"><span data-stu-id="57478-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="57478-199">Une référence à [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) est ajoutée pour les consommateurs ciblant .NET Framework 4.6.1 ou plus tard ou .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="57478-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="57478-200">La version 2.1.0 du paquet fonctionne avec ASP.NET Core 2.2 en raison de la compatibilité vers l’arrière.</span><span class="sxs-lookup"><span data-stu-id="57478-200">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="57478-201">Le cadre partagé est référencé pour les consommateurs ciblant .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="57478-201">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="57478-202">Le `Microsoft.AspNetCore.Mvc.Razor` paquet est inclus dans le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-202">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="57478-203">Alternativement, .NET Standard 2.0 pourrait être ciblé au lieu de cibler à la fois .NET Core 2.1 et .NET Framework 4.6.1:</span><span class="sxs-lookup"><span data-stu-id="57478-203">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="57478-204">Avec le dossier de projet précédent, les mises en garde suivantes existent :</span><span class="sxs-lookup"><span data-stu-id="57478-204">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="57478-205">Étant donné que la bibliothèque ne contient que Tag Helpers, il est plus simple de cibler les plates-formes spécifiques sur lesquelles ASP.NET Core fonctionne: .NET Core et .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="57478-205">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="57478-206">Tag Helpers ne peut pas être utilisé par d’autres cadres cibles compatibles .NET Standard 2.0 tels que Unity, UWP et Xamarin.</span><span class="sxs-lookup"><span data-stu-id="57478-206">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="57478-207">L’utilisation de .NET Standard 2.0 dans .NET Framework entraîne certains problèmes qui ont été résolus dans .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="57478-207">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="57478-208">Vous pouvez améliorer l’expérience pour les consommateurs en utilisant .NET Framework 4.6.1 à 4.7.1 en ciblant .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="57478-208">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="57478-209">Si votre bibliothèque doit appeler des API spécifiques à la plate-forme, ciblez des implémentations .NET spécifiques au lieu de .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="57478-209">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="57478-210">Pour plus d’informations, voir [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="57478-210">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="57478-211">Utilisez une API qui n’a pas changé</span><span class="sxs-lookup"><span data-stu-id="57478-211">Use an API that hasn't changed</span></span>

<span data-ttu-id="57478-212">Imaginez un scénario dans lequel vous améliorez une bibliothèque middleware de .NET Core 2.2 à 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-212">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="57478-213">Les API de middleware core ASP.NET utilisées dans la bibliothèque n’ont pas changé entre ASP.NET Core 2.2 et 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-213">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="57478-214">Pour continuer à soutenir la bibliothèque middleware en .NET Core 3.0, prenez les mesures suivantes :</span><span class="sxs-lookup"><span data-stu-id="57478-214">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="57478-215">Suivez les [conseils standard de la bibliothèque](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="57478-215">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="57478-216">Ajoutez une référence de paquet pour le paquet NuGet de chaque API si l’assemblage correspondant n’existe pas dans le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-216">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="57478-217">Utilisez une API qui a changé</span><span class="sxs-lookup"><span data-stu-id="57478-217">Use an API that changed</span></span>

<span data-ttu-id="57478-218">Imaginez un scénario dans lequel vous améliorez une bibliothèque de .NET Core 2.2 à .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-218">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="57478-219">Une ASP.NET’API de base utilisée dans la bibliothèque a un [changement de rupture](/dotnet/core/compatibility/breaking-changes) dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-219">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="57478-220">Examinez si la bibliothèque peut être réécrite pour ne pas utiliser l’API cassée dans toutes les versions.</span><span class="sxs-lookup"><span data-stu-id="57478-220">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="57478-221">Si vous pouvez réécrire la bibliothèque, faites-le et continuez à cibler un cadre cible antérieur (par exemple, .NET Standard 2.0 ou .NET Framework 4.6.1) avec des références de paquet.</span><span class="sxs-lookup"><span data-stu-id="57478-221">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="57478-222">Si vous ne pouvez pas réécrire la bibliothèque, prenez les mesures suivantes :</span><span class="sxs-lookup"><span data-stu-id="57478-222">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="57478-223">Ajoutez une cible pour .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-223">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="57478-224">Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-224">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="57478-225">Utilisez la [directive #if préprocesseur](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole du cadre cible approprié pour compiler sous condition le code.</span><span class="sxs-lookup"><span data-stu-id="57478-225">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="57478-226">Par exemple, les lectures synchrones et les écritures sur les flux de demande et de réponse HTTP sont désactivées par défaut à partir de ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-226">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="57478-227">ASP.NET Core 2.2 prend en charge le comportement synchrone par défaut.</span><span class="sxs-lookup"><span data-stu-id="57478-227">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="57478-228">Considérez une bibliothèque middleware dans laquelle les lectures synchrones et les écrits devraient être activés où I / O se produit.</span><span class="sxs-lookup"><span data-stu-id="57478-228">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="57478-229">La bibliothèque doit inclure le code pour activer les caractéristiques synchrones dans la directive préprocesseur appropriée.</span><span class="sxs-lookup"><span data-stu-id="57478-229">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="57478-230">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="57478-230">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="57478-231">Utiliser une API introduite dans 3.0</span><span class="sxs-lookup"><span data-stu-id="57478-231">Use an API introduced in 3.0</span></span>

<span data-ttu-id="57478-232">Imaginez que vous souhaitez utiliser une API de base ASP.NET qui a été introduite dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-232">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="57478-233">Considérez les questions suivantes :</span><span class="sxs-lookup"><span data-stu-id="57478-233">Consider the following questions:</span></span>

1. <span data-ttu-id="57478-234">La bibliothèque a-t-elle besoin fonctionnellement de la nouvelle API?</span><span class="sxs-lookup"><span data-stu-id="57478-234">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="57478-235">La bibliothèque peut-elle implémenter cette fonctionnalité d’une manière différente ?</span><span class="sxs-lookup"><span data-stu-id="57478-235">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="57478-236">Si la bibliothèque nécessite fonctionnellement l’API et qu’il n’y a aucun moyen de la mettre en œuvre à un niveau bas :</span><span class="sxs-lookup"><span data-stu-id="57478-236">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="57478-237">Cible .NET Core 3.x seulement.</span><span class="sxs-lookup"><span data-stu-id="57478-237">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="57478-238">Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-238">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="57478-239">Si la bibliothèque peut implémenter la fonctionnalité d’une manière différente :</span><span class="sxs-lookup"><span data-stu-id="57478-239">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="57478-240">Ajouter .NET Core 3.x comme cadre cible.</span><span class="sxs-lookup"><span data-stu-id="57478-240">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="57478-241">Ajoutez `<FrameworkReference>` un élément pour le cadre partagé.</span><span class="sxs-lookup"><span data-stu-id="57478-241">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="57478-242">Utilisez la [directive #if préprocesseur](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole du cadre cible approprié pour compiler sous condition le code.</span><span class="sxs-lookup"><span data-stu-id="57478-242">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="57478-243">Par exemple, l’aide Tag <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> suivante utilise l’interface introduite dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="57478-243">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="57478-244">Les consommateurs ciblant .NET Core 3.0 `NETCOREAPP3_0` exécutent la trajectoire de code définie par le symbole du cadre cible.</span><span class="sxs-lookup"><span data-stu-id="57478-244">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="57478-245">Le type de paramètre constructeur de <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Tag Helper passe à .NET Core 2.1 et .NET Framework 4.6.1 consommateurs.</span><span class="sxs-lookup"><span data-stu-id="57478-245">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="57478-246">Ce changement était nécessaire parce que ASP.NET Core `IHostingEnvironment` 3.0 a été qualifié d’obsolète et recommandé `IWebHostEnvironment` comme remplaçant.</span><span class="sxs-lookup"><span data-stu-id="57478-246">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="57478-247">Le fichier de projet multi-ciblés suivant prend en charge ce scénario Tag Helper :</span><span class="sxs-lookup"><span data-stu-id="57478-247">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="57478-248">Utiliser une API supprimée du cadre partagé</span><span class="sxs-lookup"><span data-stu-id="57478-248">Use an API removed from the shared framework</span></span>

<span data-ttu-id="57478-249">Pour utiliser un assemblage ASP.NET Core qui a été retiré du cadre partagé, ajoutez la référence de paquet appropriée.</span><span class="sxs-lookup"><span data-stu-id="57478-249">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="57478-250">Pour une liste de paquets supprimés du cadre partagé dans ASP.NET Core 3.0, voir [Supprimer les références de paquets obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="57478-250">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="57478-251">Par exemple, pour ajouter le client web API:</span><span class="sxs-lookup"><span data-stu-id="57478-251">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="57478-252">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="57478-252">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/class-libraries>
* [<span data-ttu-id="57478-253">Prise en charge des implémentations de .NET</span><span class="sxs-lookup"><span data-stu-id="57478-253">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="57478-254">.NET politiques de soutien</span><span class="sxs-lookup"><span data-stu-id="57478-254">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
