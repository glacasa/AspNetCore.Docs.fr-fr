---
title: Liste de sécurité IP client pour ASP.NET Core
author: damienbod
description: Découvrez comment écrire des middleware ou des filtres d’action pour valider les adresses IP à distance par rapport à une liste d’adresses IP approuvées.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471801"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Liste de sécurité IP client pour ASP.NET Core

Par [Damien Bowden](https://twitter.com/damien_bod) et [Tom Dykstra](https://github.com/tdykstra)
 
Cet article montre trois façons de mettre en œuvre une liste de sécurité d’adresse IP (également connue sous le nom de liste d’autorisation) dans une application ASP.NET Core. Une application d’échantillon d’accompagnement démontre les trois approches. Vous pouvez utiliser :

* Middleware pour vérifier l’adresse IP à distance de chaque demande.
* Filtres d’action MVC pour vérifier l’adresse IP à distance des demandes de contrôleurs spécifiques ou méthodes d’action.
* Razor Pages filtre pour vérifier l’adresse IP à distance des demandes de pages Razor.

Dans chaque cas, une chaîne contenant des adresses IP client approuvées est stockée dans un paramètre d’application. Le middleware ou le filtre :

* Parse la ficelle dans un tableau. 
* Vérifie si l’adresse IP à distance existe dans le tableau.

L’accès est autorisé si le tableau contient l’adresse IP. Dans le cas contraire, un code de statut HTTP 403 Interdit est retourné.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Liste de sécurité de l’adresse IP

Dans l’application de l’échantillon, la liste de sécurité de l’adresse IP est :

* Défini par `AdminSafeList` la propriété dans le fichier *appsettings.json.*
* Une chaîne semi-alignée qui peut contenir à la fois [la version 4 du protocole Internet (IPv4)](https://wikipedia.org/wiki/IPv4) et [la version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) du protocole Internet.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Dans l’exemple précédent, les `127.0.0.1` adresses IPv4 de `192.168.1.5` et `::1` l’adresse `0:0:0:0:0:0:0:1`IPv6 loopback de (format compressé pour ) sont autorisés.

## <a name="middleware"></a>Middlewares

La `Startup.Configure` méthode ajoute `AdminSafeListMiddleware` le type de middleware personnalisé au pipeline de demande de l’application. La liste de sécurité est récupérée avec le fournisseur de configuration .NET Core et est passée comme paramètre constructeur.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Le middleware analyse la chaîne dans un tableau et recherche l’adresse IP à distance dans le tableau. Si l’adresse IP à distance n’est pas trouvée, le middleware renvoie HTTP 403 Interdit. Ce processus de validation est contourné pour les demandes HTTP GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtre d’action

Si vous souhaitez un contrôle d’accès piloté par une liste de sécurité pour des contrôleurs MVC spécifiques ou des méthodes d’action, utilisez un filtre d’action. Par exemple :

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

Ajouter `Startup.ConfigureServices`le filtre d’action à la collection de filtres MVC. Dans l’exemple `ClientIpCheckActionFilter` suivant, un filtre d’action est ajouté. Une liste de sécurité et une instance d’enregistreur de console sont passées comme paramètres de constructeur.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Le filtre d’action peut ensuite être appliqué à un contrôleur ou à une méthode d’action avec l’attribut [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

Dans l’application d’échantillon, le filtre `Get` d’action est appliqué à la méthode d’action du contrôleur. Lorsque vous testez l’application en envoyant :

* Une demande HTTP `[ServiceFilter]` GET, l’attribut valide l’adresse IP du client. Si l’accès `Get` est autorisé à la méthode d’action, une variation de la sortie de la console suivante est produite par le filtre d’action et la méthode d’action :

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Un verbe de demande `AdminSafeListMiddleware` HTTP autre que GET, le middleware valide l’adresse IP du client.

## <a name="razor-pages-filter"></a>Filtre De pages de rasoir

Si vous souhaitez un contrôle d’accès safelist pour une application Razor Pages, utilisez un filtre Razor Pages. Par exemple :

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

Dans `Startup.ConfigureServices`, activez le filtre Razor Pages en l’ajoutant à la collection de filtres MVC. Dans l’exemple `ClientIpCheckPageFilter` suivant, un filtre Razor Pages est ajouté. Une liste de sécurité et une instance d’enregistreur de console sont passées comme paramètres de constructeur.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Lorsque la page *Razor Index* de l’application de l’échantillon est demandée, le filtre Razor Pages valide l’adresse IP du client. Le filtre produit une variation de la sortie de la console suivante :

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/middleware/index>
* [Filtres d’actions](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
