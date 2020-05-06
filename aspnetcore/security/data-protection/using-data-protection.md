---
title: Prise en main des API de protection des données dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser les API de protection des données ASP.NET Core pour protéger et ôter la protection des données dans une application.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: d5e9e61db39a67e8ccb7b345dfa4c97353312857
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774260"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Prise en main des API de protection des données dans ASP.NET Core

<a name="security-data-protection-getting-started"></a>

Pour la plus simple, la protection des données comprend les étapes suivantes :

1. Créer un protecteur de données à partir d’un fournisseur de protection des données.

2. Appelez la `Protect` méthode avec les données que vous souhaitez protéger.

3. Appelez la `Unprotect` méthode avec les données que vous souhaitez réactiver en texte brut.

La plupart des infrastructures et des modèles d’application, tels SignalRque ASP.net Core ou, configurent déjà le système de protection des données et l’ajoutent à un conteneur de service auquel vous accédez via l’injection de dépendances. L’exemple suivant illustre la configuration d’un conteneur de service pour l’injection de dépendances et l’inscription de la pile de protection des données, la réception du fournisseur de protection des données via l’injection de dépendances, la création d’un protecteur et la protection, puis la déprotection des données.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Lorsque vous créez un protecteur, vous devez fournir une ou plusieurs [chaînes d’objectif](xref:security/data-protection/consumer-apis/purpose-strings). Une chaîne d’objectif assure l’isolement entre les consommateurs. Par exemple, un protecteur créé avec une chaîne d’objectif « Green » ne serait pas en mesure d’ôter la protection des données fournies par un protecteur avec un objectif « violet ».

>[!TIP]
> Les instances `IDataProtectionProvider` de `IDataProtector` et de sont thread-safe pour plusieurs appelants. Il est prévu qu’une fois qu’un composant obtient une référence `IDataProtector` à un via un `CreateProtector`appel à, il utilise cette référence pour plusieurs appels `Protect` à `Unprotect`et.
>
>Un appel à `Unprotect` lèvera CryptographicException si la charge utile protégée ne peut pas être vérifiée ou déchiffrée. Certains composants peuvent souhaiter ignorer des erreurs lors de l’opération de non-protection. un composant qui lit les cookies d’authentification peut gérer cette erreur et traiter la demande comme s’il n’avait aucun cookie au lieu de faire échouer la demande. Les composants qui veulent ce comportement doivent intercepter spécifiquement CryptographicException au lieu d’absorber toutes les exceptions.
