---
<span data-ttu-id="12287-101">titre : « Debug ASP.NET Core Blazor Webassembly » auteur : guardrex Description : « Découvrez comment déboguer des Blazor applications. »</span><span class="sxs-lookup"><span data-stu-id="12287-101">title: 'Debug ASP.NET Core Blazor WebAssembly' author: guardrex description: 'Learn how to debug Blazor apps.'</span></span>
<span data-ttu-id="12287-102">monikerRange : ' >= aspnetcore-3,1 'ms. Author : Riande ms. Custom : MVC ms. Date : 05/29/2020 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="12287-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/29/2020 no-loc:</span></span>
- <span data-ttu-id="12287-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="12287-103">'Blazor'</span></span>
- <span data-ttu-id="12287-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="12287-104">'Identity'</span></span>
- <span data-ttu-id="12287-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="12287-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="12287-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="12287-106">'Razor'</span></span>
- <span data-ttu-id="12287-107">' SignalR 'UID : éblouissant/débogage</span><span class="sxs-lookup"><span data-stu-id="12287-107">'SignalR' uid: blazor/debug</span></span>

---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="12287-108">Déboguer ASP.NET Core Blazor Webassembly</span><span class="sxs-lookup"><span data-stu-id="12287-108">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="12287-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="12287-109">Daniel Roth</span></span>](https://github.com/danroth27)

Blazor<span data-ttu-id="12287-110">Les applications webassembly peuvent être déboguées à l’aide des outils de développement de navigateur dans les navigateurs basés sur le chrome (Edge/chrome).</span><span class="sxs-lookup"><span data-stu-id="12287-110"> WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="12287-111">Vous pouvez également déboguer votre application à l’aide de Visual Studio ou Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="12287-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="12287-112">Les scénarios disponibles sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="12287-112">Available scenarios include:</span></span>

* <span data-ttu-id="12287-113">Définir et supprimer des points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="12287-113">Set and remove breakpoints.</span></span>
* <span data-ttu-id="12287-114">Exécutez l’application avec prise en charge du débogage dans Visual Studio et Visual Studio Code (prise en charge de<kbd>F5</kbd> ).</span><span class="sxs-lookup"><span data-stu-id="12287-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="12287-115">Une étape (<kbd>F10</kbd>) à l’aide du code.</span><span class="sxs-lookup"><span data-stu-id="12287-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="12287-116">Reprendre l’exécution du code avec <kbd>F8</kbd> dans un navigateur ou <kbd>F5</kbd> dans Visual Studio ou Visual Studio code.</span><span class="sxs-lookup"><span data-stu-id="12287-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="12287-117">Dans l' *affichage des variables locales,* observez les valeurs des variables locales.</span><span class="sxs-lookup"><span data-stu-id="12287-117">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="12287-118">Consultez la pile des appels, y compris les chaînes d’appel qui passent de JavaScript à .NET et de .NET à JavaScript.</span><span class="sxs-lookup"><span data-stu-id="12287-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="12287-119">Pour le moment, vous *ne pouvez pas*:</span><span class="sxs-lookup"><span data-stu-id="12287-119">For now, you *can't*:</span></span>

* <span data-ttu-id="12287-120">Arrêt sur les exceptions non gérées.</span><span class="sxs-lookup"><span data-stu-id="12287-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="12287-121">Atteindre les points d’arrêt pendant le démarrage de l’application.</span><span class="sxs-lookup"><span data-stu-id="12287-121">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="12287-122">Nous continuerons à améliorer l’expérience de débogage dans les versions à venir.</span><span class="sxs-lookup"><span data-stu-id="12287-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="12287-123">Prérequis</span><span class="sxs-lookup"><span data-stu-id="12287-123">Prerequisites</span></span>

<span data-ttu-id="12287-124">Le débogage requiert l’un des navigateurs suivants :</span><span class="sxs-lookup"><span data-stu-id="12287-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="12287-125">Microsoft Edge (version 80 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="12287-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="12287-126">Google Chrome (version 70 ou ultérieure)</span><span class="sxs-lookup"><span data-stu-id="12287-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="12287-127">Activer le débogage pour Visual Studio et Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12287-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="12287-128">Pour activer le débogage pour une Blazor application Webassembly existante, mettez à jour le fichier *launchSettings. JSON* dans le projet de démarrage pour inclure la `inspectUri` propriété suivante dans chaque profil de lancement :</span><span class="sxs-lookup"><span data-stu-id="12287-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="12287-129">Une fois mis à jour, le fichier *launchSettings. JSON* doit ressembler à l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="12287-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="12287-130">La `inspectUri` propriété :</span><span class="sxs-lookup"><span data-stu-id="12287-130">The `inspectUri` property:</span></span>

* <span data-ttu-id="12287-131">Permet à l’IDE de détecter que l’application est une Blazor application Webassembly.</span><span class="sxs-lookup"><span data-stu-id="12287-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="12287-132">Indique à l’infrastructure de débogage de script de se connecter au navigateur via le Blazor proxy de débogage de.</span><span class="sxs-lookup"><span data-stu-id="12287-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="12287-133">Les valeurs d’espace réservé pour le protocole WebSockets ( `wsProtocol` ), l’hôte ( `url.hostname` ), le port ( `url.port` ) et l’URI de l’inspecteur sur le navigateur lancé ( `browserInspectUri` ) sont fournies par l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="12287-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="12287-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="12287-134">Visual Studio</span></span>

<span data-ttu-id="12287-135">Pour déboguer une Blazor application Webassembly dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="12287-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="12287-136">Créez une nouvelle ASP.NET Core Blazor application Webassembly hébergée.</span><span class="sxs-lookup"><span data-stu-id="12287-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="12287-137">Appuyez sur <kbd>F5</kbd> pour exécuter l’application dans le débogueur.</span><span class="sxs-lookup"><span data-stu-id="12287-137">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="12287-138">Définissez un point d’arrêt dans *Counter. Razor* dans la `IncrementCount` méthode.</span><span class="sxs-lookup"><span data-stu-id="12287-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="12287-139">Accédez à l’onglet **compteur** et sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="12287-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![Compteur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="12287-141">Examinez la valeur du `currentCount` champ dans la fenêtre variables locales :</span><span class="sxs-lookup"><span data-stu-id="12287-141">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![Afficher les variables locales](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="12287-143">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution.</span><span class="sxs-lookup"><span data-stu-id="12287-143">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="12287-144">Lors du débogage de votre Blazor application Webassembly, vous pouvez également déboguer votre code serveur :</span><span class="sxs-lookup"><span data-stu-id="12287-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="12287-145">Définissez un point d’arrêt dans la page *fetchData. Razor* dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="12287-145">Set a breakpoint in the *FetchData.razor* page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="12287-146">Définissez un point d’arrêt dans la `WeatherForecastController` `Get` méthode d’action.</span><span class="sxs-lookup"><span data-stu-id="12287-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="12287-147">Accédez à l’onglet **extraire les données** pour atteindre le premier point d’arrêt dans le `FetchData` composant juste avant qu’il envoie une requête HTTP au serveur :</span><span class="sxs-lookup"><span data-stu-id="12287-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![Déboguer extraire des données](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="12287-149">Appuyez sur <kbd>F5</kbd> pour poursuivre l’exécution, puis appuyez sur le point d’arrêt sur le serveur dans le `WeatherForecastController` :</span><span class="sxs-lookup"><span data-stu-id="12287-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![Serveur de débogage](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="12287-151">Appuyez de nouveau sur <kbd>F5</kbd> pour permettre à l’exécution de se poursuivre et consultez le tableau prévisions météo rendu.</span><span class="sxs-lookup"><span data-stu-id="12287-151">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="12287-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="12287-152">Visual Studio Code</span></span>

<span data-ttu-id="12287-153">Pour déboguer une Blazor application Webassembly dans Visual Studio code :</span><span class="sxs-lookup"><span data-stu-id="12287-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="12287-154">Installez l' [extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="12287-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![Extensions](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Débogueur JS preview](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="12287-157">Ouvrez une Blazor application Webassembly existante avec le débogage activé.</span><span class="sxs-lookup"><span data-stu-id="12287-157">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="12287-158">Si vous recevez la notification suivante indiquant qu’une configuration supplémentaire est requise pour activer le débogage, vérifiez que les extensions appropriées sont installées et que le débogage de l’aperçu JavaScript est activé, puis rechargez la fenêtre :</span><span class="sxs-lookup"><span data-stu-id="12287-158">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![Configuration supplémentaire requise](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="12287-160">Une notification propose d’ajouter les ressources requises à l’application pour la génération et le débogage.</span><span class="sxs-lookup"><span data-stu-id="12287-160">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="12287-161">Sélectionnez **Oui**:</span><span class="sxs-lookup"><span data-stu-id="12287-161">Select **Yes**:</span></span>

     ![Ajouter les ressources requises](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="12287-163">Le démarrage de l’application dans le débogueur est un processus en deux étapes :</span><span class="sxs-lookup"><span data-stu-id="12287-163">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="12287-164">1 \.</span><span class="sxs-lookup"><span data-stu-id="12287-164">1\.</span></span> <span data-ttu-id="12287-165">**Tout d’abord**, démarrez l’application à l’aide de la configuration de lancement **de .net Core Launch ( Blazor autonome)** .</span><span class="sxs-lookup"><span data-stu-id="12287-165">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="12287-166">2 \.</span><span class="sxs-lookup"><span data-stu-id="12287-166">2\.</span></span> <span data-ttu-id="12287-167">**Une fois l’application démarrée**, démarrez le navigateur à l’aide de l' \*\* Blazor Assembly Web de débogage .net core dans\*\* la configuration de lancement chrome (nécessite chrome).</span><span class="sxs-lookup"><span data-stu-id="12287-167">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="12287-168">Pour utiliser Edge au lieu de chrome, remplacez l' `type` de la configuration de lancement dans *. vscode/Launch. JSON* par `pwa-chrome` `pwa-msedge` .</span><span class="sxs-lookup"><span data-stu-id="12287-168">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="12287-169">Définissez un point d’arrêt dans la `IncrementCount` méthode du `Counter` composant, puis sélectionnez le bouton pour atteindre le point d’arrêt :</span><span class="sxs-lookup"><span data-stu-id="12287-169">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![Déboguer le compteur dans VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="12287-171">Déboguer dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="12287-171">Debug in the browser</span></span>

1. <span data-ttu-id="12287-172">Exécutez une version Debug de l’application dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="12287-172">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="12287-173">Appuyez sur <kbd>MAJ</kbd> + <kbd>ALT</kbd> + <kbd>D</kbd>.</span><span class="sxs-lookup"><span data-stu-id="12287-173">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="12287-174">Le navigateur doit être exécuté avec le débogage distant activé.</span><span class="sxs-lookup"><span data-stu-id="12287-174">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="12287-175">Si le débogage distant est désactivé, une page d’erreur **Impossible de trouver un onglet de navigateur pouvant être débogué** est générée.</span><span class="sxs-lookup"><span data-stu-id="12287-175">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="12287-176">La page d’erreur contient des instructions pour l’exécution du navigateur avec le port de débogage ouvert, afin que le Blazor proxy de débogage puisse se connecter à l’application.</span><span class="sxs-lookup"><span data-stu-id="12287-176">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="12287-177">*Fermez toutes les instances de navigateur* et redémarrez le navigateur comme indiqué.</span><span class="sxs-lookup"><span data-stu-id="12287-177">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="12287-178">Une fois que le navigateur est en cours d’exécution avec le débogage distant activé, le raccourci clavier de débogage ouvre un nouvel onglet du débogueur. Après un moment, l’onglet **sources** affiche une liste des assemblys .net dans l’application.</span><span class="sxs-lookup"><span data-stu-id="12287-178">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="12287-179">Développez chaque assembly et recherchez les fichiers sources *. cs* / *. Razor* disponibles pour le débogage.</span><span class="sxs-lookup"><span data-stu-id="12287-179">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="12287-180">Définissez des points d’arrêt, revenez à l’onglet de l’application, et les points d’arrêt sont atteints lorsque le code s’exécute.</span><span class="sxs-lookup"><span data-stu-id="12287-180">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="12287-181">Une fois le point d’arrêt atteint, une seule étape (<kbd>F10</kbd>) passe par l’exécution du code ou de la reprise (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="12287-181">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="12287-182">fournit un proxy de débogage qui implémente le [protocole chrome devtools](https://chromedevtools.github.io/devtools-protocol/) et augmente le protocole avec. Informations spécifiques à .net.</span><span class="sxs-lookup"><span data-stu-id="12287-182"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="12287-183">Quand le raccourci clavier de débogage est enfoncé, Blazor pointe le devtools chrome au niveau du proxy.</span><span class="sxs-lookup"><span data-stu-id="12287-183">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="12287-184">Le proxy se connecte à la fenêtre du navigateur que vous cherchez à déboguer (par conséquent, il est nécessaire d’activer le débogage distant).</span><span class="sxs-lookup"><span data-stu-id="12287-184">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="12287-185">Mappages des sources du navigateur</span><span class="sxs-lookup"><span data-stu-id="12287-185">Browser source maps</span></span>

<span data-ttu-id="12287-186">Les mappages de source de navigateur permettent au navigateur de mapper les fichiers compilés à leurs fichiers sources d’origine et sont couramment utilisés pour le débogage côté client.</span><span class="sxs-lookup"><span data-stu-id="12287-186">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="12287-187">Toutefois, Blazor ne mappe actuellement pas C# directement à JavaScript/WASM.</span><span class="sxs-lookup"><span data-stu-id="12287-187">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="12287-188">Au lieu de cela, Blazor fait l’interprétation du langage intermédiaire dans le navigateur, les mappages de source ne sont donc pas pertinents.</span><span class="sxs-lookup"><span data-stu-id="12287-188">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="12287-189">Dépanner</span><span class="sxs-lookup"><span data-stu-id="12287-189">Troubleshoot</span></span>

<span data-ttu-id="12287-190">Si vous rencontrez des erreurs, les conseils suivants peuvent vous aider :</span><span class="sxs-lookup"><span data-stu-id="12287-190">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="12287-191">Dans l’onglet **débogueur** , ouvrez les outils de développement de votre navigateur.</span><span class="sxs-lookup"><span data-stu-id="12287-191">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="12287-192">Dans la console, exécutez `localStorage.clear()` pour supprimer tous les points d’arrêt.</span><span class="sxs-lookup"><span data-stu-id="12287-192">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="12287-193">Confirmez que vous avez installé et approuvé le certificat de développement ASP.NET Core HTTPs.</span><span class="sxs-lookup"><span data-stu-id="12287-193">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="12287-194">Pour plus d'informations, consultez <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="12287-194">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
