---
title: DevOps avec ASP.NET Core et Azure
author: CamSoper
description: Un guide qui fournit des conseils de bout en bout sur la création d’un pipeline DevOps pour une application ASP.NET Core hébergée dans Azure.
ms.author: casoper
ms.date: 08/07/2018
ms.custom: mvc, seodec18
uid: azure/devops/index
ms.openlocfilehash: f45bb2a5dd4b3d1a820085ede7ce3219045ed80b
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658080"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="805ea-103">DevOps avec ASP.NET Core et Azure</span><span class="sxs-lookup"><span data-stu-id="805ea-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="805ea-104">[![Image de couverture](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="805ea-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="805ea-105">Par [Cam Soper](https://twitter.com/camsoper) et [Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="805ea-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="805ea-106">Ce guide est disponible sous forme de [livre électronique PDF téléchargeable](https://aka.ms/devopsbook).</span><span class="sxs-lookup"><span data-stu-id="805ea-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="805ea-107">Bienvenue</span><span class="sxs-lookup"><span data-stu-id="805ea-107">Welcome</span></span> 

<span data-ttu-id="805ea-108">Bienvenue dans le guide du cycle de vie du développement Azure pour .NET !</span><span class="sxs-lookup"><span data-stu-id="805ea-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="805ea-109">Ce guide présente les concepts de base de la création d’un cycle de vie du développement autour d’Azure avec les outils et les processus .NET.</span><span class="sxs-lookup"><span data-stu-id="805ea-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="805ea-110">Après avoir terminé ce guide, vous pourrez tirer parti d’une chaîne d’outils DevOps arrivés à maturation.</span><span class="sxs-lookup"><span data-stu-id="805ea-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="805ea-111">Audience à laquelle ce guide est destiné</span><span class="sxs-lookup"><span data-stu-id="805ea-111">Who this guide is for</span></span>

<span data-ttu-id="805ea-112">Vous devez être un développeur ASP.NET Core expérimenté (niveau 200-300).</span><span class="sxs-lookup"><span data-stu-id="805ea-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="805ea-113">Vous n’avez pas besoin de connaissances sur Azure, car nous couvrons cet aspect dans cette introduction.</span><span class="sxs-lookup"><span data-stu-id="805ea-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="805ea-114">Ce guide peut également être utile aux ingénieurs DevOps qui se consacrent davantage aux opérations qu’au développement.</span><span class="sxs-lookup"><span data-stu-id="805ea-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="805ea-115">Ce guide cible les développeurs Windows.</span><span class="sxs-lookup"><span data-stu-id="805ea-115">This guide targets Windows developers.</span></span> <span data-ttu-id="805ea-116">Cependant, Linux et macOS sont entièrement pris en charge par .NET Core.</span><span class="sxs-lookup"><span data-stu-id="805ea-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="805ea-117">Pour adapter ce guide à Linux/macOS, consultez les indications faisant état des différences pour Linux/macOS.</span><span class="sxs-lookup"><span data-stu-id="805ea-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="805ea-118">Ce que ce guide ne couvre pas</span><span class="sxs-lookup"><span data-stu-id="805ea-118">What this guide doesn't cover</span></span>

<span data-ttu-id="805ea-119">Ce guide est centré sur une expérience de déploiement continu de bout en bout pour les développeurs .NET.</span><span class="sxs-lookup"><span data-stu-id="805ea-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="805ea-120">Il ne s’agit pas d’un guide exhaustif sur Azure, et il n’aborde pas de façon détaillée les API .NET pour les services Azure.</span><span class="sxs-lookup"><span data-stu-id="805ea-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="805ea-121">L’accent est mis sur tout ce qui a trait à l’intégration continue, au déploiement, à la surveillance et au débogage.</span><span class="sxs-lookup"><span data-stu-id="805ea-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="805ea-122">Vous pouvez trouver des recommandations pour les étapes suivantes vers la fin du guide.</span><span class="sxs-lookup"><span data-stu-id="805ea-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="805ea-123">Les services de plateforme Azure qui sont utiles aux développeurs ASP.NET Core figurent dans les suggestions.</span><span class="sxs-lookup"><span data-stu-id="805ea-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="805ea-124">Contenu de ce guide</span><span class="sxs-lookup"><span data-stu-id="805ea-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="805ea-125">Outils et téléchargements</span><span class="sxs-lookup"><span data-stu-id="805ea-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="805ea-126">Découvrez où vous procurer les outils utilisés dans ce guide.</span><span class="sxs-lookup"><span data-stu-id="805ea-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="805ea-127">Déployer dans App Service</span><span class="sxs-lookup"><span data-stu-id="805ea-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="805ea-128">Découvrez les différentes méthodes de déploiement d’une application ASP.NET Core sur Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="805ea-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="805ea-129">Intégration et déploiement continus</span><span class="sxs-lookup"><span data-stu-id="805ea-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="805ea-130">Créez une solution d’intégration et de déploiement continus de bout en bout pour votre application ASP.NET Core avec GitHub, Azure DevOps Services et Azure.</span><span class="sxs-lookup"><span data-stu-id="805ea-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="805ea-131">Surveiller et débouger</span><span class="sxs-lookup"><span data-stu-id="805ea-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="805ea-132">Utilisez les outils d’Azure pour surveiller, dépanner et optimiser votre application.</span><span class="sxs-lookup"><span data-stu-id="805ea-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="805ea-133">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="805ea-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="805ea-134">Autres parcours d’apprentissage pour les développeurs ASP.NET Core découvrant Azure.</span><span class="sxs-lookup"><span data-stu-id="805ea-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="805ea-135">Articles d’introduction supplémentaires</span><span class="sxs-lookup"><span data-stu-id="805ea-135">Additional introductory reading</span></span>

<span data-ttu-id="805ea-136">S’il s’agit de votre premier contact avec le cloud computing, ces articles expliquent les principes de base.</span><span class="sxs-lookup"><span data-stu-id="805ea-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="805ea-137">Qu’est-ce que le cloud computing ?</span><span class="sxs-lookup"><span data-stu-id="805ea-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="805ea-138">Exemples de cloud computing</span><span class="sxs-lookup"><span data-stu-id="805ea-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="805ea-139">Qu’est-ce que IaaS ?</span><span class="sxs-lookup"><span data-stu-id="805ea-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="805ea-140">Qu’est-ce que PaaS ?</span><span class="sxs-lookup"><span data-stu-id="805ea-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
