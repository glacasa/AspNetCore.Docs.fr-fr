---
title: RazorBibliothèques de classes des composants ASP.net Core
author: guardrex
description: Découvrez comment les composants peuvent être inclus dans des Blazor applications à partir d’une bibliothèque de composants externes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 8293d61f88f53e55d94b114ca2143fdfb6fd8468
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819065"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>RazorBibliothèques de classes des composants ASP.net Core

Par [Simon Timms](https://github.com/stimms)

Les composants peuvent être partagés dans une [ Razor bibliothèque de classes (RCL)](xref:razor-pages/ui-class) d’un projet à l’autre. Vous pouvez inclure une * Razor bibliothèque de classes de composants* à partir de :

* Un autre projet dans la solution.
* Package NuGet.
* Bibliothèque .NET référencée.

Tout comme les composants sont des types .NET standard, les composants fournis par un RCL sont des assemblys .NET normaux.

## <a name="create-an-rcl"></a>Créer un RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Créez un projet.
1. Sélectionnez ** Razor bibliothèque de classes**. Sélectionnez **Suivant**.
1. Dans la boîte de dialogue **créer une nouvelle Razor bibliothèque de classes** , sélectionnez **créer**.
1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Les exemples de cette rubrique utilisent le nom du projet `ComponentLibrary` . Sélectionnez **Create** (Créer).
1. Ajouter RCL à une solution :
   1. Cliquez avec le bouton droit sur la solution. Sélectionnez **Ajouter**un  >  **projet existant**.
   1. Accédez au fichier projet de RCL.
   1. Sélectionnez le fichier projet de RCL ( `.csproj` ).
1. Ajoutez une référence à l’RCL à partir de l’application :
   1. Cliquez avec le bouton droit sur le projet d’application. Sélectionnez **Ajouter**une  >  **référence**.
   1. Sélectionnez le projet RCL. Sélectionnez **OK**.

> [!NOTE]
> Si la case à cocher **pages et vues de support** est activée lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ajoutez manuellement le fichier à la racine du projet généré.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

1. Utilisez le modèle de ** Razor bibliothèque de classes** ( `razorclasslib` ) avec la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans un interpréteur de commandes. Dans l’exemple suivant, un RCL est créé nommé `ComponentLibrary` . Le dossier qui contient `ComponentLibrary` est créé automatiquement lors de l’exécution de la commande :

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Si le `-s|--support-pages-and-views` commutateur est utilisé lors de la génération du RCL à partir du modèle, ajoutez également un `_Imports.razor` fichier à la racine du projet généré avec le contenu suivant pour activer la Razor création de composants :
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ajoutez manuellement le fichier à la racine du projet généré.

1. Pour ajouter la bibliothèque à un projet existant, utilisez la [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) commande dans un interpréteur de commandes. Dans l’exemple suivant, le RCL est ajouté à l’application. Exécutez la commande suivante à partir du dossier du projet de l’application avec le chemin d’accès à la bibliothèque :

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consommer un composant de bibliothèque

Pour utiliser des composants définis dans une bibliothèque dans un autre projet, utilisez l’une des approches suivantes :

* Utilisez le nom de type complet avec l’espace de noms.
* Utilisez Razor [`@using`](xref:mvc/views/razor#using) la directive de. Des composants individuels peuvent être ajoutés par nom.

Dans les exemples suivants, `ComponentLibrary` est une bibliothèque de composants contenant le `Component1` composant ( `Component1.razor` ). Le `Component1` composant est un exemple de composant automatiquement ajouté par le modèle de projet RCL lors de la création de la bibliothèque.

Référencez le `Component1` composant à l’aide de son espace de noms :

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Vous pouvez également placer la bibliothèque dans la portée avec une [`@using`](xref:mvc/views/razor#using) directive et utiliser le composant sans son espace de noms :

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Si vous le souhaitez, incluez la `@using ComponentLibrary` directive dans le fichier de niveau supérieur `_Import.razor` pour mettre les composants de la bibliothèque à la disposition d’un projet entier. Ajoutez la directive à un `_Import.razor` fichier à tout niveau pour appliquer l’espace de noms à un composant unique ou à un ensemble de composants dans un dossier.

::: moniker range=">= aspnetcore-5.0"

Pour fournir `Component1` `my-component` la classe CSS du composant, liez-le à la feuille de style de la bibliothèque à l’aide du [ `Link` composant](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) de l’infrastructure dans `Component1.razor` :

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

Pour fournir la feuille de style à l’ensemble de l’application, vous pouvez également créer un lien vers la feuille de style de la bibliothèque dans le fichier `wwwroot/index.html` () ou le fichier de l’application Blazor WebAssembly `Pages/_Host.cshtml` ( Blazor Server ) :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

Lorsque le `Link` composant est utilisé dans un composant enfant, la ressource liée est également disponible pour tout autre composant enfant du composant parent, à condition que l’enfant avec le `Link` composant soit rendu. La distinction entre l’utilisation du `Link` composant dans un composant enfant et la mise en place d’une `<link>` balise HTML dans `wwwroot/index.html` ou `Pages/_Host.cshtml` est que la balise HTML rendue d’un composant de l’infrastructure :

* Peut être modifié par l’état de l’application. Une `<link>` balise HTML codée en dur ne peut pas être modifiée par l’état de l’application.
* Est supprimé du code HTML `<head>` lorsque le composant parent n’est plus rendu.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Pour fournir `Component1` la `my-component` classe CSS de, liez-la à la feuille de style de la bibliothèque dans le fichier `wwwroot/index.html` () ou le fichier de l’application Blazor WebAssembly `Pages/_Host.cshtml` ( Blazor Server ) :

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Créer une Razor bibliothèque de classes de composants avec des ressources statiques

Un RCL peut inclure des ressources statiques. Les ressources statiques sont disponibles pour toutes les applications qui consomment la bibliothèque. Pour plus d'informations, consultez <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Fournir des composants et des ressources statiques à plusieurs applications hébergées Blazor

Pour plus d'informations, consultez <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

## <a name="build-pack-and-ship-to-nuget"></a>Générer, empaqueter et envoyer à NuGet

Étant donné que les bibliothèques de composants sont des bibliothèques .NET standard, leur empaquetage et leur envoi à NuGet ne sont pas différents de l’empaquetage et de l’expédition d’une bibliothèque à NuGet. L’empaquetage est effectué à l’aide [`dotnet pack`](/dotnet/core/tools/dotnet-pack) de la commande dans une interface de commande :

```dotnetcli
dotnet pack
```

Téléchargez le package dans NuGet à l’aide de la [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) commande dans un interpréteur de commandes.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/ui-class>
* [Ajouter un fichier de configuration de l’éditeur de liens XML à une bibliothèque](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
