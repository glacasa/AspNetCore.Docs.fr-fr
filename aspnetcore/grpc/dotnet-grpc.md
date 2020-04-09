---
title: Gérer les références Protobuf avec dotnet-GRPC
author: juntaoluo
description: Renseignez-vous sur l’ajout, la mise à jour, la suppression et la liste des références Protobuf avec l’outil global dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667334"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Gérer les références Protobuf avec dotnet-GRPC

Par [John Luo](https://github.com/juntaoluo)

`dotnet-grpc`est un outil mondial de base .NET pour gérer les références [Protobuf (*.proto)*](xref:grpc/basics#proto-file) au sein d’un projet .NET gRPC. L’outil peut être utilisé pour ajouter, actualiser, supprimer et énumérer les références Protobuf.

## <a name="installation"></a>Installation

Pour installer `dotnet-grpc` l’outil [global de base .NET](/dotnet/core/tools/global-tools), exécutez la commande suivante :

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Ajouter des références

`dotnet-grpc`peut être utilisé pour ajouter `<Protobuf />` des références Protobuf comme éléments du fichier *.csproj* :

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Les références Protobuf sont utilisées pour générer les actifs du client et/ou du serveur C. L’outil `dotnet-grpc` peut :

* Créez une référence Protobuf à partir de fichiers locaux sur disque.
* Créez une référence Protobuf à partir d’un fichier distant spécifié par une URL.
* Assurez-vous que les bonnes dépendances de paquet de gRPC sont ajoutées au projet.

Par exemple, `Grpc.AspNetCore` le paquet est ajouté à une application web. `Grpc.AspNetCore`contient des bibliothèques de serveurs et de clients gRPC et le support d’outillage. Alternativement, `Grpc.Net.Client`le `Grpc.Tools` `Google.Protobuf` , et les paquets, qui ne contiennent que les bibliothèques de clients gRPC et le support d’outillage, sont ajoutés à une application Console.

### <a name="add-file"></a>Ajouter un fichier

La `add-file` commande est utilisée pour ajouter des fichiers locaux sur disque comme références Protobuf. Les trajectoires de fichiers fournies :

* Peut être par rapport à l’annuaire actuel ou des chemins absolus.
* Peut contenir des wild cards pour le [glissement](https://wikipedia.org/wiki/Glob_(programming))de fichier basé sur le modèle .

Si des fichiers sont en dehors `Link` de l’annuaire du projet, `Protos` un élément est ajouté pour afficher le fichier sous le dossier dans Visual Studio.

### <a name="usage"></a>Usage

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| files | Les références de fichiers protobuf. Ceux-ci peuvent être un chemin à glob pour les fichiers protobuf locaux. |

#### <a name="options"></a>Options

| Option courte | Option longue | Description |
|-|-|-|
| -p | --projet | Le chemin vers le dossier du projet pour fonctionner. Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.
| -S | --services | Le type de services gRPC qui devraient être générés. Si `Default` elle `Both` est spécifiée, `Client` est utilisée pour des projets Web et est utilisée pour des projets non-Web. Les valeurs `Both` `Client`acceptées `None` `Server`sont , , `Default`, .
| -i | --dirs supplémentaires-importation | D’autres répertoires à utiliser lors de la résolution des importations pour les fichiers protobuf. Il s’agit d’une liste de chemins séparés par le point-virgule.
| | --accès | Le modificateur d’accès à utiliser pour les classes C générées. La valeur par défaut est `Public`. Les valeurs acceptées sont `Internal` et `Public`.

### <a name="add-url"></a>Ajouter une URL

La `add-url` commande est utilisée pour ajouter un fichier distant spécifié par une URL source comme référence Protobuf. Un chemin de fichier doit être fourni pour spécifier où télécharger le fichier à distance. Le chemin de fichier peut être par rapport à l’annuaire actuel ou un chemin absolu. Si le chemin de fichier est `Link` en dehors de l’annuaire du `Protos` projet, un élément est ajouté pour afficher le fichier sous le dossier virtuel dans Visual Studio.

### <a name="usage"></a>Usage

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| url | L’URL d’un fichier protobuf distant. |

#### <a name="options"></a>Options

| Option courte | Option longue | Description |
|-|-|-|
| -o | --output | Spécifie le chemin de téléchargement du fichier protobuf distant. C'est une option obligatoire.
| -p | --projet | Le chemin vers le dossier du projet pour fonctionner. Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.
| -S | --services | Le type de services gRPC qui devraient être générés. Si `Default` elle `Both` est spécifiée, `Client` est utilisée pour des projets Web et est utilisée pour des projets non-Web. Les valeurs `Both` `Client`acceptées `None` `Server`sont , , `Default`, .
| -i | --dirs supplémentaires-importation | D’autres répertoires à utiliser lors de la résolution des importations pour les fichiers protobuf. Il s’agit d’une liste de chemins séparés par le point-virgule.
| | --accès | Le modificateur d’accès à utiliser pour les classes C générées. La valeur par défaut est `Public`. Les valeurs acceptées sont `Internal` et `Public`.

## <a name="remove"></a>Supprimer

La `remove` commande est utilisée pour supprimer les références Protobuf du fichier *.csproj.* La commande accepte les arguments de voie et les URL de source comme arguments. L’outil :

* Supprime uniquement la référence Protobuf.
* Ne supprime pas le fichier *.proto,* même s’il a été téléchargé à l’origine à partir d’une URL à distance.

### <a name="usage"></a>Usage

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| references | Les URL ou les trajectoires de fichier des références protobuf à supprimer. |

### <a name="options"></a>Options

| Option courte | Option longue | Description |
|-|-|-|
| -p | --projet | Le chemin vers le dossier du projet pour fonctionner. Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.

## <a name="refresh"></a>Actualiser

La `refresh` commande est utilisée pour mettre à jour une référence à distance avec le dernier contenu de l’URL source. Le chemin du fichier de téléchargement et l’URL source peuvent être utilisés pour spécifier la référence à mettre à jour. Remarque :

* Les hachages du contenu du fichier sont comparés pour déterminer si le fichier local doit être mis à jour.
* Aucune information sur l’allphe n’est comparée.

L’outil remplace toujours le fichier local par le fichier à distance si une mise à jour est nécessaire.

### <a name="usage"></a>Usage

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Arguments

| Argument | Description |
|-|-|
| references | Les URL ou les trajectoires de fichiers vers des références protobuf à distance qui doivent être mises à jour. Laissez cet argument vide pour rafraîchir toutes les références à distance. |

### <a name="options"></a>Options

| Option courte | Option longue | Description |
|-|-|-|
| -p | --projet | Le chemin vers le dossier du projet pour fonctionner. Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.
| | --à sec | Sortie d’une liste de fichiers qui seraient mis à jour sans télécharger de nouveau contenu.

## <a name="list"></a>List

La `list` commande est utilisée pour afficher toutes les références Protobuf dans le fichier du projet. Si toutes les valeurs d’une colonne sont des valeurs par défaut, la colonne peut être omise.

### <a name="usage"></a>Usage

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Options

| Option courte | Option longue | Description |
|-|-|-|
| -p | --projet | Le chemin vers le dossier du projet pour fonctionner. Si un fichier n’est pas spécifié, la commande recherche un répertoire actuel.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
