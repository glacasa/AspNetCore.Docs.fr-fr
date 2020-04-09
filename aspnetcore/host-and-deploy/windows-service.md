---
title: Héberger ASP.NET Core dans un service Windows
author: rick-anderson
description: Découvrez comment héberger une application ASP.NET Core dans un service Windows.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417575"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Héberger ASP.NET Core dans un service Windows

::: moniker range=">= aspnetcore-3.0"

Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS. Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus](https://dotnet.microsoft.com/download)
* [PowerShell version 6.2 ou ultérieure](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Modèle Service Worker

Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables. Pour utiliser le modèle en tant que base d’une application de service Windows :

1. Créez une application Service Worker à partir du modèle .NET Core.
1. Suivez l’aide fournie dans la section [Configuration d’application](#app-configuration) pour mettre à jour l’application Service Worker afin qu’elle puisse s’exécuter en tant que service Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>la configuration d’une application ;

L’application nécessite une référence de paquet pour [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService`est appelé lors de la construction de l’hôte. Si l’application s’exécute comme un service Windows, la méthode :

* définit la durée de vie de l’hôte sur `WindowsServiceLifetime` ;
* Définit la racine du [contenu](xref:fundamentals/index#content-root) à [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory). Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).
* Permet de vous connecter au journal de l’événement :
  * Le nom de l’application est utilisé comme nom source par défaut.
  * Le niveau de journal par défaut est *Avertissement* ou plus pour `CreateDefaultBuilder` une application basée sur un modèle ASP.NET Core qui appelle à construire l’hôte.
  * Remplacer le niveau de `Logging:EventLog:LogLevel:Default` journal par défaut avec la clé dans *appsettings.json*/*appsettings. Environnement-json* ou tout autre fournisseur de configuration.
  * Seuls les administrateurs peuvent créer des sources d’événement. Si une source d’événement ne peut pas être créée en utilisant le nom de l’application, un avertissement est consigné dans la source *Application* source et les journaux d’événements sont désactivés.

Dans `CreateHostBuilder` de *Program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Les exemples d’applications suivants accompagnent ce sujet :

* Exemple &ndash; de service des travailleurs de fond Un échantillon d’application non web basé sur le [modèle de service aux travailleurs](#worker-service-template) qui utilise des services [hébergés](xref:fundamentals/host/hosted-services) pour des tâches d’arrière-plan.
* Web App &ndash; Service Sample A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.

Pour les conseils MVC, <xref:mvc/overview> <xref:migration/22-to-30>voir les articles sous et .

## <a name="deployment-type"></a>Type de déploiement

Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Kit SDK

Pour un service web basé sur une application qui utilise les pages Razor ou les cadres MVC, spécifiez le SDK Web dans le fichier du projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le service n’exécute que des tâches d’arrière-plan (par exemple, [les services hébergés),](xref:fundamentals/host/hosted-services)spécifiez le Travailleur SDK dans le fichier du projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Déploiement dépendant du framework

Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible. Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.

Si l’utilisation du [Web SDK](#sdk), un fichier *web.config,* qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application Windows Services. Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Déploiement autonome

Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte. Le runtime et les dépendances de l’application sont déployés avec l’application.

Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Pour publier pour plusieurs RID :

* Fournissez les RID dans une liste séparée par des points-virgules.
* Utilisez le [ \<nom de propriété RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).

Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Compte d’utilisateur du service

Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.

Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.

Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.

Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).

Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés. Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Droits d’ouverture de session en tant que service

Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :

1. Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.
1. Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.
1. Ouvrez la stratégie **Ouvrir une session en tant que service**.
1. Sélectionnez **Ajouter un utilisateur ou un groupe**.
1. Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :
   1. Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.
   1. Sélectionnez **Avancé**. Sélectionnez **Rechercher maintenant**. Sélectionnez le compte d’utilisateur dans la liste. Sélectionnez **OK**. Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.
1. Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.

## <a name="create-and-manage-the-windows-service"></a>Créer et gérer le service Windows

### <a name="create-a-service"></a>Créer un service

Utilisez les commandes PowerShell pour enregistrer un service. À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Chemin vers le dossier de l’application sur `d:\myservice`l’hôte (par exemple, ). N’incluez pas le fichier exécutable de l’application dans le chemin. Aucune barre oblique de fin n’est nécessaire.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Compte utilisateur de service `Contoso\ServiceUser`(par exemple, ).
* `{SERVICE NAME}`&ndash; Nom de service `MyService`(par exemple, ).
* `{EXE FILE PATH}`&ndash; Le chemin exécutable de `d:\myservice\myservice.exe`l’application (par exemple, ). Incluez le nom de fichier de l’exécutable avec l’extension.
* `{DESCRIPTION}`&ndash; Description de service `My sample service`(par exemple, ).
* `{DISPLAY NAME}`&ndash; Nom d’affichage de `My Service`service (par exemple, ).

### <a name="start-a-service"></a>Démarrer un service

Démarrez un service avec la commande PowerShell 6 suivante :

```powershell
Start-Service -Name {SERVICE NAME}
```

La commande prend quelques secondes pour démarrer le service.

### <a name="determine-a-services-status"></a>Déterminer l’état d’un service

Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :

```powershell
Get-Service -Name {SERVICE NAME}
```

L’état est signalé comme étant l’une des valeurs suivantes :

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Arrêter un service

Arrêtez un service avec la commande Powershell 6 suivante :

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Supprimer un service

Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire. Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuration des points de terminaison

Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Configurer l’URL et `ASPNETCORE_URLS` le port en définissant la variable de l’environnement.

Pour des approches supplémentaires de configuration d’URL et de port, consultez l’article serveur pertinent :

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Les directives précédentes couvrent le support pour les points de terminaison HTTPS. Par exemple, configurez l’application pour HTTPS lorsque l’authentification est utilisée avec un service Windows.

> [!NOTE]
> L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.

## <a name="current-directory-and-content-root"></a>Répertoire actif et racine du contenu

Le répertoire de travail <xref:System.IO.Directory.GetCurrentDirectory*> actuel retourné en appelant à un service Windows est le dossier *C:\\WINDOWS\\system32.* Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres). Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Utiliser ContentRootPath ou ContentRootFileProvider

Utilisez les éléments [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> pour localiser les ressources d’une application.

Lorsque l’application s’exécute en tant que service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> définit le <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> vers [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).

Les fichiers de paramètres par défaut de l’application, *appsettings.json* et *appsettings. Environnement-.json*, sont chargés à partir de la racine de contenu de l’application en appelant [CreateDefaultBuilder pendant](xref:fundamentals/host/generic-host#set-up-a-host)la construction hôte .

Pour d’autres fichiers de <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>paramètres chargés par le <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>code développeur en , il n’est pas nécessaire d’appeler . Dans l’exemple suivant, le fichier *custom_settings.json* existe dans la racine de contenu de l’application et est chargé sans définir explicitement un chemin de base :

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

N’essayez pas <xref:System.IO.Directory.GetCurrentDirectory*> d’utiliser pour obtenir un chemin de ressources parce qu’une application De service Windows renvoie le dossier *C:\\WINDOWS\\system32* comme répertoire actuel.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Stocker les fichiers d’un service dans un emplacement approprié sur le disque

Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.

## <a name="troubleshoot"></a>Dépanner

Pour dépanner une application <xref:test/troubleshoot>Windows Service, voir .

### <a name="common-errors"></a>Erreurs courantes

* Une version ancienne ou pré-version de PowerShell est utilisée.
* Le service enregistré n’utilise pas la sortie **publiée** de l’application à partir de la commande [de publication dotnet.](/dotnet/core/tools/dotnet-publish) La sortie de la commande de [construction dotnet](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement de l’application. Les actifs publiés se trouvent dans l’un ou l’autre des dossiers suivants selon le type de déploiement :
  * *bin/Release/'TARGET FRAMEWORK'/publié* (FDD)
  * *bin/Release/'TARGET FRAMEWORK'/'RUNTIME IDENTIFIER'/publier* (SCD)
* Le service n’est pas dans l’état RUNNING.
* Les voies vers les ressources que l’application utilise (par exemple, les certificats) sont incorrectes. Le chemin de base d’un service Windows est *c:\\Windows\\System32*.
* L’utilisateur n’a pas de connexion comme un droit *de service.*
* Le mot de passe de l’utilisateur est `New-Service` expiré ou incorrectement passé lors de l’exécution de la commande PowerShell.
* L’application nécessite ASP.NET authentification Core mais n’est pas configurée pour les connexions sécurisées (HTTPS).
* Le port d’URL de demande est incorrect ou non configuré correctement dans l’application.

### <a name="system-and-application-event-logs"></a>Journaux d’événements système et d’application

Accédez aux journaux d’événements système et d’application :

1. Ouvrez le menu Démarrer, recherchez *le Visual event et*sélectionnez l’application Event **Viewer.**
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **le système** pour ouvrir le journal d’événements système. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux d’événements. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte. Pour enregistrer les détails supplémentaires de l’application, abaissez le [niveau de journal](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l’environnement [développement](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Caches de paquets clairs

Une application fonctionnant peut échouer immédiatement après la mise à niveau de la SDK .NET Core sur la machine de développement ou de changer les versions de paquets dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacer les caches de paquet en exécutant [dotnet nuget locaux tous - clair](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une coquille de commande.

   Les caches de paquets de compensation peuvent également être accomplies avec l’outil [nuget.exe](https://www.nuget.org/downloads) et l’exécution de la commande `nuget locals all -clear`. *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimer tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

### <a name="slow-or-hanging-app"></a>Application lente ou bloquée

Un *déchargement* de collision est un instantané de la mémoire du système et peut aider à déterminer la cause d’un plantage d’application, défaillance de démarrage, ou application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.
1. Exécutez le [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) avec le nom exécutable de l’application :

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) :

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Lorsqu’une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage, ou s’exécute normalement, voir Fichiers de décharge en [mode utilisateur: Choisir le meilleur outil](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pour sélectionner un outil approprié pour produire le dépotoir.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS. Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus](https://dotnet.microsoft.com/download)
* [PowerShell version 6.2 ou ultérieure](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>la configuration d’une application ;

L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console. Vérifiez si le débogueur est attaché ou si un switch `--console` est présent. Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :

* Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application. N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée. Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root). Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.
* Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.

Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.

Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.

Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application. Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Type de déploiement

Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Kit SDK

Pour un service web basé sur une application qui utilise les pages Razor ou les cadres MVC, spécifiez le SDK Web dans le fichier du projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le service n’exécute que des tâches d’arrière-plan (par exemple, [les services hébergés),](xref:fundamentals/host/hosted-services)spécifiez le Travailleur SDK dans le fichier du projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Déploiement dépendant du framework

Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible. Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.

L’identifiant Windows [Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contient le cadre cible. Dans l’exemple suivant, le RID est défini sur `win7-x64`. La propriété `<SelfContained>` a la valeur `false`. Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.

Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services. Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Déploiement autonome

Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte. Le runtime et les dépendances de l’application sont déployés avec l’application.

Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Pour publier pour plusieurs RID :

* Fournissez les RID dans une liste séparée par des points-virgules.
* Utilisez le [ \<nom de propriété RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).

Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).

Une propriété `<SelfContained>` est définie sur `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Compte d’utilisateur du service

Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.

Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.

Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.

Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).

Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés. Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Droits d’ouverture de session en tant que service

Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :

1. Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.
1. Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.
1. Ouvrez la stratégie **Ouvrir une session en tant que service**.
1. Sélectionnez **Ajouter un utilisateur ou un groupe**.
1. Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :
   1. Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.
   1. Sélectionnez **Avancé**. Sélectionnez **Rechercher maintenant**. Sélectionnez le compte d’utilisateur dans la liste. Sélectionnez **OK**. Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.
1. Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.

## <a name="create-and-manage-the-windows-service"></a>Créer et gérer le service Windows

### <a name="create-a-service"></a>Créer un service

Utilisez les commandes PowerShell pour enregistrer un service. À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Chemin vers le dossier de l’application sur `d:\myservice`l’hôte (par exemple, ). N’incluez pas le fichier exécutable de l’application dans le chemin. Aucune barre oblique de fin n’est nécessaire.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Compte utilisateur de service `Contoso\ServiceUser`(par exemple, ).
* `{SERVICE NAME}`&ndash; Nom de service `MyService`(par exemple, ).
* `{EXE FILE PATH}`&ndash; Le chemin exécutable de `d:\myservice\myservice.exe`l’application (par exemple, ). Incluez le nom de fichier de l’exécutable avec l’extension.
* `{DESCRIPTION}`&ndash; Description de service `My sample service`(par exemple, ).
* `{DISPLAY NAME}`&ndash; Nom d’affichage de `My Service`service (par exemple, ).

### <a name="start-a-service"></a>Démarrer un service

Démarrez un service avec la commande PowerShell 6 suivante :

```powershell
Start-Service -Name {SERVICE NAME}
```

La commande prend quelques secondes pour démarrer le service.

### <a name="determine-a-services-status"></a>Déterminer l’état d’un service

Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :

```powershell
Get-Service -Name {SERVICE NAME}
```

L’état est signalé comme étant l’une des valeurs suivantes :

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Arrêter un service

Arrêtez un service avec la commande Powershell 6 suivante :

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Supprimer un service

Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Gérer les événements de démarrage et d’arrêt

Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :

1. Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire. Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuration des points de terminaison

Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Configurer l’URL et `ASPNETCORE_URLS` le port en définissant la variable de l’environnement.

Pour des approches supplémentaires de configuration d’URL et de port, consultez l’article serveur pertinent :

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Les directives précédentes couvrent le support pour les points de terminaison HTTPS. Par exemple, configurez l’application pour HTTPS lorsque l’authentification est utilisée avec un service Windows.

> [!NOTE]
> L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.

## <a name="current-directory-and-content-root"></a>Répertoire actif et racine du contenu

Le répertoire de travail <xref:System.IO.Directory.GetCurrentDirectory*> actuel retourné en appelant à un service Windows est le dossier *C:\\WINDOWS\\system32.* Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres). Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Définir le dossier de l’application comme chemin d’accès racine du contenu

La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé. Au lieu `GetCurrentDirectory` d’appeler pour créer <xref:System.IO.Directory.SetCurrentDirectory*> des chemins vers des fichiers de paramètres, appelez avec le chemin vers la racine de [contenu](xref:fundamentals/index#content-root)de l’application .

Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Stocker les fichiers d’un service dans un emplacement approprié sur le disque

Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.

## <a name="troubleshoot"></a>Dépanner

Pour dépanner une application <xref:test/troubleshoot>Windows Service, voir .

### <a name="common-errors"></a>Erreurs courantes

* Une version ancienne ou pré-version de PowerShell est utilisée.
* Le service enregistré n’utilise pas la sortie **publiée** de l’application à partir de la commande [de publication dotnet.](/dotnet/core/tools/dotnet-publish) La sortie de la commande de [construction dotnet](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement de l’application. Les actifs publiés se trouvent dans l’un ou l’autre des dossiers suivants selon le type de déploiement :
  * *bin/Release/'TARGET FRAMEWORK'/publié* (FDD)
  * *bin/Release/'TARGET FRAMEWORK'/'RUNTIME IDENTIFIER'/publier* (SCD)
* Le service n’est pas dans l’état RUNNING.
* Les voies vers les ressources que l’application utilise (par exemple, les certificats) sont incorrectes. Le chemin de base d’un service Windows est *c:\\Windows\\System32*.
* L’utilisateur n’a pas de connexion comme un droit *de service.*
* Le mot de passe de l’utilisateur est `New-Service` expiré ou incorrectement passé lors de l’exécution de la commande PowerShell.
* L’application nécessite ASP.NET authentification Core mais n’est pas configurée pour les connexions sécurisées (HTTPS).
* Le port d’URL de demande est incorrect ou non configuré correctement dans l’application.

### <a name="system-and-application-event-logs"></a>Journaux d’événements système et d’application

Accédez aux journaux d’événements système et d’application :

1. Ouvrez le menu Démarrer, recherchez *le Visual event et*sélectionnez l’application Event **Viewer.**
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **le système** pour ouvrir le journal d’événements système. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux d’événements. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte. Pour enregistrer les détails supplémentaires de l’application, abaissez le [niveau de journal](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l’environnement [développement](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Caches de paquets clairs

Une application fonctionnant peut échouer immédiatement après la mise à niveau de la SDK .NET Core sur la machine de développement ou de changer les versions de paquets dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacer les caches de paquet en exécutant [dotnet nuget locaux tous - clair](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une coquille de commande.

   Les caches de paquets de compensation peuvent également être accomplies avec l’outil [nuget.exe](https://www.nuget.org/downloads) et l’exécution de la commande `nuget locals all -clear`. *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimer tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

### <a name="slow-or-hanging-app"></a>Application lente ou bloquée

Un *déchargement* de collision est un instantané de la mémoire du système et peut aider à déterminer la cause d’un plantage d’application, défaillance de démarrage, ou application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.
1. Exécutez le [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom exécutable de l’application :

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Lorsqu’une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage, ou s’exécute normalement, voir Fichiers de décharge en [mode utilisateur: Choisir le meilleur outil](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pour sélectionner un outil approprié pour produire le dépotoir.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS. Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus](https://dotnet.microsoft.com/download)
* [PowerShell version 6.2 ou ultérieure](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>la configuration d’une application ;

L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console. Vérifiez si le débogueur est attaché ou si un switch `--console` est présent. Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :

* Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application. N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée. Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root). Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.
* Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.

Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.

Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.

Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application. Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Type de déploiement

Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>Kit SDK

Pour un service web basé sur une application qui utilise les pages Razor ou les cadres MVC, spécifiez le SDK Web dans le fichier du projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le service n’exécute que des tâches d’arrière-plan (par exemple, [les services hébergés),](xref:fundamentals/host/hosted-services)spécifiez le Travailleur SDK dans le fichier du projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Déploiement dépendant du framework

Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible. Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.

L’identifiant Windows [Runtime (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contient le cadre cible. Dans l’exemple suivant, le RID est défini sur `win7-x64`. La propriété `<SelfContained>` a la valeur `false`. Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.

La propriété `<UseAppHost>` a la valeur `true`. Cette propriété fournit au service un chemin d’activation (un fichier exécutable *.exe*) pour un déploiement dépendant du framework (FDD).

Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services. Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Déploiement autonome

Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte. Le runtime et les dépendances de l’application sont déployés avec l’application.

Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Pour publier pour plusieurs RID :

* Fournissez les RID dans une liste séparée par des points-virgules.
* Utilisez le [ \<nom de propriété RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).

Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).

Une propriété `<SelfContained>` est définie sur `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Compte d’utilisateur du service

Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.

Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.

Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.

Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).

Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés. Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Droits d’ouverture de session en tant que service

Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :

1. Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.
1. Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.
1. Ouvrez la stratégie **Ouvrir une session en tant que service**.
1. Sélectionnez **Ajouter un utilisateur ou un groupe**.
1. Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :
   1. Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.
   1. Sélectionnez **Avancé**. Sélectionnez **Rechercher maintenant**. Sélectionnez le compte d’utilisateur dans la liste. Sélectionnez **OK**. Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.
1. Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.

## <a name="create-and-manage-the-windows-service"></a>Créer et gérer le service Windows

### <a name="create-a-service"></a>Créer un service

Utilisez les commandes PowerShell pour enregistrer un service. À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Chemin vers le dossier de l’application sur `d:\myservice`l’hôte (par exemple, ). N’incluez pas le fichier exécutable de l’application dans le chemin. Aucune barre oblique de fin n’est nécessaire.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Compte utilisateur de service `Contoso\ServiceUser`(par exemple, ).
* `{SERVICE NAME}`&ndash; Nom de service `MyService`(par exemple, ).
* `{EXE FILE PATH}`&ndash; Le chemin exécutable de `d:\myservice\myservice.exe`l’application (par exemple, ). Incluez le nom de fichier de l’exécutable avec l’extension.
* `{DESCRIPTION}`&ndash; Description de service `My sample service`(par exemple, ).
* `{DISPLAY NAME}`&ndash; Nom d’affichage de `My Service`service (par exemple, ).

### <a name="start-a-service"></a>Démarrer un service

Démarrez un service avec la commande PowerShell 6 suivante :

```powershell
Start-Service -Name {SERVICE NAME}
```

La commande prend quelques secondes pour démarrer le service.

### <a name="determine-a-services-status"></a>Déterminer l’état d’un service

Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :

```powershell
Get-Service -Name {SERVICE NAME}
```

L’état est signalé comme étant l’une des valeurs suivantes :

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Arrêter un service

Arrêtez un service avec la commande Powershell 6 suivante :

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Supprimer un service

Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Gérer les événements de démarrage et d’arrêt

Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :

1. Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire. Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuration des points de terminaison

Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Configurer l’URL et `ASPNETCORE_URLS` le port en définissant la variable de l’environnement.

Pour des approches supplémentaires de configuration d’URL et de port, consultez l’article serveur pertinent :

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Les directives précédentes couvrent le support pour les points de terminaison HTTPS. Par exemple, configurez l’application pour HTTPS lorsque l’authentification est utilisée avec un service Windows.

> [!NOTE]
> L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.

## <a name="current-directory-and-content-root"></a>Répertoire actif et racine du contenu

Le répertoire de travail <xref:System.IO.Directory.GetCurrentDirectory*> actuel retourné en appelant à un service Windows est le dossier *C:\\WINDOWS\\system32.* Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres). Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Définir le dossier de l’application comme chemin d’accès racine du contenu

La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé. Au lieu `GetCurrentDirectory` d’appeler pour créer <xref:System.IO.Directory.SetCurrentDirectory*> des chemins vers des fichiers de paramètres, appelez avec le chemin vers la racine de [contenu](xref:fundamentals/index#content-root)de l’application .

Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Stocker les fichiers d’un service dans un emplacement approprié sur le disque

Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.

## <a name="troubleshoot"></a>Dépanner

Pour dépanner une application <xref:test/troubleshoot>Windows Service, voir .

### <a name="common-errors"></a>Erreurs courantes

* Une version ancienne ou pré-version de PowerShell est utilisée.
* Le service enregistré n’utilise pas la sortie **publiée** de l’application à partir de la commande [de publication dotnet.](/dotnet/core/tools/dotnet-publish) La sortie de la commande de [construction dotnet](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement de l’application. Les actifs publiés se trouvent dans l’un ou l’autre des dossiers suivants selon le type de déploiement :
  * *bin/Release/'TARGET FRAMEWORK'/publié* (FDD)
  * *bin/Release/'TARGET FRAMEWORK'/'RUNTIME IDENTIFIER'/publier* (SCD)
* Le service n’est pas dans l’état RUNNING.
* Les voies vers les ressources que l’application utilise (par exemple, les certificats) sont incorrectes. Le chemin de base d’un service Windows est *c:\\Windows\\System32*.
* L’utilisateur n’a pas de connexion comme un droit *de service.*
* Le mot de passe de l’utilisateur est `New-Service` expiré ou incorrectement passé lors de l’exécution de la commande PowerShell.
* L’application nécessite ASP.NET authentification Core mais n’est pas configurée pour les connexions sécurisées (HTTPS).
* Le port d’URL de demande est incorrect ou non configuré correctement dans l’application.

### <a name="system-and-application-event-logs"></a>Journaux d’événements système et d’application

Accédez aux journaux d’événements système et d’application :

1. Ouvrez le menu Démarrer, recherchez *le Visual event et*sélectionnez l’application Event **Viewer.**
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **le système** pour ouvrir le journal d’événements système. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux d’événements. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte. Pour enregistrer les détails supplémentaires de l’application, abaissez le [niveau de journal](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l’environnement [développement](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Caches de paquets clairs

Une application fonctionnant peut échouer immédiatement après la mise à niveau de la SDK .NET Core sur la machine de développement ou de changer les versions de paquets dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacer les caches de paquet en exécutant [dotnet nuget locaux tous - clair](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une coquille de commande.

   Les caches de paquets de compensation peuvent également être accomplies avec l’outil [nuget.exe](https://www.nuget.org/downloads) et l’exécution de la commande `nuget locals all -clear`. *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimer tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

### <a name="slow-or-hanging-app"></a>Application lente ou bloquée

Un *déchargement* de collision est un instantané de la mémoire du système et peut aider à déterminer la cause d’un plantage d’application, défaillance de démarrage, ou application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.
1. Exécutez le [script EnableDumps PowerShell](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom exécutable de l’application :

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Lorsqu’une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage, ou s’exécute normalement, voir Fichiers de décharge en [mode utilisateur: Choisir le meilleur outil](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) pour sélectionner un outil approprié pour produire le dépotoir.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
