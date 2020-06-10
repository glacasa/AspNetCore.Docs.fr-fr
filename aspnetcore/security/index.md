---
title: Vue d’ensemble de la sécurité ASP.NET Core
author: rick-anderson
description: Découvrez les concepts de base de l’authentification, de l’autorisation et de la sécurité dans ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/index
ms.openlocfilehash: 413b4cf786c31ac3a538c8663014b53502d90d0c
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "83838290"
---
# <a name="overview-of-aspnet-core-security"></a><span data-ttu-id="10c8d-103">Vue d’ensemble de la sécurité ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10c8d-103">Overview of ASP.NET Core Security</span></span>

<span data-ttu-id="10c8d-104">ASP.NET Core permet aux développeurs de configurer et de gérer facilement la sécurité de leurs applications.</span><span class="sxs-lookup"><span data-stu-id="10c8d-104">ASP.NET Core enables developers to easily configure and manage security for their apps.</span></span> <span data-ttu-id="10c8d-105">ASP.NET Core comporte des fonctionnalités de gestion de l’authentification, des autorisations et des secrets des applications, de protection des données, d’application du protocole HTTPS, de protection contre la falsification de requête et de gestion CORS.</span><span class="sxs-lookup"><span data-stu-id="10c8d-105">ASP.NET Core contains features for managing authentication, authorization, data protection, HTTPS enforcement, app secrets, anti-request forgery protection, and CORS management.</span></span> <span data-ttu-id="10c8d-106">Ces fonctionnalités de sécurité vous permettent de générer des applications ASP.NET Core robustes et néanmoins sécurisées.</span><span class="sxs-lookup"><span data-stu-id="10c8d-106">These security features allow you to build robust yet secure ASP.NET Core apps.</span></span>

## <a name="aspnet-core-security-features"></a><span data-ttu-id="10c8d-107">Fonctionnalités de sécurité ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10c8d-107">ASP.NET Core security features</span></span>

<span data-ttu-id="10c8d-108">ASP.NET Core fournit de nombreux outils et bibliothèques pour sécuriser vos applications, notamment les fournisseurs d’identité intégrés, mais vous pouvez utiliser des services d’identité tiers tels que Facebook, Twitter et LinkedIn.</span><span class="sxs-lookup"><span data-stu-id="10c8d-108">ASP.NET Core provides many tools and libraries to secure your apps including built-in identity providers, but you can use third-party identity services such as Facebook, Twitter, and LinkedIn.</span></span> <span data-ttu-id="10c8d-109">Avec ASP.NET Core, vous pouvez facilement gérer les secrets des applications, qui sont un moyen de stocker et d’utiliser des informations confidentielles sans avoir à les exposer dans le code.</span><span class="sxs-lookup"><span data-stu-id="10c8d-109">With ASP.NET Core, you can easily manage app secrets, which are a way to store and use confidential information without having to expose it in the code.</span></span>

## <a name="authentication-vs-authorization"></a><span data-ttu-id="10c8d-110">Authentification et autorisation</span><span class="sxs-lookup"><span data-stu-id="10c8d-110">Authentication vs. Authorization</span></span>

<span data-ttu-id="10c8d-111">L’authentification est un processus selon lequel un utilisateur fournit des informations d’identification qui sont ensuite comparées à celles stockées dans un système d’exploitation, une base de données, une application ou une ressource.</span><span class="sxs-lookup"><span data-stu-id="10c8d-111">Authentication is a process in which a user provides credentials that are then compared to those stored in an operating system, database, app or resource.</span></span> <span data-ttu-id="10c8d-112">Si elles correspondent, les utilisateurs sont authentifiés et peuvent alors effectuer les actions pour lesquelles ils disposent d’autorisations pendant un processus d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="10c8d-112">If they match, users authenticate successfully, and can then perform actions that they're authorized for, during an authorization process.</span></span> <span data-ttu-id="10c8d-113">L’autorisation désigne le processus qui détermine ce qu’un utilisateur est autorisé à faire.</span><span class="sxs-lookup"><span data-stu-id="10c8d-113">The authorization refers to the process that determines what a user is allowed to do.</span></span>

<span data-ttu-id="10c8d-114">Vous pouvez aussi vous représenter l’authentification comme un moyen d’entrer dans un espace, tel qu’un serveur, une base de données, une application ou une ressource, tandis que l’autorisation consiste à définir quelles actions l’utilisateur peut effectuer sur quels objets à l’intérieur de cet espace (serveur, base de données ou application).</span><span class="sxs-lookup"><span data-stu-id="10c8d-114">Another way to think of authentication is to consider it as a way to enter a space, such as a server, database, app or resource, while authorization is which actions the user can perform to which objects inside that space (server, database, or app).</span></span>

## <a name="common-vulnerabilities-in-software"></a><span data-ttu-id="10c8d-115">Failles de sécurité courantes dans les logiciels</span><span class="sxs-lookup"><span data-stu-id="10c8d-115">Common Vulnerabilities in software</span></span>

<span data-ttu-id="10c8d-116">ASP.NET Core et Entity Framework contiennent des fonctionnalités qui vous aident à sécuriser vos applications et à empêcher les violations de sécurité.</span><span class="sxs-lookup"><span data-stu-id="10c8d-116">ASP.NET Core and EF contain features that help you secure your apps and prevent security breaches.</span></span> <span data-ttu-id="10c8d-117">La liste de liens ci-après vous permet d’accéder à une documentation décrivant en détail des techniques destinées à éviter les failles de sécurité les plus courantes dans les applications web :</span><span class="sxs-lookup"><span data-stu-id="10c8d-117">The following list of links takes you to documentation detailing techniques to avoid the most common security vulnerabilities in web apps:</span></span>

* [<span data-ttu-id="10c8d-118">Attaques par exécution de scripts de site à site</span><span class="sxs-lookup"><span data-stu-id="10c8d-118">Cross-site scripting attacks</span></span>](xref:security/cross-site-scripting)
* [<span data-ttu-id="10c8d-119">Attaques par injection de code SQL</span><span class="sxs-lookup"><span data-stu-id="10c8d-119">SQL injection attacks</span></span>](/ef/core/querying/raw-sql)
* [<span data-ttu-id="10c8d-120">Falsification de requête intersites (CSRF, Cross Site Request Forgery)</span><span class="sxs-lookup"><span data-stu-id="10c8d-120">Cross-Site Request Forgery (CSRF)</span></span>](xref:security/anti-request-forgery)
* [<span data-ttu-id="10c8d-121">Attaques par redirection ouverte</span><span class="sxs-lookup"><span data-stu-id="10c8d-121">Open redirect attacks</span></span>](xref:security/preventing-open-redirects)

<span data-ttu-id="10c8d-122">Il existe d’autres failles de sécurité que vous devez connaître.</span><span class="sxs-lookup"><span data-stu-id="10c8d-122">There are more vulnerabilities that you should be aware of.</span></span> <span data-ttu-id="10c8d-123">Pour plus d’informations, reportez-vous aux autres Articles de la section \*\*sécurité et Identity \*\* de la table des matières.</span><span class="sxs-lookup"><span data-stu-id="10c8d-123">For more information, see the other articles in the **Security and Identity** section of the table of contents.</span></span>
