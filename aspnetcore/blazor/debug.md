---
title: Debug ASP.NET Core Blazor WebAssembly
author: guardrex
description: Apprenez à déboiffer Blazor les applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 8b63444ba5c8cd45e64e722c8978ba4e6d90af36
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488746"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="e48f3-103">Debug ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e48f3-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="e48f3-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="e48f3-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="e48f3-105">Les applications WebAssembly peuvent être déboisées à l’aide des outils de développement du navigateur dans les navigateurs basés sur le Chrome (Edge/Chrome).</span><span class="sxs-lookup"><span data-stu-id="e48f3-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="e48f3-106">Vous pouvez également déboiffer votre application à l’aide de Visual Studio ou de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e48f3-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="e48f3-107">Les scénarios disponibles comprennent :</span><span class="sxs-lookup"><span data-stu-id="e48f3-107">Available scenarios include:</span></span>

* <span data-ttu-id="e48f3-108">Réglez et supprimez les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="e48f3-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="e48f3-109">Exécutez l’application avec un support de débogage dans Visual Studio et Visual Studio Code (support<kbd>F5).</kbd></span><span class="sxs-lookup"><span data-stu-id="e48f3-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="e48f3-110">Une seule étape (<kbd>F10</kbd>) à travers le code.</span><span class="sxs-lookup"><span data-stu-id="e48f3-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="e48f3-111">Reprenez l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="e48f3-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="e48f3-112">Dans l’affichage local, observez les valeurs des variables *locales.*</span><span class="sxs-lookup"><span data-stu-id="e48f3-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="e48f3-113">Voir la pile d’appels, y compris les chaînes d’appels qui vont de JavaScript en .NET et de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e48f3-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="e48f3-114">Pour *l’instant,* vous ne pouvez pas :</span><span class="sxs-lookup"><span data-stu-id="e48f3-114">For now, you *can't*:</span></span>

* <span data-ttu-id="e48f3-115">Inspectez les tableaux.</span><span class="sxs-lookup"><span data-stu-id="e48f3-115">Inspect arrays.</span></span>
* <span data-ttu-id="e48f3-116">Planer pour inspecter les membres.</span><span class="sxs-lookup"><span data-stu-id="e48f3-116">Hover to inspect members.</span></span>
* <span data-ttu-id="e48f3-117">Débogé d’étape dans ou hors du code géré.</span><span class="sxs-lookup"><span data-stu-id="e48f3-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="e48f3-118">Ayez un soutien complet pour inspecter les types de valeur.</span><span class="sxs-lookup"><span data-stu-id="e48f3-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="e48f3-119">Cassez-vous sur des exceptions non manipulées.</span><span class="sxs-lookup"><span data-stu-id="e48f3-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="e48f3-120">Hit points d’arrêt pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="e48f3-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="e48f3-121">Débogé d’une application avec un travailleur de service.</span><span class="sxs-lookup"><span data-stu-id="e48f3-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="e48f3-122">Nous continuerons d’améliorer l’expérience de débogage dans les prochaines versions.</span><span class="sxs-lookup"><span data-stu-id="e48f3-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e48f3-123">Prérequis</span><span class="sxs-lookup"><span data-stu-id="e48f3-123">Prerequisites</span></span>

<span data-ttu-id="e48f3-124">Le débogage nécessite l’un ou l’autre des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="e48f3-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="e48f3-125">Microsoft Edge (version 80 ou plus tard)</span><span class="sxs-lookup"><span data-stu-id="e48f3-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="e48f3-126">Google Chrome (version 70 ou plus tard)</span><span class="sxs-lookup"><span data-stu-id="e48f3-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="e48f3-127">Activez le débogage pour Visual Studio et Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e48f3-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="e48f3-128">Le débogage est activé automatiquement pour les nouveaux projets créés à l’aide Blazor du ASP.NET Core 3.2 Preview 3 ou plus tard du modèle de projet WebAssembly[(la version actuelle est de 3,2 Aperçu 4](xref:blazor/get-started)).</span><span class="sxs-lookup"><span data-stu-id="e48f3-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="e48f3-129">Pour permettre le débogage d’une application WebAssembly existante, Blazor mettez à jour le fichier *launchSettings.json* dans le projet de démarrage afin d’inclure la propriété suivante `inspectUri` dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="e48f3-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="e48f3-130">Une fois mis à jour, le fichier *launchSettings.json* doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e48f3-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="e48f3-131">La `inspectUri` propriété:</span><span class="sxs-lookup"><span data-stu-id="e48f3-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="e48f3-132">Permet à l’IDE de Blazor détecter que l’application est une application WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="e48f3-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="e48f3-133">Instructe l’infrastructure de débogage de Blazorscript pour se connecter au navigateur par le proxy de débogage de '.</span><span class="sxs-lookup"><span data-stu-id="e48f3-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="e48f3-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e48f3-134">Visual Studio</span></span>

<span data-ttu-id="e48f3-135">Pour débomber Blazor une application WebAssembly dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="e48f3-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="e48f3-136">Assurez-vous d’avoir [installé la dernière version préliminaire de Visual Studio 2019 16.6](https://visualstudio.com/preview) (Aperçu 2 ou plus tard).</span><span class="sxs-lookup"><span data-stu-id="e48f3-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="e48f3-137">Créez une nouvelle application WebAssembly hébergée Blazor ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e48f3-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="e48f3-138">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débbugger.</span><span class="sxs-lookup"><span data-stu-id="e48f3-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="e48f3-139">Définissez un point d’arrêt `IncrementCount` dans *Counter.razor* dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="e48f3-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="e48f3-140">Naviguez sur l’onglet **Compteur** et sélectionnez le bouton pour appuyer sur le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="e48f3-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Compteur Debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="e48f3-142">Découvrez la valeur `currentCount` du terrain dans la fenêtre locale:</span><span class="sxs-lookup"><span data-stu-id="e48f3-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Voir les habitants](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="e48f3-144">Appuyez <kbd>sur F5</kbd> pour continuer l’exécution.</span><span class="sxs-lookup"><span data-stu-id="e48f3-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="e48f3-145">Tout en débogage de votre Blazor application WebAssembly, vous pouvez également déboguer votre code serveur :</span><span class="sxs-lookup"><span data-stu-id="e48f3-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="e48f3-146">Définissez un point d’arrêt dans `OnInitializedAsync`la page *FetchData.razor* dans .</span><span class="sxs-lookup"><span data-stu-id="e48f3-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="e48f3-147">Définissez un point `WeatherForecastController` d’arrêt dans la méthode d’action. `Get`</span><span class="sxs-lookup"><span data-stu-id="e48f3-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="e48f3-148">Naviguez sur l’onglet **Fetch Data** pour `FetchData` atteindre le premier point d’arrêt du composant juste avant qu’il n’émete une demande HTTP au serveur :</span><span class="sxs-lookup"><span data-stu-id="e48f3-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Debug Fetch Données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="e48f3-150">Appuyez sur <kbd>F5</kbd> pour continuer l’exécution, `WeatherForecastController`puis frapper le point d’arrêt sur le serveur dans le :</span><span class="sxs-lookup"><span data-stu-id="e48f3-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serveur Debug](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="e48f3-152">Appuyez à nouveau sur <kbd>F5</kbd> pour que l’exécution se poursuive et que la table de prévision météorologique soit rendue.</span><span class="sxs-lookup"><span data-stu-id="e48f3-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="e48f3-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e48f3-153">Visual Studio Code</span></span>

<span data-ttu-id="e48f3-154">Pour débomber Blazor une application WebAssembly dans Visual Studio Code :</span><span class="sxs-lookup"><span data-stu-id="e48f3-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="e48f3-155">Installez [l’extension C et l’extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` ensemble à `true`.</span><span class="sxs-lookup"><span data-stu-id="e48f3-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS aperçu debugger](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="e48f3-158">Ouvrez Blazor une application WebAssembly existante avec un débogage activé.</span><span class="sxs-lookup"><span data-stu-id="e48f3-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="e48f3-159">Si vous obtenez la notification suivante que la configuration supplémentaire est nécessaire pour activer le débogage, confirmez que vous avez les extensions correctes installées et JavaScript aperçu de débogage activé, puis recharger la fenêtre:</span><span class="sxs-lookup"><span data-stu-id="e48f3-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Configuration supplémentaire requried](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="e48f3-161">Une notification offre d’ajouter les actifs requis à l’application pour la construction et le débogage.</span><span class="sxs-lookup"><span data-stu-id="e48f3-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="e48f3-162">Sélectionnez **Oui**:</span><span class="sxs-lookup"><span data-stu-id="e48f3-162">Select **Yes**:</span></span>

     ![Ajouter les actifs requis](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="e48f3-164">Démarrer l’application dans le débbugger est un processus en deux étapes:</span><span class="sxs-lookup"><span data-stu-id="e48f3-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="e48f3-165">1\.</span><span class="sxs-lookup"><span data-stu-id="e48f3-165">1\.</span></span> <span data-ttu-id="e48f3-166">**Tout d’abord,** démarrer l’application en utilisant la configuration de lancement **.NET Core Launch (Blazor Standalone).**</span><span class="sxs-lookup"><span data-stu-id="e48f3-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="e48f3-167">2\.</span><span class="sxs-lookup"><span data-stu-id="e48f3-167">2\.</span></span> <span data-ttu-id="e48f3-168">**Après le début de l’application,** démarrez le navigateur à l’aide de **l’assemblage Web .NET Core Debug Blazor dans la** configuration de lancement Chrome (nécessite Chrome).</span><span class="sxs-lookup"><span data-stu-id="e48f3-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="e48f3-169">Pour utiliser Edge au lieu `type` de Chrome, modifiez la configuration du `pwa-chrome` `pwa-msedge`lancement dans *.vscode/launch.json* de .</span><span class="sxs-lookup"><span data-stu-id="e48f3-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="e48f3-170">Définissez un point `IncrementCount` d’arrêt dans la méthode du `Counter` composant, puis sélectionnez le bouton pour appuyer sur le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="e48f3-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Compteur de débbug dans le code VS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="e48f3-172">Debug dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="e48f3-172">Debug in the browser</span></span>

1. <span data-ttu-id="e48f3-173">Exécuter une version Debug de l’application dans l’environnement Développement.</span><span class="sxs-lookup"><span data-stu-id="e48f3-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="e48f3-174">Changement <kbd>de</kbd>+presse<kbd>Alt</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="e48f3-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="e48f3-175">Le navigateur doit être exécuté avec un débogage à distance activé.</span><span class="sxs-lookup"><span data-stu-id="e48f3-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="e48f3-176">Si le débogage à distance est désactivé, une page d’erreur **de l’onglet de navigateur débbuggable** est générée.</span><span class="sxs-lookup"><span data-stu-id="e48f3-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="e48f3-177">La page d’erreur contient des instructions pour exécuter le Blazor navigateur avec le port de débogage ouvert de sorte que le proxy de débogage peut se connecter à l’application.</span><span class="sxs-lookup"><span data-stu-id="e48f3-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="e48f3-178">*Fermez toutes les instances du navigateur* et redémarrez le navigateur tel qu’il est indiqué.</span><span class="sxs-lookup"><span data-stu-id="e48f3-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="e48f3-179">Une fois que le navigateur est en cours d’exécution avec un débogage à distance activé, le raccourci clavier débogage ouvre un nouvel onglet de débogage. Après un moment, l’onglet **Sources** affiche une liste des assemblages .NET dans l’application.</span><span class="sxs-lookup"><span data-stu-id="e48f3-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="e48f3-180">Élargissez chaque assemblage et trouvez les fichiers source *.cs*/*.razor* disponibles pour le débogage.</span><span class="sxs-lookup"><span data-stu-id="e48f3-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="e48f3-181">Définissez les points de rupture, revenez à l’onglet de l’application et les points d’arrêt sont touchés lorsque le code s’exécute.</span><span class="sxs-lookup"><span data-stu-id="e48f3-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="e48f3-182">Après qu’un point d’arrêt est touché, une seule étape (<kbd>F10</kbd>) à travers le code ou le résumé (<kbd>F8</kbd>) exécution de code normalement.</span><span class="sxs-lookup"><span data-stu-id="e48f3-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="e48f3-183">fournit un proxy de débogage qui implémente le [protocole Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec . Informations spécifiques à NET.</span><span class="sxs-lookup"><span data-stu-id="e48f3-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="e48f3-184">Lorsque le raccourci clavier de Blazor débogage est pressé, pointe les Chrome DevTools au proxy.</span><span class="sxs-lookup"><span data-stu-id="e48f3-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="e48f3-185">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (d’où la nécessité d’activer le débogage à distance).</span><span class="sxs-lookup"><span data-stu-id="e48f3-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="e48f3-186">Cartes source de navigateur</span><span class="sxs-lookup"><span data-stu-id="e48f3-186">Browser source maps</span></span>

<span data-ttu-id="e48f3-187">Les cartes source du navigateur permettent au navigateur de cartographier les fichiers compilés vers leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="e48f3-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="e48f3-188">Cependant, Blazor ne carte pas actuellement C directement à JavaScript / WASM.</span><span class="sxs-lookup"><span data-stu-id="e48f3-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="e48f3-189">Au Blazor lieu de cela, ne l’interprétation IL dans le navigateur, de sorte que les cartes source ne sont pas pertinentes.</span><span class="sxs-lookup"><span data-stu-id="e48f3-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e48f3-190">Dépanner</span><span class="sxs-lookup"><span data-stu-id="e48f3-190">Troubleshoot</span></span>

<span data-ttu-id="e48f3-191">Si vous tombez dans des erreurs, la pointe suivante peut aider:</span><span class="sxs-lookup"><span data-stu-id="e48f3-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="e48f3-192">Dans l’onglet **Debugger,** ouvrez les outils de développeur dans votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="e48f3-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="e48f3-193">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="e48f3-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
