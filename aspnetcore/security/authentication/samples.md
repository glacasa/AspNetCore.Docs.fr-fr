---
title: Exemples d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les exemples d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/samples
ms.openlocfilehash: 7cd0fe60d7917abda7d8ac0e071deca13a4136ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776550"
---
# <a name="authentication-samples-for-aspnet-core"></a>Exemples d’authentification pour ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :

* [Transformation de revendications](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Authentification par cookie](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Options et schémas d’authentification dynamique](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Sélection entre un cookie et un autre schéma d’authentification en fonction de la demande](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Restreint l’accès aux fichiers statiques](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Exécuter des exemples

* Sélectionnez une [branche](https://github.com/dotnet/AspNetCore). Par exemple : `release/3.1`
* Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).
* Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.
* Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple `dotnet run`avec.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore) contient les exemples d’authentification suivants dans le dossier *AspNetCore/SRC/Security/Samples* :

* [Transformation de revendications](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Authentification par cookie](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Options et schémas d’authentification dynamique](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Revendications externes](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Sélection entre un cookie et un autre schéma d’authentification en fonction de la demande](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restreint l’accès aux fichiers statiques](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Exécuter des exemples

* Sélectionnez une [branche](https://github.com/dotnet/AspNetCore). Par exemple : `release/2.2`
* Clonez ou téléchargez le [référentiel ASP.net Core](https://github.com/dotnet/AspNetCore).
* Vérifiez que vous avez installé la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) qui correspond au clone du référentiel ASP.net core.
* Accédez à un exemple dans *AspNetCore/SRC/Security/Samples* et exécutez l’exemple `dotnet run`avec.

::: moniker-end
