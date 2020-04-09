---
title: Fournisseurs de fichiers dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751106"
---
# <a name="file-providers-in-aspnet-core"></a>Fournisseurs de fichiers dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers. Les fournisseurs de fichiers sont utilisés dans l’ensemble du cadre ASP.NET Core. Par exemple :

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>expose la racine de [contenu](xref:fundamentals/index#content-root) et `IFileProvider` la racine [web](xref:fundamentals/index#web-root) de l’application en tant que types.
* [L’intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files) utilise des fournisseurs de fichiers pour localiser les fichiers statiques.
* [Razor](xref:mvc/views/razor) utilise des fournisseurs de fichiers pour localiser des pages et des vues.
* Les outils .NET Core utilisent des fournisseurs de fichiers et des modèles d’utilisation des caractères génériques pour spécifier les fichiers qui doivent être publiés.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfaces de fournisseur de fichiers

L’interface principale est <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` expose des méthodes pour :

* Obtenir des informations sur le fichier (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obtenir des informations sur le répertoire (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Configurer des notifications de modification (à l’aide un <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` fournit des méthodes et propriétés d’utilisation des fichiers :

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en octets)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>date

Vous pouvez lire à <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> partir du fichier en utilisant la méthode.

*L’application d’échantillons FileProviderSample* montre comment `Startup.ConfigureServices` configurer un fournisseur de fichiers pour une utilisation dans toute l’application via injection [de dépendance](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implémentations de fournisseur de fichiers

Le tableau suivant répertorie les implémentations de `IFileProvider`.

| Implémentation | Description |
| -------------- | ----------- |
| [CompositeFileProvider](#compositefileprovider) | Utilisé pour fournir un accès combiné aux fichiers et annuaires d’un ou de plusieurs autres fournisseurs. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Utilisé pour accéder aux fichiers intégrés dans les assemblages. |
| [PhysicalFileProvider](#physicalfileprovider) | Utilisé pour accéder aux fichiers physiques du système. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

La classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fournit un accès au système de fichiers physique. `PhysicalFileProvider` utilise le type <xref:System.IO.File?displayProperty=fullName> (pour le fournisseur physique) et définissant comme portée tous les chemins d’un répertoire et de ses enfants. Cette portée empêche l’accès au système de fichiers en dehors du répertoire spécifié et ses enfants. Le scénario le plus courant pour créer et utiliser un `PhysicalFileProvider` consiste à demander un `IFileProvider` dans un constructeur par le biais de l’[injection de dépendances](xref:fundamentals/dependency-injection).

Lors de l’instantanéise de ce fournisseur directement, un chemin d’annuaire absolu est nécessaire et sert de voie de base pour toutes les demandes faites à l’aide du fournisseur. Les motifs glob ne sont pas pris en charge dans le chemin du répertoire.

Le code suivant montre `PhysicalFileProvider` comment utiliser pour obtenir le contenu de l’annuaire et les informations de fichier:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Types dans l’exemple précédent :

* `provider` est une `IFileProvider`.
* `contents` est une `IDirectoryContents`.
* `fileInfo` est une `IFileInfo`.

Le fournisseur de fichiers peut être utilisé pour itérer au sein du répertoire spécifié par `applicationRoot` ou pour appeler `GetFileInfo` afin d’obtenir des informations sur un fichier. Les motifs glob ne `GetFileInfo` peuvent pas être transmis à la méthode. Le fournisseur de fichiers n’a pas accès en dehors du répertoire `applicationRoot`.

*L’application d’échantillon FileProviderSample* crée le fournisseur dans la `Startup.ConfigureServices` méthode en utilisant <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> est utilisé pour accéder à des fichiers incorporés dans des assemblys. `ManifestEmbeddedFileProvider` utilise un manifeste compilé dans l’assembly pour reconstruire les chemins d’accès d’origine des fichiers intégrés.

Pour générer un manifeste des fichiers intégrés :

1. Ajoutez le package [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet à votre projet.
1. Attribuez à la propriété `<GenerateEmbeddedFilesManifest>` la valeur `true`. Spécifiez les fichiers à intégrer à [ \<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Utilisez les [modèles glob](#glob-patterns) pour spécifier un ou plusieurs fichiers à incorporer dans l’assembly.

*L’application d’échantillon FileProviderSample* crée un `ManifestEmbeddedFileProvider` assemblage et passe l’assemblage actuellement exécutant à son constructeur.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Des surcharges supplémentaires vous permettent de :

* Spécifier un chemin de fichier relatif.
* Définir la portée de fichiers sur une date de dernière modification.
* Nommer la ressource incorporée contenant le manifeste de fichier incorporé.

| Surcharge | Description |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Accepte un paramètre de chemin d'accès relatif `root` facultatif. Spécifiez `root` pour définir la portée des appels à <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> sur ces ressources sous le chemin d’accès fourni. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepte un paramètre de chemin relatif `root` facultatif et un paramètre de date `lastModified` (<xref:System.DateTimeOffset>). La date `lastModified` définit la portée de la dernière date de modification pour les instances <xref:Microsoft.Extensions.FileProviders.IFileInfo> retournées par <xref:Microsoft.Extensions.FileProviders.IFileProvider>. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepte un chemin d'accès relatif `root` facultatif, une date `lastModified` et des paramètres `manifestName`. `manifestName` représente le nom de la ressource incorporée contenant la manifeste. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combine des instances `IFileProvider`, en exposant une interface unique qui permet d’utiliser des fichiers de différents fournisseurs. Quand vous créez `CompositeFileProvider`, vous passez une ou plusieurs instances `IFileProvider` à son constructeur.

Dans *l’application d’échantillon FileProviderSample,* a `PhysicalFileProvider` et un `ManifestEmbeddedFileProvider` fichier de fournir à un `CompositeFileProvider` enregistrement dans le conteneur de service de l’application. Le code suivant se trouve `Startup.ConfigureServices` dans la méthode du projet :

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Suivre les modifications apportées

La <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> méthode fournit un scénario pour regarder un ou plusieurs fichiers ou répertoires pour les changements. La méthode `Watch` :

* Accepte une chaîne de trajectoire de fichier, qui peut utiliser [des modèles de glob](#glob-patterns) pour spécifier plusieurs fichiers.
* Retourne un <xref:Microsoft.Extensions.Primitives.IChangeToken>.

Le jeton de changement qui en résulte expose :

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Une propriété qui peut être inspectée pour déterminer si un changement s’est produit.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Appelé lorsque des modifications sont détectées à la chaîne de chemin spécifiée. Chaque jeton de modification appelle uniquement son rappel associé en réponse à un changement unique. Pour activer une surveillance constante, vous pouvez utiliser une <xref:System.Threading.Tasks.TaskCompletionSource`1> comme indiqué ci-dessous, ou recréer des instances `IChangeToken` en réponse aux changements.

*L’application d’échantillon WatchConsole* écrit un message chaque fois qu’un fichier *.txt* dans le répertoire *TextFiles* est modifié :

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Certains systèmes de fichiers, comme les conteneurs Docker et les partages réseau, peuvent ne pas envoyer de manière fiable les notifications de modifications. Définissez la variable d’environnement `DOTNET_USE_POLLING_FILE_WATCHER` sur `1` ou `true` pour interroger le système de fichiers à la recherche de changements toutes les quatre secondes (non configurable).

### <a name="glob-patterns"></a>Modèles Glob

Les chemins de système de fichiers utilisent des modèles à caractères génériques appelés *modèles Glob (ou d’utilisation des caractères génériques)*. Spécifiez les groupes de fichiers avec ces modèles. Les deux caractères génériques sont `*` et `**` :

**`*`**  
Établit une correspondance avec n’importe quel élément au niveau de dossier actuel, avec n’importe quel nom de fichier ou avec n’importe quelle extension de fichier. Les correspondances sont terminées par des caractères `/` et `.` dans le chemin des fichiers.

**`**`**  
Établit une correspondance avec n’importe quel élément sur plusieurs niveaux de répertoire. Peut être utilisé pour établir une correspondance avec plusieurs fichiers dans une hiérarchie de répertoires de manière récursive.

Le tableau suivant fournit des exemples communs de modèles de glob.

|Modèle  |Description  |
|---------|---------|
|`directory/file.txt`|Établit une correspondance avec un fichier spécifique dans un répertoire spécifique.|
|`directory/*.txt`|Établit une correspondance avec tous les fichiers ayant l’extension *.txt* dans un répertoire spécifique.|
|`directory/*/appsettings.json`|Correspond à tous les fichiers *appsettings.json* dans les répertoires exactement un niveau en dessous du dossier *d’annuaire.*|
|`directory/**/*.txt`|Correspond à tous les fichiers avec une extension *.txt* trouvé n’importe où sous le dossier *d’annuaire.*|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core fournit un accès au système de fichiers en utilisant des fournisseurs de fichiers. Des fournisseurs de fichiers sont utilisés dans l’infrastructure ASP.NET Core :

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>expose la racine de [contenu](xref:fundamentals/index#content-root) et `IFileProvider` la racine [web](xref:fundamentals/index#web-root) de l’application en tant que types.
* [L’intergiciel (middleware) de fichiers statiques](xref:fundamentals/static-files) utilise des fournisseurs de fichiers pour localiser les fichiers statiques.
* [Razor](xref:mvc/views/razor) utilise des fournisseurs de fichiers pour localiser des pages et des vues.
* Les outils .NET Core utilisent des fournisseurs de fichiers et des modèles d’utilisation des caractères génériques pour spécifier les fichiers qui doivent être publiés.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Interfaces de fournisseur de fichiers

L’interface principale est <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` expose des méthodes pour :

* Obtenir des informations sur le fichier (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obtenir des informations sur le répertoire (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Configurer des notifications de modification (à l’aide un <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` fournit des méthodes et propriétés d’utilisation des fichiers :

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (en octets)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>date

Vous pouvez lire à partir du fichier en utilisant la méthode [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*).

L’exemple d’application montre comment configurer un fournisseur de fichiers dans `Startup.ConfigureServices` pour une utilisation dans toute l’application via [l’injection de dépendances](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implémentations de fournisseur de fichiers

Trois implémentations de `IFileProvider` sont disponibles.

| Implémentation | Description |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Le fournisseur physique est utilisé pour accéder aux fichiers physiques du système. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Le fournisseur incorporé de manifeste est utilisé pour accéder à des fichiers incorporés dans des assemblys. |
| [CompositeFileProvider](#compositefileprovider) | Le fournisseur composite est utilisé pour fournir un accès combiné à des fichiers et à des répertoires à partir d’un ou de plusieurs fournisseurs. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

La classe <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> fournit un accès au système de fichiers physique. `PhysicalFileProvider` utilise le type <xref:System.IO.File?displayProperty=fullName> (pour le fournisseur physique) et définissant comme portée tous les chemins d’un répertoire et de ses enfants. Cette portée empêche l’accès au système de fichiers en dehors du répertoire spécifié et ses enfants. Le scénario le plus courant pour créer et utiliser un `PhysicalFileProvider` consiste à demander un `IFileProvider` dans un constructeur par le biais de l’[injection de dépendances](xref:fundamentals/dependency-injection).

Lors de l’instanciation directe de ce fournisseur, un chemin de répertoire est obligatoire et sert de chemin de base pour toutes les demandes effectuées à l’aide du fournisseur.

Le code suivant montre comment créer un `PhysicalFileProvider` et l’utiliser pour obtenir le contenu du répertoire et les informations sur le fichier :

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Types dans l’exemple précédent :

* `provider` est une `IFileProvider`.
* `contents` est une `IDirectoryContents`.
* `fileInfo` est une `IFileInfo`.

Le fournisseur de fichiers peut être utilisé pour itérer au sein du répertoire spécifié par `applicationRoot` ou pour appeler `GetFileInfo` afin d’obtenir des informations sur un fichier. Le fournisseur de fichiers n’a pas accès en dehors du répertoire `applicationRoot`.

L’exemple d’application crée le fournisseur dans la classe `Startup.ConfigureServices` de l’application à l’aide de [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider) :

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> est utilisé pour accéder à des fichiers incorporés dans des assemblys. `ManifestEmbeddedFileProvider` utilise un manifeste compilé dans l’assembly pour reconstruire les chemins d’accès d’origine des fichiers intégrés.

Pour générer un manifeste des fichiers incorporés, définissez la propriété `<GenerateEmbeddedFilesManifest>` sur `true`. Spécifier les fichiers à intégrer avec [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Utilisez les [modèles glob](#glob-patterns) pour spécifier un ou plusieurs fichiers à incorporer dans l’assembly.

L’exemple d’application crée un `ManifestEmbeddedFileProvider` et transmet l’assembly en cours d’exécution à son constructeur.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Des surcharges supplémentaires vous permettent de :

* Spécifier un chemin de fichier relatif.
* Définir la portée de fichiers sur une date de dernière modification.
* Nommer la ressource incorporée contenant le manifeste de fichier incorporé.

| Surcharge | Description |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Accepte un paramètre de chemin d'accès relatif `root` facultatif. Spécifiez `root` pour définir la portée des appels à <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> sur ces ressources sous le chemin d’accès fourni. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepte un paramètre de chemin relatif `root` facultatif et un paramètre de date `lastModified` (<xref:System.DateTimeOffset>). La date `lastModified` définit la portée de la dernière date de modification pour les instances <xref:Microsoft.Extensions.FileProviders.IFileInfo> retournées par <xref:Microsoft.Extensions.FileProviders.IFileProvider>. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepte un chemin d'accès relatif `root` facultatif, une date `lastModified` et des paramètres `manifestName`. `manifestName` représente le nom de la ressource incorporée contenant la manifeste. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combine des instances `IFileProvider`, en exposant une interface unique qui permet d’utiliser des fichiers de différents fournisseurs. Quand vous créez `CompositeFileProvider`, vous passez une ou plusieurs instances `IFileProvider` à son constructeur.

Dans l’exemple d’application, un `PhysicalFileProvider` et un `ManifestEmbeddedFileProvider` fournissent des fichiers à un `CompositeFileProvider` inscrit dans le conteneur de service de l’application :

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Suivre les modifications apportées

La méthode [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) offre un moyen d’observer un ou plusieurs fichiers ou répertoires afin de détecter les changements. `Watch` accepte une chaîne de chemin, qui peut utiliser des [modèles d’utilisation des caractères génériques](#glob-patterns) pour spécifier plusieurs fichiers. `Watch`Retourne un <xref:Microsoft.Extensions.Primitives.IChangeToken>. Le jeton de modification expose :

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Une propriété qui peut être inspectée pour déterminer si un changement s’est produit.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Appelé lorsque des modifications sont détectées à la chaîne de chemin spécifiée. Chaque jeton de modification appelle uniquement son rappel associé en réponse à un changement unique. Pour activer une surveillance constante, vous pouvez utiliser une <xref:System.Threading.Tasks.TaskCompletionSource`1> comme indiqué ci-dessous, ou recréer des instances `IChangeToken` en réponse aux changements.

Dans l’exemple d’application, l’application console *WatchConsole* est configurée pour afficher un message chaque fois qu’un fichier texte est modifié :

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Certains systèmes de fichiers, comme les conteneurs Docker et les partages réseau, peuvent ne pas envoyer de manière fiable les notifications de modifications. Définissez la variable d’environnement `DOTNET_USE_POLLING_FILE_WATCHER` sur `1` ou `true` pour interroger le système de fichiers à la recherche de changements toutes les quatre secondes (non configurable).

## <a name="glob-patterns"></a>Modèles Glob

Les chemins de système de fichiers utilisent des modèles à caractères génériques appelés *modèles Glob (ou d’utilisation des caractères génériques)*. Spécifiez les groupes de fichiers avec ces modèles. Les deux caractères génériques sont `*` et `**` :

**`*`**  
Établit une correspondance avec n’importe quel élément au niveau de dossier actuel, avec n’importe quel nom de fichier ou avec n’importe quelle extension de fichier. Les correspondances sont terminées par des caractères `/` et `.` dans le chemin des fichiers.

**`**`**  
Établit une correspondance avec n’importe quel élément sur plusieurs niveaux de répertoire. Peut être utilisé pour établir une correspondance avec plusieurs fichiers dans une hiérarchie de répertoires de manière récursive.

**Exemples de modèles d’utilisation des caractères génériques**

**`directory/file.txt`**  
Établit une correspondance avec un fichier spécifique dans un répertoire spécifique.

**`directory/*.txt`**  
Établit une correspondance avec tous les fichiers ayant l’extension *.txt* dans un répertoire spécifique.

**`directory/*/appsettings.json`**  
Établit une correspondance avec tous les fichiers `appsettings.json` dans les répertoires situés exactement un niveau en dessous du dossier *répertoire*.

**`directory/**/*.txt`**  
Établit une correspondance avec tous les fichiers ayant l’extension *.txt* et se trouvant n’importe où sous le dossier *répertoire*.

::: moniker-end
