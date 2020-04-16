---
title: Héberger ASP.NET Core dans des conteneurs Docker
author: rick-anderson
description: Découvrez des liens vers des ressources pour savoir comment héberger des applications ASP.NET Core dans des conteneurs Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: 78eb6f467c7e779e1bf998e8ac8e917a90895db9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440725"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="9f412-103">Héberger ASP.NET Core dans des conteneurs Docker</span><span class="sxs-lookup"><span data-stu-id="9f412-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="9f412-104">Les articles suivants sont disponibles pour en savoir plus sur l’hébergement d’applications ASP.NET Core dans Docker :</span><span class="sxs-lookup"><span data-stu-id="9f412-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="9f412-105">Introduction aux conteneurs et à Docker</span><span class="sxs-lookup"><span data-stu-id="9f412-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="9f412-106">Découvrez dans quelle mesure la mise en conteneur est une approche de développement de logiciels qui consiste à empaqueter une application ou un service, ses dépendances et sa configuration sous forme d’image de conteneur.</span><span class="sxs-lookup"><span data-stu-id="9f412-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="9f412-107">L’image peut être testée, puis déployée sur un hôte.</span><span class="sxs-lookup"><span data-stu-id="9f412-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="9f412-108">Qu’est-ce que Docker</span><span class="sxs-lookup"><span data-stu-id="9f412-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="9f412-109">Découvrez Docker, projet open source permettant d’automatiser le déploiement d’applications en tant que conteneurs portables et autonomes exécutables sur le cloud ou localement.</span><span class="sxs-lookup"><span data-stu-id="9f412-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="9f412-110">Terminologie Docker</span><span class="sxs-lookup"><span data-stu-id="9f412-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="9f412-111">Découvrez les termes et définitions de la technologie Docker.</span><span class="sxs-lookup"><span data-stu-id="9f412-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="9f412-112">Conteneurs, images et registres Docker</span><span class="sxs-lookup"><span data-stu-id="9f412-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="9f412-113">Découvrez comment les images de conteneur Docker sont stockées dans un registre d’image pour un déploiement cohérent entre les environnements.</span><span class="sxs-lookup"><span data-stu-id="9f412-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="9f412-114"><xref:host-and-deploy/docker/building-net-docker-images>Découvrez comment générer et dockeriser une application ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9f412-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="9f412-115">Explorez les images Docker gérées par Microsoft et examinez des cas d’usage.</span><span class="sxs-lookup"><span data-stu-id="9f412-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="9f412-116">Outils de conteneurs de studio visuel</span><span class="sxs-lookup"><span data-stu-id="9f412-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="9f412-117">Découvrez dans quelle mesure Visual Studio prend en charge la création, le débogage et l’exécution d’applications ASP.NET Core ciblant le .NET Framework ou .NET Core sur Docker pour Windows.</span><span class="sxs-lookup"><span data-stu-id="9f412-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="9f412-118">Les conteneurs Windows et Linux sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="9f412-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="9f412-119">Publier sur Azure Container Registry</span><span class="sxs-lookup"><span data-stu-id="9f412-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="9f412-120">Découvrez comment utiliser l’extension Outils de conteneur Visual pour déployer une application ASP.NET Core sur un hôte Docker sur Azure avec PowerShell.</span><span class="sxs-lookup"><span data-stu-id="9f412-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="9f412-121">Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge</span><span class="sxs-lookup"><span data-stu-id="9f412-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="9f412-122">Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge.</span><span class="sxs-lookup"><span data-stu-id="9f412-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="9f412-123">Le passage des requêtes à travers un proxy masque souvent des informations sur la requête d’origine, comme le schéma et l’adresse IP du client.</span><span class="sxs-lookup"><span data-stu-id="9f412-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="9f412-124">Il peut être nécessaire de transférer manuellement à l’application certaines informations sur la requête.</span><span class="sxs-lookup"><span data-stu-id="9f412-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="9f412-125">[GC à l’aide de Docker et de petits conteneurs](xref:performance/memory#sc) Discute de la sélection GC avec de petits conteneurs.</span><span class="sxs-lookup"><span data-stu-id="9f412-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>