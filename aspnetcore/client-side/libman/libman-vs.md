---
title: Utilisez LibMan avec ASP.NET Core dans Visual Studio
author: scottaddie
description: Apprenez à utiliser LibMan dans un projet ASP.NET Core avec Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658311"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Utilisez LibMan avec ASP.NET Core dans Visual Studio

Par [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio a intégré le soutien de [LibMan](xref:client-side/libman/index) dans ASP.NET projets de base, notamment :

* Soutien à la configuration et à la gestion de LibMan restaurer les opérations sur la construction.
* Éléments de menu pour déclencher LibMan restaurer et nettoyer les opérations.
* Rechercher le dialogue pour trouver des bibliothèques et ajouter les fichiers à un projet.
* Modification du support pour *libman.json*&mdash;le fichier manifeste LibMan.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(comment télécharger)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Prérequis

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) avec la charge **de travail ASP.NET et le développement web**

## <a name="add-library-files"></a>Ajouter des fichiers de bibliothèque

Les dossiers de bibliothèque peuvent être ajoutés à un projet ASP.NET Core de deux façons différentes :

1. [Utilisez le dialogue Add Client-Side Library](#use-the-add-client-side-library-dialog)
1. [Configurer manuellement les entrées de fichiers manifestes LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Utilisez le dialogue Add Client-Side Library

Suivez ces étapes pour installer une bibliothèque côté client :

* Dans **Solution Explorer**, cliquez à droite sur le dossier de projet dans lequel les fichiers doivent être ajoutés. Choisissez **Ajouter** > **la bibliothèque Client-Side**. Le **dialogue Add Client-Side Library** apparaît :

  ![Ajouter le dialogue de la bibliothèque client-côté](_static/add-library-dialog.png)

* Sélectionnez le fournisseur de bibliothèque du **fournisseur** déposer. CDNJS est le fournisseur par défaut.
* Tapez le nom de la bibliothèque pour aller chercher dans la boîte à texte **de la Bibliothèque.** IntelliSense fournit une liste de bibliothèques commençant par le texte fourni.
* Sélectionnez la bibliothèque de la liste IntelliSense. Notez que le nom de `@` la bibliothèque est suffisant avec le symbole et la dernière version stable connue du fournisseur sélectionné.
* Décider quels fichiers inclure :
  * Sélectionnez **le bouton Inclure tous les fichiers** de bibliothèque pour inclure tous les fichiers de la bibliothèque.
  * Sélectionnez le bouton radio **De fichiers spécifiques Choisir** pour inclure un sous-ensemble des fichiers de la bibliothèque. Lorsque le bouton radio est sélectionné, l’arbre du sélecteur de fichiers est activé. Cochez les cases à gauche des noms de fichiers à télécharger.
* Spécifiez le dossier de projet pour stocker les fichiers dans la boîte de texte **Target Location.** Par recommandation, entreposez chaque bibliothèque dans un dossier séparé.

  Le dossier **Target Location** suggéré est basé sur l’emplacement à partir duquel le dialogue a été lancé :

  * Si elle est lancée à partir de la racine du projet :
    * *wwwroot/lib* est utilisé si *wwwroot* existe.
    * *lib* est utilisé si *wwwroot* n’existe pas.
  * Si elle est lancée à partir d’un dossier de projet, le nom correspondant du dossier est utilisé.

  La suggestion de dossier est suffixe avec le nom de la bibliothèque. Le tableau suivant illustre les suggestions de dossier lors de l’installation de jQuery dans un projet Razor Pages.
  
  |Emplacement de lancement                           |Dossier suggéré      |
  |------------------------------------------|----------------------|
  |racine du projet (si *wwwroot* existe)        |*wwwroot/lib/jquery/* |
  |racine du projet (si *wwwroot* n’existe pas) |*lib/jquery/*         |
  |*Dossier de pages* dans le projet                 |*Pages/jquery/*       |

* Cliquez sur le bouton **Installer** pour télécharger les fichiers, par configuration dans *libman.json*.
* Examinez l’alimentation **du gestionnaire** de bibliothèque de la fenêtre de sortie pour obtenir **des** détails d’installation. Par exemple :

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>Configurer manuellement les entrées de fichiers manifestes LibMan

Toutes les opérations LibMan dans Visual Studio sont basées sur le contenu du manifeste LibMan de la racine du projet (*libman.json*). Vous pouvez modifier manuellement *libman.json* pour configurer les fichiers de bibliothèque pour le projet. Visual Studio restaure tous les fichiers de bibliothèque une fois *que libman.json* est enregistré.

Pour ouvrir *libman.json* pour l’édition, les options suivantes existent:

* Double-cliquez sur le fichier *libman.json* dans **Solution Explorer**.
* Cliquez à droite sur le projet dans **Solution Explorer** et **sélectionnez Gérer les bibliothèques client-côté**. **&#8224;**
* Sélectionnez **Gérer les bibliothèques client-côté** dans le menu Visual Studio **Project.** **&#8224;**

**&#8224;** Si le fichier *libman.json* n’existe pas déjà dans la racine du projet, il sera créé avec le contenu du modèle d’élément par défaut.

Visual Studio offre un support d’édition JSON riche tels que la colorisation, le formatage, IntelliSense, et la validation de schémas. Le schéma JSON du manifeste LibMan [https://json.schemastore.org/libman](https://json.schemastore.org/libman)se trouve à .

Avec le fichier manifeste suivant, LibMan récupère les `libraries` fichiers par configuration définie dans la propriété. Une explication des littérals `libraries` d’objet définis dans les suivants :

* Un sous-ensemble de [version jQuery](https://jquery.com/) 3.3.1 est récupéré auprès du fournisseur CDNJS. Le sous-ensemble est `files` &mdash;défini dans la propriété*jquery.min.js*, *jquery.js*, et *jquery.min.map*. Les fichiers sont placés dans le dossier *wwwroot/lib/jquery* du projet.
* L’intégralité de la version [Bootstrap](https://getbootstrap.com/) 4.1.3 est récupérée et placée dans un dossier *wwwroot/lib/bootstrap.* La propriété littérale `provider` de l’objet `defaultProvider` l’emporte sur la valeur de la propriété. LibMan récupère les fichiers Bootstrap du fournisseur de dépkg.
* Un sous-ensemble de [Lodash](https://lodash.com/) a été approuvé par un organe directeur au sein de l’organisation. Les fichiers *lodash.js* et *lodash.min.js* sont récupérés à partir du système de fichiers local à *C:\\temp\\lodash\\*. Les fichiers sont copiés sur le dossier *wwwroot/lib/lodash* du projet.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan ne prend en charge qu’une version de chaque bibliothèque de chaque fournisseur. Le fichier *libman.json* échoue à la validation du schéma s’il contient deux bibliothèques avec le même nom de bibliothèque pour un fournisseur donné.

## <a name="restore-library-files"></a>Restaurer les fichiers de la bibliothèque

Pour restaurer les fichiers de bibliothèque à partir de Visual Studio, il doit y avoir un fichier *libman.json* valide dans la racine du projet. Les fichiers restaurés sont placés dans le projet à l’endroit spécifié pour chaque bibliothèque.

Les dossiers de bibliothèque peuvent être restaurés dans le cadre d’un projet ASP.NET Core de deux façons :

1. [Restaurer les fichiers pendant la construction](#restore-files-during-build)
1. [Restaurer manuellement les fichiers](#restore-files-manually)

### <a name="restore-files-during-build"></a>Restaurer les fichiers pendant la construction

LibMan peut restaurer les fichiers de bibliothèque définis dans le cadre du processus de construction. Par défaut, le comportement *de restauration sur la construction* est désactivé.

Pour activer et tester le comportement de restauration sur la construction :

* Clic droit *libman.json* dans **Solution Explorer** et **sélectionnez Enable Restore Client-Side Libraries on Build** à partir du menu context.
* Cliquez sur le bouton **Oui** lorsqu’on vous demande d’installer un forfait NuGet. Le paquet [NuGet Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) est ajouté au projet :

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Construire le projet pour confirmer la restauration du fichier LibMan se produit. Le `Microsoft.Web.LibraryManager.Build` paquet injecte une cible MSBuild qui exécute LibMan pendant l’opération de construction du projet.
* Passez en revue le flux **Build** de la fenêtre **de sortie** pour un journal d’activité LibMan :

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Lorsque le comportement de restauration sur la construction est activé, le menu de contexte *libman.json* affiche une option **Disable Restore Client-Side Libraries on Build.** La sélection de cette `Microsoft.Web.LibraryManager.Build` option supprime la référence du paquet du fichier de projet. Par conséquent, les bibliothèques côté client ne sont plus restaurées sur chaque construction.

Indépendamment du réglage de restauration sur la construction, vous pouvez restaurer manuellement à tout moment à partir du menu de contexte *libman.json.* Pour plus d’informations, voir [Restaurer les fichiers manuellement](#restore-files-manually).

### <a name="restore-files-manually"></a>Restaurer manuellement les fichiers

Pour restaurer manuellement les fichiers de bibliothèque :

* Pour tous les projets de la solution :
  * Cliquez à droite sur le nom de la solution dans **Solution Explorer**.
  * Sélectionnez l’option Restaurer les **bibliothèques côté client.**
* Pour un projet spécifique :
  * Cliquez à droite sur le fichier *libman.json* dans **Solution Explorer**.
  * Sélectionnez l’option Restaurer les **bibliothèques côté client.**

Pendant que l’opération de restauration est en cours d’exécution :

* L’icône du Task Status Center (TSC) sur la barre d’état Visual Studio sera animée et lira *l’opération Restaurer commencée.* En cliquant sur l’icône ouvre une boîte à outils énumérant les tâches d’arrière-plan connues.
* Les messages seront envoyés à la barre d’état et au flux de la fenêtre **De sortie** du gestionnaire de **bibliothèque.** Par exemple :

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Supprimer les fichiers de bibliothèque

Pour effectuer l’opération *propre,* qui supprime les fichiers de bibliothèque précédemment restaurés dans Visual Studio :

* Cliquez à droite sur le fichier *libman.json* dans **Solution Explorer**.
* Sélectionnez l’option **Bibliothèques côté client propre.**

Pour empêcher la suppression involontaire de fichiers non-bibliothèque, l’opération propre ne supprime pas les répertoires entiers. Il supprime uniquement les fichiers qui ont été inclus dans la restauration précédente.

Pendant que l’opération propre est en cours d’exécution:

* L’icône TSC sur le bar d’état Visual Studio sera animée et lira *l’opération des bibliothèques clientes commencée.* En cliquant sur l’icône ouvre une boîte à outils énumérant les tâches d’arrière-plan connues.
* Les messages sont envoyés à la barre d’état et au flux **library Manager** de la fenêtre **De sortie.** Par exemple :

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

L’opération propre ne supprime que les fichiers du projet. Les fichiers de bibliothèque restent dans le cache pour une récupération plus rapide sur les opérations de restauration futures. Pour gérer les fichiers de bibliothèque stockés dans le cache de la machine locale, utilisez le [LibMan CLI](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Désinstaller les fichiers de bibliothèque

Pour désinstaller les fichiers de bibliothèque :

* Ouvrez *libman.json*.
* Placez le caret `libraries` à l’intérieur de l’objet correspondant littéral.
* Cliquez sur l’icône de l’ampoule qui apparaît dans la marge gauche, et **sélectionnez Uninstall \<library_name>\<library_version>**:

  ![Désinstaller l’option de menu contextuelle de bibliothèque](_static/uninstall-menu-option.png)

Alternativement, vous pouvez modifier manuellement et enregistrer le manifeste LibMan (*libman.json*). [L’opération de restauration s’exécute](#restore-library-files) lorsque le fichier est enregistré. Les fichiers de bibliothèque qui ne sont plus définis dans *libman.json* sont supprimés du projet.

## <a name="update-library-version"></a>Mettre à jour la version de la bibliothèque

Pour vérifier une version de bibliothèque mise à jour :

* Ouvrez *libman.json*.
* Placez le caret `libraries` à l’intérieur de l’objet correspondant littéral.
* Cliquez sur l’icône de l’ampoule qui apparaît dans la marge gauche. Planer au-dessus **de vérifier pour les mises à jour**.

LibMan vérifie une version bibliothèque plus récente que la version installée. Les résultats suivants peuvent se produire :

* Un aucun message **trouvé de mise à jour** est affiché si la dernière version est déjà installée.
* La dernière version stable est affichée si elle n’est pas déjà installée.

  ![Vérifiez l’option de menu contextuelle mises à jour](_static/update-menu-option.png)

* Si une version préalable plus récente que la version installée est disponible, la pré-version s’affiche.

Pour rétrograder à une ancienne version de bibliothèque, modifiez manuellement le fichier *libman.json.* Lorsque le fichier est enregistré, le LibMan [restaurent l’opération](#restore-library-files):

* Supprime les fichiers redondants de la version précédente.
* Ajoute des fichiers nouveaux et mis à jour de la nouvelle version.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:client-side/libman/libman-cli>
* [Dépôt GitHub LibMan](https://github.com/aspnet/LibraryManager)
