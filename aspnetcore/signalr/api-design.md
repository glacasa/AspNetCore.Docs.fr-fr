---
title: SignalRConsidérations relatives à la conception d’API
author: anurse
description: Découvrez comment concevoir des SignalR API pour la compatibilité entre les versions de votre application.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/api-design
ms.openlocfilehash: ef0285c611bd41d7fe686a4b370b6daae9be9174
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018986"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="d26f7-103">SignalRConsidérations relatives à la conception d’API</span><span class="sxs-lookup"><span data-stu-id="d26f7-103">SignalR API design considerations</span></span>

<span data-ttu-id="d26f7-104">Par [Andrew Stanton-infirmière](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="d26f7-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="d26f7-105">Cet article fournit des conseils sur la création d' SignalR API basées sur.</span><span class="sxs-lookup"><span data-stu-id="d26f7-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="d26f7-106">Utiliser des paramètres d’objet personnalisés pour garantir la compatibilité descendante</span><span class="sxs-lookup"><span data-stu-id="d26f7-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="d26f7-107">L’ajout de paramètres à une SignalR méthode de concentrateur (sur le client ou le serveur) est une *modification avec rupture*.</span><span class="sxs-lookup"><span data-stu-id="d26f7-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="d26f7-108">Cela signifie que les clients/serveurs plus anciens recevront des erreurs lorsqu’ils essaieront d’appeler la méthode sans le nombre approprié de paramètres.</span><span class="sxs-lookup"><span data-stu-id="d26f7-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="d26f7-109">Toutefois, l’ajout de propriétés à un paramètre d’objet personnalisé n’est **pas** une modification avec rupture.</span><span class="sxs-lookup"><span data-stu-id="d26f7-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="d26f7-110">Cela peut être utilisé pour concevoir des API compatibles qui résistent aux modifications sur le client ou le serveur.</span><span class="sxs-lookup"><span data-stu-id="d26f7-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="d26f7-111">Par exemple, considérez une API côté serveur comme suit :</span><span class="sxs-lookup"><span data-stu-id="d26f7-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="d26f7-112">Le client JavaScript appelle cette méthode à l’aide `invoke` de comme suit :</span><span class="sxs-lookup"><span data-stu-id="d26f7-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="d26f7-113">Si vous ajoutez ultérieurement un deuxième paramètre à la méthode serveur, les clients plus anciens ne fourniront pas cette valeur de paramètre.</span><span class="sxs-lookup"><span data-stu-id="d26f7-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="d26f7-114">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="d26f7-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="d26f7-115">Lorsque l’ancien client tente d’appeler cette méthode, il obtient une erreur semblable à celle-ci :</span><span class="sxs-lookup"><span data-stu-id="d26f7-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="d26f7-116">Sur le serveur, un message de journal semblable à celui-ci s’affiche :</span><span class="sxs-lookup"><span data-stu-id="d26f7-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="d26f7-117">L’ancien client n’a envoyé qu’un seul paramètre, mais l’API serveur la plus récente nécessitait deux paramètres.</span><span class="sxs-lookup"><span data-stu-id="d26f7-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="d26f7-118">L’utilisation d’objets personnalisés comme paramètres vous offre plus de souplesse.</span><span class="sxs-lookup"><span data-stu-id="d26f7-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="d26f7-119">Nous allons modifier la conception de l’API d’origine pour utiliser un objet personnalisé :</span><span class="sxs-lookup"><span data-stu-id="d26f7-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="d26f7-120">À présent, le client utilise un objet pour appeler la méthode :</span><span class="sxs-lookup"><span data-stu-id="d26f7-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="d26f7-121">Au lieu d’ajouter un paramètre, ajoutez une propriété à l' `TotalLengthRequest` objet :</span><span class="sxs-lookup"><span data-stu-id="d26f7-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="d26f7-122">Lorsque l’ancien client envoie un seul paramètre, la `Param2` propriété supplémentaire est conservée `null` .</span><span class="sxs-lookup"><span data-stu-id="d26f7-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="d26f7-123">Vous pouvez détecter un message envoyé par un ancien client en activant la `Param2` pour `null` et en appliquant une valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="d26f7-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="d26f7-124">Un nouveau client peut envoyer les deux paramètres.</span><span class="sxs-lookup"><span data-stu-id="d26f7-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="d26f7-125">La même technique fonctionne pour les méthodes définies sur le client.</span><span class="sxs-lookup"><span data-stu-id="d26f7-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="d26f7-126">Vous pouvez envoyer un objet personnalisé du côté serveur :</span><span class="sxs-lookup"><span data-stu-id="d26f7-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="d26f7-127">Côté client, vous accédez à la `Message` propriété au lieu d’utiliser un paramètre :</span><span class="sxs-lookup"><span data-stu-id="d26f7-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="d26f7-128">Si vous décidez par la suite d’ajouter l’expéditeur du message à la charge utile, ajoutez une propriété à l’objet :</span><span class="sxs-lookup"><span data-stu-id="d26f7-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="d26f7-129">Les clients plus anciens n’attendent pas la `Sender` valeur, donc ils l’ignorent.</span><span class="sxs-lookup"><span data-stu-id="d26f7-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="d26f7-130">Un nouveau client peut l’accepter en mettant à jour pour lire la nouvelle propriété :</span><span class="sxs-lookup"><span data-stu-id="d26f7-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="d26f7-131">Dans ce cas, le nouveau client tolère également un ancien serveur qui ne fournit pas la `Sender` valeur.</span><span class="sxs-lookup"><span data-stu-id="d26f7-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="d26f7-132">Étant donné que l’ancien serveur ne fournit pas la `Sender` valeur, le client vérifie s’il existe avant d’y accéder.</span><span class="sxs-lookup"><span data-stu-id="d26f7-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
