---
title: plates-formes de Blazor soutien ASP.NET Core
author: guardrex
description: En savoir plus sur les Blazorplates-formes prises en charge pour ASP.NET Core .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658857"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET plates-formes de support Core Blazor

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

&dagger;Microsoft Internet Explorer ne prend pas en charge [WebAssembly](https://webassembly.org).

### <a name="blazor-server"></a>Serveur Blazor

| Browser                          | Version    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Current    |
| Mozilla Firefox                  | Current    |
| Google Chrome, y compris Android | Current    |
| Safari, y compris iOS            | Current    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Des polyfills supplémentaires sont nécessaires (par exemple, des promesses peuvent être ajoutées via un faisceau [de Polyfill.io).](https://polyfill.io/v3/)

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/hosting-models>
