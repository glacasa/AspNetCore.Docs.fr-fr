---
title: Échantillons d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les échantillons d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308213"
---
# <a name="authentication-samples-for-aspnet-core"></a>Échantillons d’authentification pour ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Le [dépôt ASP.NET Core](https://github.com/dotnet/AspNetCore) contient les échantillons d’authentification suivants dans le dossier *AspNetCore/src/Security/samples* :

* [Transformation de revendications](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Authentification de cookie](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Fournisseur de stratégie personnalisé - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Systèmes et options d’authentification dynamiques](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Réclamations externes](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Sélection entre cookie et un autre schéma d’authentification basé sur la demande](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Restreint l’accès aux fichiers statiques](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Exécuter des exemples

* Sélectionnez une [branche](https://github.com/dotnet/AspNetCore). Par exemple : `release/3.1`
* Clonez ou téléchargez le [référentiel ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Vérifiez que vous avez installé la version [SDK core .NET](https://dotnet.microsoft.com/download/dotnet-core) correspondant au clone du référentiel ASP.NET Core.
* Naviguez vers un échantillon dans *AspNetCore/src/Security/samples* et exécutez l’échantillon avec `dotnet run`.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Le [dépôt ASP.NET Core](https://github.com/dotnet/AspNetCore) contient les échantillons d’authentification suivants dans le dossier *AspNetCore/src/Security/samples* :

* [Transformation de revendications](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Authentification de cookie](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Fournisseur de stratégie personnalisé - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Systèmes et options d’authentification dynamiques](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Réclamations externes](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Sélection entre cookie et un autre schéma d’authentification basé sur la demande](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Restreint l’accès aux fichiers statiques](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Exécuter des exemples

* Sélectionnez une [branche](https://github.com/dotnet/AspNetCore). Par exemple : `release/2.2`
* Clonez ou téléchargez le [référentiel ASP.NET Core](https://github.com/dotnet/AspNetCore).
* Vérifiez que vous avez installé la version [SDK core .NET](https://dotnet.microsoft.com/download/dotnet-core) correspondant au clone du référentiel ASP.NET Core.
* Naviguez vers un échantillon dans *AspNetCore/src/Security/samples* et exécutez l’échantillon avec `dotnet run`.

::: moniker-end
