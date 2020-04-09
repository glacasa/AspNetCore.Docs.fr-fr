---
title: Démarrer avec ASP.NET CoreBlazor
author: guardrex
description: Démarssez-vous Blazor Blazor par la construction d’une application avec l’outillage de votre choix.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: c49209afde21046a6bc0b197cc4b8d93b164b23e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80471816"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Lancez-vous avec ASP.NET Core Blazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pour commencer avec Blazor, suivez les conseils pour votre choix d’outillage:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Pour créer des applications Blazor Server, installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **ASP.NET et le développement web.**

   Pour créer des applications Blazor Server et Blazor WebAssembly, installez le dernier aperçu de [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) avec la charge de travail **ASP.NET et le développement web.**

   Pour plus d’informations sur les deux modèles d’hébergement Blazor, <xref:blazor/hosting-models> *Blazor WebAssembly* et *Blazor Server*, voir .

1. Créez un projet.

1. Sélectionnez **Blazor App**. Sélectionnez **Suivant**.

1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Confirmez que **l’entrée de localisation** est correcte ou fournissez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Pour une expérience WebAssembly Blazor (Visual Studio 16.6 Aperçu 2 ou plus tard), choisissez le modèle **Blazor WebAssembly App.** Pour une expérience Blazor Server (Visual Studio 16.4 ou plus tard), choisissez le modèle **Blazor Server App.** Sélectionnez **Create** (Créer).

1. Appuyez sur <kbd>Ctrl</kbd>+<kbd>F5</kbd> pour exécuter l’application.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installer le [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Installez le modèle [d’aperçu WebAssembly De Blazor](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > La [version SDK Core .NET 3.1.201 ou plus tard](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **nécessaire** pour utiliser le modèle WebAssembly 3.2 Preview 3 Blazor. Confirmez la version SDK core `dotnet --version` .NET installée en exécutant dans une coque de commande.

1. Installer [Visual Studio Code](https://code.visualstudio.com/).

1. Installez le dernier [C pour l’extension visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` ensemble à `true`.

1. Pour une expérience Blazor Server, exécutez la commande suivante dans une coque de commande :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pour une expérience Blazor WebAssembly, exécutez la commande suivante dans une coque de commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Pour plus d’informations sur les deux modèles d’hébergement Blazor, <xref:blazor/hosting-models> *Blazor Server* et *Blazor WebAssembly*, voir .

1. Ouvrez le dossier *WebApplication1* dans Visual Studio Code.

1. L’IDE vous demande d’ajouter des actifs pour construire et déboquer le projet. Sélectionnez **Oui**.

1. Exécutez l’application à l’aide du débbugger Visual Studio Code.

1. Dans un navigateur, accédez à `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Blazor Server est pris en charge dans Visual Studio pour Mac. Blazor WebAssembly n’est pas pris en charge pour le moment. Pour créer des applications Blazor WebAssembly sur macOS, suivez les conseils sur l’onglet **CLI Core .NET.**

1. Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Sélectionnez **File** > **New Solution** ou créez un nouveau **projet**.

1. Dans la barre latérale, sélectionnez **.NET Core** > **App**.

1. Sélectionnez le modèle **Blazor Server App.** Sélectionnez **Create** (Créer).

   Pour plus d’informations sur le <xref:blazor/hosting-models>modèle d’hébergement Blazor Server, voir .

1. Définissez le **cadre cible** à **.NET Core 3.1** et **sélectionnez Next**.

1. Dans le domaine du nom `WebApplication1`de **projet,** nommez l’application . Sélectionnez **Create** (Créer).

1. Sélectionnez **Run** > **Run Without Debugging** pour exécuter l’application sans le *débbugger*. Exécutez l’application avec **Start Debugging** pour exécuter l’application *avec le débogage*.

Si une invite semble faire confiance au certificat de développement, faites confiance au certificat et continuez.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Installer le [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Installez le modèle [d’aperçu WebAssembly De Blazor](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview3.20168.3
   ```

   > [!NOTE]
   > La [version SDK Core .NET 3.1.201 ou plus tard](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **nécessaire** pour utiliser le modèle WebAssembly 3.2 Preview 3 Blazor. Confirmez la version SDK core `dotnet --version` .NET installée en exécutant dans une coque de commande.

1. Pour une expérience Blazor Server, exécutez les commandes suivantes dans une coque de commande :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pour une expérience Blazor WebAssembly, exécutez les commandes suivantes dans une coque de commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pour plus d’informations sur les deux modèles d’hébergement Blazor, <xref:blazor/hosting-models> *Blazor Server* et *Blazor WebAssembly*, voir .

1. Dans un navigateur, accédez à `https://localhost:5001`.

---

Plusieurs pages sont disponibles à partir des onglets dans la barre latérale:

* Accueil
* Counter
* Aller chercher des données

Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page. Incrémenter un compteur dans une page Web nécessite Blazor normalement l’écriture JavaScript, mais avec vous pouvez utiliser C .

*Pages/Counter.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Une demande `/counter` dans le navigateur, `@page` comme spécifié par la `Counter` directive en haut, provoque le composant à rendre son contenu. Les composants se transforment en une représentation en mémoire de l’arbre de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur d’une manière flexible et efficace.

Chaque fois que le bouton **Cliquez sur moi** est sélectionné :

* L’événement `onclick` est déclenché.
* La méthode `IncrementCount` est appelée.
* Le `currentCount` est incrémenté.
* Le composant est rendu à nouveau.

Le temps d’exécution compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié au modèle d’objet de document (DOM).

Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML. Par exemple, `Counter` ajoutez le composant à la page `<Counter />` d’accueil de l’application en ajoutant un élément au `Index` composant.

*Pages/Index.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Exécutez l'application. La page d’accueil a `Counter` son propre compteur fourni par le composant.

Les paramètres des composants sont spécifiés à l’aide d’attributs ou [de contenu pour enfants,](xref:blazor/components#child-content)qui vous permettent de définir des propriétés sur la composante enfant. Pour ajouter un `Counter` paramètre au composant, `@code` mettez à jour le bloc du composant :

* Ajoutez une propriété `IncrementAmount` publique `[Parameter]` pour un attribut.
* Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.

*Pages/Counter.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Spécifier l’élément `IncrementAmount` de l’élément de `Index` `<Counter>` l’élément à l’aide d’un attribut.

*Pages/Index.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Exécutez l'application. Le `Index` composant a son propre compteur qui incréments par dix chaque fois que le bouton **Cliquez sur moi** est sélectionné. Le `Counter` composant (*Counter.razor*) à `/counter` continue d’incrément par un.

## <a name="next-steps"></a>Étapes suivantes

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/templates>
* <xref:signalr/introduction>
