---
title: Gérer les utilisateurs et les groupes dansSignalR
author: bradygaster
description: Vue d’ensemble de la SignalR gestion des utilisateurs et des groupes ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/groups
ms.openlocfilehash: dfa989654069bc85f0a936751dbabe384c63e917
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "83549996"
---
# <a name="manage-users-and-groups-in-signalr"></a>Gérer les utilisateurs et les groupes dansSignalR

Par [Brennan Conroy](https://github.com/BrennanConroy)

SignalRpermet d’envoyer des messages à toutes les connexions associées à un utilisateur spécifique, ainsi qu’à des groupes nommés de connexions.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(procédure de téléchargement)](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>Utilisateurs dansSignalR

Un utilisateur unique dans SignalR peut avoir plusieurs connexions à une application. Par exemple, un utilisateur peut être connecté sur son bureau, ainsi que sur son téléphone. Chaque appareil dispose d’une SignalR connexion distincte, mais tous sont associés au même utilisateur. Si un message est envoyé à l’utilisateur, toutes les connexions associées à cet utilisateur reçoivent le message. L’identificateur d’utilisateur pour une connexion est accessible par la `Context.UserIdentifier` propriété dans le concentrateur.

Par défaut, SignalR utilise `ClaimTypes.NameIdentifier` à partir du `ClaimsPrincipal` associé à la connexion comme identificateur d’utilisateur. Pour personnaliser ce comportement, consultez [utiliser des revendications pour personnaliser la gestion des identités](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

Envoyez un message à un utilisateur spécifique en passant l’identificateur d’utilisateur à la `User` fonction dans une méthode de concentrateur, comme illustré dans l’exemple suivant :

> [!NOTE]
> L’identificateur d’utilisateur respecte la casse.

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>Groupes dansSignalR

Un groupe est une collection de connexions associées à un nom. Les messages peuvent être envoyés à toutes les connexions d’un groupe. Les groupes sont la méthode recommandée pour l’envoi à une connexion ou à plusieurs connexions, car les groupes sont gérés par l’application. Une connexion peut être membre de plusieurs groupes. Les groupes sont idéaux pour une application de conversation, où chaque pièce peut être représentée en tant que groupe. Les connexions sont ajoutées ou supprimées des groupes via `AddToGroupAsync` les `RemoveFromGroupAsync` méthodes et.

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

L’appartenance au groupe n’est pas conservée lorsqu’une connexion se reconnecte. La connexion doit rejoindre le groupe lorsqu’elle est rétablie. Il n’est pas possible de compter les membres d’un groupe, car ces informations ne sont pas disponibles si l’application est mise à l’échelle sur plusieurs serveurs.

Pour protéger l’accès aux ressources lors de l’utilisation de groupes, utilisez la fonctionnalité d' [authentification et d’autorisation](xref:signalr/authn-and-authz) dans ASP.net core. Si un utilisateur est ajouté à un groupe uniquement lorsque les informations d’identification sont valides pour ce groupe, les messages envoyés à ce groupe sont uniquement dirigés vers les utilisateurs autorisés. Toutefois, les groupes ne sont pas une fonctionnalité de sécurité. Les revendications d’authentification ont des fonctionnalités que les groupes n’ont pas, telles que l’expiration et la révocation. Si l’autorisation d’un utilisateur pour accéder au groupe est révoquée, l’application doit supprimer l’utilisateur du groupe de manière explicite.

> [!NOTE]
> Les noms de groupes respectent la casse.

## <a name="related-resources"></a>Ressources associées

* [Prise en main](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Publication dans Azure](xref:signalr/publish-to-azure-web-app)
