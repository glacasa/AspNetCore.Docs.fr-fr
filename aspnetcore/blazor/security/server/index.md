---
title: Sécuriser les Blazor Server applications ASP.net Core
author: guardrex
description: Découvrez comment sécuriser des applications Blazor Server en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/server/index
ms.openlocfilehash: 69a24fc955a0f2fb524ec817eb50372052f538a1
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944254"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Sécuriser les Blazor Server applications ASP.net Core

Par [Luke Latham](https://github.com/guardrex)

Blazor Serverles applications sont configurées pour la sécurité de la même façon que les applications ASP.NET Core. Pour plus d’informations, consultez les articles sous <xref:security/index> . Les rubriques de cette vue d’ensemble s’appliquent spécifiquement à Blazor Server . 

## <a name="blazor-server-project-template"></a>Blazor Servermodèle de projet

Le Blazor Server modèle de projet peut être configuré pour l’authentification lors de la création du projet.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Suivez les instructions de Visual Studio dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification.

Après avoir choisi le modèle d' ** Blazor Server application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.

Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :

* **Aucune authentification**
* **Comptes d’utilisateur individuels : les**comptes d’utilisateur peuvent être stockés :
  * Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .
  * Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).
* **Comptes professionnels ou scolaires**
* **Authentification Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Suivez les instructions de Visual Studio Code dans <xref:blazor/tooling> pour créer un nouveau Blazor Server projet avec un mécanisme d’authentification :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification | Description |
| ------------------------ | ----------- |
| `None` (valeur par défaut)         | Aucune authentification |
| `Individual`             | Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity |
| `IndividualB2C`          | Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Authentification d’organisation pour un seul locataire |
| `MultiOrg`               | Authentification d’organisation pour plusieurs locataires |
| `Windows`                | Authentification Windows |

À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :

* Créez un dossier pour le projet.
* Nommez ce projet.

Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/tooling> .

1. Dans l’étape **configurer votre nouvelle Blazor Server application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .

1. L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Créez un Blazor Server projet avec un mécanisme d’authentification à l’aide de la commande suivante dans une interface de commande :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification | Description |
| ------------------------ | ----------- |
| `None` (valeur par défaut)         | Aucune authentification |
| `Individual`             | Utilisateurs stockés dans l’application avec ASP.NET CoreIdentity |
| `IndividualB2C`          | Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Authentification d’organisation pour un seul locataire |
| `MultiOrg`               | Authentification d’organisation pour plusieurs locataires |
| `Windows`                | Authentification Windows |

À l’aide de l' `-o|--output` option, la commande utilise la valeur fournie pour l' `{APP NAME}` espace réservé à :

* Créez un dossier pour le projet.
* Nommez ce projet.

Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.

---

## <a name="scaffold-identity"></a>DestinIdentity

Génération Identity de modèles automatique dans un Blazor Server projet :

* [Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
