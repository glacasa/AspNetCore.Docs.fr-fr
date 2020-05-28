---
<span data-ttu-id="6c64e-101">titre : Auteur : Description : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="6c64e-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6c64e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6c64e-102">'Blazor'</span></span>
- <span data-ttu-id="6c64e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6c64e-103">'Identity'</span></span>
- <span data-ttu-id="6c64e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6c64e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6c64e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6c64e-105">'Razor'</span></span>
- <span data-ttu-id="6c64e-106">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="6c64e-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="6c64e-107">Résoudre les problèmes et déboguer des projets ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c64e-107">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="6c64e-108">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6c64e-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6c64e-109">Les liens suivants fournissent des conseils de dépannage :</span><span class="sxs-lookup"><span data-stu-id="6c64e-109">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="6c64e-110">Conférence norvégiens (Londres, 2018) : diagnostic des problèmes dans les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c64e-110">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="6c64e-111">Blog ASP.NET : résolution des problèmes de performances de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c64e-111">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="6c64e-112">Avertissements de kit SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c64e-112">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="6c64e-113">Les versions 32 bits et 64 bits de la kit SDK .NET Core sont installées</span><span class="sxs-lookup"><span data-stu-id="6c64e-113">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="6c64e-114">Dans la boîte de dialogue **nouveau projet** pour ASP.net Core, l’avertissement suivant peut s’afficher :</span><span class="sxs-lookup"><span data-stu-id="6c64e-114">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="6c64e-115">Les versions 32 bits et 64 bits de la kit SDK .NET Core sont installées.</span><span class="sxs-lookup"><span data-stu-id="6c64e-115">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="6c64e-116">Seuls les modèles des versions 64 bits installées à l’adresse « C : \\ Program Files \\ dotnet \\ SDK \\ » s’affichent.</span><span class="sxs-lookup"><span data-stu-id="6c64e-116">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="6c64e-117">Cet avertissement s’affiche lorsque les versions 32 bits (x86) et 64 bits (x64) du [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) sont installées.</span><span class="sxs-lookup"><span data-stu-id="6c64e-117">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="6c64e-118">Les raisons courantes pour lesquelles les deux versions peuvent être installées sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="6c64e-118">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="6c64e-119">À l’origine, vous avez téléchargé le programme d’installation de kit SDK .NET Core à l’aide d’un ordinateur 32 bits, puis vous l’avez copié et installé sur un ordinateur 64 bits.</span><span class="sxs-lookup"><span data-stu-id="6c64e-119">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="6c64e-120">Le kit SDK .NET Core 32 bits a été installé par une autre application.</span><span class="sxs-lookup"><span data-stu-id="6c64e-120">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="6c64e-121">La version incorrecte a été téléchargée et installée.</span><span class="sxs-lookup"><span data-stu-id="6c64e-121">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="6c64e-122">Désinstallez le kit SDK .NET Core 32 bits pour éviter cet avertissement.</span><span class="sxs-lookup"><span data-stu-id="6c64e-122">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="6c64e-123">Désinstaller dans **le panneau de configuration**  >  **programmes et fonctionnalités**  >  **désinstaller ou modifier un programme**.</span><span class="sxs-lookup"><span data-stu-id="6c64e-123">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="6c64e-124">Si vous comprenez la raison pour laquelle l’avertissement se produit et ses implications, vous pouvez ignorer l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="6c64e-124">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="6c64e-125">Le kit SDK .NET Core est installé à plusieurs emplacements</span><span class="sxs-lookup"><span data-stu-id="6c64e-125">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="6c64e-126">Dans la boîte de dialogue **nouveau projet** pour ASP.net Core, l’avertissement suivant peut s’afficher :</span><span class="sxs-lookup"><span data-stu-id="6c64e-126">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="6c64e-127">Le kit SDK .NET Core est installé à plusieurs emplacements.</span><span class="sxs-lookup"><span data-stu-id="6c64e-127">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="6c64e-128">Seuls les modèles des kits de développement logiciel (SDK) installés à l’adresse « C : \\ Program Files \\ dotnet \\ SDK \\ » s’affichent.</span><span class="sxs-lookup"><span data-stu-id="6c64e-128">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="6c64e-129">Ce message s’affiche lorsque vous avez au moins une installation du kit SDK .NET Core dans un répertoire en dehors de \*C : \\ Program Files \\ dotnet \\ SDK \\ \*.</span><span class="sxs-lookup"><span data-stu-id="6c64e-129">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="6c64e-130">Cela se produit généralement lorsque le kit SDK .NET Core a été déployé sur un ordinateur à l’aide de la fonction copier/coller au lieu du programme d’installation MSI.</span><span class="sxs-lookup"><span data-stu-id="6c64e-130">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="6c64e-131">Désinstallez tous les kits de développement logiciel (SDK) .NET Core 32 bits et les runtimes pour éviter cet avertissement.</span><span class="sxs-lookup"><span data-stu-id="6c64e-131">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="6c64e-132">Désinstaller dans **le panneau de configuration**  >  **programmes et fonctionnalités**  >  **désinstaller ou modifier un programme**.</span><span class="sxs-lookup"><span data-stu-id="6c64e-132">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="6c64e-133">Si vous comprenez la raison pour laquelle l’avertissement se produit et ses implications, vous pouvez ignorer l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="6c64e-133">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="6c64e-134">Aucun SDK .NET Core n’a été détecté</span><span class="sxs-lookup"><span data-stu-id="6c64e-134">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="6c64e-135">Dans la boîte de dialogue **nouveau projet** de Visual Studio pour ASP.net Core, l’avertissement suivant peut s’afficher :</span><span class="sxs-lookup"><span data-stu-id="6c64e-135">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="6c64e-136">Aucun kit de développement logiciel (SDK) .NET Core n’a été détecté, vérifiez qu’ils sont inclus dans la variable d’environnement `PATH` .</span><span class="sxs-lookup"><span data-stu-id="6c64e-136">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="6c64e-137">Lors de l’exécution d’une `dotnet` commande, l’avertissement s’affiche comme suit :</span><span class="sxs-lookup"><span data-stu-id="6c64e-137">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="6c64e-138">Il n’était pas possible de trouver les kits de développement logiciel (SDK) dotnet installés.</span><span class="sxs-lookup"><span data-stu-id="6c64e-138">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="6c64e-139">Ces avertissements s’affichent lorsque la variable d’environnement `PATH` ne pointe pas vers les kits de développement logiciel (SDK) .net Core sur l’ordinateur.</span><span class="sxs-lookup"><span data-stu-id="6c64e-139">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="6c64e-140">Pour résoudre ce problème :</span><span class="sxs-lookup"><span data-stu-id="6c64e-140">To resolve this problem:</span></span>

* <span data-ttu-id="6c64e-141">Installez le SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6c64e-141">Install the .NET Core SDK.</span></span> <span data-ttu-id="6c64e-142">Obtenez le programme d’installation le plus récent à partir de [téléchargements .net](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="6c64e-142">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="6c64e-143">Vérifiez que la `PATH` variable d’environnement pointe vers l’emplacement où le kit de développement logiciel (SDK) est installé ( `C:\Program Files\dotnet\` pour 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` pour 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="6c64e-143">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="6c64e-144">Le programme d’installation du kit de développement logiciel (SDK) définit normalement `PATH` .</span><span class="sxs-lookup"><span data-stu-id="6c64e-144">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="6c64e-145">Installez toujours les mêmes kits de développement logiciel (SDK) et runtimes sur le même ordinateur.</span><span class="sxs-lookup"><span data-stu-id="6c64e-145">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="6c64e-146">SDK manquant après l’installation du bundle d’hébergement .NET Core</span><span class="sxs-lookup"><span data-stu-id="6c64e-146">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="6c64e-147">L’installation du [bundle d’hébergement .net Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifie le `PATH` quand il installe le Runtime .net Core pour pointer vers la version 32 bits (x86) de .net Core ( `C:\Program Files (x86)\dotnet\` ).</span><span class="sxs-lookup"><span data-stu-id="6c64e-147">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="6c64e-148">Cela peut entraîner des kits de développement logiciel (SDK) manquants lors de l’utilisation de la commande .NET Core 32 bits (x86) `dotnet` ([aucun SDK .net Core n’a été détecté](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="6c64e-148">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="6c64e-149">Pour résoudre ce problème, passez `C:\Program Files\dotnet\` à une position antérieure à `C:\Program Files (x86)\dotnet\` sur `PATH` .</span><span class="sxs-lookup"><span data-stu-id="6c64e-149">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="6c64e-150">Obtenir des données à partir d’une application</span><span class="sxs-lookup"><span data-stu-id="6c64e-150">Obtain data from an app</span></span>

<span data-ttu-id="6c64e-151">Si une application est capable de répondre aux demandes, vous pouvez obtenir les données suivantes à partir de l’application à l’aide de l’intergiciel (middleware) :</span><span class="sxs-lookup"><span data-stu-id="6c64e-151">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="6c64e-152">Requête : méthode, schéma, hôte, pathbase, chemin d’accès, chaîne de requête, en-têtes</span><span class="sxs-lookup"><span data-stu-id="6c64e-152">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="6c64e-153">Connexion : adresse IP distante, port distant, adresse IP locale, port local, certificat client</span><span class="sxs-lookup"><span data-stu-id="6c64e-153">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="6c64e-154">Paramètres de configuration</span><span class="sxs-lookup"><span data-stu-id="6c64e-154">Configuration settings</span></span>
* <span data-ttu-id="6c64e-155">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="6c64e-155">Environment variables</span></span>

<span data-ttu-id="6c64e-156">Placez le code [intergiciel](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) suivant au début du `Startup.Configure` pipeline de traitement des demandes de la méthode.</span><span class="sxs-lookup"><span data-stu-id="6c64e-156">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="6c64e-157">L’environnement est vérifié avant l’exécution de l’intergiciel pour s’assurer que le code est exécuté uniquement dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="6c64e-157">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="6c64e-158">Pour obtenir l’environnement, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="6c64e-158">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="6c64e-159">Injectez `IHostingEnvironment` dans la `Startup.Configure` méthode et vérifiez l’environnement avec la variable locale.</span><span class="sxs-lookup"><span data-stu-id="6c64e-159">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="6c64e-160">L’exemple de code suivant illustre cette approche.</span><span class="sxs-lookup"><span data-stu-id="6c64e-160">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="6c64e-161">Assignez l’environnement à une propriété dans la `Startup` classe.</span><span class="sxs-lookup"><span data-stu-id="6c64e-161">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="6c64e-162">Vérifiez l’environnement à l’aide de la propriété (par exemple, `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="6c64e-162">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="6c64e-163">Déboguer les applications ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c64e-163">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="6c64e-164">Les liens suivants fournissent des informations sur le débogage des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6c64e-164">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="6c64e-165">Débogage d’ASP Core sur Linux</span><span class="sxs-lookup"><span data-stu-id="6c64e-165">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="6c64e-166">Débogage de .NET Core sur UNIX sur SSH</span><span class="sxs-lookup"><span data-stu-id="6c64e-166">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="6c64e-167">Démarrage rapide : Déboguer du code ASP.NET avec le débogueur Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6c64e-167">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="6c64e-168">Pour plus d’informations sur le débogage, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) .</span><span class="sxs-lookup"><span data-stu-id="6c64e-168">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
