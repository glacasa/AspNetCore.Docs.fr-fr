---
title: Prise en main de ASP.NET CoreBlazor
author: guardrex
description: Commencez Blazor par créer une Blazor application avec les outils de votre choix.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111069"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Prise en main d’ASP.NET Core éblouissante

Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Pour vous familiariser avec éblouissant, suivez les instructions de votre choix d’outils :

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Pour créer des applications de serveur éblouissantes, installez la dernière version de [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail **développement Web et ASP.net** .

   Pour créer un serveur éblouissant et des applications webassembly éblouissantes, installez la dernière version préliminaire de [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) avec la charge de travail **développement Web et ASP.net** .

   Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *Webassembly éblouissant* et le <xref:blazor/hosting-models> *serveur éblouissant*, consultez.

1. Installez le modèle d’aperçu de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Créez un projet.

1. Sélectionnez l' **application éblouissant**. Sélectionnez **Suivant**.

1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Confirmez que l’entrée d' **emplacement** est correcte ou indiquez un emplacement pour le projet. Sélectionnez **Créer**.

1. Pour une expérience webassembly éblouissant (Visual Studio 16,6 Preview 2 ou version ultérieure), choisissez le modèle **application éblouissant Webassembly** . Pour une expérience de serveur éblouissant (Visual Studio 16,4 ou version ultérieure), choisissez le modèle **application de serveur éblouissant** . Sélectionnez **Créer**.

1. Appuyez sur <kbd>CTRL</kbd>+<kbd>F5</kbd> pour exécuter l’application.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installez le [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Si vous le souhaitez, vous pouvez installer le modèle d’aperçu de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > La [version kit SDK .net Core 3.1.201 ou ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **requise** pour utiliser le modèle webassembly 3,2 Preview 4. Confirmez la version de kit SDK .NET Core installée `dotnet --version` en exécutant dans un interpréteur de commandes.

1. Installez [Visual Studio code](https://code.visualstudio.com/).

1. Installez la dernière [extension C# for Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) et l’extension de [débogueur JavaScript (nocturne)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) avec `debug.javascript.usePreview` la valeur `true`.

1. Pour une expérience de serveur éblouissant, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Pour une expérience de webassembly éblouissant, exécutez la commande suivante dans une interface de commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *serveur éblouissant* et le *webassembly éblouissant*, consultez <xref:blazor/hosting-models>.

1. Ouvrez le dossier *WebApplication1* dans Visual Studio code.

1. L’IDE demande que vous ajoutiez des ressources pour générer et déboguer le projet. Sélectionnez **Oui**.

1. Avec le serveur éblouissant, exécutez l’application à l’aide du débogueur Visual Studio Code.

   Avec éblouissant webassembly, démarrez l’application à l’aide de la configuration de lancement de **.net Core (éblouissant)** , puis démarrez le navigateur à l’aide de l' **Assembly Web de débogage .net Core éblouissant dans** la configuration de lancement chrome (nécessite chrome). Pour plus d’informations, consultez <xref:blazor/debug#visual-studio-code>.

1. Dans un navigateur, accédez à `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Le serveur éblouissant est pris en charge dans Visual Studio pour Mac. Le webassembly éblouissant n’est pas pris en charge pour le moment. Pour générer des applications webassembly éblouissantes sur macOS, suivez les instructions de l’onglet **CLI .net Core** .

1. Installez [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Sélectionnez **fichier** > **nouvelle solution** ou créer un **nouveau projet**.

1. Dans la barre latérale, sélectionnez**application** **.net Core** > .

1. Sélectionnez le modèle **application de serveur éblouissant** . Sélectionnez **Créer**.

   Pour plus d’informations sur le modèle d’hébergement de serveur <xref:blazor/hosting-models>éblouissant, consultez.

1. Définissez la version **cible** de **.NET Framework sur .net Core 3,1** , puis sélectionnez **suivant**.

1. Dans le champ **nom du projet** , nommez `WebApplication1`l’application. Sélectionnez **Créer**.

1.  > Sélectionnez **exécuter****exécuter sans débogage** pour exécuter l’application *sans le débogueur*. Exécutez l’application avec **Démarrer le débogage** pour exécuter l’application *avec le débogueur*.

Si une invite s’affiche pour faire confiance au certificat de développement, approuvez le certificat et continuez.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

1. Installez le [Kit de développement logiciel (SDK) .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Si vous le souhaitez, vous pouvez installer le modèle d’aperçu de [Webassembly éblouissant](xref:blazor/hosting-models#blazor-webassembly) en exécutant la commande suivante :

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > La [version kit SDK .net Core 3.1.201 ou ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1) est **requise** pour utiliser le modèle webassembly 3,2 Preview 4. Confirmez la version de kit SDK .NET Core installée `dotnet --version` en exécutant dans un interpréteur de commandes.

1. Pour une expérience de serveur éblouissant, exécutez les commandes suivantes dans une interface de commande :

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pour une expérience de webassembly éblouissant, exécutez les commandes suivantes dans une interface de commande :

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Pour plus d’informations sur les deux modèles d’hébergement éblouissants, le *serveur éblouissant* et le *webassembly éblouissant*, consultez <xref:blazor/hosting-models>.

1. Dans un navigateur, accédez à `https://localhost:5001`.

---

Plusieurs pages sont disponibles à partir des onglets de la barre latérale :

* Accueil
* Compteur
* Extraire les données

Sur la page Counter, sélectionnez le bouton **Click me** pour incrémenter le compteur sans actualisation de la page. L’incrémentation d’un compteur dans une page Web nécessite normalement l’écriture Blazor de JavaScript, mais avec vous pouvez utiliser C#.

*Pages/Counter.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Une demande de `/counter` dans le navigateur, comme spécifié par la `@page` directive en haut, fait en sorte `Counter` que le composant restitue son contenu. Les composants sont rendus dans une représentation en mémoire de l’arborescence de rendu qui peut ensuite être utilisée pour mettre à jour l’interface utilisateur de manière flexible et efficace.

Chaque fois que le bouton **Click Me** est sélectionné :

* L' `onclick` événement est déclenché.
* La méthode `IncrementCount` est appelée.
* `currentCount` Est incrémenté.
* Le composant est de nouveau restitué.

Le runtime compare le nouveau contenu au contenu précédent et applique uniquement le contenu modifié à l’Document Object Model (DOM).

Ajoutez un composant à un autre composant à l’aide de la syntaxe HTML. Par exemple, ajoutez le `Counter` composant à la page d’accueil de l’application `<Counter />` en ajoutant un `Index` élément au composant.

*Pages/Index.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Exécutez l'application. La page d’accueil possède son propre compteur fourni `Counter` par le composant.

Les paramètres de composant sont spécifiés à l’aide d’attributs ou de [contenu enfant](xref:blazor/components#child-content), ce qui vous permet de définir des propriétés sur le composant enfant. Pour ajouter un paramètre au `Counter` composant, mettez à jour le bloc `@code` du composant :

* Ajoutez une propriété publique pour `IncrementAmount` avec un `[Parameter]` attribut.
* Modifiez la méthode `IncrementCount` pour utiliser `IncrementAmount` lorsque vous augmentez la valeur de `currentCount`.

*Pages/Counter.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Spécifiez `IncrementAmount` dans l' `Index` élément du `<Counter>` composant à l’aide d’un attribut.

*Pages/Index.razor* :

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Exécutez l'application. Le `Index` composant a son propre compteur qui est incrémenté de dix chaque fois que le bouton **Click Me** est sélectionné. Le `Counter` composant (*Counter. Razor*) de `/counter` continue à être incrémenté d’une unité.

## <a name="next-steps"></a>Étapes suivantes

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/templates>
* <xref:signalr/introduction>
