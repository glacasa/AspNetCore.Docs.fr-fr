---
title: Compilation de fichiers Razor dans ASP.NET Core
author: rick-anderson
description: Découvrez comment se produit la compilation de fichiers Razor dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994607"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="abe27-103">Compilation de fichiers Razor dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="abe27-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="abe27-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="abe27-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="abe27-105">Les fichiers Razor avec une extension *.cshtml* sont compilés à la fois le temps de construction et de publication en utilisant le [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="abe27-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="abe27-106">La compilation au moment de l’exécution peut éventuellement être activée en configurant votre application.</span><span class="sxs-lookup"><span data-stu-id="abe27-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="abe27-107">Compilation Razor</span><span class="sxs-lookup"><span data-stu-id="abe27-107">Razor compilation</span></span>

<span data-ttu-id="abe27-108">La compilation au moment de la génération et de la publication des fichiers Razor est activée par défaut par le kit SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="abe27-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="abe27-109">Quand elle est activée, la compilation au moment de l’exécution complète la compilation au moment de la génération, ce qui permet aux fichiers Razor d’être mis à jour s’ils sont modifiés.</span><span class="sxs-lookup"><span data-stu-id="abe27-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="abe27-110">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="abe27-110">Runtime compilation</span></span>

<span data-ttu-id="abe27-111">Pour activer la compilation de temps d’exécution pour tous les environnements et modes de configuration :</span><span class="sxs-lookup"><span data-stu-id="abe27-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="abe27-112">Installez le package [NuGet Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation.](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)</span><span class="sxs-lookup"><span data-stu-id="abe27-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="abe27-113">Mettre à jour `Startup.ConfigureServices` la méthode du `AddRazorRuntimeCompilation`projet pour inclure un appel à .</span><span class="sxs-lookup"><span data-stu-id="abe27-113">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="abe27-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="abe27-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="abe27-115">Activer conditionnellement la compilation de l’exécution</span><span class="sxs-lookup"><span data-stu-id="abe27-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="abe27-116">La compilation Runtime peut être activée de telle sorte qu’elle n’est disponible que pour le développement local.</span><span class="sxs-lookup"><span data-stu-id="abe27-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="abe27-117">L’activation conditionnelle de cette manière garantit que la sortie publiée :</span><span class="sxs-lookup"><span data-stu-id="abe27-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="abe27-118">Utilise les vues compilées.</span><span class="sxs-lookup"><span data-stu-id="abe27-118">Uses compiled views.</span></span>
* <span data-ttu-id="abe27-119">Est plus petit dans la taille.</span><span class="sxs-lookup"><span data-stu-id="abe27-119">Is smaller in size.</span></span>
* <span data-ttu-id="abe27-120">N’active pas les observateurs de fichiers en production.</span><span class="sxs-lookup"><span data-stu-id="abe27-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="abe27-121">Pour activer la compilation en temps d’exécution en fonction de l’environnement et du mode configuration :</span><span class="sxs-lookup"><span data-stu-id="abe27-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="abe27-122">Référence conditionnelle du package [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) `Configuration` basé sur la valeur active :</span><span class="sxs-lookup"><span data-stu-id="abe27-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="abe27-123">Mettre à jour `Startup.ConfigureServices` la méthode du `AddRazorRuntimeCompilation`projet pour inclure un appel à .</span><span class="sxs-lookup"><span data-stu-id="abe27-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="abe27-124">Exécuter `AddRazorRuntimeCompilation` sous condition de telle sorte qu’il `ASPNETCORE_ENVIRONMENT` ne fonctionne `Development`en mode Debug que lorsque la variable est réglée sur :</span><span class="sxs-lookup"><span data-stu-id="abe27-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="abe27-125">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="abe27-125">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="abe27-126">Un fichier Razor est compilé au moment de l’exécution, quand la vue MVC ou la page Razor associée est appelée.</span><span class="sxs-lookup"><span data-stu-id="abe27-126">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="abe27-127">Les fichiers Razor sont compilés au moment de la génération et de la publication à l’aide du kit [SDK Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="abe27-127">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="abe27-128">Compilation Razor</span><span class="sxs-lookup"><span data-stu-id="abe27-128">Razor compilation</span></span>

<span data-ttu-id="abe27-129">La compilation au moment de la génération et de la publication des fichiers Razor est activée par défaut par le kit SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="abe27-129">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="abe27-130">La modification des fichiers Razor une fois que ceux-ci sont mis à jour est prise en charge au moment de la génération.</span><span class="sxs-lookup"><span data-stu-id="abe27-130">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="abe27-131">Par défaut, seuls les fichiers *Views.dll* et les fichiers autres que *.cshtml* compilés ou les assemblys de référence nécessaires à la compilation des fichiers Razor sont déployés avec votre application.</span><span class="sxs-lookup"><span data-stu-id="abe27-131">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="abe27-132">L’outil de précompilation est désormais déprécié et sera supprimé dans ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="abe27-132">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="abe27-133">Nous vous recommandons de migrer vers le [SDK Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="abe27-133">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="abe27-134">Le kit SDK Razor est actif seulement si aucune propriété spécifique à la précompilation n’est définie dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="abe27-134">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="abe27-135">Par exemple, la définition de la propriété `MvcRazorCompileOnPublish` du fichier *.csproj* sur la valeur `true` désactive le kit SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="abe27-135">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="abe27-136">Compilation du runtime</span><span class="sxs-lookup"><span data-stu-id="abe27-136">Runtime compilation</span></span>

<span data-ttu-id="abe27-137">La compilation au moment de la génération est complétée par la compilation au moment de l’exécution des fichiers Razor.</span><span class="sxs-lookup"><span data-stu-id="abe27-137">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="abe27-138">ASP.NET Core MVC recompile les fichiers Razor quand le contenu d’un fichier *.cshtml* change.</span><span class="sxs-lookup"><span data-stu-id="abe27-138">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="abe27-139">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="abe27-139">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end