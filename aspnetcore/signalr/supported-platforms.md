---
title: Plateformes prises en charge ASP.NET Core SignalR
author: bradygaster
description: En savoir plus sur les plateformes SignalRprises en charge pour ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772603"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>Plateformes prises en charge par ASP.NET Core Signalr

## <a name="server-system-requirements"></a>Configuration requise pour le serveur

Signalr pour ASP.NET Core prend en charge toute plateforme de serveur prise en charge par ASP.NET Core.

## <a name="javascript-client"></a>Client JavaScript

Le [client JavaScript](xref:signalr/javascript-client) s’exécute sur NodeJS 8 et versions ultérieures, ainsi que sur les navigateurs suivants :

| Browser                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Actif&dagger; |
| Mozilla Firefox                 | Actif&dagger; |
| Google Chrome ; comprend Android | Actif&dagger; |
| Safari comprend iOS            | Actif&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*Current* fait référence à la dernière version du navigateur.

## <a name="net-client"></a>Client .NET

Le [client .net](xref:signalr/dotnet-client) s’exécute sur n’importe quelle plateforme prise en charge par ASP.net core. Par exemple, [les développeurs Xamarin peuvent SignalR utiliser](https://github.com/aspnet/Announcements/issues/305) pour créer des applications Android à l’aide de Xamarin. Android 8.4.0.1 et versions ultérieures et des applications iOS à l’aide de Xamarin. iOS 11.14.0.4 et versions ultérieures.

Si le serveur exécute IIS, le transport WebSockets requiert IIS 8,0 ou une version ultérieure sur Windows Server 2012 ou version ultérieure. D’autres transports sont pris en charge sur toutes les plateformes.

## <a name="java-client"></a>Client Java

Le [client Java](xref:signalr/java-client) prend en charge Java 8 et versions ultérieures.

## <a name="unsupported-clients"></a>Clients non pris en charge

Les clients suivants sont disponibles, mais sont expérimentaux ou non officiels. Ils ne sont actuellement pas pris en charge et peuvent ne jamais être.

* [Client C++](https://github.com/aspnet/SignalR-Client-Cpp)

* [Client SWIFT](https://github.com/moozzyk/SignalR-Client-Swift)
