---
title: Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI
author: RicoSuter
description: Ce tutoriel montre comment ajouter Swagger pour générer des pages d’aide et de documentation pour une application d’API web.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 4408e02996b958bf009903aa1e4eeda9ad4f457c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658472"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="d69c5-103">Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="d69c5-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="d69c5-104">Par [Christoph Nienaber](https://twitter.com/zuckerthoben) et [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="d69c5-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="d69c5-105">Les différentes méthodes qui permettent d’utiliser une API web ne sont pas toujours simples à comprendre pour les développeurs.</span><span class="sxs-lookup"><span data-stu-id="d69c5-105">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="d69c5-106">[Swagger](https://swagger.io/), également appelé [OpenAPI](https://www.openapis.org/), résout le problème de la génération de pages d’aide et de documentation qu’utilisent les API web.</span><span class="sxs-lookup"><span data-stu-id="d69c5-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="d69c5-107">Ses avantages sont, entre autres, la documentation interactive, la génération de SDK client et la découvertibilité des API.</span><span class="sxs-lookup"><span data-stu-id="d69c5-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="d69c5-108">Cet article décrit les implémentations .NET Swagger suivantes [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) et [NSwag](https://github.com/RicoSuter/NSwag) :</span><span class="sxs-lookup"><span data-stu-id="d69c5-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="d69c5-109">**Swashbuckle.AspNetCore** est un projet open source pour la génération de documents Swagger pour des API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d69c5-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="d69c5-110">**NSwag** est un autre projet open source pour générer des documents Swagger et intégrer [l’IU Swagger](https://swagger.io/swagger-ui/) ou [ReDoc](https://github.com/Rebilly/ReDoc) aux API web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d69c5-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="d69c5-111">NSwag fournit aussi des méthodes pour générer du code client C# et TypeScript pour votre API.</span><span class="sxs-lookup"><span data-stu-id="d69c5-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="d69c5-112">Qu’est-ce que Swagger/OpenAPI ?</span><span class="sxs-lookup"><span data-stu-id="d69c5-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="d69c5-113">Swagger est une spécification indépendante du langage pour décrire les API [REST](https://en.wikipedia.org/wiki/Representational_state_transfer).</span><span class="sxs-lookup"><span data-stu-id="d69c5-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="d69c5-114">Le projet Swagger a été donné au projet [OpenAPI Initiative](https://www.openapis.org/) et s’appelle maintenant OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="d69c5-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="d69c5-115">Les deux noms sont utilisés indifféremment, mais OpenAPI est préféré.</span><span class="sxs-lookup"><span data-stu-id="d69c5-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="d69c5-116">Il permet aux ordinateurs et aux utilisateurs de comprendre les fonctionnalités d’un service sans aucun accès direct à l’implémentation (code source, accès réseau, documentation).</span><span class="sxs-lookup"><span data-stu-id="d69c5-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="d69c5-117">L’un des objectifs est de limiter la quantité de travail nécessaire pour connecter des services dissociés.</span><span class="sxs-lookup"><span data-stu-id="d69c5-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="d69c5-118">Un autre objectif est de réduire le temps nécessaire pour documenter un service avec précision.</span><span class="sxs-lookup"><span data-stu-id="d69c5-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="swagger-specification-swaggerjson"></a><span data-ttu-id="d69c5-119">Spécification Swagger (swagger.json)</span><span class="sxs-lookup"><span data-stu-id="d69c5-119">Swagger specification (swagger.json)</span></span>

<span data-ttu-id="d69c5-120">Au cœur du flux Swagger se trouve la spécification Swagger, document nommé par défaut *swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="d69c5-120">The core to the Swagger flow is the Swagger specification&mdash;by default, a document named *swagger.json*.</span></span> <span data-ttu-id="d69c5-121">Elle est générée par la chaîne d’outils Swagger (ou des implémentations tierces) en fonction de votre service.</span><span class="sxs-lookup"><span data-stu-id="d69c5-121">It's generated by the Swagger tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="d69c5-122">Elle décrit les fonctionnalités de votre API et comment y accéder avec HTTP.</span><span class="sxs-lookup"><span data-stu-id="d69c5-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="d69c5-123">Elle gère l’interface utilisateur Swagger et est utilisée par la chaîne d’outils pour activer la découverte et la génération de code client.</span><span class="sxs-lookup"><span data-stu-id="d69c5-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="d69c5-124">Voici l’exemple d’une spécification Swagger, réduite par souci de concision :</span><span class="sxs-lookup"><span data-stu-id="d69c5-124">Here's an example of a Swagger specification, reduced for brevity:</span></span>

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a><span data-ttu-id="d69c5-125">Interface utilisateur Swagger</span><span class="sxs-lookup"><span data-stu-id="d69c5-125">Swagger UI</span></span>

<span data-ttu-id="d69c5-126">[L’IU Swagger](https://swagger.io/swagger-ui/) est une interface utilisateur web qui fournit des informations sur le service, à l’aide de la spécification Swagger générée.</span><span class="sxs-lookup"><span data-stu-id="d69c5-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated Swagger specification.</span></span> <span data-ttu-id="d69c5-127">Swashbuckle et NSwag ont une version incorporée à l’interface utilisateur Swagger pour que vous puissiez l’héberger dans votre application ASP.NET Core à l’aide d’un appel d’inscription d’intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="d69c5-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="d69c5-128">L’IU web ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="d69c5-128">The web UI looks like this:</span></span>

![Interface utilisateur Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="d69c5-130">Chaque méthode d’action publique dans vos contrôleurs peut être testée à partir de l’IU.</span><span class="sxs-lookup"><span data-stu-id="d69c5-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="d69c5-131">Cliquez sur un nom de méthode pour développer la section.</span><span class="sxs-lookup"><span data-stu-id="d69c5-131">Click a method name to expand the section.</span></span> <span data-ttu-id="d69c5-132">Ajoutez tous les paramètres nécessaires, et cliquez sur **Essayez-le!**.</span><span class="sxs-lookup"><span data-stu-id="d69c5-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Exemple de test GET Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="d69c5-134">La version de l’IU Swagger utilisée pour les captures d’écran est la version 2.</span><span class="sxs-lookup"><span data-stu-id="d69c5-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="d69c5-135">Pour obtenir un exemple de la version 3, consultez [l’exemple Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="d69c5-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="d69c5-136">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="d69c5-136">Next steps</span></span>

* [<span data-ttu-id="d69c5-137">Bien démarrer avec Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="d69c5-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="d69c5-138">Bien démarrer avec NSwag</span><span class="sxs-lookup"><span data-stu-id="d69c5-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
