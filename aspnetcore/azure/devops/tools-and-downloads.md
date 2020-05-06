---
title: Outils et téléchargements-DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Outils et téléchargements requis pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 1917a329a5dcbe60542541cfcdc746799307e3d5
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850407"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="25065-103">Outils et téléchargements</span><span class="sxs-lookup"><span data-stu-id="25065-103">Tools and downloads</span></span>

<span data-ttu-id="25065-104">Azure dispose de plusieurs interfaces pour la configuration et la gestion des ressources, telles que les [portail Azure](https://portal.azure.com), les [Azure CLI](/cli/azure/), les [Azure PowerShell](/powershell/azure/overview), les [Azure Cloud Shell](https://shell.azure.com/bash)et Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25065-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="25065-105">Ce guide prend une approche minimaliste et utilise le Azure Cloud Shell chaque fois que cela est possible pour réduire les étapes requises.</span><span class="sxs-lookup"><span data-stu-id="25065-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="25065-106">Toutefois, le Portail Azure doit être utilisé pour certaines parties.</span><span class="sxs-lookup"><span data-stu-id="25065-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="25065-107">Prérequis</span><span class="sxs-lookup"><span data-stu-id="25065-107">Prerequisites</span></span>

<span data-ttu-id="25065-108">Les abonnements suivants sont requis :</span><span class="sxs-lookup"><span data-stu-id="25065-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="25065-109">Azure &mdash; si vous n’avez pas de compte, [procurez-vous un essai gratuit](https://azure.microsoft.com/free/dotnet/).</span><span class="sxs-lookup"><span data-stu-id="25065-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/dotnet/).</span></span>
* <span data-ttu-id="25065-110">Azure DevOps Services &mdash; votre abonnement et votre organisation Azure DevOps sont créés dans le chapitre 4.</span><span class="sxs-lookup"><span data-stu-id="25065-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="25065-111">GitHub &mdash; si vous n’avez pas de compte, inscrivez-vous [gratuitement](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="25065-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="25065-112">Les outils suivants sont requis :</span><span class="sxs-lookup"><span data-stu-id="25065-112">The following tools are required:</span></span>

* <span data-ttu-id="25065-113">[Git](https://git-scm.com/downloads) &mdash; une compréhension fondamentale de git est recommandée pour ce guide.</span><span class="sxs-lookup"><span data-stu-id="25065-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="25065-114">Consultez la [documentation git](https://git-scm.com/doc), en particulier [git remote](https://git-scm.com/docs/git-remote) et [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="25065-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="25065-115">[Kit SDK .net Core](https://dotnet.microsoft.com/download/) &mdash; version 2.1.300 ou ultérieure est nécessaire pour générer et exécuter l’exemple d’application.</span><span class="sxs-lookup"><span data-stu-id="25065-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="25065-116">Si Visual Studio est installé avec la charge de travail de **développement multiplateforme .net Core** , le kit SDK .net Core est déjà installé.</span><span class="sxs-lookup"><span data-stu-id="25065-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="25065-117">Vérifiez votre installation de kit SDK .NET Core.</span><span class="sxs-lookup"><span data-stu-id="25065-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="25065-118">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="25065-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="25065-119">Outils recommandés (Windows uniquement)</span><span class="sxs-lookup"><span data-stu-id="25065-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="25065-120">Les outils Azure robustes de [Visual Studio](https://visualstudio.microsoft.com)fournissent une interface utilisateur graphique pour la plupart des fonctionnalités décrites dans ce guide.</span><span class="sxs-lookup"><span data-stu-id="25065-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="25065-121">Toutes les éditions de Visual Studio fonctionnent, y compris la version gratuite de Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="25065-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="25065-122">Les didacticiels sont rédigés pour illustrer le développement, le déploiement et la DevOps à la fois avec et sans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="25065-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="25065-123">Vérifiez que les [charges de travail](/visualstudio/install/modify-visual-studio) suivantes sont installées sur Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="25065-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="25065-124">Développement web et ASP.NET</span><span class="sxs-lookup"><span data-stu-id="25065-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="25065-125">Développement Azure</span><span class="sxs-lookup"><span data-stu-id="25065-125">Azure development</span></span>
  * <span data-ttu-id="25065-126">Développement multiplateforme .NET Core</span><span class="sxs-lookup"><span data-stu-id="25065-126">.NET Core cross-platform development</span></span>
