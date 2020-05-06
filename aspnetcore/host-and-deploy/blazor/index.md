---
title: Héberger et déployer des ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment héberger et déployer Blazor des applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 9d57b81cd813d02a65b6d3a39c7f1a1aa8a069c7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775165"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Héberger et déployer ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publier l’application

Les applications sont publiées pour le déploiement dans la configuration Release.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Sélectionnez **générer** > **publier {application}** dans la barre de navigation.
1. Sélectionnez l’onglet *Cible de publication*. Pour publier localement, sélectionnez **Dossier**.
1. Acceptez l’emplacement par défaut dans le champ **Choisir un dossier** ou spécifiez un autre emplacement. Cliquez sur le bouton **Publier**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Sélectionnez **générer** > **publier dans le dossier**.
1. Confirmez le dossier pour recevoir les ressources publiées et sélectionnez **publier**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) pour publier l’application avec une configuration Release :

```dotnetcli
dotnet publish -c Release
```

---

La publication de l’application déclenche une [restauration](/dotnet/core/tools/dotnet-restore) des dépendances du projet et [crée](/dotnet/core/tools/dotnet-build) le projet avant de créer les ressources pour le déploiement. Dans le cadre du processus de génération, les assemblys et méthodes inutilisés sont supprimés pour réduire la durée du chargement et la taille du téléchargement de l’application.

Emplacements de publication :

* BlazorWebassembly
  * Autonome &ndash; l’application est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* . Pour déployer l’application en tant que site statique, copiez le contenu du dossier *wwwroot* sur l’hôte de site statique.
  * L' &ndash; application webassembly client Blazor hébergée est publiée dans le dossier */bin/Release/{Target Framework}/Publish/wwwroot* de l’application serveur, avec toutes les autres ressources Web statiques de l’application serveur. Déployez le contenu du dossier de *publication* sur l’hôte.
* BlazorServeur &ndash; l’application est publiée dans le dossier */bin/Release/{Target Framework}/Publish* . Déployez le contenu du dossier de *publication* sur l’hôte.

Les ressources du dossier sont déployées sur le serveur web. Le déploiement peut être un processus manuel ou automatisé, en fonction des outils de développement utilisés.

## <a name="app-base-path"></a>Chemin de base de l’application

Le *chemin d’accès de base* de l’application est le chemin de l’URL racine de l’application. Considérez les éléments suivants ASP.NET Core Blazor application et sous-application :

* L’application ASP.NET Core est nommée `MyApp`:
  * L’application réside physiquement dans *d:/MyApp*.
  * Les demandes sont reçues `https://www.contoso.com/{MYAPP RESOURCE}`à l’adresse.
* Une Blazor application nommée `CoolApp` est une sous-application de `MyApp`:
  * La sous-application réside physiquement dans *d:/MyApp/coolapp*.
  * Les demandes sont reçues `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`à l’adresse.

Si vous ne spécifiez `CoolApp`pas de configuration supplémentaire pour, la sous-application dans ce scénario n’a aucune connaissance de son emplacement sur le serveur. Par exemple, l’application ne peut pas construire des URL relatives correctes pour ses ressources sans savoir qu’elle réside sur le chemin `/CoolApp/`d’URL relatif.

Pour fournir la configuration du Blazor chemin d’accès de base `https://www.contoso.com/CoolApp/`de l' `<base>` application, `href` l’attribut de la balise est défini sur le chemin d’accès racine relatif dans leBlazor fichier *Pages/_Host. cshtml* (serveur) ou le fichier *wwwroot/index.html* (Blazor webassembly) :

```html
<base href="/CoolApp/">
```

BlazorLes applications serveur définissent également le chemin d’accès de base <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> côté serveur en appelant dans le pipeline de demande de `Startup.Configure`l’application :

```csharp
app.UsePathBase("/CoolApp");
```

En fournissant le chemin d’URL relatif, un composant qui ne se trouve pas dans le répertoire racine peut construire des URL relatives au chemin d’accès racine de l’application. Les composants à différents niveaux de la structure de répertoires peuvent créer des liens vers d’autres ressources à des emplacements de l’application. Le chemin d’accès de base de l’application est également utilisé pour intercepter `href` les liens hypertexte sélectionnés où la cible du lien se trouve dans l’espace URI du chemin d’accès de base de l’application. Le Blazor routeur gère la navigation interne.

Dans de nombreux scénarios d’hébergement, le chemin d’URL relatif à l’application est la racine de l’application. Dans ce cas, le chemin d’accès de base de l’URL relative de l'`<base href="/" />`application est une barre oblique (), qui Blazor est la configuration par défaut pour une application. Dans d’autres scénarios d’hébergement, tels que les pages GitHub et les sous-applications IIS, le chemin d’accès de base de l’application doit être défini sur le chemin d’URL relatif du serveur de l’application.

Pour définir le chemin de base de l’application, `<base>` mettez à jour `<head>` la balise dans les éléments tag du fichier *pages/_Host. cshtml* (Blazor Server)Blazor ou du fichier *wwwroot/index.html* (webassembly). Affectez `href` à `/{RELATIVE URL PATH}/` l’attribut la valeur (la barre oblique de fin est requise `{RELATIVE URL PATH}` ), où est le chemin d’URL relatif complet de l’application.

Pour une Blazor application webassembly avec un chemin d’URL relatif non racine (par exemple `<base href="/CoolApp/">`,), l’application ne parvient pas à trouver ses ressources lorsqu’elle est *exécutée localement*. Pour surmonter ce problème pendant le développement local et les tests, vous pouvez fournir un argument de *base de chemin* qui correspond à la valeur `href` de la balise `<base>` au moment de l’exécution. N’incluez pas de barre oblique finale. Pour passer l’argument de base Path lors de l’exécution locale de l' `dotnet run` application, exécutez la commande à partir du `--pathbase` répertoire de l’application avec l’option :

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pour une Blazor application webassembly avec un chemin d’URL `/CoolApp/` relatif`<base href="/CoolApp/">`(), la commande est la suivante :

```dotnetcli
dotnet run --pathbase=/CoolApp
```

L' Blazor application webassembly répond localement `http://localhost:port/CoolApp`à l’adresse.

## <a name="deployment"></a>Déploiement

Pour obtenir des conseils de déploiement, consultez les rubriques suivantes :

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
