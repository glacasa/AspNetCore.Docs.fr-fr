---
title: Sécuriser les Blazor applications ASP.net Core Server
author: guardrex
description: Découvrez comment sécuriser des applications Blazor serveur en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 5ba7bbde49bfc232795d375a1ec644825a0dee1e
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454634"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Sécuriser les Blazor applications ASP.net Core Server

Par [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>BlazorModèle de projet serveur

Le Blazor modèle de projet serveur peut être configuré pour l’authentification lors de la création du projet.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Suivez les instructions de Visual Studio dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification.

Après avoir choisi le modèle d’application ** Blazor serveur** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.

Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :

* **Aucune authentification**
* **Comptes d’utilisateur individuels : les**comptes d’utilisateur peuvent être stockés :
  * Au sein de l’application à l’aide du système de ASP.NET Core [Identity](xref:security/authentication/identity) .
  * Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).
* **Comptes professionnels ou scolaires**
* **Authentification Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Suivez les instructions de Visual Studio Code dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification :

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

Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Suivez les instructions de Visual Studio pour Mac dans l' <xref:blazor/get-started> article.

1. Dans l’étape **configurer votre Blazor application de serveur** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .

1. L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core Identity .

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Suivez les instructions de CLI .NET Core dans l' <xref:blazor/get-started> article pour créer un nouveau Blazor projet serveur avec un mécanisme d’authentification :

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

Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.

---

## <a name="secure-an-existing-app"></a>Sécuriser une application existante

BlazorLes applications serveur sont configurées pour la sécurité de la même façon que les applications ASP.NET Core. Pour plus d’informations, consultez les articles sous <xref:security/index> .

## <a name="scaffold-identity"></a>DestinIdentity

Génération Identity de modèles automatique dans un Blazor projet serveur :

* [Sans autorisation existante](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Avec autorisation](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).
