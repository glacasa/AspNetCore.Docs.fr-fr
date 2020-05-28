---
<span data-ttu-id="da755-101">titre : Auteur : Description : monikerRange : ms. Author : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="da755-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="da755-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="da755-102">'Blazor'</span></span>
- <span data-ttu-id="da755-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="da755-103">'Identity'</span></span>
- <span data-ttu-id="da755-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="da755-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="da755-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="da755-105">'Razor'</span></span>
- <span data-ttu-id="da755-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="da755-106">'SignalR' uid:</span></span> 

---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="da755-107">Détecter les modifications avec des jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da755-107">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="da755-108">Un *jeton de modification* est un module à usage général de bas niveau, utilisé pour effectuer le suivi des modifications de l’état.</span><span class="sxs-lookup"><span data-stu-id="da755-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="da755-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da755-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="da755-110">Interface d’IChangeToken</span><span class="sxs-lookup"><span data-stu-id="da755-110">IChangeToken interface</span></span>

<span data-ttu-id="da755-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propage des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="da755-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="da755-112">`IChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="da755-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="da755-113">Le package NuGet [Microsoft. extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) est fourni implicitement aux applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="da755-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="da755-114">`IChangeToken` a deux propriétés :</span><span class="sxs-lookup"><span data-stu-id="da755-114">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="da755-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indique si le jeton déclenche des rappels de façon proactive.</span><span class="sxs-lookup"><span data-stu-id="da755-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="da755-116">Si `ActiveChangedCallbacks` est défini sur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` pour connaître les modifications.</span><span class="sxs-lookup"><span data-stu-id="da755-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="da755-117">Il est également possible qu’un jeton ne soit jamais annulé si aucune modification ne se produit, ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.</span><span class="sxs-lookup"><span data-stu-id="da755-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="da755-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> obtient une valeur qui indique si une modification a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="da755-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="da755-119">L' `IChangeToken` interface inclut la méthode [RegisterChangeCallback (action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , qui inscrit un rappel appelé lorsque le jeton a changé.</span><span class="sxs-lookup"><span data-stu-id="da755-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="da755-120">`HasChanged` doit être défini avant que le rappel soit appelé.</span><span class="sxs-lookup"><span data-stu-id="da755-120">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="da755-121">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="da755-121">ChangeToken class</span></span>

<span data-ttu-id="da755-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> est une classe statique utilisée pour propager des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="da755-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="da755-123">`ChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="da755-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="da755-124">Le package NuGet [Microsoft. extensions. Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) est fourni implicitement aux applications ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="da755-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="da755-125">La méthode [ChangeToken. OnChange (Func \<IChangeToken> , action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) inscrit un `Action` à appeler chaque fois que le jeton change :</span><span class="sxs-lookup"><span data-stu-id="da755-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="da755-126">`Func<IChangeToken>` produit le jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-126">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="da755-127">`Action` est appelée quand le jeton change.</span><span class="sxs-lookup"><span data-stu-id="da755-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="da755-128">La surcharge [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) prend un paramètre supplémentaire `TState` passé dans le consommateur de jetons `Action` .</span><span class="sxs-lookup"><span data-stu-id="da755-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="da755-129">`OnChange`Retourne un <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="da755-129">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="da755-130">L’appel de <xref:System.IDisposable.Dispose*> fait que le jeton cesse d’être à l’écoute des modifications suivantes et libère les ressources du jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="da755-131">Exemples d’utilisation de jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da755-131">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="da755-132">Les jetons de modification sont utilisés dans des zones importantes d’ASP.NET Core pour surveiller les modifications apportées aux objets :</span><span class="sxs-lookup"><span data-stu-id="da755-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="da755-133">Pour surveiller les modifications apportées aux fichiers, la méthode <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crée un `IChangeToken` pour les fichiers ou le dossier à surveiller.</span><span class="sxs-lookup"><span data-stu-id="da755-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="da755-134">Les jetons `IChangeToken` peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache en cas de modification.</span><span class="sxs-lookup"><span data-stu-id="da755-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="da755-135">Pour les modifications `TOptions`, l’implémentation <xref:Microsoft.Extensions.Options.OptionsMonitor`1> par défaut de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> a une surcharge qui accepte une ou plusieurs instances <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="da755-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="da755-136">Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification pour les modifications des options de suivi.</span><span class="sxs-lookup"><span data-stu-id="da755-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="da755-137">Surveiller les modifications de configuration</span><span class="sxs-lookup"><span data-stu-id="da755-137">Monitor for configuration changes</span></span>

<span data-ttu-id="da755-138">Par défaut, les modèles ASP.NET Core utilisent des [fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* et *appsettings.Production.json*) pour charger les paramètres de configuration des applications.</span><span class="sxs-lookup"><span data-stu-id="da755-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="da755-139">Ces fichiers sont configurés avec la méthode d’extension [AddJsonFile(IConfigurationBuilder, chaîne, booléen, booléen)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) sur <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> qui accepte un paramètre `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="da755-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="da755-140">`reloadOnChange` indique si la configuration doit être rechargée en cas de modification d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="da755-141">Ce paramètre s’affiche dans la méthode pratique <xref:Microsoft.Extensions.Hosting.Host><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="da755-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="da755-142">La configuration basée sur les fichiers est représentée par <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="da755-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="da755-143">`FileConfigurationSource` utilise <xref:Microsoft.Extensions.FileProviders.IFileProvider> pour surveiller les fichiers.</span><span class="sxs-lookup"><span data-stu-id="da755-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="da755-144">Par défaut, `IFileMonitor` est fourni par un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, qui utilise <xref:System.IO.FileSystemWatcher> pour surveiller les modifications des fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="da755-145">L’exemple d’application montre les deux implémentations de la surveillance des modifications de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="da755-146">Si l’un des fichiers *appsettings* change, les deux implémentations d’analyse du fichier exécutent du code personnalisé&mdash;l’exemple d’application écrit un message dans la console.</span><span class="sxs-lookup"><span data-stu-id="da755-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="da755-147">Le `FileSystemWatcher` d’un fichier de configuration peut déclencher plusieurs rappels de jeton pour une même modification du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="da755-148">Pour vous assurer que le code personnalisé n’est exécuté qu’une seule fois lors du déclenchement de plusieurs rappels de jeton, l’implémentation de l’exemple vérifie les hachages des fichiers.</span><span class="sxs-lookup"><span data-stu-id="da755-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="da755-149">L’exemple utilise le hachage de fichier SHA1.</span><span class="sxs-lookup"><span data-stu-id="da755-149">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="da755-150">Une nouvelle tentative est implémentée avec une interruption d’une durée exponentielle.</span><span class="sxs-lookup"><span data-stu-id="da755-150">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="da755-151">Une nouvelle tentative est effectuée, car il peut se produire un verrouillage du fichier qui empêche temporairement le calcul d’un nouveau hachage sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="da755-152">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-152">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="da755-153">Jeton de modification de démarrage simple</span><span class="sxs-lookup"><span data-stu-id="da755-153">Simple startup change token</span></span>

<span data-ttu-id="da755-154">Inscrivez un rappel `Action` de consommateur de jeton pour les notifications de modification au jeton de rechargement de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="da755-155">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="da755-155">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="da755-156">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-156">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="da755-157">Le rappel est la méthode `InvokeChanged` :</span><span class="sxs-lookup"><span data-stu-id="da755-157">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="da755-158">Le `state` du rappel est utilisé pour transmettre le `IWebHostEnvironment`, ce qui est utile pour spécifier le bon fichier de configuration *appsettings* à surveiller (par exemple, *appsettings.Development.json* dans l’environnement de développement).</span><span class="sxs-lookup"><span data-stu-id="da755-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="da755-159">Les hachages de fichier sont utilisés pour empêcher l’instruction `WriteConsole` d’être exécutée plusieurs fois en raison de plusieurs rappels de jeton alors que le fichier de configuration n’a été modifié qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="da755-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="da755-160">Ce système s’exécute tant que l’application est en cours d’exécution et ne peut pas être désactivé par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="da755-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="da755-161">Surveiller les modifications de configuration en tant que service</span><span class="sxs-lookup"><span data-stu-id="da755-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="da755-162">L’exemple implémente :</span><span class="sxs-lookup"><span data-stu-id="da755-162">The sample implements:</span></span>

* <span data-ttu-id="da755-163">Surveillance de jeton du démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="da755-163">Basic startup token monitoring.</span></span>
* <span data-ttu-id="da755-164">Surveillance en tant que service.</span><span class="sxs-lookup"><span data-stu-id="da755-164">Monitoring as a service.</span></span>
* <span data-ttu-id="da755-165">Un mécanisme pour activer et désactiver la surveillance.</span><span class="sxs-lookup"><span data-stu-id="da755-165">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="da755-166">L’exemple établit une interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="da755-166">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="da755-167">*Extensions/Configurationmonitor.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-167">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="da755-168">Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :</span><span class="sxs-lookup"><span data-stu-id="da755-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="da755-169">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-169">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="da755-170">`InvokeChanged` est la méthode de rappel.</span><span class="sxs-lookup"><span data-stu-id="da755-170">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="da755-171">Le `state` dans cette instance est une référence à l’instance `IConfigurationMonitor` qui est utilisée pour accéder à l’état du monitoring.</span><span class="sxs-lookup"><span data-stu-id="da755-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="da755-172">Deux propriétés sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="da755-172">Two properties are used:</span></span>

* <span data-ttu-id="da755-173">`MonitoringEnabled`: Indique si le rappel doit exécuter son code personnalisé.</span><span class="sxs-lookup"><span data-stu-id="da755-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="da755-174">`CurrentState`: Décrit l’état d’analyse actuel pour une utilisation dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="da755-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="da755-175">La méthode `InvokeChanged` est similaire à l’approche précédente, excepté que :</span><span class="sxs-lookup"><span data-stu-id="da755-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="da755-176">Elle n’exécute pas son code, sauf si `MonitoringEnabled` est `true`.</span><span class="sxs-lookup"><span data-stu-id="da755-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="da755-177">Elle indique le `state` actuel dans sa sortie `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="da755-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="da755-178">Une instance `ConfigurationMonitor` est inscrite en tant que service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da755-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="da755-179">La page Index permet à l’utilisateur de contrôler la surveillance de la configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="da755-180">L’instance de `IConfigurationMonitor` est injectée dans le `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="da755-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="da755-181">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-181">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="da755-182">Le moniteur de configuration (`_monitor`) est utilisé pour activer ou désactiver l’analyse et définir l’état actuel pour les commentaires sur l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="da755-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="da755-183">Quand `OnPostStartMonitoring` est déclenché, la surveillance est activée et l’état actuel est effacé.</span><span class="sxs-lookup"><span data-stu-id="da755-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="da755-184">Quand `OnPostStopMonitoring` est déclenché, la surveillance est désactivée et l’état est défini de façon à indiquer que la surveillance n’est pas effectuée.</span><span class="sxs-lookup"><span data-stu-id="da755-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="da755-185">Des boutons dans l’IU activent et désactivent la surveillance.</span><span class="sxs-lookup"><span data-stu-id="da755-185">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="da755-186">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="da755-186">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="da755-187">Surveiller les modifications de fichiers mis en cache</span><span class="sxs-lookup"><span data-stu-id="da755-187">Monitor cached file changes</span></span>

<span data-ttu-id="da755-188">Le contenu des fichiers peut être mis en cache en mémoire avec <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="da755-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="da755-189">La mise en cache en mémoire est décrite dans la rubrique [Mise en cache en mémoire](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="da755-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="da755-190">Sans actions supplémentaires, comme l’implémentation décrite ci-dessous, les données *périmées* (obsolètes) sont retournées depuis un cache si la source de données change.</span><span class="sxs-lookup"><span data-stu-id="da755-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="da755-191">Par exemple, le fait de ne pas prendre en compte l’état d’un fichier source en cache lors du renouvellement d’une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) rend obsolètes les données du fichier mis cache.</span><span class="sxs-lookup"><span data-stu-id="da755-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="da755-192">Chaque demande de données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache.</span><span class="sxs-lookup"><span data-stu-id="da755-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="da755-193">Toutes les fonctionnalités d’une application qui utilisent le contenu mis en cache d’un fichier sont exposées au risque de recevoir du contenu obsolète.</span><span class="sxs-lookup"><span data-stu-id="da755-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="da755-194">L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche la présence de contenu obsolète dans le cache.</span><span class="sxs-lookup"><span data-stu-id="da755-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="da755-195">L’exemple d’application montre une implémentation de l’approche.</span><span class="sxs-lookup"><span data-stu-id="da755-195">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="da755-196">L’exemple utilise `GetFileContent` pour :</span><span class="sxs-lookup"><span data-stu-id="da755-196">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="da755-197">Retourner le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-197">Return file content.</span></span>
* <span data-ttu-id="da755-198">Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier empêche temporairement la lecture d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="da755-199">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-199">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="da755-200">Un `FileService` est créé pour gérer les recherches des fichiers mis en cache.</span><span class="sxs-lookup"><span data-stu-id="da755-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="da755-201">L’appel de la méthode `GetFileContent` du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourne à l’appelant (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="da755-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="da755-202">Si le contenu en cache n’est pas trouvé avec la clé du cache, les actions suivantes sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="da755-202">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="da755-203">Le contenu du fichier est obtenu à l’aide de `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="da755-203">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="da755-204">Un jeton de modification est obtenu auprès du fournisseur du fichier avec [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="da755-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="da755-205">Le rappel du jeton est déclenché quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="da755-205">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="da755-206">Le contenu du fichier est mis en cache avec une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="da755-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="da755-207">Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) pour supprimer l’entrée du cache si le fichier change alors qu’il est mis en cache.</span><span class="sxs-lookup"><span data-stu-id="da755-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="da755-208">Dans l’exemple suivant, les fichiers sont stockés dans la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="da755-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="da755-209">`IWebHostEnvironment.ContentRootFileProvider`est utilisé pour obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointage au niveau de l’application `IWebHostEnvironment.ContentRootPath` .</span><span class="sxs-lookup"><span data-stu-id="da755-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="da755-210">`filePath` est obtenue avec [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="da755-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="da755-211">`FileService` est inscrit dans le conteneur de service, ainsi que le service de mise en cache en mémoire.</span><span class="sxs-lookup"><span data-stu-id="da755-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="da755-212">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da755-212">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="da755-213">Le modèle de page charge le contenu du fichier en utilisant le service.</span><span class="sxs-lookup"><span data-stu-id="da755-213">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="da755-214">Dans la méthode `OnGet` de la page Index (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="da755-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="da755-215">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="da755-215">CompositeChangeToken class</span></span>

<span data-ttu-id="da755-216">Pour représenter une ou plusieurs instances de `IChangeToken` dans un même objet, utilisez la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="da755-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="da755-217">`HasChanged` sur le jeton composite indique `true` si l’élément `HasChanged` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="da755-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="da755-218">`ActiveChangeCallbacks` sur le jeton composite indique `true` si l’élément `ActiveChangeCallbacks` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="da755-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="da755-219">Si plusieurs modifications simultanées se produisent, le rappel de modification composite n’est appelé qu’une fois.</span><span class="sxs-lookup"><span data-stu-id="da755-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="da755-220">Un *jeton de modification* est un module à usage général de bas niveau, utilisé pour effectuer le suivi des modifications de l’état.</span><span class="sxs-lookup"><span data-stu-id="da755-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="da755-221">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da755-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="da755-222">Interface d’IChangeToken</span><span class="sxs-lookup"><span data-stu-id="da755-222">IChangeToken interface</span></span>

<span data-ttu-id="da755-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propage des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="da755-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="da755-224">`IChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="da755-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="da755-225">Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="da755-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="da755-226">`IChangeToken` a deux propriétés :</span><span class="sxs-lookup"><span data-stu-id="da755-226">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="da755-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indique si le jeton déclenche des rappels de façon proactive.</span><span class="sxs-lookup"><span data-stu-id="da755-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="da755-228">Si `ActiveChangedCallbacks` est défini sur `false`, un rappel n’est jamais appelé, et l’application doit interroger `HasChanged` pour connaître les modifications.</span><span class="sxs-lookup"><span data-stu-id="da755-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="da755-229">Il est également possible qu’un jeton ne soit jamais annulé si aucune modification ne se produit, ou si l’écouteur de modifications sous-jacent est supprimé ou désactivé.</span><span class="sxs-lookup"><span data-stu-id="da755-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="da755-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> obtient une valeur qui indique si une modification a eu lieu.</span><span class="sxs-lookup"><span data-stu-id="da755-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="da755-231">L' `IChangeToken` interface inclut la méthode [RegisterChangeCallback (action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , qui inscrit un rappel appelé lorsque le jeton a changé.</span><span class="sxs-lookup"><span data-stu-id="da755-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="da755-232">`HasChanged` doit être défini avant que le rappel soit appelé.</span><span class="sxs-lookup"><span data-stu-id="da755-232">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="da755-233">Classe ChangeToken</span><span class="sxs-lookup"><span data-stu-id="da755-233">ChangeToken class</span></span>

<span data-ttu-id="da755-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> est une classe statique utilisée pour propager des notifications indiquant qu’une modification s’est produite.</span><span class="sxs-lookup"><span data-stu-id="da755-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="da755-235">`ChangeToken` réside dans l’espace de noms <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="da755-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="da755-236">Pour les applications qui n’utilisent pas le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), créez une référence au package NuGet [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="da755-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="da755-237">La méthode [ChangeToken. OnChange (Func \<IChangeToken> , action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) inscrit un `Action` à appeler chaque fois que le jeton change :</span><span class="sxs-lookup"><span data-stu-id="da755-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="da755-238">`Func<IChangeToken>` produit le jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-238">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="da755-239">`Action` est appelée quand le jeton change.</span><span class="sxs-lookup"><span data-stu-id="da755-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="da755-240">La surcharge [ChangeToken. OnChange \<TState> (Func \<IChangeToken> , action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) prend un paramètre supplémentaire `TState` passé dans le consommateur de jetons `Action` .</span><span class="sxs-lookup"><span data-stu-id="da755-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="da755-241">`OnChange`Retourne un <xref:System.IDisposable>.</span><span class="sxs-lookup"><span data-stu-id="da755-241">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="da755-242">L’appel de <xref:System.IDisposable.Dispose*> fait que le jeton cesse d’être à l’écoute des modifications suivantes et libère les ressources du jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="da755-243">Exemples d’utilisation de jetons de modification dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da755-243">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="da755-244">Les jetons de modification sont utilisés dans des zones importantes d’ASP.NET Core pour surveiller les modifications apportées aux objets :</span><span class="sxs-lookup"><span data-stu-id="da755-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="da755-245">Pour surveiller les modifications apportées aux fichiers, la méthode <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> de <xref:Microsoft.Extensions.FileProviders.IFileProvider> crée un `IChangeToken` pour les fichiers ou le dossier à surveiller.</span><span class="sxs-lookup"><span data-stu-id="da755-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="da755-246">Les jetons `IChangeToken` peuvent être ajoutés aux entrées du cache pour déclencher des suppressions dans le cache en cas de modification.</span><span class="sxs-lookup"><span data-stu-id="da755-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="da755-247">Pour les modifications `TOptions`, l’implémentation <xref:Microsoft.Extensions.Options.OptionsMonitor`1> par défaut de <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> a une surcharge qui accepte une ou plusieurs instances <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>.</span><span class="sxs-lookup"><span data-stu-id="da755-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="da755-248">Chaque instance retourne un `IChangeToken` pour inscrire un rappel de notification de modification pour les modifications des options de suivi.</span><span class="sxs-lookup"><span data-stu-id="da755-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="da755-249">Surveiller les modifications de configuration</span><span class="sxs-lookup"><span data-stu-id="da755-249">Monitor for configuration changes</span></span>

<span data-ttu-id="da755-250">Par défaut, les modèles ASP.NET Core utilisent des [fichiers de configuration JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* et *appsettings.Production.json*) pour charger les paramètres de configuration des applications.</span><span class="sxs-lookup"><span data-stu-id="da755-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="da755-251">Ces fichiers sont configurés avec la méthode d’extension [AddJsonFile(IConfigurationBuilder, chaîne, booléen, booléen)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) sur <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> qui accepte un paramètre `reloadOnChange`.</span><span class="sxs-lookup"><span data-stu-id="da755-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="da755-252">`reloadOnChange` indique si la configuration doit être rechargée en cas de modification d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="da755-253">Ce paramètre s’affiche dans la méthode pratique <xref:Microsoft.AspNetCore.WebHost><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :</span><span class="sxs-lookup"><span data-stu-id="da755-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="da755-254">La configuration basée sur les fichiers est représentée par <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="da755-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="da755-255">`FileConfigurationSource` utilise <xref:Microsoft.Extensions.FileProviders.IFileProvider> pour surveiller les fichiers.</span><span class="sxs-lookup"><span data-stu-id="da755-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="da755-256">Par défaut, `IFileMonitor` est fourni par un <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, qui utilise <xref:System.IO.FileSystemWatcher> pour surveiller les modifications des fichiers de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="da755-257">L’exemple d’application montre les deux implémentations de la surveillance des modifications de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="da755-258">Si l’un des fichiers *appsettings* change, les deux implémentations d’analyse du fichier exécutent du code personnalisé&mdash;l’exemple d’application écrit un message dans la console.</span><span class="sxs-lookup"><span data-stu-id="da755-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="da755-259">Le `FileSystemWatcher` d’un fichier de configuration peut déclencher plusieurs rappels de jeton pour une même modification du fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="da755-260">Pour vous assurer que le code personnalisé n’est exécuté qu’une seule fois lors du déclenchement de plusieurs rappels de jeton, l’implémentation de l’exemple vérifie les hachages des fichiers.</span><span class="sxs-lookup"><span data-stu-id="da755-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="da755-261">L’exemple utilise le hachage de fichier SHA1.</span><span class="sxs-lookup"><span data-stu-id="da755-261">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="da755-262">Une nouvelle tentative est implémentée avec une interruption d’une durée exponentielle.</span><span class="sxs-lookup"><span data-stu-id="da755-262">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="da755-263">Une nouvelle tentative est effectuée, car il peut se produire un verrouillage du fichier qui empêche temporairement le calcul d’un nouveau hachage sur un fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="da755-264">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-264">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="da755-265">Jeton de modification de démarrage simple</span><span class="sxs-lookup"><span data-stu-id="da755-265">Simple startup change token</span></span>

<span data-ttu-id="da755-266">Inscrivez un rappel `Action` de consommateur de jeton pour les notifications de modification au jeton de rechargement de configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="da755-267">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="da755-267">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="da755-268">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-268">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="da755-269">Le rappel est la méthode `InvokeChanged` :</span><span class="sxs-lookup"><span data-stu-id="da755-269">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="da755-270">Le `state` du rappel est utilisé pour transmettre le `IHostingEnvironment`, ce qui est utile pour spécifier le bon fichier de configuration *appsettings* à surveiller (par exemple, *appsettings.Development.json* dans l’environnement de développement).</span><span class="sxs-lookup"><span data-stu-id="da755-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="da755-271">Les hachages de fichier sont utilisés pour empêcher l’instruction `WriteConsole` d’être exécutée plusieurs fois en raison de plusieurs rappels de jeton alors que le fichier de configuration n’a été modifié qu’une seule fois.</span><span class="sxs-lookup"><span data-stu-id="da755-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="da755-272">Ce système s’exécute tant que l’application est en cours d’exécution et ne peut pas être désactivé par l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="da755-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="da755-273">Surveiller les modifications de configuration en tant que service</span><span class="sxs-lookup"><span data-stu-id="da755-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="da755-274">L’exemple implémente :</span><span class="sxs-lookup"><span data-stu-id="da755-274">The sample implements:</span></span>

* <span data-ttu-id="da755-275">Surveillance de jeton du démarrage de base.</span><span class="sxs-lookup"><span data-stu-id="da755-275">Basic startup token monitoring.</span></span>
* <span data-ttu-id="da755-276">Surveillance en tant que service.</span><span class="sxs-lookup"><span data-stu-id="da755-276">Monitoring as a service.</span></span>
* <span data-ttu-id="da755-277">Un mécanisme pour activer et désactiver la surveillance.</span><span class="sxs-lookup"><span data-stu-id="da755-277">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="da755-278">L’exemple établit une interface `IConfigurationMonitor`.</span><span class="sxs-lookup"><span data-stu-id="da755-278">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="da755-279">*Extensions/Configurationmonitor.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-279">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="da755-280">Le constructeur de la classe implémentée, `ConfigurationMonitor`, inscrit un rappel pour les notifications de modification :</span><span class="sxs-lookup"><span data-stu-id="da755-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="da755-281">`config.GetReloadToken()` fournit le jeton.</span><span class="sxs-lookup"><span data-stu-id="da755-281">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="da755-282">`InvokeChanged` est la méthode de rappel.</span><span class="sxs-lookup"><span data-stu-id="da755-282">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="da755-283">Le `state` dans cette instance est une référence à l’instance `IConfigurationMonitor` qui est utilisée pour accéder à l’état du monitoring.</span><span class="sxs-lookup"><span data-stu-id="da755-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="da755-284">Deux propriétés sont utilisées :</span><span class="sxs-lookup"><span data-stu-id="da755-284">Two properties are used:</span></span>

* <span data-ttu-id="da755-285">`MonitoringEnabled`: Indique si le rappel doit exécuter son code personnalisé.</span><span class="sxs-lookup"><span data-stu-id="da755-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="da755-286">`CurrentState`: Décrit l’état d’analyse actuel pour une utilisation dans l’interface utilisateur.</span><span class="sxs-lookup"><span data-stu-id="da755-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="da755-287">La méthode `InvokeChanged` est similaire à l’approche précédente, excepté que :</span><span class="sxs-lookup"><span data-stu-id="da755-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="da755-288">Elle n’exécute pas son code, sauf si `MonitoringEnabled` est `true`.</span><span class="sxs-lookup"><span data-stu-id="da755-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="da755-289">Elle indique le `state` actuel dans sa sortie `WriteConsole`.</span><span class="sxs-lookup"><span data-stu-id="da755-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="da755-290">Une instance `ConfigurationMonitor` est inscrite en tant que service dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da755-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="da755-291">La page Index permet à l’utilisateur de contrôler la surveillance de la configuration.</span><span class="sxs-lookup"><span data-stu-id="da755-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="da755-292">L’instance de `IConfigurationMonitor` est injectée dans le `IndexModel`.</span><span class="sxs-lookup"><span data-stu-id="da755-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="da755-293">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-293">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="da755-294">Le moniteur de configuration (`_monitor`) est utilisé pour activer ou désactiver l’analyse et définir l’état actuel pour les commentaires sur l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="da755-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="da755-295">Quand `OnPostStartMonitoring` est déclenché, la surveillance est activée et l’état actuel est effacé.</span><span class="sxs-lookup"><span data-stu-id="da755-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="da755-296">Quand `OnPostStopMonitoring` est déclenché, la surveillance est désactivée et l’état est défini de façon à indiquer que la surveillance n’est pas effectuée.</span><span class="sxs-lookup"><span data-stu-id="da755-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="da755-297">Des boutons dans l’IU activent et désactivent la surveillance.</span><span class="sxs-lookup"><span data-stu-id="da755-297">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="da755-298">*Pages/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="da755-298">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="da755-299">Surveiller les modifications de fichiers mis en cache</span><span class="sxs-lookup"><span data-stu-id="da755-299">Monitor cached file changes</span></span>

<span data-ttu-id="da755-300">Le contenu des fichiers peut être mis en cache en mémoire avec <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span><span class="sxs-lookup"><span data-stu-id="da755-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="da755-301">La mise en cache en mémoire est décrite dans la rubrique [Mise en cache en mémoire](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="da755-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="da755-302">Sans actions supplémentaires, comme l’implémentation décrite ci-dessous, les données *périmées* (obsolètes) sont retournées depuis un cache si la source de données change.</span><span class="sxs-lookup"><span data-stu-id="da755-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="da755-303">Par exemple, le fait de ne pas prendre en compte l’état d’un fichier source en cache lors du renouvellement d’une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) rend obsolètes les données du fichier mis cache.</span><span class="sxs-lookup"><span data-stu-id="da755-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="da755-304">Chaque demande de données renouvelle la période d’expiration décalée, mais le fichier n’est jamais rechargé dans le cache.</span><span class="sxs-lookup"><span data-stu-id="da755-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="da755-305">Toutes les fonctionnalités d’une application qui utilisent le contenu mis en cache d’un fichier sont exposées au risque de recevoir du contenu obsolète.</span><span class="sxs-lookup"><span data-stu-id="da755-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="da755-306">L’utilisation de jetons de modification dans un scénario de mise en cache de fichier empêche la présence de contenu obsolète dans le cache.</span><span class="sxs-lookup"><span data-stu-id="da755-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="da755-307">L’exemple d’application montre une implémentation de l’approche.</span><span class="sxs-lookup"><span data-stu-id="da755-307">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="da755-308">L’exemple utilise `GetFileContent` pour :</span><span class="sxs-lookup"><span data-stu-id="da755-308">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="da755-309">Retourner le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-309">Return file content.</span></span>
* <span data-ttu-id="da755-310">Implémenter un algorithme de nouvelle tentative avec interruption exponentielle pour couvrir les cas où un verrou de fichier empêche temporairement la lecture d’un fichier.</span><span class="sxs-lookup"><span data-stu-id="da755-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="da755-311">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="da755-311">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="da755-312">Un `FileService` est créé pour gérer les recherches des fichiers mis en cache.</span><span class="sxs-lookup"><span data-stu-id="da755-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="da755-313">L’appel de la méthode `GetFileContent` du service tente d’obtenir le contenu du fichier à partir du cache en mémoire et le retourne à l’appelant (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="da755-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="da755-314">Si le contenu en cache n’est pas trouvé avec la clé du cache, les actions suivantes sont effectuées :</span><span class="sxs-lookup"><span data-stu-id="da755-314">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="da755-315">Le contenu du fichier est obtenu à l’aide de `GetFileContent`.</span><span class="sxs-lookup"><span data-stu-id="da755-315">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="da755-316">Un jeton de modification est obtenu auprès du fournisseur du fichier avec [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span><span class="sxs-lookup"><span data-stu-id="da755-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="da755-317">Le rappel du jeton est déclenché quand le fichier est modifié.</span><span class="sxs-lookup"><span data-stu-id="da755-317">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="da755-318">Le contenu du fichier est mis en cache avec une période [d’expiration décalée](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration).</span><span class="sxs-lookup"><span data-stu-id="da755-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="da755-319">Le jeton de modification est attaché avec [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) pour supprimer l’entrée du cache si le fichier change alors qu’il est mis en cache.</span><span class="sxs-lookup"><span data-stu-id="da755-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="da755-320">Dans l’exemple suivant, les fichiers sont stockés dans la [racine de contenu](xref:fundamentals/index#content-root)de l’application.</span><span class="sxs-lookup"><span data-stu-id="da755-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="da755-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) est utilisé pour obtenir un <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointant vers <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> de l’application.</span><span class="sxs-lookup"><span data-stu-id="da755-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="da755-322">`filePath` est obtenue avec [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span><span class="sxs-lookup"><span data-stu-id="da755-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="da755-323">`FileService` est inscrit dans le conteneur de service, ainsi que le service de mise en cache en mémoire.</span><span class="sxs-lookup"><span data-stu-id="da755-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="da755-324">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="da755-324">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="da755-325">Le modèle de page charge le contenu du fichier en utilisant le service.</span><span class="sxs-lookup"><span data-stu-id="da755-325">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="da755-326">Dans la méthode `OnGet` de la page Index (*Pages/Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="da755-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="da755-327">Classe CompositeChangeToken</span><span class="sxs-lookup"><span data-stu-id="da755-327">CompositeChangeToken class</span></span>

<span data-ttu-id="da755-328">Pour représenter une ou plusieurs instances de `IChangeToken` dans un même objet, utilisez la classe <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="da755-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="da755-329">`HasChanged` sur le jeton composite indique `true` si l’élément `HasChanged` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="da755-329">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="da755-330">`ActiveChangeCallbacks` sur le jeton composite indique `true` si l’élément `ActiveChangeCallbacks` d’un jeton représenté est `true`.</span><span class="sxs-lookup"><span data-stu-id="da755-330">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="da755-331">Si plusieurs modifications simultanées se produisent, le rappel de modification composite n’est appelé qu’une fois.</span><span class="sxs-lookup"><span data-stu-id="da755-331">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="da755-332">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="da755-332">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
