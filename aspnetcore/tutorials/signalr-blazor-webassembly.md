---
title: Utiliser ASP.NET Core SignalR avec Blazor webassembly
author: guardrex
description: Créer une application de conversation qui utilise SignalR ASP.net Core Blazor avec webassembly.
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773786"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Utiliser ASP.NET Core Signalr avec le webassembly éblouissant

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Ce didacticiel enseigne les bases de la création d’une application en temps réel à l’aide de Signalr avec le webassembly éblouissant. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un projet d’application hébergée webassembly éblouissant
> * Ajouter la bibliothèque de client SignalR
> * Ajouter un concentrateur Signalr
> * Ajouter des services Signalr et un point de terminaison pour le concentrateur Signalr
> * Ajouter du code de composant Razor pour la conversation

À la fin de ce didacticiel, vous disposerez d’une application de conversation de travail.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

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

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Créer un projet d’application webassembly éblouissant hébergé

Si vous n’utilisez pas Visual Studio version 16,6 Preview 2 ou une version ultérieure, installez le modèle de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) . Le package [Microsoft. AspNetCore. Components. Webassembly. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) a une préversion alors que le composant webassembly éblouissant est en version préliminaire. Dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

Suivez les instructions de votre choix d’outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Créez un projet.

1. Sélectionnez l' **application éblouissant** , puis sélectionnez **suivant**.

1. Tapez « BlazorSignalRApp » dans le champ **nom du projet** . Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Créer**.

1. Choisissez le modèle **application éblouissant Webassembly** .

1. Sous **avancé**, activez la case à cocher **ASP.net Core hébergé** .

1. Sélectionnez **Créer**.

> [!NOTE]
> Si vous avez mis à niveau ou installé une nouvelle version de Visual Studio et que le modèle éblouissant webassembly n’apparaît pas dans l’interface utilisateur VS, réinstallez le modèle à l’aide de la `dotnet new` commande indiquée précédemment.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Dans une interface de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Dans Visual Studio Code, ouvrez le dossier du projet de l’application.

1. Quand la boîte de dialogue semble ajouter des éléments multimédias pour générer et déboguer l’application, sélectionnez **Oui**. Visual Studio Code ajoute automatiquement le dossier *. vscode* avec les fichiers *Launch. JSON* et *Tasks. JSON* générés.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans une interface de commande, exécutez la commande suivante :

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Dans Visual Studio pour Mac, ouvrez le projet en accédant au dossier du projet et en ouvrant le fichier solution du projet (*. sln*).

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez la commande suivante :

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Ajouter la bibliothèque de client SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la **source du package** est définie sur *NuGet.org*.

1. Avec l’option **Parcourir** sélectionnée, tapez « Microsoft. AspNetCore. signalr. client » dans la zone de recherche.

1. Dans les résultats de la recherche, `Microsoft.AspNetCore.SignalR.Client` sélectionnez le package et sélectionnez **installer**.

1. Si la boîte de dialogue **aperçu des modifications** s’affiche, sélectionnez **OK**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **J’accepte** si vous acceptez les termes du contrat de licence.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Dans le **Terminal intégré** (**Afficher** > **Terminal** à partir de la barre d’outils), exécutez les commandes suivantes :

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , cliquez avec le bouton droit sur le projet **BlazorSignalRApp. client** , puis sélectionnez **gérer les packages NuGet**.

1. Dans la boîte de dialogue **gérer les packages NuGet** , vérifiez que la liste déroulante source est définie sur *NuGet.org*.

1. Avec l’option **Parcourir** sélectionnée, tapez « Microsoft. AspNetCore. signalr. client » dans la zone de recherche.

1. Dans les résultats de la recherche, activez la case à cocher en regard du `Microsoft.AspNetCore.SignalR.Client` package, puis sélectionnez Ajouter un **package**.

1. Si la boîte de dialogue acceptation de la **licence** s’affiche, sélectionnez **accepter** si vous acceptez les termes du contrat de licence.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Dans une interface de commande, exécutez les commandes suivantes :

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Ajouter un concentrateur Signalr

Dans le projet **BlazorSignalRApp. Server** , créez un dossier *hubs* (plural) et ajoutez la classe `ChatHub` suivante (*hubs/ChatHub. cs*) :

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Ajouter des services et un point de terminaison pour le concentrateur Signalr

1. Dans le projet **BlazorSignalRApp. Server** , ouvrez le fichier *Startup.cs* .

1. Ajoutez l’espace de noms `ChatHub` de la classe au début du fichier :

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Ajoutez des services d’intergiciel (middleware) de compression `Startup.ConfigureServices`de signalr et de réponse à :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. `Startup.Configure` Entre les points de terminaison pour les contrôleurs et le secours côté client, ajoutez un point de terminaison pour le Hub :

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>Ajouter du code de composant Razor pour la conversation

1. Dans le projet **BlazorSignalRApp. client** , ouvrez le fichier *pages/index. Razor* .

1. Remplacez le balisage par le code suivant :

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Exécuter l’application

1. Suivez les instructions pour vos outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Dans **Explorateur de solutions**, sélectionnez le projet **BlazorSignalRApp. Server** . Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd>+<kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Lorsque VS Code propose de créer un profil de lancement pour l’application serveur (*. vscode/Launch. JSON*), `program` l’entrée ressemble à ce qui suit pour pointer vers l’assembly`{APPLICATION NAME}.Server.dll`de l’application () :

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Appuyez sur <kbd>F5</kbd> pour exécuter l’application avec débogage ou sur <kbd>CTRL</kbd>+<kbd>F5</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Dans la barre latérale **solution** , sélectionnez le projet **BlazorSignalRApp. Server** . Appuyez sur <kbd>⌘</kbd>+<kbd>↩</kbd>* * pour exécuter l’application avec débogage ou <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> pour exécuter l’application sans débogage.

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Dans une interface de commande, exécutez les commandes suivantes :

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Copiez l’URL à partir de la barre d’adresse, ouvrez un autre onglet ou instance du navigateur, puis collez l’URL dans la barre d’adresse.

1. Choisissez l’un des navigateurs, entrez un nom et un message, puis sélectionnez le bouton **Envoyer**. Le nom et le message s’affichent instantanément sur les deux pages :

   ![L’exemple d’application signalr du webassembly éblouissant est ouvert dans deux fenêtres de navigateur qui affichent les messages échangés.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Devis &copy;: *Star Trek VI : le pays indécouvert* 1991 [primordial](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un Blazor projet d’application hébergée webassembly
> * Ajouter la SignalR bibliothèque cliente
> * Ajouter un SignalR Hub
> * Ajouter SignalR des services et un point de SignalR terminaison pour le Hub
> * Ajouter Razor du code de composant pour la conversation

Pour en savoir plus sur Blazor la création d’applications Blazor , consultez la documentation :

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:signalr/introduction>
