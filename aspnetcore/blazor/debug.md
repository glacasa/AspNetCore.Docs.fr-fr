---
title: Déboguer ASP.NET Core Blazor webassembly
author: guardrex
description: Découvrez comment déboguer Blazor des applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 9acbb8e7b122a8d527d16ce33af01c2e7e7608bf
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767536"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="0023b-103">Déboguer ASP.NET Core Blazor webassembly</span><span class="sxs-lookup"><span data-stu-id="0023b-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="0023b-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="0023b-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="0023b-105">Les applications webassembly peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).</span><span class="sxs-lookup"><span data-stu-id="0023b-105"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="0023b-106">Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="0023b-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="0023b-107">Les scénarios disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="0023b-107">Available scenarios include:</span></span>

* <span data-ttu-id="0023b-108">Définir et supprimer des points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="0023b-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="0023b-109">Exécutez l’application avec prise en charge du débogage dans Visual Studio et Visual Studio Code (prise en charge de<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="0023b-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="0023b-110">Une étape (<kbd>F10</kbd>) à l’aide du code.</span><span class="sxs-lookup"><span data-stu-id="0023b-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="0023b-111">Reprendre l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="0023b-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="0023b-112">Dans l' *affichage des variables locales,* observez les valeurs des variables locales.</span><span class="sxs-lookup"><span data-stu-id="0023b-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="0023b-113">Consultez la pile des appels, y compris les chaînes d’appel qui passent de JavaScript à .NET et de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0023b-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="0023b-114">Pour le moment, vous *ne pouvez pas*:</span><span class="sxs-lookup"><span data-stu-id="0023b-114">For now, you *can't*:</span></span>

* <span data-ttu-id="0023b-115">Inspectez les tableaux.</span><span class="sxs-lookup"><span data-stu-id="0023b-115">Inspect arrays.</span></span>
* <span data-ttu-id="0023b-116">Pointez sur inspecter les membres.</span><span class="sxs-lookup"><span data-stu-id="0023b-116">Hover to inspect members.</span></span>
* <span data-ttu-id="0023b-117">Déboguez l’étape dans ou hors du code managé.</span><span class="sxs-lookup"><span data-stu-id="0023b-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="0023b-118">Bénéficiez d’une prise en charge complète pour l’inspection des types valeur.</span><span class="sxs-lookup"><span data-stu-id="0023b-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="0023b-119">Arrêt sur les exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="0023b-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="0023b-120">Atteindre les points d’arrêt pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="0023b-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="0023b-121">Déboguez une application avec un service Worker.</span><span class="sxs-lookup"><span data-stu-id="0023b-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="0023b-122">Nous continuerons à améliorer l’expérience de débogage dans les versions à venir.</span><span class="sxs-lookup"><span data-stu-id="0023b-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0023b-123">Prérequis</span><span class="sxs-lookup"><span data-stu-id="0023b-123">Prerequisites</span></span>

<span data-ttu-id="0023b-124">Le débogage requiert l’un des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="0023b-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="0023b-125">Microsoft Edge (version 80 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="0023b-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="0023b-126">Google Chrome (version 70 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="0023b-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="0023b-127">Activer le débogage pour Visual Studio et Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0023b-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="0023b-128">Le débogage est activé automatiquement pour les nouveaux projets créés à l’aide du modèle de projet ASP.NET Core 3,2 Blazor Preview 3 ou un modèle de projet webassembly ultérieur (la[version actuelle est 3,2 Preview 4](xref:blazor/get-started)).</span><span class="sxs-lookup"><span data-stu-id="0023b-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template ([current release is 3.2 Preview 4](xref:blazor/get-started)).</span></span>

<span data-ttu-id="0023b-129">Pour activer le débogage pour une application Blazor webassembly existante, mettez à jour le fichier *launchSettings. JSON* dans le projet de démarrage `inspectUri` pour inclure la propriété suivante dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="0023b-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="0023b-130">Une fois mis à jour, le fichier *launchSettings. JSON* doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="0023b-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="0023b-131">La `inspectUri` propriété :</span><span class="sxs-lookup"><span data-stu-id="0023b-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="0023b-132">Permet à l’IDE de détecter que l’application est Blazor une application webassembly.</span><span class="sxs-lookup"><span data-stu-id="0023b-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="0023b-133">Indique à l’infrastructure de débogage de script de se connecter au navigateur Blazorvia le proxy de débogage de.</span><span class="sxs-lookup"><span data-stu-id="0023b-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="0023b-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0023b-134">Visual Studio</span></span>

<span data-ttu-id="0023b-135">Pour déboguer une Blazor application webassembly dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="0023b-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="0023b-136">Vérifiez que vous avez [installé la dernière version d’évaluation de Visual Studio 2019 16,6](https://visualstudio.com/preview) (Preview 2 ou version ultérieure).</span><span class="sxs-lookup"><span data-stu-id="0023b-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="0023b-137">Créez une nouvelle ASP.NET Core application Blazor webassembly hébergée.</span><span class="sxs-lookup"><span data-stu-id="0023b-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="0023b-138">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="0023b-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="0023b-139">Définissez un point d’arrêt dans *Counter. Razor* dans la `IncrementCount` méthode.</span><span class="sxs-lookup"><span data-stu-id="0023b-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="0023b-140">Accédez à l’onglet **compteur** et sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="0023b-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="0023b-142">Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :</span><span class="sxs-lookup"><span data-stu-id="0023b-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="0023b-144">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="0023b-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="0023b-145">Lors du débogage de Blazor votre application webassembly, vous pouvez également déboguer votre code serveur :</span><span class="sxs-lookup"><span data-stu-id="0023b-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="0023b-146">Définissez un point d’arrêt dans la page *fetchData. Razor* dans `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="0023b-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="0023b-147">Définissez un point d’arrêt `WeatherForecastController` dans la `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="0023b-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="0023b-148">Accédez à l’onglet **extraire les données** pour atteindre le premier point d' `FetchData` arrêt dans le composant juste avant qu’il envoie une requête HTTP au serveur :</span><span class="sxs-lookup"><span data-stu-id="0023b-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="0023b-150">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt `WeatherForecastController`sur le serveur dans le :</span><span class="sxs-lookup"><span data-stu-id="0023b-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="0023b-152">Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.</span><span class="sxs-lookup"><span data-stu-id="0023b-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="0023b-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0023b-153">Visual Studio Code</span></span>

<span data-ttu-id="0023b-154">Pour déboguer une Blazor application webassembly dans Visual Studio code :</span><span class="sxs-lookup"><span data-stu-id="0023b-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="0023b-155">Installez l' [extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` la valeur `true`.</span><span class="sxs-lookup"><span data-stu-id="0023b-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Débogueur JS preview](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="0023b-158">Ouvrez une application Blazor webassembly existante avec le débogage activé.</span><span class="sxs-lookup"><span data-stu-id="0023b-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="0023b-159">Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage, vérifiez que les extensions appropriées sont installées et que le débogage de l’aperçu JavaScript est activé, puis rechargez la fenêtre :</span><span class="sxs-lookup"><span data-stu-id="0023b-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Installation supplémentaire obligatoire](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="0023b-161">Une notification propose d’ajouter les ressources requises à l’application pour la génération et le débogage.</span><span class="sxs-lookup"><span data-stu-id="0023b-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="0023b-162">Sélectionnez **Oui**:</span><span class="sxs-lookup"><span data-stu-id="0023b-162">Select **Yes**:</span></span>

     ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="0023b-164">Le démarrage de l’application dans le débogueur est un processus en deux étapes :</span><span class="sxs-lookup"><span data-stu-id="0023b-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="0023b-165">1 \.</span><span class="sxs-lookup"><span data-stu-id="0023b-165">1\.</span></span> <span data-ttu-id="0023b-166">**Tout d’abord**, démarrez l’application à l’aide de la configuration de lancement **de .net Core LaunchBlazor (autonome)** .</span><span class="sxs-lookup"><span data-stu-id="0023b-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="0023b-167">2 \.</span><span class="sxs-lookup"><span data-stu-id="0023b-167">2\.</span></span> <span data-ttu-id="0023b-168">**Une fois l’application démarrée**, démarrez le navigateur à l’aide de l' **Assembly Web de débogage Blazor .net core dans** la configuration de lancement chrome (nécessite chrome).</span><span class="sxs-lookup"><span data-stu-id="0023b-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="0023b-169">Pour utiliser Edge au lieu de chrome, remplacez `type` l’de la configuration de lancement dans *. vscode/Launch. JSON* par `pwa-chrome` `pwa-msedge`.</span><span class="sxs-lookup"><span data-stu-id="0023b-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="0023b-170">Définissez un point d’arrêt `IncrementCount` dans la méthode `Counter` du composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="0023b-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="0023b-172">Déboguer dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="0023b-172">Debug in the browser</span></span>

1. <span data-ttu-id="0023b-173">Exécutez une version Debug de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="0023b-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="0023b-174">Appuyez sur <kbd>MAJ</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="0023b-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="0023b-175">Le navigateur doit être exécuté avec le débogage distant activé.</span><span class="sxs-lookup"><span data-stu-id="0023b-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="0023b-176">Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est générée.</span><span class="sxs-lookup"><span data-stu-id="0023b-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="0023b-177">La page d’erreur contient des instructions pour l’exécution du navigateur avec le port de débogage ouvert Blazor , afin que le proxy de débogage puisse se connecter à l’application.</span><span class="sxs-lookup"><span data-stu-id="0023b-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="0023b-178">*Fermez toutes les instances de navigateur* et redémarrez le navigateur comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="0023b-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="0023b-179">Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage ouvre un nouvel onglet du débogueur. Après un moment, l’onglet **sources** affiche une liste des assemblys .net dans l’application.</span><span class="sxs-lookup"><span data-stu-id="0023b-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="0023b-180">Développez chaque assembly et recherchez les fichiers sources *. cs*/*. Razor* disponibles pour le débogage.</span><span class="sxs-lookup"><span data-stu-id="0023b-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="0023b-181">Définissez des points d’arrêt, revenez à l’onglet de l’application, et les points d’arrêt sont atteints lorsque le code s’exécute.</span><span class="sxs-lookup"><span data-stu-id="0023b-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="0023b-182">Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="0023b-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="0023b-183">fournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net.</span><span class="sxs-lookup"><span data-stu-id="0023b-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="0023b-184">Quand le raccourci clavier de débogage est enfoncé Blazor , pointe le devtools chrome au niveau du proxy.</span><span class="sxs-lookup"><span data-stu-id="0023b-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="0023b-185">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).</span><span class="sxs-lookup"><span data-stu-id="0023b-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="0023b-186">Mappages des sources du navigateur</span><span class="sxs-lookup"><span data-stu-id="0023b-186">Browser source maps</span></span>

<span data-ttu-id="0023b-187">Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="0023b-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="0023b-188">Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="0023b-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="0023b-189">Au lieu Blazor de cela, fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="0023b-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0023b-190">Dépanner</span><span class="sxs-lookup"><span data-stu-id="0023b-190">Troubleshoot</span></span>

<span data-ttu-id="0023b-191">Si vous rencontrez des erreurs, le Conseil suivant peut vous aider :</span><span class="sxs-lookup"><span data-stu-id="0023b-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="0023b-192">Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="0023b-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="0023b-193">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="0023b-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
