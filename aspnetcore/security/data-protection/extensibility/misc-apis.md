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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="cee2c-103">API de protection des données diverses ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cee2c-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="cee2c-104">Les types qui implémentent l’une des interfaces suivantes doivent être thread-safe pour plusieurs appelants.</span><span class="sxs-lookup"><span data-stu-id="cee2c-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="cee2c-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="cee2c-105">ISecret</span></span>

<span data-ttu-id="cee2c-106">L' `ISecret` interface représente une valeur secrète, telle que le matériel de clé de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="cee2c-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="cee2c-107">Il contient la surface de l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="cee2c-107">It contains the following API surface:</span></span>

* <span data-ttu-id="cee2c-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="cee2c-108">`Length`: `int`</span></span>

* <span data-ttu-id="cee2c-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="cee2c-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="cee2c-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="cee2c-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="cee2c-111">La `WriteSecretIntoBuffer` méthode remplit la mémoire tampon fournie avec la valeur de secret brut.</span><span class="sxs-lookup"><span data-stu-id="cee2c-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="cee2c-112">La raison pour laquelle cette API prend la mémoire tampon comme paramètre au lieu de retourner `byte[]` directement est que cela donne à l’appelant la possibilité d’épingler l’objet de mémoire tampon, ce qui limite l’exposition secrète au garbage collector géré.</span><span class="sxs-lookup"><span data-stu-id="cee2c-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="cee2c-113">Le `Secret` type est une implémentation concrète de `ISecret` où la valeur secrète est stockée dans la mémoire in-process.</span><span class="sxs-lookup"><span data-stu-id="cee2c-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="cee2c-114">Sur les plateformes Windows, la valeur de la clé secrète est chiffrée via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="cee2c-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
