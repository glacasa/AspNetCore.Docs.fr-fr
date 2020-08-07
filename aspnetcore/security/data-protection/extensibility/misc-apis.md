---
title: API de protection des données diverses ASP.NET Core
author: rick-anderson
description: En savoir plus sur l’interface ISecret de protection des données ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 2e319cdcec1e005682555c4e03c52632e6d8521a
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913815"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>API de protection des données diverses ASP.NET Core

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Les types qui implémentent l’une des interfaces suivantes doivent être thread-safe pour plusieurs appelants.

## <a name="isecret"></a>ISecret

L' `ISecret` interface représente une valeur secrète, telle que le matériel de clé de chiffrement. Il contient la surface de l’API suivante :

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

La `WriteSecretIntoBuffer` méthode remplit la mémoire tampon fournie avec la valeur de secret brut. La raison pour laquelle cette API prend la mémoire tampon comme paramètre au lieu de retourner `byte[]` directement est que cela donne à l’appelant la possibilité d’épingler l’objet de mémoire tampon, ce qui limite l’exposition secrète au garbage collector géré.

Le `Secret` type est une implémentation concrète de `ISecret` où la valeur secrète est stockée dans la mémoire in-process. Sur les plateformes Windows, la valeur de la clé secrète est chiffrée via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).
