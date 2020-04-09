---
title: Outils et téléchargements - DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Outils et téléchargements requis pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511143"
---
# <a name="tools-and-downloads"></a>Outils et téléchargements

Azure dispose de plusieurs interfaces pour l’approvisionnement et la gestion des ressources, telles que le [portail Azure](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), et Visual Studio. Ce guide adopte une approche minimaliste et utilise la coquille Cloud Azure dans la mesure du possible pour réduire les étapes requises. Cependant, le portail Azure doit être utilisé pour certaines portions.

## <a name="prerequisites"></a>Prérequis

Les abonnements suivants sont requis :

* Azure &mdash; Si vous n’avez pas de compte, [obtenez un essai gratuit](https://azure.microsoft.com/free/).
* Azure DevOps &mdash; Services votre abonnement Azure DevOps et l’organisation est créé dans le chapitre 4.
* GitHub &mdash; Si vous n’avez pas de compte, [inscrivez-vous gratuitement](https://github.com/join).

Les outils suivants sont nécessaires :

* [Git](https://git-scm.com/downloads) &mdash; Une compréhension fondamentale de Git est recommandée pour ce guide. Examiner la [documentation Git](https://git-scm.com/doc), spécifiquement [git à distance](https://git-scm.com/docs/git-remote) et [git pousser](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 ou plus tard est nécessaire pour construire et exécuter l’application d’échantillon. Si Visual Studio est installé avec la charge de travail **de développement multiplateforme .NET Core,** le .NET Core SDK est déjà installé.

    Vérifiez votre installation SDK core .NET. Ouvrez une coque de commande et exécutez la commande suivante :

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Outils recommandés (Windows uniquement)

* [Les](https://visualstudio.microsoft.com)outils Azure robustes de Visual Studio fournissent une interface utilisateur pour la plupart des fonctionnalités décrites dans ce guide. Toute édition de Visual Studio fonctionnera, y compris la gratuité Visual Studio Community Edition. Les tutoriels sont écrits pour démontrer le développement, le déploiement, et DevOps à la fois avec et sans Visual Studio.

  Confirmez que Visual Studio a installé les [charges de travail](/visualstudio/install/modify-visual-studio) suivantes :

  * Développement web et ASP.NET
  * Développement Azure
  * Développement multiplateforme .NET Core
