---
title: Injection de dépendances dans les gestionnaires d’exigences dans ASP.NET Core
author: rick-anderson
description: Découvrez comment injecter des gestionnaires d’exigence d’autorisation dans une application ASP.NET Core à l’aide de l’injection de dépendances.
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: d12253ad1c1442c0db5cd497393daabe280fae8d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406353"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Injection de dépendances dans les gestionnaires d’exigences dans ASP.NET Core

<a name="security-authorization-di"></a>

Les [gestionnaires d’autorisations doivent être inscrits](xref:security/authorization/policies#handler-registration) dans la collection de services pendant la configuration (à l’aide de l' [injection de dépendances](xref:fundamentals/dependency-injection)).

Supposons que vous disposiez d’un référentiel de règles que vous souhaitiez évaluer à l’intérieur d’un gestionnaire d’autorisations et que ce dépôt ait été enregistré dans la collection de services. L’autorisation va résoudre et l’injecter dans votre constructeur.

Par exemple, si vous souhaitez utiliser ASP. L’infrastructure de journalisation de .net que vous souhaiteriez injecter `ILoggerFactory` dans votre gestionnaire. Un tel gestionnaire peut se présenter comme suit :

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Vous devez enregistrer le gestionnaire avec `services.AddSingleton()` :

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Une instance du gestionnaire est créée au démarrage de votre application, et DI injecte le inscrit `ILoggerFactory` dans votre constructeur.

> [!NOTE]
> Les gestionnaires qui utilisent Entity Framework ne doivent pas être inscrits en tant que singletons.
