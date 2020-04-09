---
title: Hôte et déploiement ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment héberger Blazor et déployer des applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434263"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>Héberger et déployer ASP.NET Core Blazor

Par [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) et [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>Publier l’application

Les applications sont publiées pour le déploiement dans la configuration Release.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Sélectionnez **Build** > **Publier 'APPLICATION'** à partir de la barre de navigation.
1. Sélectionnez l’onglet *Cible de publication*. Pour publier localement, sélectionnez **Dossier**.
1. Acceptez l’emplacement par défaut dans le champ **Choisir un dossier** ou spécifiez un autre emplacement. Cliquez sur le bouton **Publier**.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Utilisez la commande [dotnet publish](/dotnet/core/tools/dotnet-publish) pour publier l’application avec une configuration Release :

```dotnetcli
dotnet publish -c Release
```

---

La publication de l’application déclenche une [restauration](/dotnet/core/tools/dotnet-restore) des dépendances du projet et [crée](/dotnet/core/tools/dotnet-build) le projet avant de créer les ressources pour le déploiement. Dans le cadre du processus de génération, les assemblys et méthodes inutilisés sont supprimés pour réduire la durée du chargement et la taille du téléchargement de l’application.

Publier les lieux:

* BlazorWebAssembly (en)
  * Autonome &ndash; L’application est publiée dans le *dossier /bin/Release/TARGET FRAMEWORK/publish/wwwroot.* Pour déployer l’application en tant que site statique, copiez le contenu du dossier *wwwroot* à l’hôte statique du site.
  * Hébergé &ndash; L’application WebAssembly client Blazor est publiée dans le dossier */bin/Release/TARGET FRAMEWORK/publish/wwwroot* de l’application serveur, ainsi que tous les autres actifs Web statiques de l’application serveur. Déployez le contenu du dossier *de publication* à l’hôte.
* BlazorServeur &ndash; L’application est publiée dans le *dossier /bin/Release/TARGET FRAMEWORK/publish.* Déployez le contenu du dossier *de publication* à l’hôte.

Les ressources du dossier sont déployées sur le serveur web. Le déploiement peut être un processus manuel ou automatisé, en fonction des outils de développement utilisés.

## <a name="app-base-path"></a>Chemin de base de l’application

Le *chemin de base de l’application* est le chemin d’URL racine de l’application. Considérez l’application et la Blazor sous-application ASP.NET Core suivantes :

* L’application ASP.NET Core est `MyApp`nommée :
  * L’application réside physiquement à *d:/MyApp*.
  * Les demandes `https://www.contoso.com/{MYAPP RESOURCE}`sont reçues à .
* Une Blazor application `CoolApp` nommée est une `MyApp`sous-application de :
  * La sous-application réside physiquement à *d:/MyApp/CoolApp*.
  * Les demandes `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`sont reçues à .

Sans spécifier `CoolApp`une configuration supplémentaire pour , la sous-application dans ce scénario n’a aucune connaissance de l’endroit où il réside sur le serveur. Par exemple, l’application ne peut pas construire des URL relatives correctes à `/CoolApp/`ses ressources sans savoir qu’elle réside sur le chemin relatif de l’URL .

Pour fournir la Blazor configuration pour le `https://www.contoso.com/CoolApp/`chemin `<base>` de `href` base de l’application de , l’attribut de l’étiquette estBlazor réglé sur le chemin de racine relative dans le fichier *Pages/_Host.cshtml* (Serveur) ou *wwwroot/index.html* fichier (WebAssembly):Blazor

```html
<base href="/CoolApp/">
```

BlazorLes applications serveur réglent en <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> outre le chemin de `Startup.Configure`base côté serveur en appelant dans le pipeline de demande de l’application de :

```csharp
app.UsePathBase("/CoolApp");
```

En fournissant le chemin URL relatif, un composant qui n’est pas dans le répertoire racine peut construire des URL par rapport à la trajectoire de racine de l’application. Les composants à différents niveaux de la structure de l’annuaire peuvent créer des liens vers d’autres ressources à des endroits dans l’ensemble de l’application. Le chemin de base de l’application est `href` également utilisé pour intercepter certains hyperliens lorsque la cible du lien se trouve dans l’espace URI de la trajectoire de base de l’application. Le Blazor routeur s’occupe de la navigation interne.

Dans de nombreux scénarios d’hébergement, le chemin URL relatif vers l’application est la racine de l’application. Dans ces cas, le chemin de base relatif`<base href="/" />`de l’URL de Blazor l’application est une barre oblique vers l’avant (), qui est la configuration par défaut pour une application. Dans d’autres scénarios d’hébergement, tels que GitHub Pages et les sous-applications IIS, le chemin de base de l’application doit être défini sur la trajectoire relative d’URL de l’application par le serveur.

Pour définir le chemin de base `<base>` de `<head>` l’application, mettez à jour l’étiquette dansBlazor les éléments de balises du fichier *Pages/_Host.cshtml* (Serveur) ou *wwwroot/index.html* fichier (WebAssembly).Blazor Définissez `href` la `/{RELATIVE URL PATH}/` valeur d’attribut à (la barre oblique de fuite est nécessaire), où `{RELATIVE URL PATH}` est le chemin complet de l’APPLICATION URL relative.

Pour Blazor une application WebAssembly avec un chemin d’URL relative non-root (par exemple, `<base href="/CoolApp/">`), l’application ne parvient pas à trouver ses ressources *lorsqu’elle est exécutée localement*. Pour surmonter ce problème pendant le développement local et les tests, vous pouvez fournir un argument de *base de chemin* qui correspond à la valeur `href` de la balise `<base>` au moment de l’exécution. N’incluez pas une barre oblique de fuite. Pour passer l’argument de base de chemin `dotnet run` lors de l’exécution de `--pathbase` l’application localement, exécutez la commande à partir de l’annuaire de l’application avec l’option:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Pour Blazor une application WebAssembly avec `/CoolApp/` un`<base href="/CoolApp/">`chemin URL relatif de ( ), la commande est:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

L’application Blazor WebAssembly répond `http://localhost:port/CoolApp`localement à .

## <a name="deployment"></a>Déploiement

Pour obtenir des conseils de déploiement, consultez les rubriques suivantes :

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
