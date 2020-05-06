---
title: Injection de dépendances dans les gestionnaires d’exigences dans ASP.NET Core
author: rick-anderson
description: Découvrez comment injecter des gestionnaires d’exigence d’autorisation dans une application ASP.NET Core à l’aide de l’injection de dépendances.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 16285f6f731455d6e45a04f82437793891a77668
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775118"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="31088-103">Injection de dépendances dans les gestionnaires d’exigences dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="31088-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="31088-104">Les [gestionnaires d’autorisations doivent être inscrits](xref:security/authorization/policies#handler-registration) dans la collection de services pendant la configuration (à l’aide de l' [injection de dépendances](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="31088-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="31088-105">Supposons que vous disposiez d’un référentiel de règles que vous souhaitiez évaluer à l’intérieur d’un gestionnaire d’autorisations et que ce dépôt ait été enregistré dans la collection de services.</span><span class="sxs-lookup"><span data-stu-id="31088-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="31088-106">L’autorisation va résoudre et l’injecter dans votre constructeur.</span><span class="sxs-lookup"><span data-stu-id="31088-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="31088-107">Par exemple, si vous souhaitez utiliser ASP. L’infrastructure de journalisation de .net que vous `ILoggerFactory` souhaiteriez injecter dans votre gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="31088-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="31088-108">Un tel gestionnaire peut se présenter comme suit :</span><span class="sxs-lookup"><span data-stu-id="31088-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="31088-109">Vous devez enregistrer le gestionnaire avec `services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="31088-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="31088-110">Une instance du gestionnaire est créée au démarrage de votre application, et DI injecte le inscrit `ILoggerFactory` dans votre constructeur.</span><span class="sxs-lookup"><span data-stu-id="31088-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="31088-111">Les gestionnaires qui utilisent Entity Framework ne doivent pas être inscrits en tant que singletons.</span><span class="sxs-lookup"><span data-stu-id="31088-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
