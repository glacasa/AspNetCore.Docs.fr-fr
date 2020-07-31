---
title: Configurer l’authentification Windows dans ASP.NET Core
author: scottaddie
description: Découvrez comment configurer l’authentification Windows dans ASP.NET Core pour IIS et HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330680"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Configurer l’authentification Windows dans ASP.NET Core

Par [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

L’authentification Windows (également appelée négociation, Kerberos ou authentification NTLM) peut être configurée pour ASP.NET Core les applications hébergées avec [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)ou [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

L’authentification Windows (également appelée négociation, Kerberos ou authentification NTLM) peut être configurée pour les applications ASP.NET Core hébergées avec [IIS](xref:host-and-deploy/iis/index) ou [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

L’authentification Windows s’appuie sur le système d’exploitation pour authentifier les utilisateurs des applications ASP.NET Core. Vous pouvez utiliser l’authentification Windows lorsque votre serveur s’exécute sur un réseau d’entreprise à l’aide de Active Directory identités de domaine ou des comptes Windows pour identifier les utilisateurs. L’authentification Windows est mieux adaptée aux environnements intranet où les utilisateurs, les applications clientes et les serveurs Web appartiennent au même domaine Windows.

> [!NOTE]
> L’authentification Windows n’est pas prise en charge avec HTTP/2. Les défis liés à l’authentification peuvent être envoyés sur les réponses HTTP/2, mais le client doit passer à la version HTTP/1.1 avant l’authentification.

## <a name="proxy-and-load-balancer-scenarios"></a>Scénarios de proxy et d’équilibrage de charge

L’authentification Windows est un scénario avec état principalement utilisé dans un intranet, où un proxy ou un équilibreur de charge ne gère généralement pas le trafic entre les clients et les serveurs. Si un proxy ou un équilibreur de charge est utilisé, l’authentification Windows fonctionne uniquement si le proxy ou l’équilibreur de charge :

* Gère l’authentification.
* Transmet les informations d’authentification de l’utilisateur à l’application (par exemple, dans un en-tête de demande), qui agit sur les informations d’authentification.

Une alternative à l’authentification Windows dans les environnements où les proxies et les équilibreurs de charge sont utilisés est Active Directory Services fédérés (ADFS) avec OpenID Connect (OIDC).

## <a name="iisiis-express"></a>IIS/IIS Express

Ajoutez des services d’authentification en appelant <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> espace de noms) dans `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Paramètres de lancement (débogueur)

La configuration des paramètres de lancement affecte uniquement les *Propriétés/launchSettings.js* du fichier pour IIS Express et ne configure pas IIS pour l’authentification Windows. La configuration du serveur est expliquée dans la section [IIS](#iis) .

Le modèle d' **application Web** disponible via Visual Studio ou le CLI .net Core peut être configuré pour prendre en charge l’authentification Windows, ce qui met automatiquement à jour les *Propriétés/launchSettings.jssur* le fichier.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Nouveau projet**

1. Créez un projet.
1. Sélectionnez **Application web ASP.NET Core**. Sélectionnez **Suivant**.
1. Indiquez un nom dans le champ **nom du projet** . Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Créer**.
1. Sélectionnez **modifier** sous **authentification**.
1. Dans la fenêtre **modifier l’authentification** , sélectionnez **authentification Windows**. Sélectionnez **OK**.
1. Sélectionnez **application Web**.
1. Sélectionnez **Créer**.

Exécutez l'application. Le nom d’utilisateur s’affiche dans l’interface utilisateur de l’application rendue.

**Projet existant**

Les propriétés du projet activent l’authentification Windows et désactivent l’authentification anonyme :

1. Dans **Explorateur de solutions** , cliquez avec le bouton droit sur le projet, puis sélectionnez **Propriétés**.
1. Sélectionnez l’onglet **Débogage**.
1. Désactivez la case à cocher **activer l’authentification anonyme**.
1. Activez la case à cocher **activer l’authentification Windows**.
1. Enregistrez et fermez la page de propriétés.

Les propriétés peuvent également être configurées dans le `iisSettings` nœud de l' *launchSettings.jssur* le fichier :

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

**Nouveau projet**

Exécutez la commande [dotnet New](/dotnet/core/tools/dotnet-new) avec l' `webapp` argument (ASP.net Core application Web) et le `--auth Windows` commutateur :

```dotnetcli
dotnet new webapp --auth Windows
```

**Projet existant**

Mettez à jour le `iisSettings` nœud de l' *launchSettings.jssur* le fichier :

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Lors de la modification d’un projet existant, vérifiez que le fichier projet contient une référence de package pour le package [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) **ou** le package NuGet [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .

### <a name="iis"></a>IIS

IIS utilise le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) pour héberger des applications ASP.net core. L’authentification Windows est configurée pour IIS par le biais du fichier *web.config* . Les sections suivantes décrivent diverses opérations :

* Fournissez un fichier de *web.config* local qui active l’authentification Windows sur le serveur lorsque l’application est déployée.
* Utilisez le gestionnaire des services Internet pour configurer le fichier *web.config* d’une application ASP.net core qui a déjà été déployée sur le serveur.

Si vous ne l’avez pas déjà fait, activez IIS pour héberger des applications ASP.NET Core. Pour plus d’informations, consultez <xref:host-and-deploy/iis/index>.

Activez le service de rôle IIS pour l’authentification Windows. Pour plus d’informations, consultez [activer l’authentification Windows dans les services de rôle IIS (Voir l’étape 2)](xref:host-and-deploy/iis/index#iis-configuration).

L' [intergiciel (middleware) d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) est configuré pour authentifier automatiquement les demandes par défaut. Pour plus d’informations, voir [Host ASP.net Core on Windows with IIS : IIS options (AutomaticAuthentication) (](xref:host-and-deploy/iis/index#iis-options)en anglais).

Le module ASP.NET Core est configuré pour transférer le jeton d’authentification Windows à l’application par défaut. Pour plus d’informations, consultez [ASP.net Core référence de configuration du module : attributs de l’élément aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Utilisez l' **une** des approches suivantes :

* **Avant de publier et de déployer le projet,** ajoutez le fichier *web.config* suivant à la racine du projet :

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Lorsque le projet est publié par le kit SDK .NET Core (sans la `<IsTransformWebConfigDisabled>` propriété définie à `true` dans le fichier projet), le fichier *web.config* publié comprend la `<location><system.webServer><security><authentication>` section. Pour plus d’informations sur la `<IsTransformWebConfigDisabled>` propriété, consultez <xref:host-and-deploy/iis/index#webconfig-file> .

* **Après la publication et le déploiement du projet, effectuez une** configuration côté serveur à l’aide du gestionnaire des services Internet :

  1. Dans le gestionnaire des services Internet, sélectionnez le site IIS sous le nœud **sites** de la barre latérale **connexions** .
  1. Double-cliquez sur **authentification** dans la zone **IIS** .
  1. Sélectionnez **authentification anonyme**. Sélectionnez **Désactiver** dans la barre latérale **actions** .
  1. Sélectionnez **Authentification Windows**. Sélectionnez **activer** dans la barre latérale **actions** .

  Lorsque ces actions sont effectuées, le gestionnaire des services Internet modifie le fichier *web.config* de l’application. Un `<system.webServer><security><authentication>` nœud est ajouté avec les paramètres mis à jour pour `anonymousAuthentication` et `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  La `<system.webServer>` section ajoutée au fichier *web.config* par le gestionnaire des services Internet est en dehors de la section de l’application `<location>` ajoutée par le kit SDK .net Core lors de la publication de l’application. Étant donné que la section est ajoutée en dehors du `<location>` nœud, les paramètres sont hérités par toutes les [sous-applications](xref:host-and-deploy/iis/index#sub-applications) de l’application actuelle. Pour empêcher l’héritage, déplacez la `<security>` section ajoutée à l’intérieur de la `<location><system.webServer>` section que le kit SDK .net Core fourni.

  Quand le gestionnaire des services Internet est utilisé pour ajouter la configuration IIS, il n’affecte que le fichier *web.config* de l’application sur le serveur. Un déploiement ultérieur de l’application peut remplacer les paramètres sur le serveur si la copie du serveur de *web.config* est remplacée par le fichier *web.config* du projet. Utilisez l' **une** des approches suivantes pour gérer les paramètres :

  * Utilisez le gestionnaire des services Internet pour réinitialiser les paramètres dans le fichier *web.config* une fois que le fichier a été remplacé lors du déploiement.
  * Ajoutez un *fichierweb.config* à l’application localement avec les paramètres.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Le package NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) peut être utilisé avec [Kestrel](xref:fundamentals/servers/kestrel) pour prendre en charge l’authentification Windows à l’aide de Negotiate et Kerberos sur Windows, Linux et MacOS.

> [!WARNING]
> Les informations d’identification peuvent être rendues persistantes entre les demandes sur une connexion. *L’authentification par négociation ne doit pas être utilisée avec les proxies, sauf si le proxy gère une affinité de connexion 1:1 (une connexion permanente) avec Kestrel.*

> [!NOTE]
> Le gestionnaire Negotiate détecte si le serveur sous-jacent prend en charge l’authentification Windows en mode natif et s’il est activé. Si le serveur prend en charge l’authentification Windows, mais qu’elle est désactivée, une erreur est générée pour vous demander d’activer l’implémentation du serveur. Lorsque l’authentification Windows est activée sur le serveur, le gestionnaire Negotiate le transmet en toute transparence.

Ajoutez des services d’authentification en appelant <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> et <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> dans `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Ajoutez l’intergiciel (middleware) d’authentification en appelant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> dans `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Pour plus d’informations sur les intergiciels (middleware), consultez <xref:fundamentals/middleware/index> .

Les demandes anonymes sont autorisées. Utilisez [ASP.net Core autorisation](xref:security/authorization/introduction) pour défier les demandes anonymes pour l’authentification.

### <a name="windows-environment-configuration"></a>Configuration de l’environnement Windows

Le composant [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) effectue l’authentification en mode utilisateur. Les noms de principal du service (SPN) doivent être ajoutés au compte d’utilisateur qui exécute le service, et non au compte d’ordinateur. Exécutez `setspn -S HTTP/myservername.mydomain.com myuser` dans un interpréteur de commandes d’administration.

### <a name="linux-and-macos-environment-configuration"></a>Configuration de l’environnement Linux et macOS

Les instructions pour joindre un ordinateur Linux ou macOS à un domaine Windows sont disponibles dans l’article [se connecter Azure Data Studio à votre SQL Server à l’aide de l’authentification Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) . Les instructions créent un compte d’ordinateur pour la machine Linux sur le domaine. Les noms de principal du service doivent être ajoutés à ce compte d’ordinateur.

> [!NOTE]
> Lorsque vous suivez les instructions de l’article [se connecter Azure Data Studio à votre SQL Server à l’aide de l’authentification Windows-Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , remplacez `python-software-properties` par `python3-software-properties` si nécessaire.

Une fois que l’ordinateur Linux ou macOS est joint au domaine, des étapes supplémentaires sont nécessaires pour fournir un [fichier keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) avec les noms de principal du service (SPN) :

* Sur le contrôleur de domaine, ajoutez de nouveaux noms de principal du service Web au compte d’ordinateur :
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Utilisez [Ktpass](/windows-server/administration/windows-commands/ktpass) pour générer un fichier keytab :
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Certains champs doivent être spécifiés en majuscules, comme indiqué.
* Copiez le fichier keytab sur l’ordinateur Linux ou macOS.
* Sélectionnez le fichier keytab à l’aide d’une variable d’environnement :`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Appelez `klist` pour afficher les noms de principal du service actuellement disponibles.

> [!NOTE]
> Un fichier keytab contient les informations d’identification d’accès au domaine et doit être protégé en conséquence.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) prend en charge l’authentification Windows en mode noyau en utilisant Negotiate, NTLM ou l’authentification de base.

Ajoutez des services d’authentification en appelant <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> espace de noms) dans `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Configurez l’hôte Web de l’application pour utiliser HTTP.sys avec l’authentification Windows (*Program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>se trouve dans l' <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> espace de noms.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys délègue l’authentification en mode noyau avec le protocole d’authentification Kerberos. L’authentification en mode utilisateur n’est pas prise en charge avec Kerberos et HTTP.sys. Le compte d’ordinateur doit être utilisé pour déchiffrer le ticket/jeton Kerberos obtenu à partir d’Active Directory et transféré par le client au serveur afin d’authentifier l’utilisateur. Inscrivez le nom de principal du service (SPN) pour l’hôte, et non l’utilisateur de l’application.

> [!NOTE]
> HTTP.sys n’est pas pris en charge sur nano Server version 1709 ou ultérieure. Pour utiliser l’authentification Windows et HTTP.sys avec Nano Server, utilisez un [conteneur Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Pour plus d’informations sur Server Core, voir [qu’est-ce que l’option d’installation Server Core dans Windows Server ?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autoriser les utilisateurs

L’état de configuration de l’accès anonyme détermine la façon dont `[Authorize]` les `[AllowAnonymous]` attributs et sont utilisés dans l’application. Les deux sections suivantes expliquent comment gérer les États de configuration non autorisés et autorisés de l’accès anonyme.

### <a name="disallow-anonymous-access"></a>Interdire l’accès anonyme

Lorsque l’authentification Windows est activée et que l’accès anonyme est désactivé, les `[Authorize]` attributs et n' `[AllowAnonymous]` ont aucun effet. Si un site IIS est configuré pour interdire l’accès anonyme, la demande n’atteint jamais l’application. Pour cette raison, l' `[AllowAnonymous]` attribut n’est pas applicable.

### <a name="allow-anonymous-access"></a>Autoriser l’accès anonyme

Lorsque l’authentification Windows et l’accès anonyme sont activés, utilisez les `[Authorize]` `[AllowAnonymous]` attributs et. L' `[Authorize]` attribut vous permet de sécuriser les points de terminaison de l’application qui requièrent une authentification. L' `[AllowAnonymous]` attribut remplace l' `[Authorize]` attribut dans les applications qui autorisent l’accès anonyme. Pour plus d’informations sur l’utilisation des attributs, consultez <xref:security/authorization/simple> .

> [!NOTE]
> Par défaut, les utilisateurs qui n’ont pas l’autorisation d’accéder à une page s’affichent avec une réponse HTTP 403 vide. L' [intergiciel StatusCodePages](xref:fundamentals/error-handling#usestatuscodepages) peut être configuré pour fournir aux utilisateurs une expérience de « accès refusé » améliorée.

## <a name="impersonation"></a>Emprunt d'identité

ASP.NET Core n’implémente pas l’emprunt d’identité. Les applications s’exécutent avec l’identité de l’application pour toutes les demandes, à l’aide du pool d’applications ou de l’identité du processus. Si l’application doit effectuer une action pour le compte d’un utilisateur, utilisez [WindowsIdentity. RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) dans un intergiciel (middleware) en [ligne de terminal](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) dans `Startup.Configure` . Exécutez une action unique dans ce contexte, puis fermez le contexte.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`ne prend pas en charge les opérations asynchrones et ne doit pas être utilisé pour les scénarios complexes. Par exemple, l’encapsulage de demandes entières ou de chaînes middleware n’est pas pris en charge ou recommandé.

::: moniker range=">= aspnetcore-3.0"

Tandis que le package [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) permet l’authentification sur Windows, Linux et MacOS, l’emprunt d’identité est pris en charge uniquement sur Windows.

::: moniker-end

## <a name="claims-transformations"></a>Transformations de revendication

::: moniker range=">= aspnetcore-3.0"

Lors de l’hébergement avec IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur. Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut. Pour plus d’informations et pour obtenir un exemple de code qui active les transformations de revendications, consultez <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Lors de l’hébergement avec IIS en mode in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur. Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut. Pour plus d’informations et pour obtenir un exemple de code qui active les transformations de revendications lors de l’hébergement in-process, consultez <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
