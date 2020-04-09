---
title: ASP.NET Core Razor components classe bibliothèques
author: guardrex
description: Découvrez comment les composants Blazor peuvent être inclus dans les applications d’une bibliothèque de composants externes.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218764"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor components classe bibliothèques

Par [Simon Timms](https://github.com/stimms)

Les composants peuvent être partagés dans une [bibliothèque de classe Razor (RCL)](xref:razor-pages/ui-class) dans tous les projets. Une *bibliothèque de classe de composants Razor* peut être incluse à partir de :

* Un autre projet dans la solution.
* Un paquet NuGet.
* Une bibliothèque .NET référencée.

Tout comme les composants sont des types réguliers .NET, les composants fournis par un RCL sont des assemblages .NET normaux.

## <a name="create-an-rcl"></a>Créer un RCL

Suivez les conseils <xref:blazor/get-started> dans l’article pour configurer votre environnement pour Blazor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Créez un projet.
1. Sélectionnez **Razor Class Library**. Sélectionnez **Suivant**.
1. Dans le Créer un nouveau dialogue **de bibliothèque de classe Razor,** sélectionnez **Créer**.
1. Indiquez un nom de projet dans le champ **Nom du projet**, ou acceptez le nom de projet par défaut. Les exemples dans ce `MyComponentLib1`sujet utilisent le nom du projet . Sélectionnez **Create** (Créer).
1. Ajouter le RCL à une solution :
   1. Cliquez à droite sur la solution. Sélectionnez **Ajouter** > **le projet existant**.
   1. Naviguez vers le dossier de projet du RCL.
   1. Sélectionnez le fichier de projet de la RCL (*.csproj*).
1. Ajoutez une référence le RCL de l’application :
   1. Cliquez à droite sur le projet d’application. Sélectionnez **Ajouter la** > **référence**.
   1. Sélectionnez le projet RCL. Sélectionnez **OK**.

> [!NOTE]
> Si les **pages de support et** la case de cocher vues sont sélectionnées lors de la génération du RCL à partir du modèle, ajoutez également un fichier *_Imports.razor* à la racine du projet généré avec le contenu suivant pour permettre l’auteur du composant Razor :
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Ajouter manuellement le fichier à la racine du projet généré.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli)

1. Utilisez le modèle Razor`razorclasslib`Class **Library** ( ) avec la nouvelle commande [dotnet](/dotnet/core/tools/dotnet-new) dans une coque de commande. Dans l’exemple suivant, un `MyComponentLib1`RCL est créé nommé . Le dossier qui `MyComponentLib1` tient est créé automatiquement lorsque la commande est exécutée :

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > Si `-s|--support-pages-and-views` le commutateur est utilisé lors de la génération de la RCL à partir du modèle, puis aussi ajouter un fichier *_Imports.razor* à la racine du projet généré avec le contenu suivant pour permettre l’auteur du composant Razor:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Ajouter manuellement le fichier à la racine du projet généré.

1. Pour ajouter la bibliothèque à un projet existant, utilisez le [dotnet ajouter](/dotnet/core/tools/dotnet-add-reference) la commande de référence dans une coque de commande. Dans l’exemple suivant, le RCL est ajouté à l’application. Exécutez la commande suivante à partir du dossier de projet de l’application avec le chemin vers la bibliothèque :

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Consommer un composant de bibliothèque

Afin de consommer des composants définis dans une bibliothèque dans un autre projet, utilisez l’une ou l’autre des approches suivantes :

* Utilisez le nom de type complet avec l’espace nom.
* Utilisez la directive de [ \@Razor.](xref:mvc/views/razor#using) Les composants individuels peuvent être ajoutés par leur nom.

Dans les exemples suivants, `MyComponentLib1` est `SalesReport` une bibliothèque de composants contenant un composant.

Le `SalesReport` composant peut être référencé à l’aide de son nom de type complet avec namespace:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

Le composant peut également être référencé si `@using` la bibliothèque est mise en œuvre avec une directive :

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

Inclure `@using MyComponentLib1` la directive dans le fichier *_Import.razor* de haut niveau pour mettre les composants de la bibliothèque à la disposition de l’ensemble d’un projet. Ajoutez la directive à un fichier *_Import.razor* à n’importe quel niveau pour appliquer l’espace de nom à une seule page ou un ensemble de pages dans un dossier.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>Créer une bibliothèque de classe de composants Razor avec des actifs statiques

Un RCL peut inclure des actifs statiques. Les actifs statiques sont disponibles pour toute application qui consomme la bibliothèque. Pour plus d’informations, consultez <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="build-pack-and-ship-to-nuget"></a>Construire, emballer et expédier à NuGet

Parce que les bibliothèques de composants sont des bibliothèques .NET standard, les emballages et les expédier à NuGet n’est pas différent de l’emballage et l’expédition d’une bibliothèque à NuGet. L’emballage est effectué à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack) dans une coque de commande:

```dotnetcli
dotnet pack
```

Téléchargez le paquet sur NuGet à l’aide de la commande [de poussée de nuget de pointnet](/dotnet/core/tools/dotnet-nuget-push) dans une coque de commande.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:razor-pages/ui-class>
* [Ajouter un fichier de configuration de liaison XML à une bibliothèque](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
