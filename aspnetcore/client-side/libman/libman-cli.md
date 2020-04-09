---
title: Utilisez l’ICI LibMan avec ASP.NET Core
author: scottaddie
description: Apprenez à utiliser le CLI LibMan dans un projet ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664632"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Utilisez l’ICI LibMan avec ASP.NET Core

Par [Scott Addie](https://twitter.com/Scott_Addie)

Le [LibMan](xref:client-side/libman/index) CLI est un outil multiplateforme qui est pris en charge partout .NET Core est pris en charge.

## <a name="prerequisites"></a>Prérequis

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Installation

Pour installer le CLI LibMan :

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Un [outil mondial de base .NET](/dotnet/core/tools/global-tools#install-a-global-tool) est installé à partir du package [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet.

Pour installer le CLI LibMan à partir d’une source spécifique de paquet NuGet :

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Dans l’exemple précédent, un outil mondial .NET Core est installé à partir de la machine Windows locale *C: 'Temp’Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* fichier.

## <a name="usage"></a>Usage

Après l’installation réussie du CLI, la commande suivante peut être utilisée :

```console
libman
```

Pour voir la version CLI installée :

```console
libman --version
```

Pour afficher les commandes CLI disponibles :

```console
libman --help
```

La commande précédente affiche la sortie similaire à la suivante :

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

Les sections suivantes décrivent les commandes CLI disponibles.

## <a name="initialize-libman-in-the-project"></a>Initialiser LibMan dans le projet

La `libman init` commande crée un fichier *libman.json* si l’on n’existe pas. Le fichier est créé avec le contenu du modèle d’élément par défaut.

### <a name="synopsis"></a>Synopsis

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman init` :

* `-d|--default-destination <PATH>`

  Un chemin par rapport au dossier actuel. Les fichiers de bibliothèque sont `destination` installés à cet endroit si aucune propriété n’est définie pour une bibliothèque dans *libman.json*. La `<PATH>` valeur est `defaultDestination` écrite à la propriété de *libman.json*.

* `-p|--default-provider <PROVIDER>`

  Le fournisseur à utiliser si aucun fournisseur n’est défini pour une bibliothèque donnée. La `<PROVIDER>` valeur est `defaultProvider` écrite à la propriété de *libman.json*. Remplacez par `<PROVIDER>` l’une des valeurs suivantes :

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Pour créer un fichier *libman.json* dans un projet ASP.NET Core :

* Naviguez jusqu’à la racine du projet.
* Exécutez la commande suivante :

  ```console
  libman init
  ```

* Tapez le nom du fournisseur `Enter` par défaut ou appuyez sur pour utiliser le fournisseur CDNJS par défaut. Les valeurs valides sont les suivantes :

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init commande - fournisseur par défaut](_static/libman-init-provider.png)

Un fichier *libman.json* est ajouté à la racine du projet avec le contenu suivant :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Ajouter des fichiers de bibliothèque

La `libman install` commande télécharge et installe des fichiers de bibliothèque dans le projet. Un fichier *libman.json* est ajouté si l’on n’existe pas. Le fichier *libman.json* est modifié pour stocker les détails de configuration des fichiers de la bibliothèque.

### <a name="synopsis"></a>Synopsis

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Le nom de la bibliothèque à installer. Ce nom peut inclure la notation `@1.2.0`du numéro de version (par exemple, ).

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman install` :

* `-d|--destination <PATH>`

  L’emplacement pour installer la bibliothèque. S’il n’est pas spécifié, l’emplacement par défaut est utilisé. Si `defaultDestination` aucune propriété n’est spécifiée dans *libman.json*, cette option est requise.

* `--files <FILE>`

  Spécifier le nom du fichier à installer à partir de la bibliothèque. S’ils ne sont pas spécifiés, tous les fichiers de la bibliothèque sont installés. Fournir `--files` une option par fichier à installer. Les chemins relatifs sont également pris en charge. Par exemple : `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Le nom du fournisseur à utiliser pour l’acquisition de la bibliothèque. Remplacez par `<PROVIDER>` l’une des valeurs suivantes :
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  S’il n’est pas précisé, la `defaultProvider` propriété dans *libman.json* est utilisée. Si `defaultProvider` aucune propriété n’est spécifiée dans *libman.json*, cette option est requise.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Considérez le fichier *libman.json* suivant :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Pour installer la version jQuery 3.2.1 *jquery.min.js* fichier sur le *dossier wwwroot/scripts/jquery* à l’aide du fournisseur CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Le fichier *libman.json* ressemble à ce qui suit :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Pour installer les fichiers *calendar.js* et *calendar.css* de *C:\\temp\\contosoCalendar\\ * en utilisant le fournisseur de système de fichiers:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

L’invite suivante apparaît pour deux raisons :

* Le fichier *libman.json* ne `defaultDestination` contient pas de propriété.
* La `libman install` commande ne contient `-d|--destination` pas l’option.

![libman installer commande - destination](_static/libman-install-destination.png)

Après avoir accepté la destination par défaut, le fichier *libman.json* ressemble à ce qui suit :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Restaurer les fichiers de la bibliothèque

La `libman restore` commande installe des fichiers de bibliothèque définis dans *libman.json*. Les règles suivantes s’appliquent :

* Si aucun fichier *libman.json n’existe* dans la racine du projet, une erreur est retournée.
* Si une bibliothèque spécifie un fournisseur, la `defaultProvider` propriété dans *libman.json* est ignorée.
* Si une bibliothèque spécifie une destination, la `defaultDestination` propriété dans *libman.json* est ignorée.

### <a name="synopsis"></a>Synopsis

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman restore` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Restaurer les fichiers de bibliothèque définis dans *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>Supprimer les fichiers de bibliothèque

La `libman clean` commande supprime les fichiers de bibliothèque précédemment restaurés via LibMan. Les dossiers qui deviennent vides après cette opération sont supprimés. Les configurations associées aux `libraries` fichiers de bibliothèque dans la propriété de *libman.json* ne sont pas supprimées.

### <a name="synopsis"></a>Synopsis

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman clean` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Pour supprimer les fichiers de bibliothèque installés via LibMan :

```console
libman clean
```

## <a name="uninstall-library-files"></a>Désinstaller les fichiers de bibliothèque

La `libman uninstall` commande:

* Supprime tous les fichiers associés à la bibliothèque spécifiée à partir de la destination dans *libman.json*.
* Supprime la configuration de la bibliothèque associée de *libman.json*.

Une erreur se produit lorsque :

* Aucun fichier *libman.json n’existe* dans la racine du projet.
* La bibliothèque spécifiée n’existe pas.

Si plus d’une bibliothèque du même nom est installée, vous êtes invité à en choisir une.

### <a name="synopsis"></a>Synopsis

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Le nom de la bibliothèque pour désinstaller. Ce nom peut inclure la notation `@1.2.0`du numéro de version (par exemple, ).

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman uninstall` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Considérez le fichier *libman.json* suivant :

[!code-json[](samples/LibManSample/libman.json)]

* Pour désinstaller jQuery, l’une ou l’autre des commandes suivantes réussit :

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Pour désinstaller les fichiers Lodash installés via le `filesystem` fournisseur :

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Mettre à jour la version de la bibliothèque

La `libman update` commande met à jour une bibliothèque installée via LibMan à la version spécifiée.

Une erreur se produit lorsque :

* Aucun fichier *libman.json n’existe* dans la racine du projet.
* La bibliothèque spécifiée n’existe pas.

Si plus d’une bibliothèque du même nom est installée, vous êtes invité à en choisir une.

### <a name="synopsis"></a>Synopsis

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Le nom de la bibliothèque à mettre à jour.

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman update` :

* `-pre`

  Obtenez la dernière version prérelease de la bibliothèque.

* `--to <VERSION>`

  Obtenir une version spécifique de la bibliothèque.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

* Pour mettre à jour jQuery à la dernière version:

  ```console
  libman update jquery
  ```

* Pour mettre à jour jQuery à la version 3.3.1 :

  ```console
  libman update jquery --to 3.3.1
  ```

* Pour mettre à jour jQuery à la dernière version prérelease:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Gérer le cache de bibliothèque

La `libman cache` commande gère le cache de la bibliothèque LibMan. Le `filesystem` fournisseur n’utilise pas le cache de la bibliothèque.

### <a name="synopsis"></a>Synopsis

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Arguments

`PROVIDER`

Seulement utilisé `clean` avec la commande. Spécifie le cache fournisseur à nettoyer. Les valeurs valides sont les suivantes :

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman cache` :

* `--files`

  Énumérez les noms des fichiers qui sont mis en cache.

* `--libraries`

  Énumérez les noms des bibliothèques qui sont mises en cache.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

* Pour afficher les noms des bibliothèques mises en cache par fournisseur, utilisez l’une des commandes suivantes :

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Une sortie similaire à la suivante s’affiche à l’écran :

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Pour afficher les noms des fichiers de bibliothèque mis en cache par fournisseur :

  ```console
  libman cache list --files
  ```

  Une sortie similaire à la suivante s’affiche à l’écran :

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Notez la sortie précédente montre que les versions jQuery 3.2.1 et 3.3.1 sont mises en cache sous le fournisseur CDNJS.

* Pour vider le cache de bibliothèque pour le fournisseur CDNJS :

  ```console
  libman cache clean cdnjs
  ```

  Après avoir vidé le cache du `libman cache list` fournisseur CDNJS, la commande affiche ce qui suit :

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Pour vider le cache pour tous les fournisseurs pris en charge :

  ```console
  libman cache clean
  ```

  Après avoir vidé toutes les `libman cache list` caches du fournisseur, la commande affiche ce qui suit :

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Installer un outil global](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Dépôt GitHub LibMan](https://github.com/aspnet/LibraryManager)
