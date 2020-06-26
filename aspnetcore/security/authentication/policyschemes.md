---
title: Schémas de stratégie dans ASP.NET Core
author: rick-anderson
description: Les schémas de stratégie d’authentification facilitent l’utilisation d’un seul schéma d’authentification logique.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: a8bde9633f06f41ebcb55480eb2322544db4b4da
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408758"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="20cb7-103">Schémas de stratégie dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20cb7-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="20cb7-104">Les schémas de stratégie d’authentification facilitent l’utilisation d’un seul schéma d’authentification logique.</span><span class="sxs-lookup"><span data-stu-id="20cb7-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="20cb7-105">Par exemple, un modèle de stratégie peut utiliser l’authentification Google pour résoudre les problèmes et l’authentification des cookies pour tout le reste.</span><span class="sxs-lookup"><span data-stu-id="20cb7-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="20cb7-106">Les schémas de stratégie d’authentification le font :</span><span class="sxs-lookup"><span data-stu-id="20cb7-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="20cb7-107">Action d’authentification facile à transférer vers un autre schéma.</span><span class="sxs-lookup"><span data-stu-id="20cb7-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="20cb7-108">Transférer dynamiquement en fonction de la requête.</span><span class="sxs-lookup"><span data-stu-id="20cb7-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="20cb7-109">Tous les schémas d’authentification qui utilisent dérivé <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> et [le \<TOptions> AuthenticationHandler](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)associé :</span><span class="sxs-lookup"><span data-stu-id="20cb7-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [AuthenticationHandler\<TOptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="20cb7-110">Sont automatiquement des schémas de stratégie dans ASP.NET Core 2,1 et versions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="20cb7-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="20cb7-111">Peut être activé par le biais de la configuration des options du schéma.</span><span class="sxs-lookup"><span data-stu-id="20cb7-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="20cb7-112">Exemples</span><span class="sxs-lookup"><span data-stu-id="20cb7-112">Examples</span></span>

<span data-ttu-id="20cb7-113">L’exemple suivant montre un modèle de niveau supérieur qui combine des schémas de niveau inférieur.</span><span class="sxs-lookup"><span data-stu-id="20cb7-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="20cb7-114">L’authentification Google est utilisée pour les défis et l’authentification par cookie est utilisée pour tout le reste :</span><span class="sxs-lookup"><span data-stu-id="20cb7-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="20cb7-115">L’exemple suivant active la sélection dynamique de schémas pour chaque demande.</span><span class="sxs-lookup"><span data-stu-id="20cb7-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="20cb7-116">Autrement dit, comment mélanger les cookies et l’authentification de l’API :</span><span class="sxs-lookup"><span data-stu-id="20cb7-116">That is, how to mix cookies and API authentication:</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
