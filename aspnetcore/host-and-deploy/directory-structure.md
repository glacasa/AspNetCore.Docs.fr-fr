---
title: Structure de répertoires ASP.NET Core
author: rick-anderson
description: Découvrez la structure de répertoires des applications ASP.NET Core publiées.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 03/20/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: 0e7bf40520385b7719cb37120709e0a3fd2442e3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989728"
---
# <a name="aspnet-core-directory-structure"></a>Structure de répertoires ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Le répertoire *publier* contient les ressources de l’application qui peuvent être déployées et produites par la commande [dotnet publish](/dotnet/core/tools/dotnet-publish). Le répertoire contient :

* Les fichiers de l’application
* Fichiers de configuration
* Les ressources statiques
* .
* Un runtime ([déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) uniquement)

| Type d'application | Structure de répertoires |
| -------- | ------------------- |
| [Cadre-dépendant Exécutable (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Vues&dagger; des applications MVC; si les vues ne sont pas précompilées</li><li>Pages&dagger; MVC ou Razor Pages applications, si les pages ne sont pas précompilées</li><li>wwwroot&dagger;</li><li>Fichiers \*.dll</li><li>{NOM de l’ASSEMBLY}.deps.json</li><li>{NOM de l’ASSEMBLY}.dll</li><li>NOM DE L’ASSEMBLÉE {. EXTENSION. *.exe*</li><li>{NOM de l’ASSEMBLY}.pdb</li><li>{NOM de l’ASSEMBLY}.Views.dll</li><li>{NOM de l’ASSEMBLY}.Views.pdb</li><li>{NOM de l’ASSEMBLY}.runtimeconfig.json</li><li>web.config (déploiements IIS)</li><li>createdump ([Linux createdump utilitaire](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (Linux bibliothèque d’objets partagés)</li><li>\*.a (archives macOS)</li><li>\*.dylib (bibliothèque dynamique macOS)</li></ul></li></ul> |
| [Déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Vues&dagger; des applications MVC, si les vues ne sont pas précompilées</li><li>Pages&dagger; MVC ou Razor Pages applications, si les pages ne sont pas précompilées</li><li>wwwroot&dagger;</li><li>Fichiers \*.dll</li><li>{NOM de l’ASSEMBLY}.deps.json</li><li>{NOM de l’ASSEMBLY}.dll</li><li>{NOM de l’ASSEMBLY}.exe</li><li>{NOM de l’ASSEMBLY}.pdb</li><li>{NOM de l’ASSEMBLY}.Views.dll</li><li>{NOM de l’ASSEMBLY}.Views.pdb</li><li>{NOM de l’ASSEMBLY}.runtimeconfig.json</li><li>web.config (déploiements IIS)</li></ul></li></ul> |

&dagger;Indique un répertoire

Le répertoire *publish* représente le *chemin racine du contenu*, également appelé *chemin de base de l’application*, du déploiement. Quel que soit le nom donné au répertoire *publish* de l’application déployée sur le serveur, son emplacement sert de chemin physique, sur le serveur, de l’application hébergée.

Le répertoire *wwwroot*, s’il existe, contient uniquement des ressources statiques.

## <a name="additional-resources"></a>Ressources supplémentaires

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET Déploiement d’applications de base](/dotnet/core/deploying/)
* [Versions cibles de .NET Framework](/dotnet/standard/frameworks)
* [Catalogue RID .NET Core](/dotnet/core/rid-catalog)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Le répertoire *publier* contient les ressources de l’application qui peuvent être déployées et produites par la commande [dotnet publish](/dotnet/core/tools/dotnet-publish). Le répertoire contient :

* Les fichiers de l’application
* Fichiers de configuration
* Les ressources statiques
* .
* Un runtime ([déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) uniquement)

| Type d'application | Structure de répertoires |
| -------- | ------------------- |
| [Cadre-dépendant Exécutable (FDE)](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li>publish&dagger;<ul><li>Vues&dagger; des applications MVC; si les vues ne sont pas précompilées</li><li>Pages&dagger; MVC ou Razor Pages applications, si les pages ne sont pas précompilées</li><li>wwwroot&dagger;</li><li>Fichiers \*.dll</li><li>{NOM de l’ASSEMBLY}.deps.json</li><li>{NOM de l’ASSEMBLY}.dll</li><li>NOM DE L’ASSEMBLÉE {. EXTENSION. *.exe*</li><li>{NOM de l’ASSEMBLY}.pdb</li><li>{NOM de l’ASSEMBLY}.Views.dll</li><li>{NOM de l’ASSEMBLY}.Views.pdb</li><li>{NOM de l’ASSEMBLY}.runtimeconfig.json</li><li>web.config (déploiements IIS)</li><li>createdump ([Linux createdump utilitaire](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>\*.so (Linux bibliothèque d’objets partagés)</li><li>\*.a (archives macOS)</li><li>\*.dylib (bibliothèque dynamique macOS)</li></ul></li></ul> |
| [Déploiement autonome (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li>publish&dagger;<ul><li>Vues&dagger; des applications MVC, si les vues ne sont pas précompilées</li><li>Pages&dagger; MVC ou Razor Pages applications, si les pages ne sont pas précompilées</li><li>wwwroot&dagger;</li><li>Fichiers \*.dll</li><li>{NOM de l’ASSEMBLY}.deps.json</li><li>{NOM de l’ASSEMBLY}.dll</li><li>{NOM de l’ASSEMBLY}.exe</li><li>{NOM de l’ASSEMBLY}.pdb</li><li>{NOM de l’ASSEMBLY}.Views.dll</li><li>{NOM de l’ASSEMBLY}.Views.pdb</li><li>{NOM de l’ASSEMBLY}.runtimeconfig.json</li><li>web.config (déploiements IIS)</li></ul></li></ul> |
-
&dagger;Indique un répertoire

Le répertoire *publish* représente le *chemin racine du contenu*, également appelé *chemin de base de l’application*, du déploiement. Quel que soit le nom donné au répertoire *publish* de l’application déployée sur le serveur, son emplacement sert de chemin physique, sur le serveur, de l’application hébergée.

Le répertoire *wwwroot*, s’il existe, contient uniquement des ressources statiques.

La création d’un dossier *Logs* est utile à la [journalisation de débogage améliorée du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Les dossiers situés dans le chemin fourni pour la valeur `<handlerSetting>` ne sont pas créés automatiquement par le module. Ils doivent préexister dans le déploiement pour permettre au module d’écrire dans le journal de débogage.

Vous pouvez créer le répertoire *Logs* pour le déploiement à l’aide de l’une des deux approches suivantes :

* Ajoutez l’élément `<Target>` suivant au fichier projet :

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   L’élément `<MakeDir>` crée un dossier *Logs* vide dans la sortie publiée. L’élément utilise la propriété `PublishDir` pour déterminer l’emplacement cible en vue de la création du dossier. Plusieurs méthodes de déploiement, telles que Web Deploy, ignorent les dossiers vides pendant le déploiement. L’élément `<WriteLinesToFile>` génère un fichier dans le dossier *Logs*, ce qui garantit le déploiement du dossier sur le serveur. La création d’un dossier à l’aide de cette approche échoue si le processus de travail n’a pas accès en écriture au dossier cible.

* Créez physiquement le répertoire *Logs* sur le serveur dans le déploiement.

Le répertoire de déploiement requiert des autorisations de lecture et d’exécution. Le répertoire *Logs* requiert des autorisations de lecture et d’écriture. D’autres répertoires où des fichiers sont écrits nécessitent des autorisations de lecture et d’écriture.

## <a name="additional-resources"></a>Ressources supplémentaires

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* [.NET Déploiement d’applications de base](/dotnet/core/deploying/)
* [Versions cibles de .NET Framework](/dotnet/standard/frameworks)
* [Catalogue RID .NET Core](/dotnet/core/rid-catalog)

::: moniker-end
