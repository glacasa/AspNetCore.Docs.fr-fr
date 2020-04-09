---
title: Quoi de neuf dans ASP.NET Core 3.1
author: rick-anderson
description: Renseignez-vous sur les nouvelles fonctionnalités dans ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662707"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="ef933-103">Quoi de neuf dans ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="ef933-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="ef933-104">Cet article met en évidence les changements les plus significatifs dans ASP.NET Core 3.1 avec des liens vers la documentation pertinente.</span><span class="sxs-lookup"><span data-stu-id="ef933-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="ef933-105">Soutien partiel de classe pour les composants Razor</span><span class="sxs-lookup"><span data-stu-id="ef933-105">Partial class support for Razor components</span></span>

<span data-ttu-id="ef933-106">Les composants de rasoir sont maintenant générés en tant que classes partielles.</span><span class="sxs-lookup"><span data-stu-id="ef933-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="ef933-107">Le code d’un composant Razor peut être écrit à l’aide d’un fichier de code défini comme une classe partielle plutôt que de définir tout le code du composant dans un seul fichier.</span><span class="sxs-lookup"><span data-stu-id="ef933-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="ef933-108">Pour plus d’informations, voir [soutien de classe partielle](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="ef933-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="ef933-109">Composant Tag Helper et les paramètres de passage aux composants de haut niveau</span><span class="sxs-lookup"><span data-stu-id="ef933-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="ef933-110">Dans Blazor ASP.NET Core 3.0, les composants ont été rendus en pages et`Html.RenderComponentAsync`vues à l’aide d’un assistant HTML ( ).</span><span class="sxs-lookup"><span data-stu-id="ef933-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="ef933-111">Dans ASP.NET Core 3.1, rendre un composant à partir d’une page ou une vue avec le nouvel assistant d’étiquette de composant :</span><span class="sxs-lookup"><span data-stu-id="ef933-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="ef933-112">L’assistant HTML reste pris en charge dans ASP.NET Core 3.1, mais l’aide à l’étiquette des composants est recommandée.</span><span class="sxs-lookup"><span data-stu-id="ef933-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="ef933-113">Les applications serveur peuvent désormais passer des paramètres aux composants de haut niveau pendant le rendu initial.</span><span class="sxs-lookup"><span data-stu-id="ef933-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="ef933-114">Auparavant, vous ne pouviez passer les paramètres à un composant de haut niveau avec [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="ef933-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="ef933-115">Avec cette version, [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) et [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="ef933-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="ef933-116">Toutes les valeurs de paramètres spécifiées sont sérialisées sous le titre JSON et incluses dans la réponse initiale.</span><span class="sxs-lookup"><span data-stu-id="ef933-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="ef933-117">Par exemple, pré-prerender un `Counter` composant`IncrementAmount`avec une quantité incrément ( )</span><span class="sxs-lookup"><span data-stu-id="ef933-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="ef933-118">Pour plus d’informations, voir [Intégrer les composants dans les pages Razor et les applications MVC](xref:blazor/integrate-components).</span><span class="sxs-lookup"><span data-stu-id="ef933-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="ef933-119">Soutien aux files d’attente partagées dans HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ef933-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="ef933-120">[HTTP.sys](xref:fundamentals/servers/httpsys) prend en charge la création de files d’attente anonymes.</span><span class="sxs-lookup"><span data-stu-id="ef933-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="ef933-121">Dans ASP.NET Core 3.1, nous avons ajouté à la capacité de créer ou de joindre à une file d’attente existante nommée HTTP.sys demande.</span><span class="sxs-lookup"><span data-stu-id="ef933-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="ef933-122">La création ou l’attachement à une file d’attente existante de demande HTTP.sys permet des scénarios où le processus de contrôleur HTTP.sys qui possède la file d’attente est indépendant du processus d’auditeur.</span><span class="sxs-lookup"><span data-stu-id="ef933-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="ef933-123">Cette indépendance permet de préserver les connexions existantes et les demandes enqueued entre le processus d’écoute redémarre:</span><span class="sxs-lookup"><span data-stu-id="ef933-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="ef933-124">Modifications de rupture pour les cookies SameSite</span><span class="sxs-lookup"><span data-stu-id="ef933-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="ef933-125">Le comportement des cookies SameSite a changé pour refléter les changements de navigateur à venir.</span><span class="sxs-lookup"><span data-stu-id="ef933-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="ef933-126">Cela peut affecter des scénarios d’authentification comme AzureAd, OpenIdConnect ou WsFederation.</span><span class="sxs-lookup"><span data-stu-id="ef933-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="ef933-127">Pour plus d’informations, consultez <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="ef933-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="ef933-128">Prévenir les actions Blazor par défaut pour les événements dans les applications</span><span class="sxs-lookup"><span data-stu-id="ef933-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="ef933-129">Utilisez `@on{EVENT}:preventDefault` l’attribut de directive pour empêcher l’action par défaut pour un événement.</span><span class="sxs-lookup"><span data-stu-id="ef933-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="ef933-130">Dans l’exemple suivant, l’action par défaut de l’affichage du caractère de la clé dans la boîte à texte est empêchée :</span><span class="sxs-lookup"><span data-stu-id="ef933-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="ef933-131">Pour plus d’informations, voir [Prévenir les actions par défaut](xref:blazor/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="ef933-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="ef933-132">Arrêter la Blazor propagation de l’événement dans les applications</span><span class="sxs-lookup"><span data-stu-id="ef933-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="ef933-133">Utilisez `@on{EVENT}:stopPropagation` l’attribut de la directive pour arrêter la propagation de l’événement.</span><span class="sxs-lookup"><span data-stu-id="ef933-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="ef933-134">Dans l’exemple suivant, la sélection de la `<div>` case à cocher `<div>`empêche les événements de clic de l’enfant de se propager au parent :</span><span class="sxs-lookup"><span data-stu-id="ef933-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="ef933-135">Pour plus d’informations, voir [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="ef933-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="ef933-136">Erreurs détaillées Blazor lors du développement de l’application</span><span class="sxs-lookup"><span data-stu-id="ef933-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="ef933-137">Lorsqu’une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application aide à dépanner et à résoudre le problème.</span><span class="sxs-lookup"><span data-stu-id="ef933-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="ef933-138">Lorsqu’une erreur Blazor se produit, les applications affichent une barre d’or au bas de l’écran :</span><span class="sxs-lookup"><span data-stu-id="ef933-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="ef933-139">Pendant le développement, la barre d’or vous dirige vers la console du navigateur, où vous pouvez voir l’exception.</span><span class="sxs-lookup"><span data-stu-id="ef933-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="ef933-140">En production, la barre d’or informe l’utilisateur qu’une erreur s’est produite et recommande de rafraîchir le navigateur.</span><span class="sxs-lookup"><span data-stu-id="ef933-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="ef933-141">Pour plus d’informations, voir [erreurs détaillées pendant le développement](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="ef933-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
