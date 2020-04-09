---
title: Prochaines étapes - DevOps avec ASP.NET Core et Azure
author: CamSoper
description: D’autres voies d’apprentissage pour DevOps avec ASP.NET Core et Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659473"
---
# <a name="next-steps"></a><span data-ttu-id="f046d-103">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f046d-103">Next steps</span></span>

<span data-ttu-id="f046d-104">Dans ce guide, vous avez créé un pipeline DevOps pour une application d’échantillon ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f046d-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="f046d-105">Félicitations !</span><span class="sxs-lookup"><span data-stu-id="f046d-105">Congratulations!</span></span> <span data-ttu-id="f046d-106">Nous espérons que vous avez aimé apprendre à publier ASP.NET applications Web Core à Azure App Service et à automatiser l’intégration continue des changements.</span><span class="sxs-lookup"><span data-stu-id="f046d-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="f046d-107">Au-delà de l’hébergement web et DevOps, Azure dispose d’un large éventail de services Platform-as-a-Service (PaaS) utiles pour ASP.NET développeurs Core.</span><span class="sxs-lookup"><span data-stu-id="f046d-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="f046d-108">Cette section donne un bref aperçu de certains des services les plus couramment utilisés.</span><span class="sxs-lookup"><span data-stu-id="f046d-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="f046d-109">Stockage et bases de données</span><span class="sxs-lookup"><span data-stu-id="f046d-109">Storage and databases</span></span>

<span data-ttu-id="f046d-110">[Redis Cache](/azure/redis-cache/) est un cachet de données à haut débit et à faible latence disponible en tant que service.</span><span class="sxs-lookup"><span data-stu-id="f046d-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="f046d-111">Il peut être utilisé pour la mise en cache de la sortie de page, la réduction des demandes de base de données, et la fourniture de ASP.NET’état de session De base à travers plusieurs instances d’une application.</span><span class="sxs-lookup"><span data-stu-id="f046d-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="f046d-112">[Azure Storage](/azure/storage/) est le stockage en nuage massivement évolutif d’Azure.</span><span class="sxs-lookup"><span data-stu-id="f046d-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="f046d-113">Les développeurs peuvent profiter de [Queue Storage](/azure/storage/queues/storage-queues-introduction) pour faire la queue de message fiable, et [Table Storage](/azure/storage/tables/table-storage-overview) est un magasin de valeur clé NoSQL conçu pour le développement rapide à l’aide d’ensembles de données massifs semi-structurés.</span><span class="sxs-lookup"><span data-stu-id="f046d-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="f046d-114">[Azure SQL Database](/azure/sql-database/) fournit des fonctionnalités de base de données relationnelles familières en tant que service utilisant le moteur serveur SQL Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f046d-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="f046d-115">[Cosmos DB](/azure/cosmos-db/) distribué à l’échelle mondiale, multi-modèle Service de base de données NoSQL.</span><span class="sxs-lookup"><span data-stu-id="f046d-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="f046d-116">Plusieurs API sont disponibles, y compris SQL API (anciennement appelé DocumentDB), Cassandra, et MongoDB.</span><span class="sxs-lookup"><span data-stu-id="f046d-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="f046d-117">Identité</span><span class="sxs-lookup"><span data-stu-id="f046d-117">Identity</span></span>

<span data-ttu-id="f046d-118">[Azure Active Directory](/azure/active-directory/) et [Azure Active Directory B2C](/azure/active-directory-b2c/) sont tous deux des services d’identité.</span><span class="sxs-lookup"><span data-stu-id="f046d-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="f046d-119">Azure Active Directory est conçu pour des scénarios d’entreprise et permet la collaboration Azure AD B2B (business-to-business), tandis qu’Azure Active Directory B2C est destiné à des scénarios interentraux, y compris l’inscription de réseau social.</span><span class="sxs-lookup"><span data-stu-id="f046d-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="f046d-120">Mobile</span><span class="sxs-lookup"><span data-stu-id="f046d-120">Mobile</span></span>

<span data-ttu-id="f046d-121">[Notification Hubs](/azure/notification-hubs/) est un moteur multiplateforme de notification push évolutive pour envoyer rapidement des millions de messages aux applications fonctionnant sur différents types d’appareils.</span><span class="sxs-lookup"><span data-stu-id="f046d-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="f046d-122">Infrastructure Web</span><span class="sxs-lookup"><span data-stu-id="f046d-122">Web infrastructure</span></span>

<span data-ttu-id="f046d-123">[Azure Container Service](/azure/aks/) gère votre environnement Kubernetes hébergé, ce qui le rend rapide et facile à déployer et à gérer des applications conteneurisées sans l’expertise de l’orchestration de conteneurs.</span><span class="sxs-lookup"><span data-stu-id="f046d-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="f046d-124">[Azure Search](/azure/search/) est utilisé pour créer une solution de recherche d’entreprise sur un contenu privé et hétérogène.</span><span class="sxs-lookup"><span data-stu-id="f046d-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="f046d-125">[Service Fabric](/azure/service-fabric/) est une plate-forme de systèmes distribués qui facilite l’emballage, le déploiement et la gestion de microservices et de conteneurs évolutifs et fiables.</span><span class="sxs-lookup"><span data-stu-id="f046d-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
