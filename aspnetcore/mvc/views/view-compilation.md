---
title: Razorcompilation de fichiers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment la compilation des Razor fichiers se produit dans une application ASP.net core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: 71487ff2d5d7d7cf96835778f386e5f30fa32254
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405443"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>Razor<span data-ttu-id="425ad-103">compilation de fichiers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="425ad-103"> file compilation in ASP.NET Core</span></span>

<span data-ttu-id="425ad-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="425ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

Razor<span data-ttu-id="425ad-105">les fichiers avec une extension *. cshtml* sont compilés au moment de la génération et de la publication à l’aide du [ Razor Kit de développement logiciel (SDK)](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="425ad-105"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="425ad-106">La compilation du runtime peut éventuellement être activée en configurant votre projet.</span><span class="sxs-lookup"><span data-stu-id="425ad-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="425ad-107">élaboration</span><span class="sxs-lookup"><span data-stu-id="425ad-107"> compilation</span></span>

<span data-ttu-id="425ad-108">La compilation des fichiers au moment de la génération et de Razor la publication est activée par défaut par le Razor Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="425ad-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="425ad-109">Lorsqu’elle est activée, la compilation du runtime complète la compilation au moment de la génération, ce qui permet la Razor mise à jour des fichiers si elles sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="425ad-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="425ad-110">Activer la compilation du Runtime au moment de la création du projet</span><span class="sxs-lookup"><span data-stu-id="425ad-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="425ad-111">Les Razor pages et les modèles de projet MVC incluent une option permettant d’activer la compilation au moment de la création du projet.</span><span class="sxs-lookup"><span data-stu-id="425ad-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="425ad-112">Cette option est prise en charge dans ASP.NET Core 3,1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="425ad-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="425ad-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="425ad-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="425ad-114">Dans la boîte de dialogue **créer une nouvelle application web ASP.net Core** :</span><span class="sxs-lookup"><span data-stu-id="425ad-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="425ad-115">Sélectionnez l' **application Web** ou le modèle de projet **application Web (Model-View-Controller)** .</span><span class="sxs-lookup"><span data-stu-id="425ad-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="425ad-116">Activez la case à cocher **activer la Razor compilation au moment** de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="425ad-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="425ad-117">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="425ad-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="425ad-118">Utilisez l' `-rrc` `--razor-runtime-compilation` option de modèle ou.</span><span class="sxs-lookup"><span data-stu-id="425ad-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="425ad-119">Par exemple, la commande suivante crée un Razor projet de pages avec la compilation d’exécution activée :</span><span class="sxs-lookup"><span data-stu-id="425ad-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="425ad-120">Activer la compilation au moment de l’exécution dans un projet existant</span><span class="sxs-lookup"><span data-stu-id="425ad-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="425ad-121">Pour activer la compilation au moment de l’exécution pour tous les environnements d’un projet existant :</span><span class="sxs-lookup"><span data-stu-id="425ad-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="425ad-122">Installez [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Package NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="425ad-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="425ad-123">Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="425ad-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="425ad-124">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="425ad-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="425ad-125">Activer la compilation d’exécution de manière conditionnelle dans un projet existant</span><span class="sxs-lookup"><span data-stu-id="425ad-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="425ad-126">La compilation au moment de l’exécution peut être activée de sorte qu’elle ne soit disponible que pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="425ad-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="425ad-127">L’activation conditionnelle de cette manière garantit que la sortie publiée :</span><span class="sxs-lookup"><span data-stu-id="425ad-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="425ad-128">Utilise des vues compilées.</span><span class="sxs-lookup"><span data-stu-id="425ad-128">Uses compiled views.</span></span>
* <span data-ttu-id="425ad-129">N’active pas les observateurs de fichiers en production.</span><span class="sxs-lookup"><span data-stu-id="425ad-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="425ad-130">Pour activer la compilation au moment de l’exécution uniquement dans l’environnement de développement :</span><span class="sxs-lookup"><span data-stu-id="425ad-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="425ad-131">Installez [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Package NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="425ad-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="425ad-132">Modifiez la section Launch Profile `environmentVariables` dans *launchSettings.jssur*:</span><span class="sxs-lookup"><span data-stu-id="425ad-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="425ad-133">Vérifiez que `ASPNETCORE_ENVIRONMENT` a la valeur `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="425ad-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="425ad-134">Définissez `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` sur `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="425ad-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="425ad-135">Dans l’exemple suivant, la compilation au moment de l’exécution est activée dans l’environnement de développement pour les `IIS Express` `RazorPagesApp` profils de lancement et :</span><span class="sxs-lookup"><span data-stu-id="425ad-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="425ad-136">Aucune modification de code n’est nécessaire dans la `Startup` classe du projet.</span><span class="sxs-lookup"><span data-stu-id="425ad-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="425ad-137">Au moment de l’exécution, ASP.NET Core recherche un [attribut HostingStartup au niveau de l’assembly](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) dans `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="425ad-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="425ad-138">L' `HostingStartup` attribut spécifie le code de démarrage de l’application à exécuter.</span><span class="sxs-lookup"><span data-stu-id="425ad-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="425ad-139">Ce code de démarrage permet la compilation au moment de l’exécution.</span><span class="sxs-lookup"><span data-stu-id="425ad-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="425ad-140">Activer la compilation au moment de l’exécution pour une Razor bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="425ad-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="425ad-141">Imaginez un scénario dans lequel un Razor projet de pages fait référence à une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) nommée *MyClassLib*.</span><span class="sxs-lookup"><span data-stu-id="425ad-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="425ad-142">Le RCL contient un fichier *_Layout. cshtml* que tous les projets MVC et pages de votre équipe Razor consomment.</span><span class="sxs-lookup"><span data-stu-id="425ad-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="425ad-143">Vous souhaitez activer la compilation au moment de l’exécution pour le fichier *_Layout. cshtml* dans ce RCL.</span><span class="sxs-lookup"><span data-stu-id="425ad-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="425ad-144">Apportez les modifications suivantes dans le Razor projet pages :</span><span class="sxs-lookup"><span data-stu-id="425ad-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="425ad-145">Activez la compilation au moment de l’exécution avec les instructions en [activant de manière conditionnelle la compilation au moment de l’exécution dans un projet existant](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="425ad-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="425ad-146">Configurez les options de compilation du runtime dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="425ad-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="425ad-147">Dans le code précédent, un chemin d’accès absolu au RCL *MyClassLib* est construit.</span><span class="sxs-lookup"><span data-stu-id="425ad-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="425ad-148">L' [API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) est utilisée pour localiser des répertoires et des fichiers au niveau de ce chemin d’accès absolu.</span><span class="sxs-lookup"><span data-stu-id="425ad-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="425ad-149">Enfin, l' `PhysicalFileProvider` instance est ajoutée à une collection de fournisseurs de fichiers, qui autorise l’accès aux fichiers *. cshtml* du RCL.</span><span class="sxs-lookup"><span data-stu-id="425ad-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="425ad-150">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="425ad-150">Additional resources</span></span>

* <span data-ttu-id="425ad-151">Propriétés [RazorCompileOnBuild et RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="425ad-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Razor<span data-ttu-id="425ad-152">les fichiers avec une extension *. cshtml* sont compilés au moment de la génération et de la publication à l’aide du [ Razor Kit de développement logiciel (SDK)](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="425ad-152"> files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="425ad-153">La compilation au moment de l’exécution peut éventuellement être activée en configurant votre application.</span><span class="sxs-lookup"><span data-stu-id="425ad-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="425ad-154">élaboration</span><span class="sxs-lookup"><span data-stu-id="425ad-154"> compilation</span></span>

<span data-ttu-id="425ad-155">La compilation des fichiers au moment de la génération et de Razor la publication est activée par défaut par le Razor Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="425ad-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="425ad-156">Lorsqu’elle est activée, la compilation du runtime complète la compilation au moment de la génération, ce qui permet la Razor mise à jour des fichiers si elles sont modifiées.</span><span class="sxs-lookup"><span data-stu-id="425ad-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="425ad-157">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="425ad-157">Runtime compilation</span></span>

<span data-ttu-id="425ad-158">Pour activer la compilation au moment de l’exécution pour tous les environnements et modes de configuration :</span><span class="sxs-lookup"><span data-stu-id="425ad-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="425ad-159">Installez [Microsoft. AspNetCore. Mvc. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Package NuGet RuntimeCompilation.</span><span class="sxs-lookup"><span data-stu-id="425ad-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="425ad-160">Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> .</span><span class="sxs-lookup"><span data-stu-id="425ad-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="425ad-161">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="425ad-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="425ad-162">Activation conditionnelle de la compilation du Runtime</span><span class="sxs-lookup"><span data-stu-id="425ad-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="425ad-163">La compilation au moment de l’exécution peut être activée de sorte qu’elle ne soit disponible que pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="425ad-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="425ad-164">L’activation conditionnelle de cette manière garantit que la sortie publiée :</span><span class="sxs-lookup"><span data-stu-id="425ad-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="425ad-165">Utilise des vues compilées.</span><span class="sxs-lookup"><span data-stu-id="425ad-165">Uses compiled views.</span></span>
* <span data-ttu-id="425ad-166">Est de taille inférieure.</span><span class="sxs-lookup"><span data-stu-id="425ad-166">Is smaller in size.</span></span>
* <span data-ttu-id="425ad-167">N’active pas les observateurs de fichiers en production.</span><span class="sxs-lookup"><span data-stu-id="425ad-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="425ad-168">Pour activer la compilation au moment de l’exécution en fonction de l’environnement et du mode de configuration :</span><span class="sxs-lookup"><span data-stu-id="425ad-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="425ad-169">Référencez de manière conditionnelle le [Microsoft. AspNetCore. Mvc. Razor . Package RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) basé sur la `Configuration` valeur active :</span><span class="sxs-lookup"><span data-stu-id="425ad-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="425ad-170">Mettez à jour la `Startup.ConfigureServices` méthode du projet pour inclure un appel à `AddRazorRuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="425ad-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="425ad-171">Exécuter `AddRazorRuntimeCompilation` de manière conditionnelle, de telle sorte qu’elle s’exécute uniquement en mode débogage lorsque la `ASPNETCORE_ENVIRONMENT` variable est définie sur `Development` :</span><span class="sxs-lookup"><span data-stu-id="425ad-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="425ad-172">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="425ad-172">Additional resources</span></span>

* <span data-ttu-id="425ad-173">Propriétés [RazorCompileOnBuild et RazorCompileOnPublish](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="425ad-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="425ad-174">Consultez l' [exemple de compilation du Runtime sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour obtenir un exemple qui montre comment utiliser la compilation d’exécution entre les projets.</span><span class="sxs-lookup"><span data-stu-id="425ad-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="425ad-175">Un Razor fichier est compilé au moment de l’exécution, lorsque la Razor page associée ou la vue MVC est appelée.</span><span class="sxs-lookup"><span data-stu-id="425ad-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> Razor<span data-ttu-id="425ad-176">les fichiers sont compilés au moment de la génération et de la publication à l’aide du [ Razor Kit de développement logiciel](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="425ad-176"> files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a>Razor<span data-ttu-id="425ad-177">élaboration</span><span class="sxs-lookup"><span data-stu-id="425ad-177"> compilation</span></span>

<span data-ttu-id="425ad-178">La compilation des fichiers Build-and-Publish-Time Razor est activée par défaut par le Razor Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="425ad-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="425ad-179">La modification des Razor fichiers après leur mise à jour est prise en charge au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="425ad-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="425ad-180">Par défaut, seuls le *Views.dll* compilé et aucun fichier *. cshtml* ou référence les assemblys requis pour compiler les Razor fichiers sont déployés avec votre application.</span><span class="sxs-lookup"><span data-stu-id="425ad-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="425ad-181">L’outil de précompilation est désormais déprécié et sera supprimé dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="425ad-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="425ad-182">Nous vous recommandons de migrer vers le [ Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="425ad-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="425ad-183">Le Razor Kit de développement logiciel (SDK) est effectif uniquement quand aucune propriété spécifique à la précompilation n’est définie dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="425ad-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="425ad-184">Par exemple, la définition de la propriété du fichier *. csproj* `MvcRazorCompileOnPublish` pour `true` désactive le Razor Kit de développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="425ad-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="425ad-185">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="425ad-185">Runtime compilation</span></span>

<span data-ttu-id="425ad-186">La compilation au moment de la génération est complétée par la compilation des fichiers au moment de l’exécution Razor .</span><span class="sxs-lookup"><span data-stu-id="425ad-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="425ad-187">ASP.NET Core MVC RECOMPILE Razor les fichiers lorsque le contenu d’un fichier *. cshtml* est modifié.</span><span class="sxs-lookup"><span data-stu-id="425ad-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="425ad-188">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="425ad-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
