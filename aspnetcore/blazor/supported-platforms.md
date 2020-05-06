---
title: Plateformes prises en charge ASP.NET Core Blazor
author: guardrex
description: En savoir plus sur les plateformes Blazorprises en charge pour ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1c78803e6468f924bf8c8e9403a34565b114006f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771114"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>Plates-formes ASP.NET Core éblouissantes prises en charge

Par [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Configuration requise des navigateurs

### <a name="blazor-webassembly"></a>WebAssembly Blazor

| Browser                          | Version               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Current               |
| Mozilla Firefox                  | Current               |
| Google Chrome, y compris Android | Current               |
| Safari, y compris iOS            | Current               |
| Microsoft Internet Explorer      | Non pris en charge&dagger; |

&dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).

### <a name="blazor-server"></a>Serveur Blazor

| Browser                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, y compris Android | Current    |
| Safari, y compris iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Des polyremplissages supplémentaires sont nécessaires (par exemple, des promesses peuvent être ajoutées via un bundle [Polyfill.IO](https://polyfill.io/v3/) ).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/hosting-models>
