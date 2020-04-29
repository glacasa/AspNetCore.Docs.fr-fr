---
title: Sécuriser Blazor les applications ASP.net Core Server
author: guardrex
description: Découvrez comment sécuriser Blazor des applications serveur en tant qu’applications ASP.net core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/27/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server/index
ms.openlocfilehash: 0021911b731e57bc6eabf857c27a13462e7400ae
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82206367"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Sécuriser les applications de serveur ASP.NET Core éblouissantes

Par [Luke Latham](https://github.com/guardrex)

## <a name="blazor-server-project-template"></a>Modèle de projet de serveur éblouissant

Le modèle de projet de serveur éblouissant peut être configuré pour l’authentification lors de la création du projet.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Suivez les instructions de Visual Studio dans <xref:blazor/get-started> l’article pour créer un projet de serveur éblouissant avec un mécanisme d’authentification.

Après avoir choisi le modèle **Application serveur Blazor** dans la boîte de dialogue **Créer une application web ASP.NET Core.**, sélectionnez **Modifier** sous **Authentification**.

Une boîte de dialogue s’ouvre pour offrir le même ensemble de mécanismes d’authentification que ceux disponibles pour les autres projets ASP.NET Core :

* **Aucune authentification**
* **Comptes d’utilisateur individuels** &ndash; Les comptes d'utilisateur peuvent être stockés :
  * Dans l’application à l’aide du système [d’identité](xref:security/authentication/identity) d’ASP.NET Core.
  * Avec [Azure ad B2C](xref:security/authentication/azure-ad-b2c).
* **Comptes professionnels ou scolaires**
* **Authentification Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Suivez les instructions de Visual Studio Code dans <xref:blazor/get-started> l’article pour créer un projet de serveur éblouissant avec un mécanisme d’authentification :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification | Description |
| ------------------------ | ----------- |
| `None` (par défaut)         | Aucune authentification |
| `Individual`             | Utilisateurs stockés dans l’application avec ASP.NET Core identité |
| `IndividualB2C`          | Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Authentification d’organisation pour un seul locataire |
| `MultiOrg`               | Authentification d’organisation pour plusieurs locataires |
| `Windows`                | Authentification Windows |

À l' `-o|--output` aide de l’option, la commande utilise la valeur `{APP NAME}` fournie pour l’espace réservé à :

* Créez un dossier pour le projet.
* Nommez ce projet.

Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

1. Suivez les instructions de Visual Studio pour Mac dans <xref:blazor/get-started> l’article.

1. Dans l’étape **configurer votre application de serveur éblouissant** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .

1. L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core identité.

# <a name="net-core-cli"></a>[CLI .NET Core](#tab/netcore-cli/)

Suivez les instructions de CLI .NET Core dans <xref:blazor/get-started> l’article pour créer un projet de serveur éblouissant avec un mécanisme d’authentification :

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Les valeurs autorisées d’authentification (`{AUTHENTICATION}`) sont présentées dans le tableau suivant.

| Mécanisme d’authentification | Description |
| ------------------------ | ----------- |
| `None` (par défaut)         | Aucune authentification |
| `Individual`             | Utilisateurs stockés dans l’application avec ASP.NET Core identité |
| `IndividualB2C`          | Utilisateurs stockés dans [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Authentification d’organisation pour un seul locataire |
| `MultiOrg`               | Authentification d’organisation pour plusieurs locataires |
| `Windows`                | Authentification Windows |

À l' `-o|--output` aide de l’option, la commande utilise la valeur `{APP NAME}` fournie pour l’espace réservé à :

* Créez un dossier pour le projet.
* Nommez ce projet.

Pour plus d’informations, consultez la commande [dotnet new](/dotnet/core/tools/dotnet-new) dans le Guide .NET Core.

---
