---
title: Dépannage et débogé de ASP.NET projets de base
author: Rick-Anderson
description: Comprenez et résolvez les avertissements et les erreurs avec les projets ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511507"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="4614c-103">Dépannage et débogé de ASP.NET projets de base</span><span class="sxs-lookup"><span data-stu-id="4614c-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="4614c-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4614c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4614c-105">Les liens suivants fournissent des conseils de dépannage :</span><span class="sxs-lookup"><span data-stu-id="4614c-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="4614c-106">Conférence NDC (Londres, 2018): Diagnostiquer les questions dans ASP.NET applications de base</span><span class="sxs-lookup"><span data-stu-id="4614c-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="4614c-107">ASP.NET blog: Dépannage ASP.NET problèmes de performance de base</span><span class="sxs-lookup"><span data-stu-id="4614c-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="4614c-108">.NET Core SDK avertissements</span><span class="sxs-lookup"><span data-stu-id="4614c-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="4614c-109">Les versions 32-bit et 64 bits du .NET Core SDK sont installées</span><span class="sxs-lookup"><span data-stu-id="4614c-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="4614c-110">Dans le dialogue du **nouveau projet** pour ASP.NET Core, vous pouvez voir l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="4614c-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="4614c-111">Les versions 32 et 64 bits du .NET Core SDK sont installées.</span><span class="sxs-lookup"><span data-stu-id="4614c-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="4614c-112">Seuls les modèles des versions 64 bits\\installées à 'C: Program Files\\dotnet\\sdk'\\sont affichés.</span><span class="sxs-lookup"><span data-stu-id="4614c-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="4614c-113">Cet avertissement apparaît lorsque les versions 32 bits (x86) et 64 bits (x64) du [SDK de base .NET](https://dotnet.microsoft.com/download/dotnet-core) sont installées.</span><span class="sxs-lookup"><span data-stu-id="4614c-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="4614c-114">Les raisons courantes pour lesquelles les deux versions peuvent être installées sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="4614c-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="4614c-115">Vous avez initialement téléchargé l’installateur SDK .NET Core à l’aide d’une machine 32 bits, puis l’avez copié à travers et l’a installé sur une machine 64 bits.</span><span class="sxs-lookup"><span data-stu-id="4614c-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="4614c-116">Le 32 bits .NET Core SDK a été installé par une autre application.</span><span class="sxs-lookup"><span data-stu-id="4614c-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="4614c-117">La mauvaise version a été téléchargée et installée.</span><span class="sxs-lookup"><span data-stu-id="4614c-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="4614c-118">Désinstaller le 32 bits .NET Core SDK pour empêcher cet avertissement.</span><span class="sxs-lookup"><span data-stu-id="4614c-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="4614c-119">Désinstaller des**programmes et des fonctionnalités** >  **de panneau** > de contrôle**Désinstaller ou modifier un programme**.</span><span class="sxs-lookup"><span data-stu-id="4614c-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="4614c-120">Si vous comprenez pourquoi l’avertissement se produit et ses implications, vous pouvez ignorer l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="4614c-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="4614c-121">Le SDK core .NET est installé à plusieurs endroits</span><span class="sxs-lookup"><span data-stu-id="4614c-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="4614c-122">Dans le dialogue du **nouveau projet** pour ASP.NET Core, vous pouvez voir l’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="4614c-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="4614c-123">Le SDK core .NET est installé à plusieurs endroits.</span><span class="sxs-lookup"><span data-stu-id="4614c-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="4614c-124">Seuls les modèles des SDK installés\\à\\'C: Program Files dotnet\\sdk'\\sont affichés.</span><span class="sxs-lookup"><span data-stu-id="4614c-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="4614c-125">Vous voyez ce message lorsque vous avez au moins une installation de la .NET Core SDK dans un répertoire en dehors de *C:\\Program Files\\dotnet\\sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="4614c-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="4614c-126">Habituellement, cela se produit lorsque le .NET Core SDK a été déployé sur une machine à l’aide de copie / pâte au lieu de l’installateur MSI.</span><span class="sxs-lookup"><span data-stu-id="4614c-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="4614c-127">Désinstaller tous les SDKs de base de 32 bits .NET et les temps d’exécution pour empêcher cet avertissement.</span><span class="sxs-lookup"><span data-stu-id="4614c-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="4614c-128">Désinstaller des**programmes et des fonctionnalités** >  **de panneau** > de contrôle**Désinstaller ou modifier un programme**.</span><span class="sxs-lookup"><span data-stu-id="4614c-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="4614c-129">Si vous comprenez pourquoi l’avertissement se produit et ses implications, vous pouvez ignorer l’avertissement.</span><span class="sxs-lookup"><span data-stu-id="4614c-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="4614c-130">Aucun SDK de base .NET n’a été détecté</span><span class="sxs-lookup"><span data-stu-id="4614c-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="4614c-131">Dans le dialogue Visual Studio **New Project** pour ASP.NET Core, vous pouvez voir l’avertissement suivant:</span><span class="sxs-lookup"><span data-stu-id="4614c-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="4614c-132">Aucun SDK de base .NET n’a été détecté, s’assurer qu’ils sont inclus dans la variable `PATH`de l’environnement .</span><span class="sxs-lookup"><span data-stu-id="4614c-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="4614c-133">Lors de `dotnet` l’exécution d’une commande, l’avertissement apparaît comme:</span><span class="sxs-lookup"><span data-stu-id="4614c-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="4614c-134">Il n’a pas été possible de trouver des SDK dotnet installés.</span><span class="sxs-lookup"><span data-stu-id="4614c-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="4614c-135">Ces avertissements apparaissent `PATH` lorsque la variable de l’environnement ne pointe pas vers des SDKs de base .NET sur la machine.</span><span class="sxs-lookup"><span data-stu-id="4614c-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="4614c-136">Pour résoudre ce problème :</span><span class="sxs-lookup"><span data-stu-id="4614c-136">To resolve this problem:</span></span>

* <span data-ttu-id="4614c-137">Installez le SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4614c-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="4614c-138">Obtenir le dernier installateur de [.NET Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="4614c-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="4614c-139">Vérifiez que `PATH` la variable de l’environnement indique l’emplacement où le SDK est installé (pour`C:\Program Files\dotnet\` 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` pour 32 bits/x86).</span><span class="sxs-lookup"><span data-stu-id="4614c-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="4614c-140">L’installateur SDK `PATH`définit normalement le .</span><span class="sxs-lookup"><span data-stu-id="4614c-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="4614c-141">Installez toujours les mêmes bitis et les temps d’exécution sur la même machine.</span><span class="sxs-lookup"><span data-stu-id="4614c-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="4614c-142">SDK manquant après l’installation du pack d’hébergement de base .NET</span><span class="sxs-lookup"><span data-stu-id="4614c-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="4614c-143">L’installation du [groupe d’hébergement de base .NET](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifie le `PATH` moment où il installe le temps d’exécution .NET Core pour pointer vers la version 32 bits (x86) de .NET Core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="4614c-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="4614c-144">Cela peut entraîner des SDK manquants lorsque la commande 32 `dotnet` bits (x86) .NET Core est utilisée ([Aucun .NET Core SDKs ont été détectés](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="4614c-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="4614c-145">Pour résoudre ce `C:\Program Files\dotnet\` problème, passer `C:\Program Files (x86)\dotnet\` à `PATH`une position avant sur le .</span><span class="sxs-lookup"><span data-stu-id="4614c-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="4614c-146">Obtenir des données à partir d’une application</span><span class="sxs-lookup"><span data-stu-id="4614c-146">Obtain data from an app</span></span>

<span data-ttu-id="4614c-147">Si une application est capable de répondre aux demandes, vous pouvez obtenir les données suivantes à partir de l’application à l’aide de middleware :</span><span class="sxs-lookup"><span data-stu-id="4614c-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="4614c-148">Méthode &ndash; de demande, schéma, hôte, base de chemin, chemin, chaîne de requête, en-têtes</span><span class="sxs-lookup"><span data-stu-id="4614c-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="4614c-149">Adresse &ndash; IP à distance de connexion, port à distance, adresse IP locale, port local, certificat client</span><span class="sxs-lookup"><span data-stu-id="4614c-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="4614c-150">Nom &ndash; d’identité, nom d’affichage</span><span class="sxs-lookup"><span data-stu-id="4614c-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="4614c-151">Paramètres de configuration</span><span class="sxs-lookup"><span data-stu-id="4614c-151">Configuration settings</span></span>
* <span data-ttu-id="4614c-152">Variables d'environnement</span><span class="sxs-lookup"><span data-stu-id="4614c-152">Environment variables</span></span>

<span data-ttu-id="4614c-153">Placez le code [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) suivant `Startup.Configure` au début du pipeline de traitement des demandes de la méthode.</span><span class="sxs-lookup"><span data-stu-id="4614c-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="4614c-154">L’environnement est vérifié avant que le middleware ne soit exécuté pour s’assurer que le code n’est exécuté que dans l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="4614c-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="4614c-155">Pour obtenir l’environnement, utilisez l’une ou l’autre des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="4614c-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="4614c-156">Injecter `IHostingEnvironment` le `Startup.Configure` dans la méthode et vérifier l’environnement avec la variable locale.</span><span class="sxs-lookup"><span data-stu-id="4614c-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="4614c-157">Le code d’échantillon suivant démontre cette approche.</span><span class="sxs-lookup"><span data-stu-id="4614c-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="4614c-158">Attribuer l’environnement à `Startup` une propriété de la classe.</span><span class="sxs-lookup"><span data-stu-id="4614c-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="4614c-159">Vérifiez l’environnement à l’aide de la propriété (par exemple, `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="4614c-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="4614c-160">Debug ASP.NET applications Core</span><span class="sxs-lookup"><span data-stu-id="4614c-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="4614c-161">Les liens suivants fournissent des informations sur le débogage ASP.NET les applications Core.</span><span class="sxs-lookup"><span data-stu-id="4614c-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="4614c-162">Débogage ASP Core sur Linux</span><span class="sxs-lookup"><span data-stu-id="4614c-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="4614c-163">Debugging .NET Core sur Unix sur SSH</span><span class="sxs-lookup"><span data-stu-id="4614c-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="4614c-164">Démarrage rapide : Déboguer du code ASP.NET avec le débogueur Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4614c-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="4614c-165">Voir [ce numéro GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) pour plus d’informations de débogage.</span><span class="sxs-lookup"><span data-stu-id="4614c-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
