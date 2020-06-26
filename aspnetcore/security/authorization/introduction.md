---
title: Présentation de l’autorisation dans ASP.NET Core
author: rick-anderson
description: Découvrez les principes de base de l’autorisation et le fonctionnement de l’autorisation dans les applications ASP.NET Core.
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
uid: security/authorization/introduction
ms.openlocfilehash: b0d6c91adfb5dc273aeb662cb2c249626ef1d3c6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405430"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Présentation de l’autorisation dans ASP.NET Core

<a name="security-authorization-introduction"></a>

L’autorisation fait référence au processus qui détermine ce qu’un utilisateur est en mesure de faire. Par exemple, un utilisateur administratif est autorisé à créer une bibliothèque de documents, à ajouter des documents, à modifier des documents et à les supprimer. Un utilisateur non-administrateur travaillant avec la bibliothèque est autorisé uniquement à lire les documents.

L’autorisation est orthogonale et indépendante de l’authentification. Toutefois, l’autorisation nécessite un mécanisme d’authentification. L’authentification est le processus qui consiste à déterminer l’identité d’un utilisateur. L’authentification peut créer une ou plusieurs identités pour l’utilisateur actuel.

Pour plus d’informations sur l’authentification dans ASP.NET Core, consultez <xref:security/authentication/index> .

## <a name="authorization-types"></a>Types d’autorisation

ASP.NET Core autorisation fournit un [rôle](xref:security/authorization/roles) simple, déclaratif et un modèle riche [basé sur des stratégies](xref:security/authorization/policies) . L’autorisation est exprimée en spécifications et les gestionnaires évaluent les revendications d’un utilisateur par rapport aux exigences. Les vérifications impératives peuvent reposer sur des stratégies ou des stratégies simples qui évaluent à la fois l’identité de l’utilisateur et les propriétés de la ressource à laquelle l’utilisateur tente d’accéder.

## <a name="namespaces"></a>Espaces de noms

Les composants d’autorisation, y compris les `AuthorizeAttribute` `AllowAnonymousAttribute` attributs et, se trouvent dans l' `Microsoft.AspNetCore.Authorization` espace de noms.

Consultez la documentation sur l' [autorisation simple](xref:security/authorization/simple).
