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
# <a name="whats-new-in-aspnet-core-31"></a>Quoi de neuf dans ASP.NET Core 3.1

Cet article met en évidence les changements les plus significatifs dans ASP.NET Core 3.1 avec des liens vers la documentation pertinente.

## <a name="partial-class-support-for-razor-components"></a>Soutien partiel de classe pour les composants Razor

Les composants de rasoir sont maintenant générés en tant que classes partielles. Le code d’un composant Razor peut être écrit à l’aide d’un fichier de code défini comme une classe partielle plutôt que de définir tout le code du composant dans un seul fichier. Pour plus d’informations, voir [soutien de classe partielle](xref:blazor/components#partial-class-support).

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorComposant Tag Helper et les paramètres de passage aux composants de haut niveau

Dans Blazor ASP.NET Core 3.0, les composants ont été rendus en pages et`Html.RenderComponentAsync`vues à l’aide d’un assistant HTML ( ). Dans ASP.NET Core 3.1, rendre un composant à partir d’une page ou une vue avec le nouvel assistant d’étiquette de composant :

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

L’assistant HTML reste pris en charge dans ASP.NET Core 3.1, mais l’aide à l’étiquette des composants est recommandée.

BlazorLes applications serveur peuvent désormais passer des paramètres aux composants de haut niveau pendant le rendu initial. Auparavant, vous ne pouviez passer les paramètres à un composant de haut niveau avec [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Avec cette version, [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) et [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) sont pris en charge. Toutes les valeurs de paramètres spécifiées sont sérialisées sous le titre JSON et incluses dans la réponse initiale.

Par exemple, pré-prerender un `Counter` composant`IncrementAmount`avec une quantité incrément ( )

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Pour plus d’informations, voir [Intégrer les composants dans les pages Razor et les applications MVC](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>Soutien aux files d’attente partagées dans HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) prend en charge la création de files d’attente anonymes. Dans ASP.NET Core 3.1, nous avons ajouté à la capacité de créer ou de joindre à une file d’attente existante nommée HTTP.sys demande. La création ou l’attachement à une file d’attente existante de demande HTTP.sys permet des scénarios où le processus de contrôleur HTTP.sys qui possède la file d’attente est indépendant du processus d’auditeur. Cette indépendance permet de préserver les connexions existantes et les demandes enqueued entre le processus d’écoute redémarre:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Modifications de rupture pour les cookies SameSite

Le comportement des cookies SameSite a changé pour refléter les changements de navigateur à venir. Cela peut affecter des scénarios d’authentification comme AzureAd, OpenIdConnect ou WsFederation. Pour plus d’informations, consultez <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a>Prévenir les actions Blazor par défaut pour les événements dans les applications

Utilisez `@on{EVENT}:preventDefault` l’attribut de directive pour empêcher l’action par défaut pour un événement. Dans l’exemple suivant, l’action par défaut de l’affichage du caractère de la clé dans la boîte à texte est empêchée :

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Pour plus d’informations, voir [Prévenir les actions par défaut](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a>Arrêter la Blazor propagation de l’événement dans les applications

Utilisez `@on{EVENT}:stopPropagation` l’attribut de la directive pour arrêter la propagation de l’événement. Dans l’exemple suivant, la sélection de la `<div>` case à cocher `<div>`empêche les événements de clic de l’enfant de se propager au parent :

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

Pour plus d’informations, voir [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-opno-locblazor-app-development"></a>Erreurs détaillées Blazor lors du développement de l’application

Lorsqu’une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application aide à dépanner et à résoudre le problème. Lorsqu’une erreur Blazor se produit, les applications affichent une barre d’or au bas de l’écran :

* Pendant le développement, la barre d’or vous dirige vers la console du navigateur, où vous pouvez voir l’exception.
* En production, la barre d’or informe l’utilisateur qu’une erreur s’est produite et recommande de rafraîchir le navigateur.

Pour plus d’informations, voir [erreurs détaillées pendant le développement](xref:blazor/handle-errors#detailed-errors-during-development).
