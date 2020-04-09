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
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Dépannage et débogé de ASP.NET projets de base

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Les liens suivants fournissent des conseils de dépannage :

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Conférence NDC (Londres, 2018): Diagnostiquer les questions dans ASP.NET applications de base](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET blog: Dépannage ASP.NET problèmes de performance de base](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK avertissements

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Les versions 32-bit et 64 bits du .NET Core SDK sont installées

Dans le dialogue du **nouveau projet** pour ASP.NET Core, vous pouvez voir l’avertissement suivant :

> Les versions 32 et 64 bits du .NET Core SDK sont installées. Seuls les modèles des versions 64 bits\\installées à 'C: Program Files\\dotnet\\sdk'\\sont affichés.

Cet avertissement apparaît lorsque les versions 32 bits (x86) et 64 bits (x64) du [SDK de base .NET](https://dotnet.microsoft.com/download/dotnet-core) sont installées. Les raisons courantes pour lesquelles les deux versions peuvent être installées sont les suivantes :

* Vous avez initialement téléchargé l’installateur SDK .NET Core à l’aide d’une machine 32 bits, puis l’avez copié à travers et l’a installé sur une machine 64 bits.
* Le 32 bits .NET Core SDK a été installé par une autre application.
* La mauvaise version a été téléchargée et installée.

Désinstaller le 32 bits .NET Core SDK pour empêcher cet avertissement. Désinstaller des**programmes et des fonctionnalités** >  **de panneau** > de contrôle**Désinstaller ou modifier un programme**. Si vous comprenez pourquoi l’avertissement se produit et ses implications, vous pouvez ignorer l’avertissement.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Le SDK core .NET est installé à plusieurs endroits

Dans le dialogue du **nouveau projet** pour ASP.NET Core, vous pouvez voir l’avertissement suivant :

> Le SDK core .NET est installé à plusieurs endroits. Seuls les modèles des SDK installés\\à\\'C: Program Files dotnet\\sdk'\\sont affichés.

Vous voyez ce message lorsque vous avez au moins une installation de la .NET Core SDK dans un répertoire en dehors de *C:\\Program Files\\dotnet\\sdk\\*. Habituellement, cela se produit lorsque le .NET Core SDK a été déployé sur une machine à l’aide de copie / pâte au lieu de l’installateur MSI.

Désinstaller tous les SDKs de base de 32 bits .NET et les temps d’exécution pour empêcher cet avertissement. Désinstaller des**programmes et des fonctionnalités** >  **de panneau** > de contrôle**Désinstaller ou modifier un programme**. Si vous comprenez pourquoi l’avertissement se produit et ses implications, vous pouvez ignorer l’avertissement.

### <a name="no-net-core-sdks-were-detected"></a>Aucun SDK de base .NET n’a été détecté

* Dans le dialogue Visual Studio **New Project** pour ASP.NET Core, vous pouvez voir l’avertissement suivant:

  > Aucun SDK de base .NET n’a été détecté, s’assurer qu’ils sont inclus dans la variable `PATH`de l’environnement .

* Lors de `dotnet` l’exécution d’une commande, l’avertissement apparaît comme:

  > Il n’a pas été possible de trouver des SDK dotnet installés.

Ces avertissements apparaissent `PATH` lorsque la variable de l’environnement ne pointe pas vers des SDKs de base .NET sur la machine. Pour résoudre ce problème :

* Installez le SDK .NET Core. Obtenir le dernier installateur de [.NET Downloads](https://dotnet.microsoft.com/download).
* Vérifiez que `PATH` la variable de l’environnement indique l’emplacement où le SDK est installé (pour`C:\Program Files\dotnet\` 64 bits/x64 ou `C:\Program Files (x86)\dotnet\` pour 32 bits/x86). L’installateur SDK `PATH`définit normalement le . Installez toujours les mêmes bitis et les temps d’exécution sur la même machine.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>SDK manquant après l’installation du pack d’hébergement de base .NET

L’installation du [groupe d’hébergement de base .NET](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifie le `PATH` moment où il installe le temps d’exécution .NET Core pour pointer vers la version 32 bits (x86) de .NET Core (`C:\Program Files (x86)\dotnet\`). Cela peut entraîner des SDK manquants lorsque la commande 32 `dotnet` bits (x86) .NET Core est utilisée ([Aucun .NET Core SDKs ont été détectés](#no-net-core-sdks-were-detected)). Pour résoudre ce `C:\Program Files\dotnet\` problème, passer `C:\Program Files (x86)\dotnet\` à `PATH`une position avant sur le .

## <a name="obtain-data-from-an-app"></a>Obtenir des données à partir d’une application

Si une application est capable de répondre aux demandes, vous pouvez obtenir les données suivantes à partir de l’application à l’aide de middleware :

* Méthode &ndash; de demande, schéma, hôte, base de chemin, chemin, chaîne de requête, en-têtes
* Adresse &ndash; IP à distance de connexion, port à distance, adresse IP locale, port local, certificat client
* Nom &ndash; d’identité, nom d’affichage
* Paramètres de configuration
* Variables d'environnement

Placez le code [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) suivant `Startup.Configure` au début du pipeline de traitement des demandes de la méthode. L’environnement est vérifié avant que le middleware ne soit exécuté pour s’assurer que le code n’est exécuté que dans l’environnement de développement.

Pour obtenir l’environnement, utilisez l’une ou l’autre des approches suivantes :

* Injecter `IHostingEnvironment` le `Startup.Configure` dans la méthode et vérifier l’environnement avec la variable locale. Le code d’échantillon suivant démontre cette approche.

* Attribuer l’environnement à `Startup` une propriété de la classe. Vérifiez l’environnement à l’aide de la propriété (par exemple, `if (Environment.IsDevelopment())`).

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

## <a name="debug-aspnet-core-apps"></a>Debug ASP.NET applications Core

Les liens suivants fournissent des informations sur le débogage ASP.NET les applications Core.

* [Débogage ASP Core sur Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Debugging .NET Core sur Unix sur SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Démarrage rapide : Déboguer du code ASP.NET avec le débogueur Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Voir [ce numéro GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) pour plus d’informations de débogage.
