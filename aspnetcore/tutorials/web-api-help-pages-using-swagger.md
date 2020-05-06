---
title: Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI
author: RicoSuter
description: Ce tutoriel montre comment ajouter Swagger pour générer des pages d’aide et de documentation pour une application d’API web.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: bde38fcbc11ef36c42523acb182fc62a934821c3
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774520"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Pages d’aide d’API web ASP.NET Core avec Swagger/OpenAPI

Par [Christoph Nienaber](https://twitter.com/zuckerthoben) et [Rico Suter](https://blog.rsuter.com/)

Les différentes méthodes qui permettent d’utiliser une API web ne sont pas toujours simples à comprendre pour les développeurs. [Swagger](https://swagger.io/), également appelé [OpenAPI](https://www.openapis.org/), résout le problème de la génération de pages d’aide et de documentation qu’utilisent les API web. Ses avantages sont, entre autres, la documentation interactive, la génération de SDK client et la découvertibilité des API.

Cet article décrit les implémentations .NET Swagger suivantes [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) et [NSwag](https://github.com/RicoSuter/NSwag) :

* **Swashbuckle.AspNetCore** est un projet open source pour la génération de documents Swagger pour des API web ASP.NET Core.

* **NSwag** est un autre projet open source pour générer des documents Swagger et intégrer [l’IU Swagger](https://swagger.io/swagger-ui/) ou [ReDoc](https://github.com/Rebilly/ReDoc) aux API web ASP.NET Core. NSwag fournit aussi des méthodes pour générer du code client C# et TypeScript pour votre API.

## <a name="what-is-swagger--openapi"></a>Qu’est-ce que Swagger/OpenAPI ?

Swagger est une spécification indépendante du langage pour décrire les API [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Le projet Swagger a été donné au projet [OpenAPI Initiative](https://www.openapis.org/) et s’appelle maintenant OpenAPI. Les deux noms sont utilisés indifféremment, mais OpenAPI est préféré. Il permet aux ordinateurs et aux utilisateurs de comprendre les fonctionnalités d’un service sans aucun accès direct à l’implémentation (code source, accès réseau, documentation). L’un des objectifs est de limiter la quantité de travail nécessaire pour connecter des services dissociés. Un autre objectif est de réduire le temps nécessaire pour documenter un service avec précision.

## <a name="swagger-specification-swaggerjson"></a>Spécification Swagger (swagger.json)

Au cœur du flux Swagger se trouve la spécification Swagger, document nommé par défaut *swagger.json*. Elle est générée par la chaîne d’outils Swagger (ou des implémentations tierces) en fonction de votre service. Elle décrit les fonctionnalités de votre API et comment y accéder avec HTTP. Elle gère l’interface utilisateur Swagger et est utilisée par la chaîne d’outils pour activer la découverte et la génération de code client. Voici l’exemple d’une spécification Swagger, réduite par souci de concision :

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

## <a name="swagger-ui"></a>Interface utilisateur Swagger

[L’IU Swagger](https://swagger.io/swagger-ui/) est une interface utilisateur web qui fournit des informations sur le service, à l’aide de la spécification Swagger générée. Swashbuckle et NSwag ont une version incorporée à l’interface utilisateur Swagger pour que vous puissiez l’héberger dans votre application ASP.NET Core à l’aide d’un appel d’inscription d’intergiciel (middleware). L’IU web ressemble à ceci :

![Interface utilisateur Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Chaque méthode d’action publique dans vos contrôleurs peut être testée à partir de l’IU. Cliquez sur un nom de méthode pour développer la section. Ajoutez tous les paramètres nécessaires, puis cliquez sur **essayer !**.

![Exemple de test GET Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> La version de l’IU Swagger utilisée pour les captures d’écran est la version 2. Pour obtenir un exemple de la version 3, consultez [l’exemple Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Bien démarrer avec NSwag](xref:tutorials/get-started-with-nswag)
