---
title: Présentation de ASP.NET Core SignalR
author: bradygaster
description: Découvrez comment la SignalR bibliothèque ASP.net Core simplifie l’ajout de fonctionnalités en temps réel aux applications.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: ab850fa8afbee9d2664868937362388a03374908
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634694"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="fdfab-103">Présentation de ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="fdfab-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="fdfab-104">Qu’est-ce que c’est SignalR ?</span><span class="sxs-lookup"><span data-stu-id="fdfab-104">What is SignalR?</span></span>

<span data-ttu-id="fdfab-105">ASP.NET Core SignalR est une bibliothèque open source qui simplifie l’ajout de fonctionnalités Web en temps réel aux applications.</span><span class="sxs-lookup"><span data-stu-id="fdfab-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="fdfab-106">La fonctionnalité Web en temps réel permet au code côté serveur de transmettre instantanément du contenu aux clients.</span><span class="sxs-lookup"><span data-stu-id="fdfab-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="fdfab-107">Bons candidats pour SignalR :</span><span class="sxs-lookup"><span data-stu-id="fdfab-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="fdfab-108">Les applications ayant besoin de mises à jour fréquentes auprès du serveur.</span><span class="sxs-lookup"><span data-stu-id="fdfab-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="fdfab-109">Exemples : jeux, réseaux sociaux, scrutin, enchères, cartes et applications GPS.</span><span class="sxs-lookup"><span data-stu-id="fdfab-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="fdfab-110">Les tableaux de bord et les applications de monitoring.</span><span class="sxs-lookup"><span data-stu-id="fdfab-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="fdfab-111">Exemples : tableaux de bord des entreprises, mises à jour instantanées des ventes et alertes de voyage.</span><span class="sxs-lookup"><span data-stu-id="fdfab-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="fdfab-112">Les applications de collaboration.</span><span class="sxs-lookup"><span data-stu-id="fdfab-112">Collaborative apps.</span></span> <span data-ttu-id="fdfab-113">Exemples : applications de tableau blanc et logiciels de réunion d’équipe.</span><span class="sxs-lookup"><span data-stu-id="fdfab-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="fdfab-114">Les applications qui envoient des notifications.</span><span class="sxs-lookup"><span data-stu-id="fdfab-114">Apps that require notifications.</span></span> <span data-ttu-id="fdfab-115">Exemples : réseaux sociaux, messagerie, conversation instantanée, jeux, alertes de voyage, etc.</span><span class="sxs-lookup"><span data-stu-id="fdfab-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="fdfab-116">SignalR fournit une API permettant de créer des [appels de procédure distante (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)de serveur à client.</span><span class="sxs-lookup"><span data-stu-id="fdfab-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="fdfab-117">Les RPC appellent des fonctions JavaScript sur les clients à partir du code .NET Core côté serveur.</span><span class="sxs-lookup"><span data-stu-id="fdfab-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="fdfab-118">Voici quelques-unes des fonctionnalités de SignalR pour ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="fdfab-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="fdfab-119">Gère automatiquement la gestion des connexions.</span><span class="sxs-lookup"><span data-stu-id="fdfab-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="fdfab-120">Envoie des messages à tous les clients connectés simultanément.</span><span class="sxs-lookup"><span data-stu-id="fdfab-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="fdfab-121">Par exemple, une salle de conversation.</span><span class="sxs-lookup"><span data-stu-id="fdfab-121">For example, a chat room.</span></span>
* <span data-ttu-id="fdfab-122">Envoie des messages à des clients ou groupes de clients spécifiques.</span><span class="sxs-lookup"><span data-stu-id="fdfab-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="fdfab-123">Met à l’échelle pour gérer le trafic qui augmente.</span><span class="sxs-lookup"><span data-stu-id="fdfab-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="fdfab-124">La source est hébergée dans un [ SignalR référentiel sur GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="fdfab-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="fdfab-125">Transports</span><span class="sxs-lookup"><span data-stu-id="fdfab-125">Transports</span></span>

<span data-ttu-id="fdfab-126">SignalR prend en charge les techniques suivantes pour gérer les communications en temps réel (par ordre de secours normal) :</span><span class="sxs-lookup"><span data-stu-id="fdfab-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="fdfab-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="fdfab-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="fdfab-128">Événements envoyés par le serveur</span><span class="sxs-lookup"><span data-stu-id="fdfab-128">Server-Sent Events</span></span>
* <span data-ttu-id="fdfab-129">Interrogation longue</span><span class="sxs-lookup"><span data-stu-id="fdfab-129">Long Polling</span></span>

<span data-ttu-id="fdfab-130">SignalR choisit automatiquement la meilleure méthode de transport parmi les capacités du serveur et du client.</span><span class="sxs-lookup"><span data-stu-id="fdfab-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="fdfab-131">Hubs</span><span class="sxs-lookup"><span data-stu-id="fdfab-131">Hubs</span></span>

<span data-ttu-id="fdfab-132">SignalR utilise des *concentrateurs* pour la communication entre les clients et les serveurs.</span><span class="sxs-lookup"><span data-stu-id="fdfab-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="fdfab-133">Un Hub est un pipeline de haut niveau qui permet à un client et un serveur d’appeler des méthodes les unes sur les autres.</span><span class="sxs-lookup"><span data-stu-id="fdfab-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="fdfab-134">SignalR gère automatiquement la distribution à travers les limites de l’ordinateur, ce qui permet aux clients d’appeler des méthodes sur le serveur et vice versa.</span><span class="sxs-lookup"><span data-stu-id="fdfab-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="fdfab-135">Vous pouvez passer des paramètres fortement typés à des méthodes, ce qui active la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="fdfab-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="fdfab-136">SignalR fournit deux protocoles Hub intégrés : un protocole texte basé sur JSON et un protocole binaire basé sur [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="fdfab-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="fdfab-137">MessagePack crée généralement des messages plus petits par rapport à JSON.</span><span class="sxs-lookup"><span data-stu-id="fdfab-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="fdfab-138">Les anciens navigateurs doivent prendre en charge le [niveau 2 de XHR](https://caniuse.com/#feat=xhr2) pour fournir la prise en charge du protocole MessagePack.</span><span class="sxs-lookup"><span data-stu-id="fdfab-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="fdfab-139">Les hubs appellent le code côté client en envoyant des messages qui contiennent le nom et les paramètres de la méthode côté client.</span><span class="sxs-lookup"><span data-stu-id="fdfab-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="fdfab-140">Les objets envoyés en tant que paramètres de méthode sont désérialisés à l’aide du protocole configuré.</span><span class="sxs-lookup"><span data-stu-id="fdfab-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="fdfab-141">Le client tente de faire correspondre le nom à une méthode dans le code côté client.</span><span class="sxs-lookup"><span data-stu-id="fdfab-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="fdfab-142">Lorsque le client trouve une correspondance, il appelle la méthode et lui passe les données de paramètre désérialisées.</span><span class="sxs-lookup"><span data-stu-id="fdfab-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fdfab-143">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="fdfab-143">Additional resources</span></span>

* [<span data-ttu-id="fdfab-144">Prise en main SignalR de pour ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="fdfab-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="fdfab-145">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="fdfab-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="fdfab-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="fdfab-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="fdfab-147">Client JavaScript</span><span class="sxs-lookup"><span data-stu-id="fdfab-147">JavaScript client</span></span>](xref:signalr/javascript-client)
