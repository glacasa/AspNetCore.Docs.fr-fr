---
title: Lien de navigateur dans ASP.NET core
author: ncarandini
description: Explique comment Browser Link est une fonctionnalité Visual Studio qui relie l’environnement de développement avec un ou plusieurs navigateurs Web.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658850"
---
# <a name="browser-link-in-aspnet-core"></a>Lien de navigateur dans ASP.NET core

Par [Nicolà Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), et [Tom Dykstra](https://github.com/tdykstra)

Browser Link est une fonctionnalité Visual Studio. Il crée un canal de communication entre l’environnement de développement et un ou plusieurs navigateurs Web. Vous pouvez utiliser Browser Link pour actualiser votre application web dans plusieurs navigateurs à la fois, ce qui est utile pour les tests inter-navigateurs.

## <a name="browser-link-setup"></a>Configuration de lien de navigateur

::: moniker range=">= aspnetcore-3.0"

Ajoutez le package [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) à votre projet. Pour ASP.NET core Razor Pages ou projets MVC, permettent également la compilation en temps <xref:mvc/views/view-compilation>d’exécution des fichiers Razor (*.cshtml)* tel que décrit dans . Les modifications de la syntaxe razor ne sont appliquées que lorsque la compilation en temps d’exécution a été activée.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Lors de la conversion d’un projet core 2.0 ASP.NET pour ASP.NET Core 2.1 et la transition vers le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), installez le package [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) pour la fonctionnalité Browser Link. Les modèles de projet ASP.NET Core 2.1 utilisent le `Microsoft.AspNetCore.App` métapackage par défaut.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Le ASP.NET Core 2.0 **Web Application**, **Vide**, et **Web API** modèles de projet utilisent le [métapackage Microsoft.AspNetCore.All](xref:fundamentals/metapackage), qui contient une référence paquet pour [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Par conséquent, `Microsoft.AspNetCore.All` l’utilisation du métapackage ne nécessite aucune autre action pour rendre Browser Link disponible pour une utilisation.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Le modèle ASP.NET projet **d’application Web** Core 1.x a une référence de paquet pour le paquet [Microsoft.VisualStudio.Web.BrowserLink.](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) D’autres types de projet vous `Microsoft.VisualStudio.Web.BrowserLink`obligent à ajouter une référence globale à .

::: moniker-end

### <a name="configuration"></a>Configuration

Appelez `UseBrowserLink` dans la méthode `Startup.Configure` :

```csharp
app.UseBrowserLink();
```

L’appel `UseBrowserLink` est généralement `if` placé à l’intérieur d’un bloc qui ne permet browser Link dans l’environnement de développement. Par exemple :

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Pour plus d’informations, consultez <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Comment utiliser Browser Link

Lorsque vous avez un projet ASP.NET Core ouvert, Visual Studio affiche le contrôle de la barre d’outils Browser Link à côté du contrôle de la barre d’outils **Debug Target** :

![Menu déroulant Browser Link](using-browserlink/_static/browserLink-dropdown-menu.png)

À partir du contrôle de la barre d’outils Browser Link, vous pouvez :

* Rafraîchissez l’application web dans plusieurs navigateurs à la fois.
* Ouvrez le **tableau de bord de lien de navigateur**.
* Activez ou désactivez **Browser Link**. Remarque : Browser Link est désactivé par défaut dans Visual Studio.
* Activez ou désactivez [CSS Auto-Sync](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Rafraîchir l’application web dans plusieurs navigateurs à la fois

Pour choisir un seul navigateur Web pour lancer lors du démarrage du projet, utilisez le menu déroulant dans le contrôle de la barre d’outils **Debug Target** :

![Menu Déroulant F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Pour ouvrir plusieurs navigateurs à la fois, choisissez **Browse avec...** à partir de la même baisse. Maintenez la clé <kbd>Ctrl</kbd> pour sélectionner les navigateurs que vous voulez, puis cliquez sur **Parcourir**:

![Ouvrez de nombreux navigateurs à la fois](using-browserlink/_static/open-many-browsers-at-once.png)

La capture d’écran suivante montre Visual Studio avec la vue Index ouverte et deux navigateurs ouverts:

![Synchroniser avec deux navigateurs exemple](using-browserlink/_static/sync-with-two-browsers-example.png)

Survolez le contrôle de la barre d’outils Browser Link pour voir les navigateurs connectés au projet :

![Pointe de vol stationnaire](using-browserlink/_static/hoover-tip.png)

Modifiez la vue d’index, et tous les navigateurs connectés sont mis à jour lorsque vous cliquez sur le bouton de rafraîchissement de Browser Link :

![navigateurs-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Browser Link fonctionne également avec les navigateurs que vous lancez de l’extérieur de Visual Studio et naviguez vers l’URL de l’application.

### <a name="the-browser-link-dashboard"></a>Le tableau de bord de lien de navigateur

Ouvrez la fenêtre de tableau de **bord de lien** de navigateur à partir du menu de chute de Browser Link pour gérer la connexion avec les navigateurs ouverts :

![tableau de bord open-browserslink](using-browserlink/_static/open-browserlink-dashboard.png)

Si aucun navigateur n’est connecté, vous pouvez démarrer une session de non-débugging en sélectionnant le lien **View in Browser** :

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Sinon, les navigateurs connectés sont affichés avec le chemin vers la page que chaque navigateur affiche:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Vous pouvez également cliquer sur un nom de navigateur individuel pour actualiser uniquement ce navigateur.

### <a name="enable-or-disable-browser-link"></a>Activez ou désactivez le lien de navigateur

Lorsque vous réactivez Browser Link après l’avoir désactivé, vous devez actualiser les navigateurs pour les reconnecter.

### <a name="enable-or-disable-css-auto-sync"></a>Activez ou désactivez CSS Auto-Sync

Lorsque CSS Auto-Sync est activé, les navigateurs connectés sont automatiquement actualisés lorsque vous modifiez les fichiers CSS.

## <a name="how-it-works"></a>Fonctionnement

Browser Link [SignalR](xref:signalr/introduction) utilise pour créer un canal de communication entre Visual Studio et le navigateur. Lorsque Browser Link est activé, SignalR Visual Studio agit comme un serveur que plusieurs clients (navigateurs) peuvent se connecter. Browser Link enregistre également un composant middleware dans le pipeline de la demande ASP.NET Core. Ce composant injecte des références spéciales `<script>` dans chaque demande de page du serveur. Vous pouvez voir les références de script en sélectionnant la source `<body>` De **vue** dans le navigateur et en faisant défiler jusqu’à la fin du contenu de l’étiquette :

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Vos fichiers sources ne sont pas modifiés. Le composant middleware injecte dynamiquement les références de script.

Parce que le code côté navigateur est tout JavaScript, il fonctionne sur tous les navigateurs qui SignalR prend en charge sans nécessiter un plug-in navigateur.
