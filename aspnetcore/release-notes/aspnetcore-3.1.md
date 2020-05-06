---
title: Nouveautés de ASP.NET Core 3,1
author: rick-anderson
description: Découvrez les nouvelles fonctionnalités de ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 67fc972676549a02265035c129c513f11d303d51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774045"
---
# <a name="whats-new-in-aspnet-core-31"></a>Nouveautés de ASP.NET Core 3,1

Cet article met en évidence les modifications les plus importantes apportées à ASP.NET Core 3,1 avec des liens vers la documentation appropriée.

## <a name="partial-class-support-for-razor-components"></a>Prise en charge des Razor classes partielles pour les composants

Razorles composants sont maintenant générés en tant que classes partielles. Le code d' Razor un composant peut être écrit à l’aide d’un fichier code-behind défini en tant que classe partielle, plutôt que de définir tout le code du composant dans un fichier unique. Pour plus d’informations, consultez [prise en charge des classes partielles](xref:blazor/components#partial-class-support).

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>BlazorTag Helper de composant et passer des paramètres à des composants de niveau supérieur

Dans Blazor avec ASP.net Core 3,0, les composants étaient rendus dans des pages et des vues à l'`Html.RenderComponentAsync`aide d’un programme d’assistance HTML (). Dans ASP.NET Core 3,1, restituez un composant à partir d’une page ou d’une vue avec le nouveau tag Helper du composant :

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Le programme d’assistance HTML reste pris en charge dans ASP.NET Core 3,1, mais le tag Helper Component est recommandé.

BlazorLes applications serveur peuvent désormais passer des paramètres à des composants de niveau supérieur lors du rendu initial. Auparavant, vous pouviez uniquement passer des paramètres à un composant de niveau supérieur avec [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static). Avec cette version, [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) et [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) sont pris en charge. Toute valeur de paramètre spécifiée est sérialisée au format JSON et incluse dans la réponse initiale.

Par exemple, prérendez `Counter` un composant avec un volume d'`IncrementAmount`incrément () :

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Pour plus d’informations, consultez [intégrer des Razor composants dans des pages et des applications MVC](xref:blazor/integrate-components).

## <a name="support-for-shared-queues-in-httpsys"></a>Prise en charge des files d’attente partagées dans HTTP. sys

[Http. sys](xref:fundamentals/servers/httpsys) prend en charge la création de files d’attente de demandes anonymes. Dans ASP.NET Core 3,1, nous avons ajouté la possibilité de créer une file d’attente de requêtes HTTP. sys nommée existante ou de l’attacher à celle-ci. La création ou l’attachement à une file d’attente de requêtes HTTP. sys existante active les scénarios où le processus de contrôleur HTTP. sys qui possède la file d’attente est indépendant du processus d’écoute. Cette indépendance permet de conserver les connexions existantes et les demandes mises en file d’attente entre les redémarrages du processus de l’écouteur :

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a>Dernières modifications pour les cookies SameSite

Le comportement des cookies SameSite a changé pour refléter les futures modifications apportées au navigateur. Cela peut affecter les scénarios d’authentification tels que AzureAd, OpenIdConnect ou WsFederation. Pour plus d’informations, consultez <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a>Empêcher les actions par défaut des Blazor événements dans les applications

Utilisez l' `@on{EVENT}:preventDefault` attribut directive pour empêcher l’action par défaut d’un événement. Dans l’exemple suivant, l’action par défaut d’affichage du caractère de la clé dans la zone de texte est interdite :

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Pour plus d’informations, consultez [empêcher les actions par défaut](xref:blazor/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-blazor-apps"></a>Arrêter la propagation des événements Blazor dans les applications

Utilisez l' `@on{EVENT}:stopPropagation` attribut directive pour arrêter la propagation des événements. Dans l’exemple suivant, la sélection de la case à cocher empêche les `<div>` événements de clic de l’enfant `<div>`de se propager vers le parent :

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

Pour plus d’informations, consultez [arrêter la propagation des événements](xref:blazor/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-blazor-app-development"></a>Erreurs détaillées lors Blazor du développement d’applications

Quand une Blazor application ne fonctionne pas correctement pendant le développement, la réception d’informations d’erreur détaillées de l’application vous aide à résoudre les problèmes et à résoudre le problème. Lorsqu’une erreur se produit Blazor , les applications affichent une barre dorée en bas de l’écran :

* Pendant le développement, la barre dorée vous dirige vers la console du navigateur, où vous pouvez voir l’exception.
* En production, la barre dorée avertit l’utilisateur qu’une erreur s’est produite et recommande l’actualisation du navigateur.

Pour plus d’informations, consultez [erreurs détaillées lors du développement](xref:blazor/handle-errors#detailed-errors-during-development).
