---
title: Compilation de fichiers Razor dans ASP.NET Core
author: rick-anderson
description: Découvrez comment se produit la compilation de fichiers Razor dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277265"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="5f007-103">Compilation de fichiers Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f007-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="5f007-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5f007-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="5f007-105">Les fichiers Razor avec une extension *.cshtml* sont compilés à la fois le temps de construction et de publication en utilisant le [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5f007-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="5f007-106">La compilation Runtime peut être activée en configurant votre projet.</span><span class="sxs-lookup"><span data-stu-id="5f007-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="5f007-107">Compilation Razor</span><span class="sxs-lookup"><span data-stu-id="5f007-107">Razor compilation</span></span>

<span data-ttu-id="5f007-108">La compilation de fichiers Razor par défaut est activée par défaut par le Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="5f007-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="5f007-109">Lorsqu’elle est activée, la compilation runtime complète la compilation du temps de construction, permettant aux fichiers Razor d’être mis à jour s’ils sont édités.</span><span class="sxs-lookup"><span data-stu-id="5f007-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="5f007-110">Activez la compilation de l’exécution à la création de projet</span><span class="sxs-lookup"><span data-stu-id="5f007-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="5f007-111">Les modèles de projet Razor Pages et MVC comprennent une option pour activer la compilation en temps d’exécution lorsque le projet est créé.</span><span class="sxs-lookup"><span data-stu-id="5f007-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="5f007-112">Cette option est prise en charge dans ASP.NET Core 3.1 et plus tard.</span><span class="sxs-lookup"><span data-stu-id="5f007-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f007-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f007-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f007-114">Dans le **Créer un nouveau dialogue ASP.NET application web Core** :</span><span class="sxs-lookup"><span data-stu-id="5f007-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="5f007-115">Sélectionnez soit **l’application Web,** soit le modèle de projet **d’application Web (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="5f007-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="5f007-116">Sélectionnez la case à cocher **de compilation Active Razor runtime.**</span><span class="sxs-lookup"><span data-stu-id="5f007-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5f007-117">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="5f007-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5f007-118">Utilisez `-rrc` l’option ou `--razor-runtime-compilation` le modèle.</span><span class="sxs-lookup"><span data-stu-id="5f007-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="5f007-119">Par exemple, la commande suivante crée un nouveau projet Razor Pages avec une compilation en temps d’exécution activée :</span><span class="sxs-lookup"><span data-stu-id="5f007-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="5f007-120">Activer la compilation en temps d’exécution dans un projet existant</span><span class="sxs-lookup"><span data-stu-id="5f007-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="5f007-121">Pour activer la compilation en temps d’exécution pour tous les environnements d’un projet existant :</span><span class="sxs-lookup"><span data-stu-id="5f007-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="5f007-122">Installez le package [NuGet Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="5f007-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="5f007-123">Mettre à jour `Startup.ConfigureServices` la méthode du <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>projet pour inclure un appel à .</span><span class="sxs-lookup"><span data-stu-id="5f007-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="5f007-124">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="5f007-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="5f007-125">Activer conditionnellement la compilation en temps d’exécution dans un projet existant</span><span class="sxs-lookup"><span data-stu-id="5f007-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="5f007-126">La compilation Runtime peut être activée de telle sorte qu’elle n’est disponible que pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="5f007-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="5f007-127">L’activation conditionnelle de cette manière garantit que la sortie publiée :</span><span class="sxs-lookup"><span data-stu-id="5f007-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="5f007-128">Utilise les vues compilées.</span><span class="sxs-lookup"><span data-stu-id="5f007-128">Uses compiled views.</span></span>
* <span data-ttu-id="5f007-129">N’active pas les observateurs de fichiers en production.</span><span class="sxs-lookup"><span data-stu-id="5f007-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="5f007-130">Pour activer la compilation en temps d’exécution uniquement dans l’environnement développement :</span><span class="sxs-lookup"><span data-stu-id="5f007-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="5f007-131">Installez le package [NuGet Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="5f007-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="5f007-132">Modifier la `environmentVariables` section profil de lancement dans *launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5f007-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="5f007-133">Vérifier `ASPNETCORE_ENVIRONMENT` est `"Development"`réglé à .</span><span class="sxs-lookup"><span data-stu-id="5f007-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="5f007-134">Définissez `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` sur `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="5f007-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="5f007-135">Dans l’exemple suivant, la compilation en temps `IIS Express` `RazorPagesApp` d’exécution est activée dans l’environnement développement pour les profils et les profils de lancement :</span><span class="sxs-lookup"><span data-stu-id="5f007-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="5f007-136">Aucune modification de code n’est nécessaire dans la classe du `Startup` projet.</span><span class="sxs-lookup"><span data-stu-id="5f007-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="5f007-137">Au moment de l’exécution, ASP.NET Core recherche un [attribut HostingStartup au niveau de l’assemblage](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) dans `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="5f007-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="5f007-138">L’attribut `HostingStartup` spécifie le code de démarrage de l’application à exécuter.</span><span class="sxs-lookup"><span data-stu-id="5f007-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="5f007-139">Ce code de démarrage permet la compilation en temps d’exécution.</span><span class="sxs-lookup"><span data-stu-id="5f007-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f007-140">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5f007-140">Additional resources</span></span>

* <span data-ttu-id="5f007-141">[RazorCompileOnBuild et RazorCompileOnPublish](xref:razor-pages/sdk#properties) propriétés.</span><span class="sxs-lookup"><span data-stu-id="5f007-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="5f007-142">Consultez [l’échantillon de compilation en temps d’exécution sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour un échantillon qui montre faire fonctionner la compilation en temps d’exécution à travers les projets.</span><span class="sxs-lookup"><span data-stu-id="5f007-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="5f007-143">Les fichiers Razor avec une extension *.cshtml* sont compilés à la fois le temps de construction et de publication en utilisant le [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5f007-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="5f007-144">La compilation au moment de l’exécution peut éventuellement être activée en configurant votre application.</span><span class="sxs-lookup"><span data-stu-id="5f007-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="5f007-145">Compilation Razor</span><span class="sxs-lookup"><span data-stu-id="5f007-145">Razor compilation</span></span>

<span data-ttu-id="5f007-146">La compilation de fichiers Razor par défaut est activée par défaut par le Razor SDK.</span><span class="sxs-lookup"><span data-stu-id="5f007-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="5f007-147">Lorsqu’elle est activée, la compilation runtime complète la compilation du temps de construction, permettant aux fichiers Razor d’être mis à jour s’ils sont édités.</span><span class="sxs-lookup"><span data-stu-id="5f007-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="5f007-148">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="5f007-148">Runtime compilation</span></span>

<span data-ttu-id="5f007-149">Pour activer la compilation de temps d’exécution pour tous les environnements et modes de configuration :</span><span class="sxs-lookup"><span data-stu-id="5f007-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="5f007-150">Installez le package [NuGet Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="5f007-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="5f007-151">Mettre à jour `Startup.ConfigureServices` la méthode du <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>projet pour inclure un appel à .</span><span class="sxs-lookup"><span data-stu-id="5f007-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="5f007-152">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="5f007-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="5f007-153">Activer conditionnellement la compilation de l’exécution</span><span class="sxs-lookup"><span data-stu-id="5f007-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="5f007-154">La compilation Runtime peut être activée de telle sorte qu’elle n’est disponible que pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="5f007-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="5f007-155">L’activation conditionnelle de cette manière garantit que la sortie publiée :</span><span class="sxs-lookup"><span data-stu-id="5f007-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="5f007-156">Utilise les vues compilées.</span><span class="sxs-lookup"><span data-stu-id="5f007-156">Uses compiled views.</span></span>
* <span data-ttu-id="5f007-157">Est plus petit dans la taille.</span><span class="sxs-lookup"><span data-stu-id="5f007-157">Is smaller in size.</span></span>
* <span data-ttu-id="5f007-158">N’active pas les observateurs de fichiers en production.</span><span class="sxs-lookup"><span data-stu-id="5f007-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="5f007-159">Pour activer la compilation en temps d’exécution en fonction de l’environnement et du mode configuration :</span><span class="sxs-lookup"><span data-stu-id="5f007-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="5f007-160">Référence conditionnelle du package [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` basé sur la valeur active :</span><span class="sxs-lookup"><span data-stu-id="5f007-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="5f007-161">Mettre à jour `Startup.ConfigureServices` la méthode du `AddRazorRuntimeCompilation`projet pour inclure un appel à .</span><span class="sxs-lookup"><span data-stu-id="5f007-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="5f007-162">Exécuter `AddRazorRuntimeCompilation` sous condition de telle sorte qu’il `ASPNETCORE_ENVIRONMENT` ne fonctionne `Development`en mode Debug que lorsque la variable est réglée sur :</span><span class="sxs-lookup"><span data-stu-id="5f007-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5f007-163">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5f007-163">Additional resources</span></span>

* <span data-ttu-id="5f007-164">[RazorCompileOnBuild et RazorCompileOnPublish](xref:razor-pages/sdk#properties) propriétés.</span><span class="sxs-lookup"><span data-stu-id="5f007-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="5f007-165">Consultez [l’échantillon de compilation en temps d’exécution sur GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) pour un échantillon qui montre faire fonctionner la compilation en temps d’exécution à travers les projets.</span><span class="sxs-lookup"><span data-stu-id="5f007-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5f007-166">Un fichier Razor est compilé au moment de l’exécution, quand la vue MVC ou la page Razor associée est appelée.</span><span class="sxs-lookup"><span data-stu-id="5f007-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="5f007-167">Les fichiers Razor sont compilés au moment de la génération et de la publication à l’aide du kit [SDK Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5f007-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="5f007-168">Compilation Razor</span><span class="sxs-lookup"><span data-stu-id="5f007-168">Razor compilation</span></span>

<span data-ttu-id="5f007-169">La compilation au moment de la génération et de la publication des fichiers Razor est activée par défaut par le kit SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="5f007-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="5f007-170">La modification des fichiers Razor une fois que ceux-ci sont mis à jour est prise en charge au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="5f007-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="5f007-171">Par défaut, seuls les fichiers *Views.dll* et les fichiers autres que *.cshtml* compilés ou les assemblys de référence nécessaires à la compilation des fichiers Razor sont déployés avec votre application.</span><span class="sxs-lookup"><span data-stu-id="5f007-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5f007-172">L’outil de précompilation est désormais déprécié et sera supprimé dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="5f007-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="5f007-173">Nous vous recommandons de migrer vers le [SDK Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="5f007-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="5f007-174">Le kit SDK Razor est actif seulement si aucune propriété spécifique à la précompilation n’est définie dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="5f007-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="5f007-175">Par exemple, la définition de la propriété `MvcRazorCompileOnPublish` du fichier *.csproj* sur la valeur `true` désactive le kit SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="5f007-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="5f007-176">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="5f007-176">Runtime compilation</span></span>

<span data-ttu-id="5f007-177">La compilation au moment de la génération est complétée par la compilation au moment de l’exécution des fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="5f007-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="5f007-178">ASP.NET Core MVC recompile les fichiers Razor quand le contenu d’un fichier *.cshtml* change.</span><span class="sxs-lookup"><span data-stu-id="5f007-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f007-179">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5f007-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
