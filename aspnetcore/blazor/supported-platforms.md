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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401933"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>BlazorPlateformes prises en charge ASP.net Core

Par [Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>Configuration requise des navigateurs

### Blazor WebAssembly

| Navigateur                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Actuel               |
| Mozilla Firefox                  | Actuel               |
| Google Chrome, y compris Android | Actuel               |
| Safari, y compris iOS            | Actuel               |
| Microsoft Internet Explorer      | Non pris en charge&dagger; |

&dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).

### Blazor Server

| Navigateur                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Actuel    |
| Mozilla Firefox                  | Actuel    |
| Google Chrome, y compris Android | Actuel    |
| Safari, y compris iOS            | Actuel    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Des polyremplissages supplémentaires sont nécessaires (par exemple, des promesses peuvent être ajoutées via un [`Polyfill.io`](https://polyfill.io/v3/) Bundle).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/hosting-models>
