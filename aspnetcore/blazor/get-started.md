---
title: Prise en main de ASP.NET CoreBlazor
author: guardrex
description: Commencez Blazor par créer une Blazor application avec les outils de votre choix.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 1eabc35175d1b696de99488981b1382d231f5544
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402778"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Prise en main de ASP.NET CoreBlazor

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

Pour commencer à utiliser Blazor , suivez les instructions de votre choix d’outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .

1. Créez un projet.

1. Sélectionnez ** Blazor application**. Sélectionnez **Suivant**.

1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Create** (Créer).

1. Pour une Blazor WebAssembly expérience, choisissez le modèle d' ** Blazor WebAssembly application** . Pour une Blazor Server expérience, choisissez le modèle d' ** Blazor Server application** . Sélectionnez **Create** (Créer).

   Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installez la dernière version du [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet --version
   ```

1. Installez la dernière version de [Visual Studio code](https://code.visualstudio.com/).

1. Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec la `debug.javascript.usePreview` valeur `true` .

  Pour définir `debug.javascript.usePreview` à `true` l’aide de l’interface utilisateur vs code, ouvrez paramètres des préférences de **fichier**  >  **Preferences**  >  **Settings** et recherchez `debug javascript use preview` . Activez la case à cocher **utiliser le nouveau débogueur JavaScript en préversion pour Node.js et chrome**.

1. Pour une Blazor WebAssembly expérience, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Pour une Blazor Server expérience, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Ouvrez le dossier `WebApplication1` dans Visual Studio Code.

1. L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet. Sélectionnez **Oui**.

1. Appuyez sur <kbd>CTRL</kbd> + <kbd>F5</kbd> pour exécuter l’application.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Sélectionnez **fichier**  >  **nouvelle solution** ou créer un **nouveau** projet à partir de la **fenêtre démarrer**.

1. Dans la barre latérale, sélectionnez application **Web et console**  >  **App**.

   Pour une Blazor WebAssembly expérience, choisissez le modèle d' ** Blazor WebAssembly application** . Pour une Blazor Server expérience, choisissez le modèle d' ** Blazor Server application** . Sélectionnez **Suivant**.

   Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Vérifiez les configurations suivantes :

   * **Framework cible** défini sur **.net Core 3,1**.
   * **L’authentification** est définie sur **aucune authentification**.
   
   Sélectionnez **Suivant**.

1. Dans le champ **nom du projet** , nommez l’application `WebApplication1` . Sélectionnez **Create** (Créer).

1. Sélectionnez **exécuter**  >  **Démarrer sans débogage** pour exécuter l’application *sans le débogueur*. Exécutez l’application avec **Run**  >  le bouton exécuter**Démarrer le débogage** ou exécuter (&#9654;) pour exécuter l’application *avec le débogueur*.

Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez. Les mots de passe utilisateur et trousseau sont requis pour approuver le certificat.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Installez la dernière version du [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Si vous avez déjà installé le kit de développement logiciel (SDK), vous pouvez déterminer la version installée en exécutant la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet --version
   ```

1. Pour une Blazor WebAssembly expérience, exécutez les commandes suivantes dans un interpréteur de commandes :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pour une Blazor Server expérience, exécutez les commandes suivantes dans un interpréteur de commandes :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pour plus d’informations sur les deux Blazor modèles d’hébergement, *Blazor WebAssembly* et *Blazor Server* , consultez <xref:blazor/hosting-models> .

1. Dans un navigateur, accédez à `https://localhost:5001`.

---

Plusieurs pages sont disponibles à partir des onglets de la barre latérale :

* Accueil
* Counter
* Extraire les données

Sur la page compteur, sélectionnez le bouton pour incrémenter le compteur sans actualiser la page. L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture de JavaScript, mais avec Blazor vous pouvez utiliser C#.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Une demande de `/counter` dans le navigateur, comme spécifié par la `@page` directive en haut, fait en sorte que le `Counter` composant restitue son contenu. Les composants sont rendus dans une représentation en mémoire de l’arborescence de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.

À chaque fois que le bouton est sélectionné :

* L' `onclick` événement est déclenché.
* La méthode `IncrementCount` est appelée.
* `currentCount`Est incrémenté.
* Le composant est de nouveau restitué.

Le runtime compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié à l’Document Object Model (DOM).

Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML. Par exemple, ajoutez le `Counter` composant à la page d’accueil de l’application en ajoutant un `<Counter />` élément au `Index` composant.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Exécutez l'application. La page d’accueil possède son propre compteur fourni par le `Counter` composant.

Les paramètres de composant sont spécifiés à l’aide d’attributs ou de [contenu enfant](xref:blazor/components/index#child-content), ce qui vous permet de définir des propriétés sur le composant enfant. Pour ajouter un paramètre au `Counter` composant, mettez à jour le bloc du composant `@code` :

* Ajoutez une propriété publique pour `IncrementAmount` avec un [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribut.
* Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Spécifiez `IncrementAmount` dans l' `Index` élément du composant `<Counter>` à l’aide d’un attribut.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Exécutez l'application. Le `Index` composant a son propre compteur qui est incrémenté de dix chaque fois que le bouton est sélectionné. Le `Counter` composant ( `Pages/Counter.razor` ) de `/counter` continue à être incrémenté d’une unité.

## <a name="next-steps"></a>Étapes suivantes

Créer une Blazor application pas à pas :

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/templates>
* <xref:signalr/introduction>
