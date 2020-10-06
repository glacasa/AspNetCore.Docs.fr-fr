---
title: BlazorPlateformes prises en charge ASP.net Core
author: guardrex
description: En savoir plus sur les plateformes prises en charge pour ASP.NET Core Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754539"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>BlazorPlateformes prises en charge ASP.net Core

Par [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly et Blazor Server sont pris en charge dans les navigateurs présentés dans le tableau suivant.

| Navigateur                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, y compris iOS      | Actif&dagger; |
| Google Chrome, y compris Android | Actif&dagger; |
| Microsoft Edge                   | Actif&dagger; |
| Mozilla Firefox                  | Actif&dagger; |  

&dagger;*Current* fait référence à la dernière version du navigateur.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Navigateur                          | Version               |
| -------------------------------- | --------------------- |
| Apple Safari, y compris iOS      | Actif&dagger;       |
| Google Chrome, y compris Android | Actif&dagger;       |
| Microsoft Edge                   | Actif&dagger;       |
| Microsoft Internet Explorer      | Non pris en charge&Dagger; |
| Mozilla Firefox                  | Actif&dagger;       |  

&dagger;*Current* fait référence à la dernière version du navigateur.  
&Dagger;Microsoft Internet Explorer ne prend pas en charge [Webassembly](https://webassembly.org).

## Blazor Server

| Navigateur                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, y compris iOS      | Actif&dagger; |
| Google Chrome, y compris Android | Actif&dagger; |
| Microsoft Edge                   | Actif&dagger; |
| Microsoft Internet Explorer      | 11&Dagger;      |
| Mozilla Firefox                  | Actif&dagger; |

&dagger;*Current* fait référence à la dernière version du navigateur.  
&Dagger;Des polyremplissages supplémentaires sont nécessaires. Par exemple, les promesses peuvent être ajoutées via un [`Polyfill.io`](https://polyfill.io/v3/) bundle.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
