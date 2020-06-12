---
title: Utiliser ASP.NET Core SignalR avec Blazor webassembly
author: guardrex
description: Créer une application de conversation qui utilise ASP.NET Core SignalR avec Blazor webassembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 720f534426cc0e2b32778e49050c7f7d75ecd60d
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679590"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Utiliser ASP.NET Core SignalR avec Blazor webassembly

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide SignalR de avec Blazor webassembly. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un Blazor projet d’application hébergée Webassembly
> * Ajouter la SignalR bibliothèque cliente
> * Ajouter un SignalR Hub
> * Ajouter des SignalR services et un point de terminaison pour le SignalR Hub
> * Ajouter Razor du code de composant pour la conversation

À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 16,6 ou version ultérieure](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge de travail de **développement ASP.net et Web**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* [Visual Studio pour Mac version 8,6 ou ultérieure](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Créer un Blazor projet d’application Webassembly hébergé

Suivez les instructions de votre choix d’outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!NOTE]
> Visual Studio 16,6 ou version ultérieure et kit SDK .NET Core 3.1.300 ou version ultérieure sont requis.

1. Créez un projet.

1. Sélectionnez ** Blazor application** , puis sélectionnez **suivant**.

1. Tapez « BlazorSignalRApp » dans le champ **nom du projet** . Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Choisissez le modèle d' ** Blazor application webassembly** .

1. Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .

1. Sélectionnez **Create** (Créer).

> [!NOTE]
> Si vous avez mis à niveau ou installé une nouvelle version de Visual Studio et que le Blazor modèle Webassembly n’apparaît pas dans l’interface utilisateur vs, réinstallez le modèle à l’aide de la `dotnet new` commande indiquée précédemment.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Dans une interface de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Dans Visual Studio Code, ouvrez le dossier du projet de l’application.

1. Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**. Visual Studio Code ajoute automatiquement le dossier *. vscode* avec les *launch.js* générés sur et *tasks.jssur* les fichiers.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Installez la dernière version de [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/) et procédez comme suit :

1. Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.

1. Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.

1. Choisissez le modèle d' ** Blazor application webassembly** . Sélectionnez **Suivant**.

   Vérifiez les configurations suivantes :

   * **Framework cible** défini sur **.net Core 3,1**.
   * **L’authentification** est définie sur **aucune authentification**.

   Activez la case à cocher **ASP.net Core hébergé** .

   Sélectionnez **Suivant**.

1. Dans le champ **nom du projet** , nommez l’application `BlazorSignalRApp` . Sélectionnez **Create** (Créer).

   Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez. Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.

1. Ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet (*. sln*).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Ajouter la SignalR bibliothèque cliente

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur *NuGet.org*.

1. Avec l’option **Parcourir** sélectionnée, tapez «Microsoft. AspNetCore. SignalR . Client» dans la zone de recherche.

1. Dans les résultats de la recherche, sélectionnez [Microsoft. AspNetCore. SignalR ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)Et sélectionnez **installer**.

1. Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **Terminal intégré** (**Afficher**  >  **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source est définie sur *NuGet.org*.

1. Avec l’option **Parcourir** sélectionnée, tapez «Microsoft. AspNetCore. SignalR . Client» dans la zone de recherche.

1. Dans les résultats de la recherche, activez la case à cocher en regard de [Microsoft. AspNetCore. SignalR Package client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) et sélectionnez **Ajouter un package**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez les commandes suivantes :

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Ajouter un SignalR Hub

Dans le projet **BlazorSignalRApp. Server** , créez un dossier *hubs* (plural) et ajoutez la `ChatHub` classe suivante (*hubs/ChatHub. cs*) :

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Ajouter des services et un point de terminaison pour le SignalR Hub

1. Dans le projet **BlazorSignalRApp. Server** , ouvrez le fichier *Startup.cs* .

1. Ajoutez l’espace de noms de la `ChatHub` classe au début du fichier :

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Ajoutez SignalR et répondez les services d’intergiciel (middleware) de compression à `Startup.ConfigureServices` :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. Dans `Startup.Configure` :

   * Utilisez l’intergiciel de compression de réponse en haut de la configuration du pipeline de traitement.
   * Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a>Ajouter Razor du code de composant pour la conversation

1. Dans le projet **BlazorSignalRApp. client** , ouvrez le fichier *pages/index. Razor* .

1. Remplacez le balisage par le code suivant :

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Exécuter l’application

1. Suivez les instructions pour vos outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dans **Explorateur de solutions**, sélectionnez le projet **BlazorSignalRApp. Server** . Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Lorsque VS Code propose de créer un profil de lancement pour l’application serveur (*. vscode/launch.jssur*), l' `program` entrée ressemble à ce qui suit pour pointer vers l’assembly de l’application ( `{APPLICATION NAME}.Server.dll` ) :

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , sélectionnez le projet **BlazorSignalRApp. Server** . Appuyez sur <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application avec débogage ou <kbd>⌥</kbd> + <kbd>⌘</kbd> + <kbd>↩</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans une interface de commande, exécutez les commandes suivantes :

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![SignalRBlazorL’exemple d’application Webassembly est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis : *Star Trek VI : le pays indécouvert* &copy; 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un Blazor projet d’application hébergée Webassembly
> * Ajouter la SignalR bibliothèque cliente
> * Ajouter un SignalR Hub
> * Ajouter des SignalR services et un point de terminaison pour le SignalR Hub
> * Ajouter Razor du code de composant pour la conversation

Pour en savoir plus sur Blazor la création d’applications, consultez la Blazor documentation :

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/introduction>
* [SignalRnégociation Cross-Origin pour l’authentification](xref:blazor/hosting-model-configuration#signalr-cross-origin-negotiation-for-authentication)
