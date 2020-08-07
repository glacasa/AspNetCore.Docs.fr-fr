---
title: Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers
author: rick-anderson
description: Ce tutoriel montre comment installer et utiliser l’outil Observateur de fichiers (dotnet watch) de l’interface de ligne de commande .NET Core dans une application ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 5648ae9e75b48fa4749ed6bcc01b91559dbd2939
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913772"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="1db9b-103">Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers</span><span class="sxs-lookup"><span data-stu-id="1db9b-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="1db9b-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="1db9b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="1db9b-105">`dotnet watch`est un outil qui exécute une commande [CLI .net Core](/dotnet/core/tools) lorsque les fichiers sources changent.</span><span class="sxs-lookup"><span data-stu-id="1db9b-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="1db9b-106">Par exemple, un changement de fichier peut déclencher la compilation, l’exécution de tests ou le déploiement.</span><span class="sxs-lookup"><span data-stu-id="1db9b-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="1db9b-107">Ce tutoriel utilise une API web existante avec deux points de terminaison : un qui retourne une somme et un qui retourne un produit.</span><span class="sxs-lookup"><span data-stu-id="1db9b-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="1db9b-108">La méthode du produit comporte un bogue, qui est résolu dans ce tutoriel.</span><span class="sxs-lookup"><span data-stu-id="1db9b-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="1db9b-109">Téléchargez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="1db9b-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="1db9b-110">Elle se compose de deux projets : *WebApp* (API web ASP.NET Core) et *WebAppTests* (API de tests unitaires pour le web).</span><span class="sxs-lookup"><span data-stu-id="1db9b-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="1db9b-111">Dans une interface de commande, accédez au dossier *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="1db9b-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="1db9b-112">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="1db9b-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="1db9b-113">Vous pouvez utiliser `dotnet run --project <PROJECT>` pour spécifier un projet à exécuter.</span><span class="sxs-lookup"><span data-stu-id="1db9b-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="1db9b-114">Par exemple, `dotnet run --project WebApp` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="1db9b-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="1db9b-115">La sortie de la console affiche des messages semblables à ce qui suit (indiquant que l’application est en cours d’exécution et en attente de demandes) :</span><span class="sxs-lookup"><span data-stu-id="1db9b-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="1db9b-116">Dans un navigateur web, accédez à `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="1db9b-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="1db9b-117">Vous devez voir le résultat `9`.</span><span class="sxs-lookup"><span data-stu-id="1db9b-117">You should see the result of `9`.</span></span>

<span data-ttu-id="1db9b-118">Accédez à l’API du produit (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="1db9b-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="1db9b-119">Elle retourne `9`, et non pas `20` comme prévu.</span><span class="sxs-lookup"><span data-stu-id="1db9b-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="1db9b-120">Ce problème est résolu plus tard dans le tutoriel.</span><span class="sxs-lookup"><span data-stu-id="1db9b-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="1db9b-121">Ajouter `dotnet watch` à un projet</span><span class="sxs-lookup"><span data-stu-id="1db9b-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="1db9b-122">L’outil Observateur de fichiers `dotnet watch` est inclus dans la version 2.1.300 du kit SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1db9b-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="1db9b-123">Les étapes suivantes sont requises quand vous utilisez une version antérieure du kit SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="1db9b-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="1db9b-124">Ajoutez une référence de package `Microsoft.DotNet.Watcher.Tools` dans le fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="1db9b-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="1db9b-125">Installez le package `Microsoft.DotNet.Watcher.Tools` en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="1db9b-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="1db9b-126">Exécuter les commandes de l’interface CLI de .NET Core avec `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="1db9b-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="1db9b-127">Toutes les [commandes de l’interface CLI de .NET Core](/dotnet/core/tools#cli-commands) peuvent être exécutées avec `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="1db9b-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="1db9b-128">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="1db9b-128">For example:</span></span>

| <span data-ttu-id="1db9b-129">Commande</span><span class="sxs-lookup"><span data-stu-id="1db9b-129">Command</span></span> | <span data-ttu-id="1db9b-130">Commande avec watch</span><span class="sxs-lookup"><span data-stu-id="1db9b-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="1db9b-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="1db9b-131">dotnet run</span></span> | <span data-ttu-id="1db9b-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="1db9b-132">dotnet watch run</span></span> |
| <span data-ttu-id="1db9b-133">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="1db9b-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="1db9b-134">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="1db9b-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="1db9b-135">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="1db9b-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="1db9b-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="1db9b-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="1db9b-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="1db9b-137">dotnet test</span></span> | <span data-ttu-id="1db9b-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="1db9b-138">dotnet watch test</span></span> |

<span data-ttu-id="1db9b-139">Exécutez `dotnet watch run` dans le dossier *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="1db9b-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="1db9b-140">La sortie de la console indique que `watch` a démarré.</span><span class="sxs-lookup"><span data-stu-id="1db9b-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="1db9b-141">Vous pouvez utiliser `dotnet watch --project <PROJECT>` pour spécifier un projet à surveiller.</span><span class="sxs-lookup"><span data-stu-id="1db9b-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="1db9b-142">Par exemple, `dotnet watch --project WebApp run` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp* et d’en effectuer le suivi.</span><span class="sxs-lookup"><span data-stu-id="1db9b-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="1db9b-143">Apporter des modifications avec `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="1db9b-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="1db9b-144">Vérifiez que `dotnet watch` est en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="1db9b-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="1db9b-145">Corrigez le bogue présent dans la méthode `Product` de *MathController.cs* afin qu’elle retourne le produit et non la somme :</span><span class="sxs-lookup"><span data-stu-id="1db9b-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="1db9b-146">Enregistrez le fichier .</span><span class="sxs-lookup"><span data-stu-id="1db9b-146">Save the file.</span></span> <span data-ttu-id="1db9b-147">La sortie de la console indique que `dotnet watch` a détecté un changement de fichier et a redémarré l’application.</span><span class="sxs-lookup"><span data-stu-id="1db9b-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="1db9b-148">Vérifiez que `http://localhost:<port number>/api/math/product?a=4&b=5` retourne le résultat correct.</span><span class="sxs-lookup"><span data-stu-id="1db9b-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="1db9b-149">Exécuter les tests à l’aide de `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="1db9b-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="1db9b-150">Changez la méthode `Product` de *MathController.cs* pour qu’elle retourne à nouveau la somme.</span><span class="sxs-lookup"><span data-stu-id="1db9b-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="1db9b-151">Enregistrez le fichier .</span><span class="sxs-lookup"><span data-stu-id="1db9b-151">Save the file.</span></span>
1. <span data-ttu-id="1db9b-152">Dans une interface de commande, accédez au dossier *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="1db9b-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="1db9b-153">Exécutez [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="1db9b-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="1db9b-154">Exécutez `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="1db9b-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="1db9b-155">Sa sortie indique qu’un test a échoué et que l’observateur est en attente de changement de fichier :</span><span class="sxs-lookup"><span data-stu-id="1db9b-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="1db9b-156">Corrigez le code de la méthode `Product` afin qu’elle retourne le produit.</span><span class="sxs-lookup"><span data-stu-id="1db9b-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="1db9b-157">Enregistrez le fichier .</span><span class="sxs-lookup"><span data-stu-id="1db9b-157">Save the file.</span></span>

<span data-ttu-id="1db9b-158">`dotnet watch` détecte le changement de fichier et réexécute les tests.</span><span class="sxs-lookup"><span data-stu-id="1db9b-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="1db9b-159">La sortie de la console indique que les tests ont réussi.</span><span class="sxs-lookup"><span data-stu-id="1db9b-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="1db9b-160">Personnaliser la liste de fichiers à observer</span><span class="sxs-lookup"><span data-stu-id="1db9b-160">Customize files list to watch</span></span>

<span data-ttu-id="1db9b-161">Par défaut, `dotnet-watch` effectue le suivi de tous les fichiers qui correspondent aux modèles Glob suivants :</span><span class="sxs-lookup"><span data-stu-id="1db9b-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="1db9b-162">Vous pouvez ajouter plusieurs éléments à la liste de suivi en modifiant le fichier *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="1db9b-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="1db9b-163">Vous pouvez spécifier des éléments individuellement ou en utilisant des modèles Glob.</span><span class="sxs-lookup"><span data-stu-id="1db9b-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="1db9b-164">Exclusion de fichiers à observer</span><span class="sxs-lookup"><span data-stu-id="1db9b-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="1db9b-165">`dotnet-watch` peut être configuré pour ignorer ses paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="1db9b-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="1db9b-166">Pour ignorer des fichiers spécifiques, ajoutez l’attribut `Watch="false"` à la définition d’un élément dans le fichier *.csproj* :</span><span class="sxs-lookup"><span data-stu-id="1db9b-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="1db9b-167">Personnaliser les projets de suivi</span><span class="sxs-lookup"><span data-stu-id="1db9b-167">Custom watch projects</span></span>

<span data-ttu-id="1db9b-168">`dotnet-watch` n’est pas limité aux projets C#.</span><span class="sxs-lookup"><span data-stu-id="1db9b-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="1db9b-169">Vous pouvez créer des projets de suivi personnalisés pour gérer différents scénarios.</span><span class="sxs-lookup"><span data-stu-id="1db9b-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="1db9b-170">Considérez l’organisation de projets suivante :</span><span class="sxs-lookup"><span data-stu-id="1db9b-170">Consider the following project layout:</span></span>

* <span data-ttu-id="1db9b-171">**test**</span><span class="sxs-lookup"><span data-stu-id="1db9b-171">**test/**</span></span>
  * <span data-ttu-id="1db9b-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="1db9b-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="1db9b-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="1db9b-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="1db9b-174">Si l’objectif est d’observer les deux projets, créez un fichier projet personnalisé configuré à cette fin :</span><span class="sxs-lookup"><span data-stu-id="1db9b-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="1db9b-175">Pour démarrer l’observation de fichiers sur les deux projets, passez au dossier *test*.</span><span class="sxs-lookup"><span data-stu-id="1db9b-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="1db9b-176">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="1db9b-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="1db9b-177">VSTest s’exécute quand n’importe quel fichier change dans un des projets de test.</span><span class="sxs-lookup"><span data-stu-id="1db9b-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="1db9b-178">`dotnet-watch` dans GitHub</span><span class="sxs-lookup"><span data-stu-id="1db9b-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="1db9b-179">`dotnet-watch`fait partie du référentiel GitHub [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="1db9b-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
