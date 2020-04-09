---
title: ASP.NET gestion Blazor de l’État de base
author: guardrex
description: Découvrez comment maintenir Blazor l’état dans les applications Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218867"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="083bd-103">ASP.NET gestion Blazor de l’État de base</span><span class="sxs-lookup"><span data-stu-id="083bd-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="083bd-104">Par [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="083bd-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="083bd-105">Server est un cadre d’application majestueux.</span><span class="sxs-lookup"><span data-stu-id="083bd-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="083bd-106">La plupart du temps, l’application maintient une connexion continue au serveur.</span><span class="sxs-lookup"><span data-stu-id="083bd-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="083bd-107">L’état de l’utilisateur est maintenu dans la mémoire du serveur dans un *circuit*.</span><span class="sxs-lookup"><span data-stu-id="083bd-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="083bd-108">Voici quelques exemples d’état tenu pour le circuit d’un utilisateur :</span><span class="sxs-lookup"><span data-stu-id="083bd-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="083bd-109">L’interface utilisateur&mdash;rendue la hiérarchie des instances de composants et leur sortie de rendu la plus récente.</span><span class="sxs-lookup"><span data-stu-id="083bd-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="083bd-110">Les valeurs de tous les champs et propriétés dans les instances de composants.</span><span class="sxs-lookup"><span data-stu-id="083bd-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="083bd-111">Données détenues dans des cas de service [d’injection de dépendance (DI)](xref:fundamentals/dependency-injection) qui sont étendues au circuit.</span><span class="sxs-lookup"><span data-stu-id="083bd-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="083bd-112">Cet article traite Blazor de la persistance de l’état dans les applications Server.</span><span class="sxs-lookup"><span data-stu-id="083bd-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="083bd-113">Les applications WebAssembly peuvent tirer parti de la persistance de [l’état côté client dans le navigateur,](#client-side-in-the-browser) mais nécessitent des solutions personnalisées ou des paquets tiers au-delà de la portée de cet article.</span><span class="sxs-lookup"><span data-stu-id="083bd-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="083bd-114">Circuits</span><span class="sxs-lookup"><span data-stu-id="083bd-114"> circuits</span></span>

<span data-ttu-id="083bd-115">Si un utilisateur subit une perte Blazor de connexion réseau temporaire, tente de reconnecter l’utilisateur à son circuit d’origine afin qu’il puisse continuer à utiliser l’application.</span><span class="sxs-lookup"><span data-stu-id="083bd-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="083bd-116">Cependant, il n’est pas toujours possible de reconnecter un utilisateur à son circuit d’origine à la mémoire du serveur :</span><span class="sxs-lookup"><span data-stu-id="083bd-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="083bd-117">Le serveur ne peut pas conserver un circuit déconnecté pour toujours.</span><span class="sxs-lookup"><span data-stu-id="083bd-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="083bd-118">Le serveur doit libérer un circuit déconnecté après un délai d’attente ou lorsque le serveur est sous pression de mémoire.</span><span class="sxs-lookup"><span data-stu-id="083bd-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="083bd-119">Dans les environnements de déploiement multiservateurs et équilibrés, toutes les demandes de traitement de serveur peuvent devenir indisponibles à tout moment.</span><span class="sxs-lookup"><span data-stu-id="083bd-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="083bd-120">Les serveurs individuels peuvent échouer ou être automatiquement supprimés lorsqu’ils ne sont plus tenus de traiter le volume global des demandes.</span><span class="sxs-lookup"><span data-stu-id="083bd-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="083bd-121">Le serveur d’origine peut ne pas être disponible lorsque l’utilisateur tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="083bd-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="083bd-122">L’utilisateur peut fermer et rouvrir son navigateur ou recharger la page, qui supprime tout état contenu dans la mémoire du navigateur.</span><span class="sxs-lookup"><span data-stu-id="083bd-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="083bd-123">Par exemple, les valeurs définies via les appels interop JavaScript sont perdues.</span><span class="sxs-lookup"><span data-stu-id="083bd-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="083bd-124">Lorsqu’un utilisateur ne peut pas être reconnecté à son circuit d’origine, l’utilisateur reçoit un nouveau circuit avec un état vide.</span><span class="sxs-lookup"><span data-stu-id="083bd-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="083bd-125">Cela équivaut à la fermeture et à la réouverture d’une application de bureau.</span><span class="sxs-lookup"><span data-stu-id="083bd-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="083bd-126">Préserver l’état à travers les circuits</span><span class="sxs-lookup"><span data-stu-id="083bd-126">Preserve state across circuits</span></span>

<span data-ttu-id="083bd-127">Dans certains scénarios, il est souhaitable de préserver l’état à travers les circuits.</span><span class="sxs-lookup"><span data-stu-id="083bd-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="083bd-128">Une application peut conserver des données importantes pour un utilisateur si :</span><span class="sxs-lookup"><span data-stu-id="083bd-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="083bd-129">Le serveur web devient indisponible.</span><span class="sxs-lookup"><span data-stu-id="083bd-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="083bd-130">Le navigateur de l’utilisateur est contraint de démarrer un nouveau circuit avec un nouveau serveur web.</span><span class="sxs-lookup"><span data-stu-id="083bd-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="083bd-131">En général, le maintien de l’état entre les circuits s’applique aux scénarios où les utilisateurs créent activement des données, et non pas simplement à lire des données qui existent déjà.</span><span class="sxs-lookup"><span data-stu-id="083bd-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="083bd-132">Pour préserver l’état au-delà d’un seul circuit, *ne vous contentez pas de stocker les données dans la mémoire du serveur*.</span><span class="sxs-lookup"><span data-stu-id="083bd-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="083bd-133">L’application doit persister les données à un autre emplacement de stockage.</span><span class="sxs-lookup"><span data-stu-id="083bd-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="083bd-134">La persistance de&mdash;l’État n’est pas automatique, vous devez prendre des mesures lors du développement de l’application pour implémenter la persistance des données étatiques.</span><span class="sxs-lookup"><span data-stu-id="083bd-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="083bd-135">La persistance des données n’est généralement nécessaire que pour l’état de grande valeur que les utilisateurs ont dépensé des efforts pour créer.</span><span class="sxs-lookup"><span data-stu-id="083bd-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="083bd-136">Dans les exemples suivants, l’état persistant permet d’économiser du temps ou d’aide dans les activités commerciales :</span><span class="sxs-lookup"><span data-stu-id="083bd-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="083bd-137">Formulaire Web &ndash; Multistep Il est long pour un utilisateur de saisir à nouveau des données pour plusieurs étapes terminées d’un processus multistep si leur état est perdu.</span><span class="sxs-lookup"><span data-stu-id="083bd-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="083bd-138">Un utilisateur perd de l’état dans ce scénario s’il s’éloigne de la forme multistep et retourne à la forme plus tard.</span><span class="sxs-lookup"><span data-stu-id="083bd-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="083bd-139">Panier &ndash; d’achat Tout élément commercialement important d’une application qui représente des revenus potentiels peut être maintenu.</span><span class="sxs-lookup"><span data-stu-id="083bd-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="083bd-140">Un utilisateur qui perd son état, et donc son panier d’achat, peut acheter moins de produits ou de services lorsqu’il reviendra sur le site plus tard.</span><span class="sxs-lookup"><span data-stu-id="083bd-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="083bd-141">Il n’est généralement pas nécessaire de préserver l’état facilement recréé, tel que le nom d’utilisateur entré dans un dialogue de connexion qui n’a pas été soumis.</span><span class="sxs-lookup"><span data-stu-id="083bd-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="083bd-142">Une application ne peut persister *l’état de l’application*.</span><span class="sxs-lookup"><span data-stu-id="083bd-142">An app can only persist *app state*.</span></span> <span data-ttu-id="083bd-143">Les IG ne peuvent pas être persistées, comme les instances des composants et leurs arbres de rendu.</span><span class="sxs-lookup"><span data-stu-id="083bd-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="083bd-144">Les composants et les arbres de rendu ne sont généralement pas sérialisables.</span><span class="sxs-lookup"><span data-stu-id="083bd-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="083bd-145">Pour persister quelque chose de similaire à l’état d’interface utilisateur, tels que les nœuds élargis d’un TreeView, l’application doit avoir un code personnalisé pour modéliser le comportement comme état d’application sérialisable.</span><span class="sxs-lookup"><span data-stu-id="083bd-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="083bd-146">Où persister l’état</span><span class="sxs-lookup"><span data-stu-id="083bd-146">Where to persist state</span></span>

<span data-ttu-id="083bd-147">Trois emplacements communs existent pour Blazor l’état persistant dans une application Server.</span><span class="sxs-lookup"><span data-stu-id="083bd-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="083bd-148">Chaque approche est la mieux adaptée à différents scénarios et comporte des mises en garde différentes :</span><span class="sxs-lookup"><span data-stu-id="083bd-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="083bd-149">Côté serveur dans une base de données</span><span class="sxs-lookup"><span data-stu-id="083bd-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="083bd-150">URL</span><span class="sxs-lookup"><span data-stu-id="083bd-150">URL</span></span>](#url)
* [<span data-ttu-id="083bd-151">Côté client dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="083bd-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="083bd-152">Côté serveur dans une base de données</span><span class="sxs-lookup"><span data-stu-id="083bd-152">Server-side in a database</span></span>

<span data-ttu-id="083bd-153">Pour la persistance permanente des données ou pour toute donnée qui doit s’étendre sur plusieurs utilisateurs ou appareils, une base de données indépendante côté serveur est presque certainement le meilleur choix.</span><span class="sxs-lookup"><span data-stu-id="083bd-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="083bd-154">Options disponibles :</span><span class="sxs-lookup"><span data-stu-id="083bd-154">Options include:</span></span>

* <span data-ttu-id="083bd-155">Base de données SQL relationnelle</span><span class="sxs-lookup"><span data-stu-id="083bd-155">Relational SQL database</span></span>
* <span data-ttu-id="083bd-156">stockage clé-valeur</span><span class="sxs-lookup"><span data-stu-id="083bd-156">Key-value store</span></span>
* <span data-ttu-id="083bd-157">Magasin Blob</span><span class="sxs-lookup"><span data-stu-id="083bd-157">Blob store</span></span>
* <span data-ttu-id="083bd-158">Magasin de table</span><span class="sxs-lookup"><span data-stu-id="083bd-158">Table store</span></span>

<span data-ttu-id="083bd-159">Une fois que les données sont enregistrées dans la base de données, un nouveau circuit peut être lancé par un utilisateur à tout moment.</span><span class="sxs-lookup"><span data-stu-id="083bd-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="083bd-160">Les données de l’utilisateur sont conservées et disponibles dans n’importe quel nouveau circuit.</span><span class="sxs-lookup"><span data-stu-id="083bd-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="083bd-161">Pour plus d’informations sur les options de stockage de données Azure, consultez les [bases de données Azure Storage Documentation](/azure/storage/) et [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="083bd-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="083bd-162">URL</span><span class="sxs-lookup"><span data-stu-id="083bd-162">URL</span></span>

<span data-ttu-id="083bd-163">Pour les données transitoires représentant l’état de navigation, modéliser les données dans le cadre de l’URL.</span><span class="sxs-lookup"><span data-stu-id="083bd-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="083bd-164">Voici quelques exemples d’état modélisé dans l’URL :</span><span class="sxs-lookup"><span data-stu-id="083bd-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="083bd-165">L’id d’une entité vue.</span><span class="sxs-lookup"><span data-stu-id="083bd-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="083bd-166">Le numéro de page actuel dans une grille bipée.</span><span class="sxs-lookup"><span data-stu-id="083bd-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="083bd-167">Le contenu de la barre d’adresse du navigateur est conservé :</span><span class="sxs-lookup"><span data-stu-id="083bd-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="083bd-168">Si l’utilisateur recharge manuellement la page.</span><span class="sxs-lookup"><span data-stu-id="083bd-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="083bd-169">Si le serveur&mdash;Web devient indisponible, l’utilisateur est contraint de recharger la page afin de se connecter à un autre serveur.</span><span class="sxs-lookup"><span data-stu-id="083bd-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="083bd-170">Pour plus d’informations `@page` sur la <xref:blazor/routing>définition des modèles d’URL avec la directive, voir .</span><span class="sxs-lookup"><span data-stu-id="083bd-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="083bd-171">Côté client dans le navigateur</span><span class="sxs-lookup"><span data-stu-id="083bd-171">Client-side in the browser</span></span>

<span data-ttu-id="083bd-172">Pour les données transitoires que l’utilisateur crée activement, `localStorage` un `sessionStorage` magasin d’accompagnement commun est celui du navigateur et des collections.</span><span class="sxs-lookup"><span data-stu-id="083bd-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="083bd-173">L’application n’est pas nécessaire pour gérer ou effacer l’état stocké si le circuit est abandonné, ce qui est un avantage sur le stockage côté serveur.</span><span class="sxs-lookup"><span data-stu-id="083bd-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="083bd-174">"Client-side" dans cette section se réfère à des scénarios côté client dans le navigateur, pas le [ Blazor modèle d’hébergement WebAssembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="083bd-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="083bd-175">`localStorage`et `sessionStorage` peut être Blazor utilisé dans les applications WebAssembly, mais seulement en écrivant du code personnalisé ou en utilisant un paquet de 3ème partie.</span><span class="sxs-lookup"><span data-stu-id="083bd-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="083bd-176">`localStorage`et `sessionStorage` diffèrent comme suit:</span><span class="sxs-lookup"><span data-stu-id="083bd-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="083bd-177">`localStorage`est étendue au navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="083bd-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="083bd-178">Si l’utilisateur recharge la page ou ferme et rouvre le navigateur, l’état persiste.</span><span class="sxs-lookup"><span data-stu-id="083bd-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="083bd-179">Si l’utilisateur ouvre plusieurs onglets de navigateur, l’état est partagé sur les onglets.</span><span class="sxs-lookup"><span data-stu-id="083bd-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="083bd-180">Les données `localStorage` persistent jusqu’à ce qu’elles soient explicitement effacées.</span><span class="sxs-lookup"><span data-stu-id="083bd-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="083bd-181">`sessionStorage`est étendue à l’onglet navigateur de l’utilisateur. Si l’utilisateur recharge l’onglet, l’état persiste.</span><span class="sxs-lookup"><span data-stu-id="083bd-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="083bd-182">Si l’utilisateur ferme l’onglet ou le navigateur, l’état est perdu.</span><span class="sxs-lookup"><span data-stu-id="083bd-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="083bd-183">Si l’utilisateur ouvre plusieurs onglets de navigateur, chaque onglet a sa propre version indépendante des données.</span><span class="sxs-lookup"><span data-stu-id="083bd-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="083bd-184">En `sessionStorage` général, est plus sûr à utiliser.</span><span class="sxs-lookup"><span data-stu-id="083bd-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="083bd-185">`sessionStorage`évite le risque qu’un utilisateur ouvre plusieurs onglets et rencontre ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="083bd-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="083bd-186">Bugs dans le stockage de l’état à travers les onglets.</span><span class="sxs-lookup"><span data-stu-id="083bd-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="083bd-187">Comportement confus quand un onglet surwrite l’état d’autres onglets.</span><span class="sxs-lookup"><span data-stu-id="083bd-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="083bd-188">`localStorage`est le meilleur choix si l’application doit persister état à travers la fermeture et la réouverture du navigateur.</span><span class="sxs-lookup"><span data-stu-id="083bd-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="083bd-189">Mises en garde pour l’utilisation du stockage du navigateur :</span><span class="sxs-lookup"><span data-stu-id="083bd-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="083bd-190">Semblable à l’utilisation d’une base de données côté serveur, le chargement et l’enregistrement des données sont asynchrones.</span><span class="sxs-lookup"><span data-stu-id="083bd-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="083bd-191">Contrairement à une base de données côté serveur, le stockage n’est pas disponible pendant le préramanement parce que la page demandée n’existe pas dans le navigateur pendant l’étape de pré-démarrage.</span><span class="sxs-lookup"><span data-stu-id="083bd-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="083bd-192">Le stockage de quelques kilooctets de Blazor données est raisonnable pour persister pour les applications Server.</span><span class="sxs-lookup"><span data-stu-id="083bd-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="083bd-193">Au-delà de quelques kilooctets, vous devez tenir compte des implications de performance parce que les données sont chargées et enregistrées sur l’ensemble du réseau.</span><span class="sxs-lookup"><span data-stu-id="083bd-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="083bd-194">Les utilisateurs peuvent afficher ou falsifier les données.</span><span class="sxs-lookup"><span data-stu-id="083bd-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="083bd-195">ASP.NET [la protection des données de base](xref:security/data-protection/introduction) peut atténuer le risque.</span><span class="sxs-lookup"><span data-stu-id="083bd-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="083bd-196">Solutions de stockage de navigateurs tiers</span><span class="sxs-lookup"><span data-stu-id="083bd-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="083bd-197">Les forfaits NuGet tiers fournissent `localStorage` des `sessionStorage`API pour travailler avec et .</span><span class="sxs-lookup"><span data-stu-id="083bd-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="083bd-198">Il vaut la peine d’envisager de choisir un package qui utilise de manière transparente ASP.NET [la protection des données](xref:security/data-protection/introduction)de Core .</span><span class="sxs-lookup"><span data-stu-id="083bd-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="083bd-199">ASP.NET Core Data Protection crypte les données stockées et réduit le risque potentiel de falsification des données stockées.</span><span class="sxs-lookup"><span data-stu-id="083bd-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="083bd-200">Si les données Sérialisées JSON sont stockées en texte clair, les utilisateurs peuvent voir les données à l’aide d’outils de développeur de navigateurs et modifier également les données stockées.</span><span class="sxs-lookup"><span data-stu-id="083bd-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="083bd-201">La sécurisation des données n’est pas toujours un problème parce que les données peuvent être de nature triviale.</span><span class="sxs-lookup"><span data-stu-id="083bd-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="083bd-202">Par exemple, la lecture ou la modification de la couleur stockée d’un élément d’interface utilisateur n’est pas un risque de sécurité important pour l’utilisateur ou l’organisation.</span><span class="sxs-lookup"><span data-stu-id="083bd-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="083bd-203">Évitez de permettre aux utilisateurs d’inspecter ou de falsifier des *données sensibles.*</span><span class="sxs-lookup"><span data-stu-id="083bd-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="083bd-204">Paquet expérimental de stockage de navigateur protégé</span><span class="sxs-lookup"><span data-stu-id="083bd-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="083bd-205">Un exemple d’un paquet NuGet `localStorage` `sessionStorage` qui fournit la protection [des données](xref:security/data-protection/introduction) pour et est [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="083bd-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="083bd-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`est un paquet expérimental non soutenu qui ne convient pas à l’utilisation de la production en ce moment.</span><span class="sxs-lookup"><span data-stu-id="083bd-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="083bd-207">Installation</span><span class="sxs-lookup"><span data-stu-id="083bd-207">Installation</span></span>

<span data-ttu-id="083bd-208">Pour installer `Microsoft.AspNetCore.ProtectedBrowserStorage` le paquet :</span><span class="sxs-lookup"><span data-stu-id="083bd-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="083bd-209">Dans Blazor le projet d’application Server, ajoutez une référence de paquet à [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="083bd-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="083bd-210">Dans le HTML de haut niveau (par exemple, dans le fichier *Pages/_Host.cshtml* dans le modèle de projet par défaut), ajoutez l’étiquette suivante `<script>` :</span><span class="sxs-lookup"><span data-stu-id="083bd-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="083bd-211">Dans `Startup.ConfigureServices` la méthode, appelez `AddProtectedBrowserStorage` à ajouter `localStorage` et `sessionStorage` les services à la collecte de service :</span><span class="sxs-lookup"><span data-stu-id="083bd-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="083bd-212">Enregistrer et charger des données dans un composant</span><span class="sxs-lookup"><span data-stu-id="083bd-212">Save and load data within a component</span></span>

<span data-ttu-id="083bd-213">Dans tout composant qui nécessite le chargement [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) ou l’enregistrement des données au stockage du navigateur, utilisez-le pour injecter un exemple de l’un ou l’autre des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="083bd-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="083bd-214">Le choix dépend du magasin de support que vous souhaitez utiliser.</span><span class="sxs-lookup"><span data-stu-id="083bd-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="083bd-215">Dans l’exemple `sessionStorage` suivant, est utilisé:</span><span class="sxs-lookup"><span data-stu-id="083bd-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="083bd-216">L’instruction `@using` peut être placée dans un fichier *_Imports.razor* au lieu de dans le composant.</span><span class="sxs-lookup"><span data-stu-id="083bd-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="083bd-217">L’utilisation du fichier *_Imports.razor* met l’espace de nom à la disposition de plus grands segments de l’application ou de l’application entière.</span><span class="sxs-lookup"><span data-stu-id="083bd-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="083bd-218">Pour maintenir `_currentCount` la `Counter` valeur de la composante `IncrementCount` du modèle `ProtectedSessionStore.SetAsync`de projet, modifiez la méthode à utiliser :</span><span class="sxs-lookup"><span data-stu-id="083bd-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="083bd-219">Dans les applications plus grandes et plus réalistes, le stockage de champs individuels est un scénario peu probable.</span><span class="sxs-lookup"><span data-stu-id="083bd-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="083bd-220">Les applications sont plus susceptibles de stocker des objets modèles entiers qui incluent un état complexe.</span><span class="sxs-lookup"><span data-stu-id="083bd-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="083bd-221">`ProtectedSessionStore`sérialis et désélise automatiquement les données JSON.</span><span class="sxs-lookup"><span data-stu-id="083bd-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="083bd-222">Dans l’exemple de `_currentCount` code précédent, les données sont stockées comme `sessionStorage['count']` dans le navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="083bd-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="083bd-223">Les données ne sont pas stockées en texte clair mais sont plutôt protégées à l’aide de ASP.NET [protection des données](xref:security/data-protection/introduction)de Core .</span><span class="sxs-lookup"><span data-stu-id="083bd-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="083bd-224">Les données chiffrées peuvent être vues si `sessionStorage['count']` elles sont évaluées dans la console de développeur du navigateur.</span><span class="sxs-lookup"><span data-stu-id="083bd-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="083bd-225">Pour récupérer `_currentCount` les données si `Counter` l’utilisateur retourne au composant plus tard (y compris s’ils sont sur un circuit entièrement nouveau), utilisez `ProtectedSessionStore.GetAsync`:</span><span class="sxs-lookup"><span data-stu-id="083bd-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="083bd-226">Si les paramètres du composant incluent `ProtectedSessionStore.GetAsync` l’état de `OnParametersSetAsync`navigation, appelez et attribuez le résultat dans , pas `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="083bd-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="083bd-227">`OnInitializedAsync`n’est appelé qu’une seule fois lorsque le composant est d’abord instantané.</span><span class="sxs-lookup"><span data-stu-id="083bd-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="083bd-228">`OnInitializedAsync`n’est pas appelé à nouveau plus tard si l’utilisateur navigue vers une URL différente tout en restant sur la même page.</span><span class="sxs-lookup"><span data-stu-id="083bd-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="083bd-229">Pour plus d’informations, consultez <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="083bd-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="083bd-230">Les exemples dans cette section ne fonctionnent que si le serveur n’a pas de prérendering activé.</span><span class="sxs-lookup"><span data-stu-id="083bd-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="083bd-231">Avec le prerendering activé, une erreur est générée similaire à :</span><span class="sxs-lookup"><span data-stu-id="083bd-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="083bd-232">Les appels interop JavaScript ne peuvent pas être émis pour le moment.</span><span class="sxs-lookup"><span data-stu-id="083bd-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="083bd-233">C’est parce que le composant est pré-rendu.</span><span class="sxs-lookup"><span data-stu-id="083bd-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="083bd-234">Soit désactiver le prerendering ou ajouter du code supplémentaire pour travailler avec le prerendering.</span><span class="sxs-lookup"><span data-stu-id="083bd-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="083bd-235">Pour en savoir plus sur l’écriture de code qui fonctionne avec le prerendering, consultez la section [de précommandage Handle.](#handle-prerendering)</span><span class="sxs-lookup"><span data-stu-id="083bd-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="083bd-236">Gérer l’état de chargement</span><span class="sxs-lookup"><span data-stu-id="083bd-236">Handle the loading state</span></span>

<span data-ttu-id="083bd-237">Étant donné que le stockage du navigateur est asynchrone (accessible sur une connexion réseau), il ya toujours une période de temps avant que les données sont chargées et disponibles pour une utilisation par un composant.</span><span class="sxs-lookup"><span data-stu-id="083bd-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="083bd-238">Pour les meilleurs résultats, rendre un message d’état de chargement pendant que le chargement est en cours au lieu d’afficher des données vierges ou par défaut.</span><span class="sxs-lookup"><span data-stu-id="083bd-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="083bd-239">Une approche consiste à déterminer `null` si les données sont (toujours en charge) ou non.</span><span class="sxs-lookup"><span data-stu-id="083bd-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="083bd-240">Dans le `Counter` composant par défaut, `int`le nombre est tenu dans un .</span><span class="sxs-lookup"><span data-stu-id="083bd-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="083bd-241">Rendre `_currentCount` l’annulation en ajoutant`?`un point`int`d’interrogation ( ) au type ( ):</span><span class="sxs-lookup"><span data-stu-id="083bd-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="083bd-242">Au lieu d’afficher sans condition le bouton de comptage et **d’incrément,** choisissez d’afficher ces éléments uniquement si les données sont chargées :</span><span class="sxs-lookup"><span data-stu-id="083bd-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="083bd-243">Gérer le prerendering</span><span class="sxs-lookup"><span data-stu-id="083bd-243">Handle prerendering</span></span>

<span data-ttu-id="083bd-244">Pendant le précommandage :</span><span class="sxs-lookup"><span data-stu-id="083bd-244">During prerendering:</span></span>

* <span data-ttu-id="083bd-245">Il n’existe pas de connexion interactive au navigateur de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="083bd-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="083bd-246">Le navigateur n’a pas encore de page dans laquelle il peut exécuter le code JavaScript.</span><span class="sxs-lookup"><span data-stu-id="083bd-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="083bd-247">`localStorage`ou `sessionStorage` ne sont pas disponibles pendant le pré-programme.</span><span class="sxs-lookup"><span data-stu-id="083bd-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="083bd-248">Si le composant tente d’interagir avec le stockage, une erreur est générée de manière similaire à :</span><span class="sxs-lookup"><span data-stu-id="083bd-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="083bd-249">Les appels interop JavaScript ne peuvent pas être émis pour le moment.</span><span class="sxs-lookup"><span data-stu-id="083bd-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="083bd-250">C’est parce que le composant est pré-rendu.</span><span class="sxs-lookup"><span data-stu-id="083bd-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="083bd-251">Une façon de résoudre l’erreur est de désactiver le prerendering.</span><span class="sxs-lookup"><span data-stu-id="083bd-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="083bd-252">C’est généralement le meilleur choix si l’application fait un usage intensif du stockage basé sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="083bd-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="083bd-253">Prerendering ajoute de la complexité et ne profite pas à l’application `localStorage` `sessionStorage` parce que l’application ne peut pas pré-rôder tout contenu utile jusqu’à ce que ou sont disponibles.</span><span class="sxs-lookup"><span data-stu-id="083bd-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="083bd-254">Pour désactiver le prerendering, ouvrez le fichier *Pages/_Host.cshtml* et modifiez le `render-mode` [composant Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) pour <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span><span class="sxs-lookup"><span data-stu-id="083bd-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="083bd-255">Prerendering peut être utile pour d’autres pages qui n’utilisent `localStorage` pas ou `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="083bd-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="083bd-256">Pour garder le prerendering activé, reportez l’opération de chargement jusqu’à ce que le navigateur soit connecté au circuit.</span><span class="sxs-lookup"><span data-stu-id="083bd-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="083bd-257">Ce qui suit est un exemple pour stocker une valeur de comptoir :</span><span class="sxs-lookup"><span data-stu-id="083bd-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="083bd-258">Tenez compte de la préservation de l’État à un endroit commun</span><span class="sxs-lookup"><span data-stu-id="083bd-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="083bd-259">Si de nombreux composants s’appuient sur le stockage par navigateur, la ré-implémentation du code fournisseur d’État crée plusieurs fois la duplication du code.</span><span class="sxs-lookup"><span data-stu-id="083bd-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="083bd-260">Une option pour éviter la duplication de code est de créer un *composant parent fournisseur d’état* qui encapsule la logique de fournisseur d’état.</span><span class="sxs-lookup"><span data-stu-id="083bd-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="083bd-261">Les composants pour enfants peuvent fonctionner avec des données persistantes sans tenir compte du mécanisme de persistance de l’État.</span><span class="sxs-lookup"><span data-stu-id="083bd-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="083bd-262">Dans l’exemple `CounterStateProvider` suivant d’un composant, les données de compteur sont persistées :</span><span class="sxs-lookup"><span data-stu-id="083bd-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="083bd-263">Le `CounterStateProvider` composant gère la phase de chargement en ne rendant pas son contenu enfant jusqu’à ce que le chargement soit terminé.</span><span class="sxs-lookup"><span data-stu-id="083bd-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="083bd-264">Pour utiliser `CounterStateProvider` le composant, enveloppez une instance du composant autour de tout autre composant qui nécessite l’accès à l’état de comptoir.</span><span class="sxs-lookup"><span data-stu-id="083bd-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="083bd-265">Pour rendre l’état accessible à tous les `CounterStateProvider` composants `Router` d’une application, enveloppez le composant autour du `App` composant *(App.razor*) :</span><span class="sxs-lookup"><span data-stu-id="083bd-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="083bd-266">Les composants enveloppés reçoivent et peuvent modifier l’état de comptoir persistant.</span><span class="sxs-lookup"><span data-stu-id="083bd-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="083bd-267">Le `Counter` composant suivant met en œuvre le modèle :</span><span class="sxs-lookup"><span data-stu-id="083bd-267">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="083bd-268">Le composant précédent n’est `ProtectedBrowserStorage`pas nécessaire pour interagir avec, ni traiter d’une phase de «chargement».</span><span class="sxs-lookup"><span data-stu-id="083bd-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="083bd-269">Pour faire face à la prérendering comme décrit précédemment, `CounterStateProvider` peut être modifié de sorte que tous les composants qui consomment les données de compteur fonctionnent automatiquement avec le prerendering.</span><span class="sxs-lookup"><span data-stu-id="083bd-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="083bd-270">Voir la section [De pré-appel de poignée](#handle-prerendering) pour plus de détails.</span><span class="sxs-lookup"><span data-stu-id="083bd-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="083bd-271">En général, le modèle *de composante parent du fournisseur d’État* est recommandé :</span><span class="sxs-lookup"><span data-stu-id="083bd-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="083bd-272">Pour consommer l’état dans beaucoup d’autres composants.</span><span class="sxs-lookup"><span data-stu-id="083bd-272">To consume state in many other components.</span></span>
* <span data-ttu-id="083bd-273">S’ll n’y a qu’un seul objet de haut niveau à persister.</span><span class="sxs-lookup"><span data-stu-id="083bd-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="083bd-274">Pour persister de nombreux objets d’état différents et consommer différents sous-ensembles d’objets dans différents endroits, il est préférable d’éviter de manipuler le chargement et l’économie de l’État dans le monde entier.</span><span class="sxs-lookup"><span data-stu-id="083bd-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
