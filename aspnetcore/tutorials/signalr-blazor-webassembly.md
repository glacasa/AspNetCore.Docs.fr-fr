---
title: Utiliser ASP.NET Core SignalR Blazor avec WebAssembly
author: guardrex
description: Créez une application de chat qui SignalR Blazor utilise ASP.NET Core avec WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: c4843dc282e1978b39738e206ecc79ded87fcff9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306574"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Utilisez ASP.NET SignalR de base avec Blazor WebAssembly

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Ce tutoriel enseigne les bases de la construction d’une application en temps réel en utilisant SignalR avec Blazor WebAssembly. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet d’application Blazor WebAssembly Hébergé
> * Ajouter la bibliothèque de client SignalR
> * Ajouter un hub SignalR
> * Ajouter des services SignalR et un point de terminaison pour le hub SignalR
> * Ajouter le code de composant Razor pour le chat

À la fin de ce tutoriel, vous aurez une application de chat de travail.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Créer un projet d’application Blazor WebAssembly hébergé

Lorsque vous n’utilisez pas la version Visual Studio 16.6 Aperçu 2 ou plus tard, installez le modèle [WebAssembly Blazor.](xref:blazor/hosting-models#blazor-webassembly) Le paquet [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) a une version de prévisualisation tandis que Blazor WebAssembly est en avant-première. Dans une coque de commande, exécutez la commande suivante :

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
```

Suivez les conseils pour votre choix d’outillage :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Créez un projet.

1. Sélectionnez **Blazor App** et sélectionnez **Next**.

1. Tapez "BlazorSignalRApp" dans le champ du **nom du projet.** Confirmez que **l’entrée de localisation** est correcte ou fournissez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Choisissez le modèle **Blazor WebAssembly App.**

1. Sous **Advanced**, sélectionnez la case à cocher **hébergée ASP.NET Core.**

1. Sélectionnez **Create** (Créer).

> [!NOTE]
> Si vous avez mis à niveau ou installé une nouvelle version de Visual Studio et que le modèle WebAssembly de Blazor n’apparaît pas dans l’interface utilisateur VS, réinstaller le modèle à l’aide de la `dotnet new` commande indiquée précédemment.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Dans une coque de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Dans Visual Studio Code, ouvrez le dossier de projet de l’application.

1. Lorsque le dialogue semble ajouter des actifs pour construire et déboger l’application, sélectionnez **Oui**. Visual Studio Code ajoute automatiquement le dossier *.vscode* avec des fichiers *launch.json* et *tasks.json* générés.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans une coque de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Dans Visual Studio for Mac, ouvrez le projet en naviguant vers le dossier du projet et en ouvrant le fichier de solution du projet (*.sln*).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une coque de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Ajouter la bibliothèque de client SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Solution Explorer**, cliquez à droite sur le projet **BlazorSignalRApp.Client** et **sélectionnez Manage NuGet Packages**.

1. Dans le dialogue **Manage NuGet Packages,** confirmer que la **source du paquet** est configurée à *nuget.org*.

1. Avec **Browse** sélectionné, tapez "Microsoft.AspNetCore.SignalR.Client" dans la zone de recherche.

1. Dans les résultats de `Microsoft.AspNetCore.SignalR.Client` recherche, sélectionnez le paquet et sélectionnez **Installer**.

1. Si le dialogue **de modifications d’aperçu** apparaît, sélectionnez **OK**.

1. Si le dialogue **d’acceptation de licence** apparaît, sélectionnez **J’accepte** si vous êtes d’accord avec les conditions de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **terminal intégré** **(voir** > **le terminal** depuis la barre d’outils), exécutez les commandes suivantes :

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **Solution,** cliquez à droite sur le projet **BlazorSignalRApp.Client** et **sélectionnez Manage NuGet Packages**.

1. Dans le dialogue **Manage NuGet Packages,** confirmer que la source de décrochage est configuré pour *nuget.org*.

1. Avec **Browse** sélectionné, tapez "Microsoft.AspNetCore.SignalR.Client" dans la zone de recherche.

1. Dans les résultats de recherche, sélectionnez la case à cocher à côté du `Microsoft.AspNetCore.SignalR.Client` paquet et sélectionnez Add **Package**.

1. Si le dialogue **d’acceptation de licence** apparaît, **sélectionnez Accept** si vous êtes d’accord avec les conditions de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une coque de commande, exécutez les commandes suivantes :

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Ajouter un hub SignalR

Dans le cadre du projet **BlazorSignalRApp.Server,** créez un dossier `ChatHub` *Hubs* (pluriel) et ajoutez la classe suivante *(Hubs/ChatHub.cs*) :

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>Ajouter des services SignalR et un point de terminaison pour le hub SignalR

1. Dans le cadre du projet **BlazorSignalRApp.Server,** ouvrez le *fichier Startup.cs.*

1. Ajouter l’espace `ChatHub` nom pour la classe en haut du fichier :

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Ajoutez les services `Startup.ConfigureServices`SignalR à :

   ```csharp
   services.AddSignalR();
   ```

1. Entre `Startup.Configure` les points de terminaison pour l’itinéraire du contrôleur par défaut et le repli côté client, ajoutez un point de terminaison pour le hub :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Ajouter le code de composant Razor pour le chat

1. Dans le cadre du projet **BlazorSignalRApp.Client,** ouvrez le fichier *Pages/Index.razor.*

1. Remplacez la balisage par le code suivant :

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Exécuter l’application

1. Suivez les conseils pour votre outillage :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dans **Solution Explorer**, sélectionnez le projet **BlazorSignalRApp.Server.** Appuyez sur **Ctrl-F5** pour exécuter l’application sans déboguer.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message sont affichés instantanément sur les deux pages :

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Sélectionnez **Debug** > **Run Sans débogage** à partir de la barre d’outils.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message sont affichés instantanément sur les deux pages :

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **Solution,** sélectionnez le projet **BlazorSignalRApp.Server.** Dans le menu, sélectionnez **Run** > **Start Without Debugging**.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message sont affichés instantanément sur les deux pages :

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans une coque de commande, exécutez les commandes suivantes :

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message sont affichés instantanément sur les deux pages :

   ![SignalR Blazor WebAssembly exemple app ouvert dans deux fenêtres de navigateur montrant des messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Citations: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer Blazor un projet d’application WebAssembly Hosted
> * Ajouter SignalR la bibliothèque client
> * Ajouter SignalR un hub
> * Ajouter SignalR des services et SignalR un point de terminaison pour le hub
> * Ajouter le code de composant Razor pour le chat

Pour en savoir Blazor plus sur Blazor la création d’applications, consultez la documentation :

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/introduction>
