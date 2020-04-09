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
# <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez créé un pipeline DevOps pour une application d’échantillon ASP.NET Core. Félicitations ! Nous espérons que vous avez aimé apprendre à publier ASP.NET applications Web Core à Azure App Service et à automatiser l’intégration continue des changements.

Au-delà de l’hébergement web et DevOps, Azure dispose d’un large éventail de services Platform-as-a-Service (PaaS) utiles pour ASP.NET développeurs Core. Cette section donne un bref aperçu de certains des services les plus couramment utilisés.

## <a name="storage-and-databases"></a>Stockage et bases de données

[Redis Cache](/azure/redis-cache/) est un cachet de données à haut débit et à faible latence disponible en tant que service. Il peut être utilisé pour la mise en cache de la sortie de page, la réduction des demandes de base de données, et la fourniture de ASP.NET’état de session De base à travers plusieurs instances d’une application.

[Azure Storage](/azure/storage/) est le stockage en nuage massivement évolutif d’Azure. Les développeurs peuvent profiter de [Queue Storage](/azure/storage/queues/storage-queues-introduction) pour faire la queue de message fiable, et [Table Storage](/azure/storage/tables/table-storage-overview) est un magasin de valeur clé NoSQL conçu pour le développement rapide à l’aide d’ensembles de données massifs semi-structurés.

[Azure SQL Database](/azure/sql-database/) fournit des fonctionnalités de base de données relationnelles familières en tant que service utilisant le moteur serveur SQL Microsoft.

[Cosmos DB](/azure/cosmos-db/) distribué à l’échelle mondiale, multi-modèle Service de base de données NoSQL. Plusieurs API sont disponibles, y compris SQL API (anciennement appelé DocumentDB), Cassandra, et MongoDB.

## <a name="identity"></a>Identité

[Azure Active Directory](/azure/active-directory/) et [Azure Active Directory B2C](/azure/active-directory-b2c/) sont tous deux des services d’identité. Azure Active Directory est conçu pour des scénarios d’entreprise et permet la collaboration Azure AD B2B (business-to-business), tandis qu’Azure Active Directory B2C est destiné à des scénarios interentraux, y compris l’inscription de réseau social.

## <a name="mobile"></a>Mobile

[Notification Hubs](/azure/notification-hubs/) est un moteur multiplateforme de notification push évolutive pour envoyer rapidement des millions de messages aux applications fonctionnant sur différents types d’appareils.

## <a name="web-infrastructure"></a>Infrastructure Web

[Azure Container Service](/azure/aks/) gère votre environnement Kubernetes hébergé, ce qui le rend rapide et facile à déployer et à gérer des applications conteneurisées sans l’expertise de l’orchestration de conteneurs.

[Azure Search](/azure/search/) est utilisé pour créer une solution de recherche d’entreprise sur un contenu privé et hétérogène.

[Service Fabric](/azure/service-fabric/) est une plate-forme de systèmes distribués qui facilite l’emballage, le déploiement et la gestion de microservices et de conteneurs évolutifs et fiables.
