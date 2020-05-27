---
<span data-ttu-id="7f6da-101">titre : 'Debug ASP.NET Core Blazor Webassembly’Author : Description : 'Découvrez comment déboguer des Blazor applications. '</span><span class="sxs-lookup"><span data-stu-id="7f6da-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="7f6da-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="7f6da-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7f6da-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7f6da-103">'Blazor'</span></span>
- <span data-ttu-id="7f6da-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7f6da-104">'Identity'</span></span>
- <span data-ttu-id="7f6da-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7f6da-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="7f6da-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7f6da-106">'Razor'</span></span>
- <span data-ttu-id="7f6da-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="7f6da-107">'SignalR' uid:</span></span> 

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7f6da-108">Déboguer ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="7f6da-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="7f6da-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="7f6da-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="7f6da-110">Les applications webassembly peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).</span><span class="sxs-lookup"><span data-stu-id="7f6da-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="7f6da-111">Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="7f6da-111">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="7f6da-112">Les scénarios disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="7f6da-112">Available scenarios include:</span></span>

* <span data-ttu-id="7f6da-113">Définir et supprimer des points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="7f6da-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="7f6da-114">Exécutez l’application avec prise en charge du débogage dans Visual Studio et Visual Studio Code (prise en charge de<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="7f6da-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="7f6da-115">Une étape (<kbd>F10</kbd>) à l’aide du code.</span><span class="sxs-lookup"><span data-stu-id="7f6da-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="7f6da-116">Reprendre l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="7f6da-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="7f6da-117">Dans l' *affichage des variables locales,* observez les valeurs des variables locales.</span><span class="sxs-lookup"><span data-stu-id="7f6da-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="7f6da-118">Consultez la pile des appels, y compris les chaînes d’appel qui passent de JavaScript à .NET et de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="7f6da-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="7f6da-119">Pour le moment, vous *ne pouvez pas*:</span><span class="sxs-lookup"><span data-stu-id="7f6da-119">For now, you *can't*:</span></span>

* <span data-ttu-id="7f6da-120">Arrêt sur les exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="7f6da-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7f6da-121">Atteindre les points d’arrêt pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="7f6da-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="7f6da-122">Nous continuerons à améliorer l’expérience de débogage dans les versions à venir.</span><span class="sxs-lookup"><span data-stu-id="7f6da-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7f6da-123">Prérequis</span><span class="sxs-lookup"><span data-stu-id="7f6da-123">Prerequisites</span></span>

<span data-ttu-id="7f6da-124">Le débogage requiert l’un des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="7f6da-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="7f6da-125">Microsoft Edge (version 80 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="7f6da-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="7f6da-126">Google Chrome (version 70 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="7f6da-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="7f6da-127">Activer le débogage pour Visual Studio et Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f6da-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="7f6da-128">Pour activer le débogage pour une Blazor application Webassembly existante, mettez à jour le fichier *launchSettings. JSON* dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="7f6da-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7f6da-129">Une fois mis à jour, le fichier *launchSettings. JSON* doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7f6da-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7f6da-130">La `inspectUri` propriété :</span><span class="sxs-lookup"><span data-stu-id="7f6da-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="7f6da-131">Permet à l’IDE de détecter que l’application est une Blazor application Webassembly.</span><span class="sxs-lookup"><span data-stu-id="7f6da-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="7f6da-132">Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.</span><span class="sxs-lookup"><span data-stu-id="7f6da-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="7f6da-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f6da-133">Visual Studio</span></span>

<span data-ttu-id="7f6da-134">Pour déboguer une Blazor application Webassembly dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="7f6da-134">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="7f6da-135">Créez une nouvelle ASP.NET Core Blazor application Webassembly hébergée.</span><span class="sxs-lookup"><span data-stu-id="7f6da-135">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7f6da-136">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="7f6da-136">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="7f6da-137">Définissez un point d’arrêt dans *Counter. Razor* dans la `IncrementCount` méthode.</span><span class="sxs-lookup"><span data-stu-id="7f6da-137">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="7f6da-138">Accédez à l’onglet **compteur** et sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="7f6da-138">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="7f6da-140">Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :</span><span class="sxs-lookup"><span data-stu-id="7f6da-140">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="7f6da-142">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="7f6da-142">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="7f6da-143">Lors du débogage de votre Blazor application Webassembly, vous pouvez également déboguer votre code serveur :</span><span class="sxs-lookup"><span data-stu-id="7f6da-143">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="7f6da-144">Définissez un point d’arrêt dans la page *fetchData. Razor* dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7f6da-144">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="7f6da-145">Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="7f6da-145">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="7f6da-146">Accédez à l’onglet **extraire les données** pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il envoie une requête HTTP au serveur :</span><span class="sxs-lookup"><span data-stu-id="7f6da-146">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="7f6da-148">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="7f6da-148">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="7f6da-150">Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.</span><span class="sxs-lookup"><span data-stu-id="7f6da-150">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="7f6da-151">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f6da-151">Visual Studio Code</span></span>

<span data-ttu-id="7f6da-152">Pour déboguer une Blazor application Webassembly dans Visual Studio code :</span><span class="sxs-lookup"><span data-stu-id="7f6da-152">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="7f6da-153">Installez l' [extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="7f6da-153">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Débogueur JS preview](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="7f6da-156">Ouvrez une Blazor application Webassembly existante avec le débogage activé.</span><span class="sxs-lookup"><span data-stu-id="7f6da-156">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="7f6da-157">Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage, vérifiez que les extensions appropriées sont installées et que le débogage de l’aperçu JavaScript est activé, puis rechargez la fenêtre :</span><span class="sxs-lookup"><span data-stu-id="7f6da-157">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="7f6da-159">Une notification propose d’ajouter les ressources requises à l’application pour la génération et le débogage.</span><span class="sxs-lookup"><span data-stu-id="7f6da-159">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="7f6da-160">Sélectionnez **Oui**:</span><span class="sxs-lookup"><span data-stu-id="7f6da-160">Select **Yes**:</span></span>

     ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="7f6da-162">Le démarrage de l’application dans le débogueur est un processus en deux étapes :</span><span class="sxs-lookup"><span data-stu-id="7f6da-162">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="7f6da-163">1 \.</span><span class="sxs-lookup"><span data-stu-id="7f6da-163">1\.</span></span> <span data-ttu-id="7f6da-164">**Tout d’abord**, démarrez l’application à l’aide de la configuration de lancement **de .net Core Launch ( Blazor autonome)** .</span><span class="sxs-lookup"><span data-stu-id="7f6da-164">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="7f6da-165">2 \.</span><span class="sxs-lookup"><span data-stu-id="7f6da-165">2\.</span></span> <span data-ttu-id="7f6da-166">**Une fois l’application démarrée**, démarrez le navigateur à l’aide de l' \*\* Blazor Assembly Web de débogage .net core dans\*\* la configuration de lancement chrome (nécessite chrome).</span><span class="sxs-lookup"><span data-stu-id="7f6da-166">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="7f6da-167">Pour utiliser Edge au lieu de chrome, remplacez l' `type` de la configuration de lancement dans *. vscode/Launch. JSON* par `pwa-chrome` `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="7f6da-167">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="7f6da-168">Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="7f6da-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="7f6da-170">Déboguer dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="7f6da-170">Debug in the browser</span></span>

1. <span data-ttu-id="7f6da-171">Exécutez une version Debug de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="7f6da-171">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="7f6da-172">Appuyez sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="7f6da-172">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="7f6da-173">Le navigateur doit être exécuté avec le débogage distant activé.</span><span class="sxs-lookup"><span data-stu-id="7f6da-173">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="7f6da-174">Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est générée.</span><span class="sxs-lookup"><span data-stu-id="7f6da-174">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="7f6da-175">La page d’erreur contient des instructions pour l’exécution du navigateur avec le port de débogage ouvert, afin que le Blazor proxy de débogage puisse se connecter à l’application.</span><span class="sxs-lookup"><span data-stu-id="7f6da-175">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="7f6da-176">*Fermez toutes les instances de navigateur* et redémarrez le navigateur comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="7f6da-176">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="7f6da-177">Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage ouvre un nouvel onglet du débogueur. Après un moment, l’onglet **sources** affiche une liste des assemblys .net dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7f6da-177">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="7f6da-178">Développez chaque assembly et recherchez les fichiers sources *. cs* / *. Razor* disponibles pour le débogage.</span><span class="sxs-lookup"><span data-stu-id="7f6da-178">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="7f6da-179">Définissez des points d’arrêt, revenez à l’onglet de l’application, et les points d’arrêt sont atteints lorsque le code s’exécute.</span><span class="sxs-lookup"><span data-stu-id="7f6da-179">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="7f6da-180">Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="7f6da-180">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="7f6da-181">fournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net.</span><span class="sxs-lookup"><span data-stu-id="7f6da-181"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7f6da-182">Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy.</span><span class="sxs-lookup"><span data-stu-id="7f6da-182">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="7f6da-183">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).</span><span class="sxs-lookup"><span data-stu-id="7f6da-183">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7f6da-184">Mappages des sources du navigateur</span><span class="sxs-lookup"><span data-stu-id="7f6da-184">Browser source maps</span></span>

<span data-ttu-id="7f6da-185">Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="7f6da-185">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7f6da-186">Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="7f6da-186">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="7f6da-187">Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="7f6da-187">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7f6da-188">Dépanner</span><span class="sxs-lookup"><span data-stu-id="7f6da-188">Troubleshoot</span></span>

<span data-ttu-id="7f6da-189">Si vous rencontrez des erreurs, le Conseil suivant peut vous aider :</span><span class="sxs-lookup"><span data-stu-id="7f6da-189">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="7f6da-190">Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="7f6da-190">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="7f6da-191">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="7f6da-191">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
