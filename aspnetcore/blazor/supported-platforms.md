---
title: BlazorPlateformes prises en charge ASP.net Core
author: guardrex
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: c10132c87c93346af89c548363e786967609f3da
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "83607977"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>BlazorPlateformes prises en charge ASP.net Core

Par [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Configuration requise des navigateurs

### <a name="blazor-webassembly"></a>BlazorWebassembly

| Navigateur                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Actuel               |
| Mozilla Firefox                  | Actuel               |
| Google Chrome, y compris Android | Actuel               |
| Safari, y compris iOS            | Actuel               |
| Microsoft Internet Explorer      | Non pris en charge&dagger; |

&dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).

### <a name="blazor-server"></a>BlazorServeurs

| Navigateur                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Actuel    |
| Mozilla Firefox                  | Actuel    |
| Google Chrome, y compris Android | Actuel    |
| Safari, y compris iOS            | Actuel    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Des polyremplissages supplémentaires sont nécessaires (par exemple, des promesses peuvent être ajoutées via un bundle [Polyfill.IO](https://polyfill.io/v3/) ).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/hosting-models>
