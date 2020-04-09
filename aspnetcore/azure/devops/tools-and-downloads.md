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
# <a name="tools-and-downloads"></a><span data-ttu-id="0327c-103">Outils et téléchargements</span><span class="sxs-lookup"><span data-stu-id="0327c-103">Tools and downloads</span></span>

<span data-ttu-id="0327c-104">Azure dispose de plusieurs interfaces pour l’approvisionnement et la gestion des ressources, telles que le [portail Azure](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), et Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0327c-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="0327c-105">Ce guide adopte une approche minimaliste et utilise la coquille Cloud Azure dans la mesure du possible pour réduire les étapes requises.</span><span class="sxs-lookup"><span data-stu-id="0327c-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="0327c-106">Cependant, le portail Azure doit être utilisé pour certaines portions.</span><span class="sxs-lookup"><span data-stu-id="0327c-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0327c-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="0327c-107">Prerequisites</span></span>

<span data-ttu-id="0327c-108">Les abonnements suivants sont requis :</span><span class="sxs-lookup"><span data-stu-id="0327c-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="0327c-109">Azure &mdash; Si vous n’avez pas de compte, [obtenez un essai gratuit](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="0327c-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="0327c-110">Azure DevOps &mdash; Services votre abonnement Azure DevOps et l’organisation est créé dans le chapitre 4.</span><span class="sxs-lookup"><span data-stu-id="0327c-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="0327c-111">GitHub &mdash; Si vous n’avez pas de compte, [inscrivez-vous gratuitement](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="0327c-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="0327c-112">Les outils suivants sont nécessaires :</span><span class="sxs-lookup"><span data-stu-id="0327c-112">The following tools are required:</span></span>

* <span data-ttu-id="0327c-113">[Git](https://git-scm.com/downloads) &mdash; Une compréhension fondamentale de Git est recommandée pour ce guide.</span><span class="sxs-lookup"><span data-stu-id="0327c-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="0327c-114">Examiner la [documentation Git](https://git-scm.com/doc), spécifiquement [git à distance](https://git-scm.com/docs/git-remote) et [git pousser](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="0327c-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="0327c-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 ou plus tard est nécessaire pour construire et exécuter l’application d’échantillon.</span><span class="sxs-lookup"><span data-stu-id="0327c-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="0327c-116">Si Visual Studio est installé avec la charge de travail **de développement multiplateforme .NET Core,** le .NET Core SDK est déjà installé.</span><span class="sxs-lookup"><span data-stu-id="0327c-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="0327c-117">Vérifiez votre installation SDK core .NET.</span><span class="sxs-lookup"><span data-stu-id="0327c-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="0327c-118">Ouvrez une coque de commande et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="0327c-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="0327c-119">Outils recommandés (Windows uniquement)</span><span class="sxs-lookup"><span data-stu-id="0327c-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="0327c-120">[Les](https://visualstudio.microsoft.com)outils Azure robustes de Visual Studio fournissent une interface utilisateur pour la plupart des fonctionnalités décrites dans ce guide.</span><span class="sxs-lookup"><span data-stu-id="0327c-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="0327c-121">Toute édition de Visual Studio fonctionnera, y compris la gratuité Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="0327c-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="0327c-122">Les tutoriels sont écrits pour démontrer le développement, le déploiement, et DevOps à la fois avec et sans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0327c-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="0327c-123">Confirmez que Visual Studio a installé les [charges de travail](/visualstudio/install/modify-visual-studio) suivantes :</span><span class="sxs-lookup"><span data-stu-id="0327c-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="0327c-124">Développement web et ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0327c-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="0327c-125">Développement Azure</span><span class="sxs-lookup"><span data-stu-id="0327c-125">Azure development</span></span>
  * <span data-ttu-id="0327c-126">Développement multiplateforme .NET Core</span><span class="sxs-lookup"><span data-stu-id="0327c-126">.NET Core cross-platform development</span></span>
