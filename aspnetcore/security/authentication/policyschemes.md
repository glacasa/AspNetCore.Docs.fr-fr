---
title: Schémas de stratégie dans ASP.NET Core
author: rick-anderson
description: Les schémas de stratégie d’authentification facilitent l’utilisation d’un seul schéma d’authentification logique.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/authentication/policyschemes
ms.openlocfilehash: ddee613bf9c603542f17adf59a835a2ddbdc25a3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017803"
---
# <a name="policy-schemes-in-aspnet-core"></a>Schémas de stratégie dans ASP.NET Core

Les schémas de stratégie d’authentification facilitent l’utilisation d’un seul schéma d’authentification logique. Par exemple, un modèle de stratégie peut utiliser l’authentification Google pour résoudre les problèmes et cookie l’authentification pour tout le reste. Les schémas de stratégie d’authentification le font :

* Action d’authentification facile à transférer vers un autre schéma.
* Transférer dynamiquement en fonction de la requête.

Tous les schémas d’authentification qui utilisent dérivé <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> et [le \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)associé :

* Sont automatiquement des schémas de stratégie dans ASP.NET Core 2,1 et versions ultérieures.
* Peut être activé par le biais de la configuration des options du schéma.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Exemples

L’exemple suivant montre un modèle de niveau supérieur qui combine des schémas de niveau inférieur. L’authentification Google est utilisée pour les défis et cookie l’authentification est utilisée pour tout le reste :

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

L’exemple suivant active la sélection dynamique de schémas pour chaque demande. Autrement dit, comment mélanger cookie les s et l’authentification d’API :

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
