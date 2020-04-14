---
title: Routage dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ASP.NET itinéraire Core est responsable de l’appariement des demandes HTTP et de l’envoi vers des points de terminaison exécutables.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
uid: fundamentals/routing
ms.openlocfilehash: 0fc89ccf15c14c67f284a7084a21159af300a195
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277221"
---
# <a name="routing-in-aspnet-core"></a>Routage dans ASP.NET Core

Par [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), et [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Routing est responsable de l’appariement des demandes HTTP entrantes et l’envoi de ces demandes aux paramètres exécutables de l’application. [Les points de terminaison](#endpoint) sont les unités de l’application de code exécutable de traitement des demandes. Les points de terminaison sont définis dans l’application et configurés lorsque l’application démarre. Le processus de correspondance des points de terminaison peut extraire des valeurs de l’URL de la demande et fournir ces valeurs pour le traitement des demandes. En utilisant les informations de point de terminaison de l’application, le routage est également en mesure de générer des URL qui cartographient en points de terminaison.

Les applications peuvent configurer le routage à l’aide de :

- Controllers
- Pages Razor
- SignalR
- Services gRPC
- Middleware compatible avec le point de [terminaison](xref:fundamentals/middleware/index) comme [les contrôles de santé](xref:host-and-deploy/health-checks).
- Délégués et lambdas inscrits avec itinéraire.

Ce document couvre les détails de bas niveau de ASP.NET itinéraire de base. Pour plus d’informations sur le routage configuré :

* Pour les contrôleurs, voir <xref:mvc/controllers/routing>.
* Pour les conventions <xref:razor-pages/razor-pages-conventions>De Razor Pages, voir .

Le système de routage des points de terminaison décrit dans ce document s’applique à ASP.NET Core 3.0 et plus tard. Pour plus d’informations sur <xref:Microsoft.AspNetCore.Routing.IRouter>le système de routage précédent basé sur , sélectionnez la version ASP.NET Core 2.1 à l’aide d’une des approches suivantes :

* Le sélecteur de version pour une version précédente.
* Sélectionnez [ASP.NET’itinéraire Core 2.1](https://docs.microsoft.com/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

[Afficher ou télécharger le code de l’échantillon](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([comment télécharger](xref:index#how-to-download-a-sample))

Les échantillons de téléchargement de ce `Startup` document sont activés par une classe spécifique. Pour exécuter un échantillon *Program.cs* spécifique, modifiez Program.cs `Startup` pour appeler la classe désirée.

## <a name="routing-basics"></a>Concepts de base du routage

Tous les modèles ASP.NET Core incluent le routage dans le code généré. Routing est enregistré dans le `Startup.Configure`pipeline [middleware](xref:fundamentals/middleware/index) dans .

Le code suivant montre un exemple de base de routage :

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Routing utilise une paire de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> middleware, enregistrée par et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>:

* `UseRouting`ajoute l’appariement de l’itinéraire au pipeline middleware. Ce middleware examine l’ensemble des points de terminaison définis dans l’application, et sélectionne le [meilleur match](#urlm) basé sur la demande.
* `UseEndpoints`ajoute l’exécution du point de terminaison au pipeline middleware. Il exécute le délégué associé au critère de terminaison sélectionné.

L’exemple précédent comprend un itinéraire unique vers le point de terminaison *de code à* l’aide de la méthode [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*) :

* Lorsqu’une `GET` demande HTTP est `/`envoyée à l’URL racine :
  * Le délégué de demande montré exécute.
  * `Hello World!`est écrit à la réponse HTTP. Par défaut, l’URL `/` racine est `https://localhost:5001/`.
* Si la méthode `GET` de demande n’est pas ou l’URL racine n’est pas `/`, aucun itinéraire ne correspond et un HTTP 404 est retourné.

### <a name="endpoint"></a>Point de terminaison

<a name="endpoint"></a>

La `MapGet` méthode est utilisée pour définir un **critère d’évaluation**. Un point de terminaison est quelque chose qui peut être:

* Sélectionné, en faisant correspondre l’URL et la méthode HTTP.
* Exécuté, en exécutant le délégué.

Les points de terminaison qui peuvent être appariés et exécutés par l’application sont configurés dans `UseEndpoints`. Par <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>exemple, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*>, et [des méthodes similaires](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) relient les délégués de demande au système de routage.
D’autres méthodes peuvent être utilisées pour connecter ASP.NET fonctionnalités du cadre Core au système de routage :
- [MapRazorPages pour les pages Razor](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [MapControllers pour les contrôleurs](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub\<THub> pour SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<TService> pour gRPC](xref:grpc/aspnetcore)

L’exemple suivant montre l’itinéraire avec un modèle d’itinéraire plus sophistiqué :

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

La `/hello/{name:alpha}` chaîne est un **modèle d’itinéraire**. Il est utilisé pour configurer la façon dont le point final est apparié. Dans ce cas, le modèle correspond à :

* Une URL comme`/hello/Ryan`
* Tout chemin URL `/hello/` qui commence par suivi d’une séquence de caractères alphabétiques.  `:alpha`applique une contrainte d’itinéraire qui ne correspond qu’aux caractères alphabétiques. [Les contraintes d’itinéraire](#route-constraint-reference) sont expliquées plus tard dans ce document.

Le deuxième segment du `{name:alpha}`chemin URL, :

* Est lié `name` au paramètre.
* Est capturé et stocké dans [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*).

Le système de routage des points de terminaison décrit dans ce document est nouveau à partir de ASP.NET Core 3.0. Cependant, toutes les versions de ASP.NET Core prennent en charge le même ensemble de fonctionnalités de modèle d’itinéraire et de contraintes d’itinéraire.

L’exemple suivant montre l’itinéraire avec [des contrôles de santé](xref:host-and-deploy/health-checks) et l’autorisation :

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

L’exemple précédent montre comment :

* Le middleware d’autorisation peut être utilisé avec le routage.
* Les points de terminaison peuvent être utilisés pour configurer le comportement d’autorisation.

L’appel <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*> ajoute un point de terminaison de bilan de santé. L’enchaînement <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> de cet appel attache une politique d’autorisation au point de terminaison.

Appeler <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> et ajouter l’authentification et l’autorisation middleware. Ces middleware sont <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> `UseEndpoints` placés entre et afin qu’ils puissent:

* Voir quel critère d’évaluation a été choisi par `UseRouting`.
* Appliquer une politique <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> d’autorisation avant les envois au point de terminaison.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Métadonnées de point de terminaison

Dans l’exemple précédent, il y a deux critères d’évaluation, mais seul le critère de vérification de santé comporte une politique d’autorisation. Si la demande correspond au `/healthz`critère de vérification de santé, une vérification d’autorisation est effectuée. Cela démontre que les paramètres peuvent avoir des données supplémentaires qui leur sont rattachées. Ces données supplémentaires sont appelées **métadonnées**de point de terminaison :

* Les métadonnées peuvent être traitées par un moyen de restauration.
* Les métadonnées peuvent être de n’importe quel type .NET.

## <a name="routing-concepts"></a>Concepts de routage

Le système de routage s’appuie sur le pipeline middleware en ajoutant le concept de **point de terminaison** puissant. Les points de terminaison représentent des unités de fonctionnalités de l’application qui sont distinctes les unes des autres en termes de routage, d’autorisation et d’un certain nombre de systèmes ASP.NET Core.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>définition ASP.NET point de terminaison de base

Un critère d’évaluation ASP.NET Core est :

* Exécutable: <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>A un .
* Extensible: A une collection [de métadonnées.](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)
* Sélection: Optionnellement, a [des informations de routage](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Enumerable: La collecte des points de terminaison <xref:Microsoft.AspNetCore.Routing.EndpointDataSource> peut être répertoriée en récupérant le de [DI](xref:fundamentals/dependency-injection).

Le code suivant montre comment récupérer et inspecter le point de terminaison correspondant à la demande actuelle :

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Le point de terminaison, s’il est sélectionné, peut être récupéré à partir de la `HttpContext`. Ses propriétés peuvent être inspectées. Les objets de point de terminaison sont immuables et ne peuvent pas être modifiés après la création. Le type le plus commun <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>de critère d’évaluation est un . `RouteEndpoint`comprend des informations qui lui permettent d’être sélectionnés par le système de routage.

Dans le code précédent, [application. Utilisez](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) configure un [middleware](xref:fundamentals/middleware/index)en ligne .

<a name="mt"></a>

Le code suivant montre que, selon l’endroit où `app.Use` il est appelé dans le pipeline, il se peut qu’il n’y ait pas de critère d’évaluation :

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

Cet échantillon `Console.WriteLine` précédent ajoute des énoncés qui affichent si un critère d’évaluation a été choisi ou non. Pour plus de clarté, l’échantillon `/` attribue un nom d’affichage au critère de terminaison fourni.

Exécution de ce code `/` avec une URL d’affichages:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Exécution de ce code avec n’importe quel autre affichage URL:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Cette sortie démontre que :

* Le critère d’évaluation `UseRouting` est toujours nul avant d’être appelé.
* Si une correspondance est trouvée, le point `UseRouting` <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>final n’est pas nul entre et .
* Le `UseEndpoints` middleware est **terminal** quand une correspondance est trouvée. [Terminal middleware](#tm) est défini plus tard dans ce document.
* Le middleware `UseEndpoints` après exécuter seulement quand aucun match n’est trouvé.

Le `UseRouting` middleware utilise la méthode [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*) pour attacher le point de terminaison au contexte actuel. Il est possible de `UseRouting` remplacer le middleware par une logique personnalisée et encore obtenir les avantages de l’utilisation des points de terminaison. Les points de terminaison sont un primitif de bas niveau comme middleware, et ne sont pas couplés à la mise en œuvre de routage. La plupart des applications `UseRouting` n’ont pas besoin de remplacer par une logique personnalisée.

Le `UseEndpoints` middleware est conçu pour être `UseRouting` utilisé en tandem avec le middleware. La logique de base pour exécuter un point de terminaison n’est pas compliquée. Utilisez <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> pour récupérer le point de <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate> terminaison, puis invoquer sa propriété.

Le code suivant montre comment middleware peut influencer ou réagir au routage :

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

L’exemple précédent démontre deux concepts importants :

* Middleware peut `UseRouting` s’exécuter avant de modifier les données sur lesquelles le routage fonctionne.
    * Habituellement middleware qui apparaît avant le routage modifie <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>une <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*>propriété <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>de la demande, comme , ou .
* Middleware peut `UseRouting` s’exécuter entre et <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> traiter les résultats du routage avant l’exécution du point de terminaison.
    * Middleware qui `UseRouting` court `UseEndpoints`entre et :
      * Inspecte habituellement les métadonnées pour comprendre les points de terminaison.
      * Prend souvent des décisions `UseAuthorization` de `UseCors`sécurité, comme cela se fait par et .
    * La combinaison de middleware et de métadonnées permet de configurer des politiques par point de terminaison.

Le code précédent montre un exemple d’un middleware personnalisé qui prend en charge les stratégies par point de terminaison. Le middleware écrit un *journal d’audit* de l’accès aux données sensibles à la console. Le middleware peut être configuré pour `AuditPolicyAttribute` *auditer* un point de terminaison avec les métadonnées. Cet échantillon démontre un modèle *d’opt-in* où seuls les points de terminaison qui sont marqués comme sensibles sont vérifiés. Il est possible de définir cette logique à l’envers, en auditant tout ce qui n’est pas marqué comme sûr, par exemple. Le système de métadonnées de point de terminaison est flexible. Cette logique pourrait être conçue de quelque manière que ce soit convient au cas d’utilisation.

Le code d’échantillon précédent est destiné à démontrer les concepts de base des points de terminaison. **L’échantillon n’est pas destiné à l’utilisation de la production**. Une version plus complète d’un *middleware journal d’audit* serait :

* Connectez-vous à un fichier ou une base de données.
* Inclure des détails tels que l’utilisateur, l’adresse IP, le nom du point de terminaison sensible, et plus encore.

Les métadonnées `AuditPolicyAttribute` de la `Attribute` politique d’audit sont définies comme une utilisation plus facile avec des cadres basés sur des classes tels que les contrôleurs et SignalR. Lors de *l’utilisation de l’itinéraire au code*:

* Les métadonnées sont attachées à un constructeur API.
* Les cadres basés sur les classes incluent tous les attributs de la méthode et de la classe correspondantes lors de la création de critères d’évaluation.

Les meilleures pratiques pour les types de métadonnées sont de les définir soit comme des interfaces ou des attributs. Les interfaces et les attributs permettent la réutilisation du code. Le système de métadonnées est flexible et n’impose aucune limitation.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Comparaison d’un middleware terminal et d’un itinéraire

L’échantillon de code suivant contraste à l’aide de middleware avec l’utilisation du routage :

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Le style de middleware montré avec `Approach 1:` est terminal **middleware**. C’est ce qu’on appelle le middleware terminal parce qu’il fait une opération de correspondance:

* L’opération correspondante de `Path == "/"` l’échantillon précédent `Path == "/Movie"` est pour le middleware et pour le routage.
* Quand un match est réussi, il exécute certaines fonctionnalités et `next` retourne, plutôt que d’invoquer le middleware.

C’est ce qu’on appelle le middleware terminal parce qu’il met fin à la recherche, exécute certaines fonctionnalités, puis revient.

Comparaison d’un middleware terminal et d’un itinéraire :
* Les deux approches permettent de mettre fin au pipeline de traitement :
    * Middleware met fin au pipeline en `next`revenant plutôt que d’invoquer .
    * Les points de terminaison sont toujours terminaux.
* Terminal middleware permet de positionner le middleware à un endroit arbitraire dans le pipeline:
    * Les points de terminaison s’exécutent à la position de <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>.
* Terminal middleware permet au code arbitraire de déterminer quand le middleware correspond:
    * Le code d’appariement d’itinéraire personnalisé peut être verbeux et difficile à écrire correctement.
    * Routing fournit des solutions simples pour les applications typiques. La plupart des applications ne nécessitent pas de code d’appariement d’itinéraire personnalisé.
* Interface de point de terminaison avec middleware tels que `UseAuthorization` et `UseCors`.
    * L’utilisation d’un middleware terminal avec `UseAuthorization` ou `UseCors` nécessite un interfacrage manuel avec le système d’autorisation.

Un [point de terminaison](#endpoint) définit les deux :

* Un délégué pour traiter les demandes.
* Une collection de métadonnées arbitraires. Les métadonnées sont utilisées pour mettre en œuvre des préoccupations transversales basées sur les politiques et la configuration fixées à chaque point de terminaison.

Terminal middleware peut être un outil efficace, mais peut exiger:

* Une quantité importante de codage et de tests.
* Intégration manuelle avec d’autres systèmes pour atteindre le niveau de flexibilité souhaité.

Envisagez de vous intégrer au routage avant d’écrire un middleware terminal.

Middleware terminal existant qui s’intègre à [la carte](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) ou <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> peut généralement être transformé en un point de terminaison de routage conscient. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) montre le modèle pour routeur-ware:
* Écrivez une <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>méthode d’extension sur .
* Créez un pipeline de <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>middleware imbriqué à l’aide de .
* Attachez le middleware au nouveau pipeline. Dans ce cas, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.
* <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>le pipeline middleware <xref:Microsoft.AspNetCore.Http.RequestDelegate>dans un .
* Appelez `Map` et fournissez le nouveau pipeline middleware.
* Retournez l’objet `Map` constructeur fourni par la méthode d’extension.

Le code suivant montre l’utilisation de [MapHealthChecks](xref:host-and-deploy/health-checks):

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

L’échantillon précédent montre pourquoi le retour de l’objet constructeur est important. Le retour de l’objet constructeur permet au développeur d’applications de configurer des stratégies telles que l’autorisation pour le point de terminaison. Dans cet exemple, les contrôles de santé middleware n’a pas d’intégration directe avec le système d’autorisation.

Le système de métadonnées a été créé en réponse aux problèmes rencontrés par les auteurs d’extéabilité à l’aide de middleware terminal. Il est problématique pour chaque middleware de mettre en œuvre sa propre intégration avec le système d’autorisation.

<a name="urlm"></a>

### <a name="url-matching"></a>Correspondance d’URL

* Est le processus par lequel l’itinéraire correspond à une demande entrante à un [point de terminaison](#endpoint).
* Est basé sur les données dans le chemin DE l’URL et les en-têtes.
* Peut être étendu pour tenir compte de toutes les données de la demande.

Lorsqu’un middleware de routage `Endpoint` s’exécute, il définit <xref:Microsoft.AspNetCore.Http.HttpContext> une valeur et l’itinéraire à une fonction de [demande](xref:fundamentals/request-features) sur la demande actuelle :

* Appeler [HttpContext.GetEndpoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) obtient le point de terminaison.
* `HttpRequest.RouteValues` récupère la collection de valeurs d’itinéraire.

[Middleware](xref:fundamentals/middleware/index) en cours d’exécution après le middleware routage peut inspecter le point de terminaison et prendre des mesures. Par exemple, un middleware d’autorisation peut interroger la collecte des métadonnées du point final pour une politique d’autorisation. Une fois que tous les intergiciels dans le pipeline de traitement de requêtes sont exécutés, le délégué du point de terminaison sélectionné est appelé.

Le système de routage dans le routage de point de terminaison est responsable de toutes les décisions de distribution. Étant donné que le middleware applique des stratégies basées sur le critère d’évaluation sélectionné, il est important que :

* Toute décision qui peut affecter l’expédition ou l’application des politiques de sécurité est prise à l’intérieur du système de routage.

> [!WARNING]
> Pour la compatibilité à l’envers, lorsqu’un délégué de point de terminaison Controller ou Razor Pages est exécuté, les propriétés de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) sont définies à des valeurs appropriées en fonction du traitement de demande effectué jusqu’à présent.
>
> Le `RouteContext` type sera marqué obsolète dans une prochaine version:
>
> * Migrer `RouteData.Values` vers `HttpRequest.RouteValues`.
> * Migrez `RouteData.DataTokens` pour récupérer [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) à partir des métadonnées de point de terminaison.

L’appariement d’URL fonctionne dans un ensemble configurable de phases. Dans chaque phase, la sortie est un ensemble de correspondances. L’ensemble des matchs peut être réduit davantage par la phase suivante. La mise en œuvre de routage ne garantit pas une commande de traitement pour l’appariement des points de terminaison. **Tous les** correspondances possibles sont traitées à la fois. Les phases de correspondance de l’URL se produisent dans l’ordre suivant. ASP.NET Core :

1. Traite le chemin URL par rapport à l’ensemble des points de terminaison et de leurs modèles d’itinéraire, en recueillant **tous les** matchs.
1. Prend la liste précédente et supprime les correspondances qui échouent avec des contraintes d’itinéraire appliquées.
1. Prend la liste précédente et supprime les correspondances qui échouent à l’ensemble des instances [MatcherPolicy.](xref:Microsoft.AspNetCore.Routing.MatcherPolicy)
1. Utilise [l’outil d’évaluationSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector) pour prendre une décision finale de la liste précédente.

La liste des critères d’évaluation est priorisée en fonction des critères :

* Le [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)
* La [préséance du modèle d’itinéraire](#rtp)

Tous les critères d’évaluation correspondants sont traités dans chaque phase jusqu’à ce que le <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> soit atteint. C’est `EndpointSelector` la phase finale. Il choisit le point de terminaison la plus prioritaire des matchs comme meilleur match. S’il y a d’autres matchs avec la même priorité que le meilleur match, une exception de match ambigue est lancée.

La préséance de l’itinéraire est calculée sur la base d’un modèle d’itinéraire **plus spécifique** étant donné une priorité plus élevée. Par exemple, considérez `/hello` `/{message}`les modèles et :

* Les deux correspondent `/hello`au chemin URL .
* `/hello`est plus spécifique et donc une priorité plus élevée.

En général, la préséance de route fait un bon travail de choisir le meilleur match pour les types de systèmes d’URL utilisés dans la pratique. Utiliser <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> uniquement si nécessaire pour éviter une ambiguïté.

En raison des types d’extéabilité fourni par le routage, il n’est pas possible pour le système de routage de calculer à l’avance les itinéraires ambigus. Prenons un exemple tel que `/{message:alpha}` `/{message:int}`les modèles d’itinéraire et :

* La `alpha` contrainte ne correspond qu’aux caractères alphabétiques.
* La `int` contrainte ne correspond qu’aux nombres.
* Ces modèles ont la même priorité d’itinéraire, mais il n’y a pas d’URL unique qu’ils correspondent tous les deux.
* Si le système de routage a signalé une erreur d’ambiguïté au démarrage, il bloquerait ce cas d’utilisation valide.

> [!WARNING]
>
> L’ordre des <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> opérations à l’intérieur n’influence pas le comportement du routage, à une exception près. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>et <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> attribuez automatiquement une valeur de commande à leurs points de terminaison en fonction de l’ordre qu’ils sont invoqués. Cela simule le comportement à long terme des contrôleurs sans le système de routage fournissant les mêmes garanties que les anciennes implémentations de routage.
>
> Dans la mise en œuvre héritée du routage, il est possible de mettre en œuvre l’extensibility de routage qui dépend de l’ordre dans lequel les itinéraires sont traités. Itinéraire de point de terminaison dans ASP.NET Core 3.0 et plus tard :
> 
> * N’a pas de concept d’itinéraires.
> * Ne fournit pas de garanties de commande. Tous les critères d’évaluation sont traités à la fois.
>
> Si cela signifie que vous êtes coincé à l’aide du système de routage hérité, [ouvrez un problème GitHub pour obtenir de l’aide](https://github.com/dotnet/aspnetcore/issues).

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Priorité du modèle d’itinéraire et ordre de sélection des points de terminaison

[La préséance du modèle d’itinéraire](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) est un système qui attribue à chaque modèle d’itinéraire une valeur basée sur sa spécificité. Priorité du modèle d’itinéraire :

* Évite la nécessité d’ajuster l’ordre des points de terminaison dans les cas courants.
* Tentatives pour correspondre aux attentes de bon sens du comportement de routage.

Par exemple, considérez les `/Products/List` modèles et `/Products/{id}`. Il serait raisonnable de `/Products/List` supposer que `/Products/{id}` c’est `/Products/List`un meilleur match que pour le chemin URL . Les œuvres parce `/List` que le segment littéral `/{id}`est considéré comme ayant une meilleure préséance que le segment des paramètres .

Les détails du fonctionnement de la préséance sont couplés à la façon dont les modèles d’itinéraire sont définis :

* Les modèles avec plus de segments sont considérés comme plus spécifiques.
* Un segment avec du texte littéral est considéré comme plus spécifique qu’un segment de paramètres.
* Un segment de paramètre avec une contrainte est considéré comme plus spécifique qu’un sans.
* Un segment complexe est considéré comme spécifique comme un segment de paramètres avec une contrainte.
* Attraper tous les paramètres sont les moins spécifiques.

Voir le [code source sur GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189) pour une référence des valeurs exactes.

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Concepts de génération d’URL

Génération d’URL :

* Est-ce le processus par lequel le routage peut créer un chemin URL basé sur un ensemble de valeurs d’itinéraire.
* Permet une séparation logique entre les points de terminaison et les URL qui y accèdent.

Le routage des <xref:Microsoft.AspNetCore.Routing.LinkGenerator> points de terminaison comprend l’API. `LinkGenerator`est un service singleton disponible à partir de [DI](xref:fundamentals/dependency-injection). L’API `LinkGenerator` peut être utilisée en dehors du contexte d’une demande d’exécution. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) et les scénarios <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>qui s’appuient sur , tels que Tag `LinkGenerator` [Helpers](xref:mvc/views/tag-helpers/intro), HTML Helpers, et Résultats [d’action](xref:mvc/controllers/actions), utiliser l’API en interne pour fournir des capacités de génération de liens.

Le générateur de liens est basé sur le concept d’une **adresse** et de **schémas d’adresse**. Un schéma d’adresse est un moyen de déterminer les points de terminaison à prendre en compte pour la génération de liens. Par exemple, les scénarios de noms d’itinéraire et de valeurs d’itinéraire que beaucoup d’utilisateurs connaissent des contrôleurs et les pages Razor sont implémentées comme un schéma d’adresse.

Le générateur de liaison peut être relié aux contrôleurs et aux pages Razor via les méthodes d’extension suivantes :

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Les surcharges de ces méthodes `HttpContext`acceptent les arguments qui incluent le . Ces méthodes sont fonctionnellement équivalentes à [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) et [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), mais offrent une flexibilité et des options supplémentaires.

Les `GetPath*` méthodes sont `Url.Action` plus `Url.Page`similaires et, en ce qu’elles génèrent un URI contenant un chemin absolu. Les méthodes `GetUri*` génèrent toujours un URI absolu contenant un schéma et un hôte. Les méthodes qui acceptent un `HttpContext` génèrent un URI dans le contexte de la requête en cours d’exécution. Les valeurs [de l’itinéraire ambiant,](#ambient) le chemin de base de l’URL, le schéma et l’hôte de la demande d’exécution sont utilisés à moins d’être dépassés.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> est appelé avec une adresse. La génération d’un URI se fait en deux étapes :

1. Une adresse est liée à une liste de points de terminaison qui correspondent à l’adresse.
1. Le <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> de chaque point de terminaison est évalué jusqu’à ce qu’un modèle de route correspondant aux valeurs fournies soit trouvé. Le résultat obtenu est combiné avec d’autres parties de l’URI fournies par le générateur de liens, puis il est retourné.

Les méthodes fournies par <xref:Microsoft.AspNetCore.Routing.LinkGenerator> prennent en charge des fonctionnalités de génération de liens standard pour n’importe quel type d’adresse. La façon la plus pratique d’utiliser le générateur de lien est par des méthodes d’extension qui effectuent des opérations pour un type d’adresse spécifique:

| Méthode d'extension | Description |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Génère un URI avec un chemin absolu basé sur les valeurs fournies. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Génère un URI absolu basé sur les valeurs fournies.             |

> [!WARNING]
> Faites attention aux implications suivantes de l’appel de méthodes <xref:Microsoft.AspNetCore.Routing.LinkGenerator> :
>
> * Utilisez les méthodes d’extension `GetUri*` avec précaution dans une configuration d’application qui ne valide pas l’en-tête `Host` des requêtes entrantes. Si `Host` l’en-tête des demandes entrantes n’est pas validé, l’entrée de demande non fiable peut être renvoyée au client dans les URL dans une vue ou une page. Nous recommandons que toutes les applications de production configurent leur serveur pour qu’il valide l’en-tête `Host` par rapport à des valeurs valides connues.
>
> * Utilisez <xref:Microsoft.AspNetCore.Routing.LinkGenerator> avec précaution dans le middleware en combinaison avec `Map` ou `MapWhen`. `Map*` modifie le chemin de base de la requête en cours d’exécution, ce qui affecte la sortie de la génération de liens. Toutes les API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permettent la spécification d’un chemin de base. Spécifier un chemin `Map*` de base vide pour annuler l’effet sur la génération de liens.

### <a name="middleware-example"></a>Exemple de middleware

Dans l’exemple suivant, un <xref:Microsoft.AspNetCore.Routing.LinkGenerator> middleware utilise l’API pour créer un lien vers une méthode d’action qui répertorie les produits de stockage. L’utilisation du générateur de lien en `GenerateLink` l’injectant dans une classe et l’appel est disponible pour n’importe quelle classe dans une application :

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Informations de référence sur les modèles de routage

Les jetons `{}` dans définir les paramètres d’itinéraire qui sont liés si l’itinéraire est apparié. Plus d’un paramètre d’itinéraire peut être défini dans un segment d’itinéraire, mais les paramètres d’itinéraire doivent être séparés par une valeur littérale. Par exemple `{controller=Home}{action=Index}` n’est pas une route valide, car il n’y a aucune valeur littérale entre `{controller}` et `{action}`.  Les paramètres d’itinéraire doivent avoir un nom et peuvent avoir des attributs supplémentaires spécifiés.

Un texte littéral autre que les paramètres de routage (par exemple, `{id}`) et le séparateur de chemin `/` doit correspondre au texte présent dans l’URL. La correspondance de texte ne respecte pas la casse et est basée sur la représentation décodée du chemin des URL. Pour correspondre à un paramètre `{` `}`d’itinéraire littéral ou , échapper au délimital en répétant le personnage. Par `{{` exemple `}}`ou .

Astérisque `*` ou double astérisque `**`:

* Peut être utilisé comme préfixe à un paramètre d’itinéraire pour se lier au reste de l’URI.
* Sont appelés paramètres **fourre-tout.** Par exemple, `blog/{**slug}`:
  * Correspond à toute URI qui commence et `/blog` a une valeur qui la suit.
  * La valeur `/blog` suivante est attribuée à la valeur de l’itinéraire [de limace.](https://developer.mozilla.org/docs/Glossary/Slug)

Les paramètres fourre-tout peuvent également établir une correspondance avec la chaîne vide.

Le paramètre fourre-tout échappe aux caractères appropriés lorsque l’itinéraire `/` est utilisé pour générer une URL, y compris les caractères de séparation de chemin. Par exemple, la route `foo/{*path}` avec les valeurs de route `{ path = "my/path" }` génère `foo/my%2Fpath`. Notez la barre oblique d’échappement. Pour les séparateurs de chemin aller-retour, utilisez le préfixe de paramètre de routage `**`. La route `foo/{**path}` avec `{ path = "my/path" }` génère `foo/my/path`.

Les modèles d’URL qui tentent de capturer un nom de fichier avec une extension de fichier facultative doivent faire l’objet de considérations supplémentaires. Prenez par exemple le modèle `files/{filename}.{ext?}`. Quand des valeurs existent à la fois pour `filename` et pour `ext`, les deux valeurs sont renseignées. Si seulement une `filename` valeur pour existe dans l’URL, `.` l’itinéraire correspond parce que la fuite est facultative. Les URL suivantes correspondent à cette route :

* `/files/myFile.txt`
* `/files/myFile`

Les paramètres de route peuvent avoir des **valeurs par défaut**, désignées en spécifiant la valeur par défaut après le nom du paramètre, séparée par un signe égal (`=`). Par exemple, `{controller=Home}` définit `Home` comme valeur par défaut de `controller`. La valeur par défaut est utilisée si aucune valeur n’est présente dans l’URL pour le paramètre. Les paramètres d’itinéraire sont facultatifs en appending un point d’interrogation (`?`) jusqu’à la fin du nom du paramètre. Par exemple : `id?`. La différence entre les valeurs facultatives et les paramètres d’itinéraire par défaut est la suivante :

* Un paramètre d’itinéraire avec une valeur par défaut produit toujours une valeur.
* Un paramètre facultatif n’a de valeur que lorsqu’une valeur est fournie par l’URL de la demande.

Les paramètres de route peuvent avoir des contraintes, qui doivent correspondre à la valeur de route liée à partir de l’URL. L’ajout `:` et le nom de contrainte après le nom du paramètre d’itinéraire spécifie une contrainte inline sur un paramètre d’itinéraire. Si la contrainte exige des arguments, ils sont fournis entre parenthèses `(...)` après le nom de la contrainte. Plusieurs *contraintes inline* peuvent être `:` spécifiées en appending un autre nom et contrainte.

Le nom de la contrainte et les arguments sont passés au service <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> pour créer une instance de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> à utiliser dans le traitement des URL. Par exemple, le modèle de routage `blog/{article:minlength(10)}` spécifie une contrainte `minlength` avec l’argument `10`. Pour plus d’informations sur les contraintes de route et pour obtenir la liste des contraintes fournies par le framework, consultez la section [Informations de référence sur les contraintes de route](#route-constraint-reference).

Les paramètres de l’itinéraire peuvent également avoir des transformateurs de paramètres. Les transformateurs de paramètres transforment la valeur d’un paramètre lors de la génération de liens et des actions et des pages correspondantes aux URL. Comme les contraintes, les transformateurs de paramètres peuvent `:` être ajoutés en ligne à un paramètre d’itinéraire en ajoutant un nom et transformateur après le nom de paramètre d’itinéraire. Par exemple, le modèle de routage `blog/{article:slugify}` spécifie un transformateur `slugify`. Pour plus d’informations sur les transformateurs de paramètre, consultez la section [Informations de référence sur les transformateurs de paramètre](#parameter-transformer-reference).

Le tableau suivant montre des modèles d’itinéraires d’exemple et leur comportement :

| Modèle de routage                           | Exemple d’URI en correspondance    | URI de la requête&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Correspond seulement au chemin unique `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Correspond à `Page` et le définit sur `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Correspond à `Page` et le définit sur `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mappe au contrôleur `Products` et à l’action `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Cartes au `Products` contrôleur `Details` et`id` action avec réglé à 123. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Cartes au `Home` contrôleur `Index` et à la méthode. `id` est ignoré.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Cartes au `Products` contrôleur `Index` et à la méthode. `id` est ignoré.        |

L’utilisation d’un modèle est généralement l’approche la plus simple pour le routage. Il est également possible de spécifier des contraintes et des valeurs par défaut hors du modèle de routage.

### <a name="complex-segments"></a>Segments complexes

Les segments complexes sont traités en faisant correspondre les délimitatrices littérales de droite à gauche d’une manière [non gourmande.](#greedy) Par exemple, `[Route("/a{b}c{d}")]` est un segment complexe.
Les segments complexes fonctionnent d’une manière particulière qui doit être comprise pour les utiliser avec succès. L’exemple de cette section montre pourquoi les segments complexes ne fonctionnent vraiment bien que lorsque le texte de délimitant n’apparaît pas à l’intérieur des valeurs de paramètres. L’utilisation [d’un regex,](/dotnet/standard/base-types/regular-expressions) puis l’extraction manuelle des valeurs est nécessaire pour les cas plus complexes.

[!INCLUDE[](~/includes/regex.md)]

Il s’agit d’un résumé des étapes `/a{b}c{d}` que l’itinéraire effectue avec le modèle et le chemin `/abcd`DE l’URL . L’est `|` utilisé pour aider à visualiser le fonctionnement de l’algorithme:

* Le premier littéral, de `c`droite à gauche, est . Il `/abcd` est donc recherché `/ab|c|d`à partir de la droite et trouve .
* Tout à droite`d`( ) est maintenant `{d}`adapté au paramètre d’itinéraire .
* Le suivant littéral, de `a`droite à gauche, est . Il `/ab|c|d` est donc recherché à partir `a` de `/|a|b|c|d`l’endroit où nous nous sommes laissés, puis se trouve .
* La valeur à`b`droite ( ) est `{b}`maintenant appariée au paramètre d’itinéraire .
* Il n’y a pas de texte restant et aucun modèle d’itinéraire restant, donc c’est une correspondance.

Voici un exemple d’un cas négatif `/a{b}c{d}` en utilisant `/aabcd`le même modèle et le chemin DE l’URL . L’est `|` utilisé pour aider à visualiser le fonctionnement de l’algorithme. Ce cas n’est pas un match, ce qui s’explique par le même algorithme :
* Le premier littéral, de `c`droite à gauche, est . Il `/aabcd` est donc recherché `/aab|c|d`à partir de la droite et trouve .
* Tout à droite`d`( ) est maintenant `{d}`adapté au paramètre d’itinéraire .
* Le suivant littéral, de `a`droite à gauche, est . Il `/aab|c|d` est donc recherché à partir `a` de `/a|a|b|c|d`l’endroit où nous nous sommes laissés, puis se trouve .
* La valeur à`b`droite ( ) est `{b}`maintenant appariée au paramètre d’itinéraire .
* À ce stade, `a`il reste du texte, mais l’algorithme a manqué de modèle d’itinéraire à analyser, donc ce n’est pas une correspondance.

Depuis l’algorithme de correspondance est [non-gourmand:](#greedy)

* Il correspond à la plus petite quantité de texte possible à chaque étape.
* Tout cas où la valeur de délimitation apparaît à l’intérieur des valeurs de paramètre entraîne le non-correspondance.

Les expressions régulières fournissent beaucoup plus de contrôle sur leur comportement correspondant.

<a name="greedy"></a>

Greedy correspondant, également connu comme [correspondance paresseux](https://wikipedia.org/wiki/Regular_expression#Lazy_matching), correspond à la plus grande chaîne possible. Non gourmand correspond à la plus petite chaîne possible.

## <a name="route-constraint-reference"></a>Informations de référence sur les contraintes de routage

Les contraintes de route s’exécutent quand une correspondance s’est produite pour l’URL entrante, et le chemin de l’URL est tokenisé en valeurs de route. Les contraintes d’itinéraire inspectent généralement la valeur d’itinéraire associée par le modèle d’itinéraire et prennent une décision vraie ou fausse quant à savoir si la valeur est acceptable. Certaines contraintes de routage utilisent des données hors de la valeur de route pour déterminer si la requête peut être routée. Par exemple, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> peut accepter ou rejeter une requête en fonction de son verbe HTTP. Les contraintes sont utilisées dans le routage des requêtes et la génération des liens.

> [!WARNING]
> N’utilisez pas de contraintes pour la validation des entrées. Si des contraintes sont utilisées pour la `404` validation des entrées, l’entrée invalide entraîne une réponse non trouvée. L’entrée invalide doit produire une `400` mauvaise demande avec un message d’erreur approprié. Les contraintes de route sont utilisées pour lever l’ambiguïté entre des routes similaires, et non pas pour valider les entrées d’une route particulière.

Le tableau suivant montre des contraintes d’itinéraires par exemple et leur comportement attendu :

| contrainte | Exemple | Exemples de correspondances | Notes |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Correspond à n’importe quel entier |
| `bool` | `{active:bool}` | `true`, `FALSE` | Matchs `true` `false`ou . Non-respect de la casse |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Correspond à `DateTime` une valeur valable dans la culture invariante. Voir l’avertissement précédent. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Correspond à `decimal` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Correspond à `double` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Correspond à `float` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Correspond à une valeur `Guid` valide |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Correspond à une valeur `long` valide |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La chaîne doit comporter au moins 4 caractères |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | La chaîne ne doit pas comporter plus de 8 caractères |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La chaîne doit comporter exactement 12 caractères |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La chaîne doit comporter au moins 8 caractères et pas plus de 16 caractères |
| `min(value)` | `{age:min(18)}` | `19` | La valeur entière doit être au moins égale à 18 |
| `max(value)` | `{age:max(120)}` | `91` | La valeur entière ne doit pas être supérieure à 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | La valeur entière doit être au moins égale à 18 mais ne doit pas être supérieure à 120 |
| `alpha` | `{name:alpha}` | `Rick` | La chaîne doit se composer d’un ou de plusieurs caractères alphabétiques, `a` - `z` et insensible au cas. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La ficelle doit correspondre à l’expression régulière. Voir des conseils sur la définition d’une expression régulière. |
| `required` | `{name:required}` | `Rick` | Utilisé pour garantir qu’une valeur autre qu’un paramètre est présente pendant la génération de l’URL |

[!INCLUDE[](~/includes/regex.md)]

Plusieurs contraintes délimitées par le côlon peuvent être appliquées à un seul paramètre. Par exemple, la contrainte suivante limite un paramètre à une valeur entière supérieure ou égale à 1 :

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Les contraintes d’itinéraire qui vérifient l’URL et sont converties en type CLR utilisent toujours la culture invariante. Par exemple, la conversion `int` au `DateTime`type CLR ou . Ces contraintes supposent que l’URL n’est pas localisable. Les contraintes de routage fournies par le framework ne modifient pas les valeurs stockées dans les valeurs de route. Toutes les valeurs de route analysées à partir de l’URL sont stockées sous forme de chaînes. Par exemple, la contrainte `float` tente de convertir la valeur de route en valeur float, mais la valeur convertie est utilisée uniquement pour vérifier qu’elle peut être convertie en valeur float.

### <a name="regular-expressions-in-constraints"></a>Expressions régulières dans les contraintes

[!INCLUDE[](~/includes/regex.md)]

Les expressions régulières peuvent être spécifiées comme contraintes inlines utilisant la contrainte d’itinéraire. `regex(...)` Les méthodes <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> de la famille acceptent également un objet littéral de contraintes. Si cette forme est utilisée, les valeurs de chaîne sont interprétées comme des expressions régulières.

Le code suivant utilise une contrainte de regex en ligne :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Le code suivant utilise un objet littéral pour spécifier une contrainte de regex :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

Le framework ASP.NET Core ajoute `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` au constructeur d’expression régulière. Pour obtenir une description de ces membres, consultez <xref:System.Text.RegularExpressions.RegexOptions>.

Les expressions régulières utilisent des dlimiteurs et des jetons similaires à ceux utilisés par le routage et la langue C. Les jetons d’expression régulière doivent être placés dans une séquence d’échappement. Pour utiliser l’expression `^\d{3}-\d{2}-\d{4}$` régulière dans une contrainte inline, utilisez l’un des éléments suivants :

* Remplacez les `\` caractères `\\` fournis dans la chaîne en tant `\` que caractères dans le fichier source Cmd afin d’échapper au caractère d’évasion de cordes.
* [Littérats de chaîne Verbatim](/dotnet/csharp/language-reference/keywords/string).

Pour échapper à l’itinéraire `{` `}`paramètre delimiter `[`caractères , , `{{`, `[[` `]` `]]`, le double des personnages dans l’expression, par exemple, , `}}`, . Le tableau suivant montre une expression régulière et sa version échappée:

| Expression régulière    | Expression régulière échappée     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Les expressions régulières utilisées dans `^` le routage commencent souvent par le personnage et correspondent à la position de départ de la chaîne. Les expressions se `$` terminent souvent par le personnage et correspondent à la fin de la chaîne. Les `^` `$` personnages et les personnages s’assurent que l’expression régulière correspond à la valeur du paramètre d’itinéraire. Sans `^` les `$` personnages et les caractères, l’expression régulière correspond à tout sous-corde dans la chaîne, ce qui est souvent indésirable. Le tableau suivant fournit des exemples et explique pourquoi ils correspondent ou ne correspondent pas :

| Expression   | String    | Correspond | Commentaire               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | 123abc456 | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | mz        | Oui   | Correspondance avec l’expression    |
| `[a-z]{2}`   | MZ        | Oui   | Non-respect de la casse    |
| `^[a-z]{2}$` | hello     | Non    | Voir `^` et `$` ci-dessus |
| `^[a-z]{2}$` | 123abc456 | Non    | Voir `^` et `$` ci-dessus |

Pour plus d’informations sur la syntaxe des expressions régulières, consultez [Expressions régulières du .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Pour contraindre un paramètre à un ensemble connu de valeurs possibles, utilisez une expression régulière. Par exemple, `{action:regex(^(list|get|create)$)}` établit une correspondance avec la valeur de route `action` uniquement pour `list`, `get` ou `create`. Si elle est passée dans le dictionnaire de contraintes, la chaîne `^(list|get|create)$` est équivalente. Les contraintes qui sont passées dans le dictionnaire des contraintes qui ne correspondent pas à l’une des contraintes connues sont également traitées comme des expressions régulières. Les contraintes qui sont passées dans un modèle qui ne correspondent pas à l’une des contraintes connues ne sont pas traitées comme des expressions régulières.

### <a name="custom-route-constraints"></a>Contraintes d’itinéraire personnalisées

Des contraintes d’itinéraire personnalisées <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> peuvent être créées en implémentant l’interface. `IRouteConstraint` L’interface <xref:System.Web.Routing.IRouteConstraint.Match*>contient `true` , qui revient si `false` la contrainte est satisfaite et autre.

Des contraintes d’itinéraire personnalisées sont rarement nécessaires. Avant de mettre en œuvre une contrainte d’itinéraire personnalisée, envisagez des solutions de rechange, telles que la liaison de modèle.

Pour utiliser `IRouteConstraint`une coutume, le type de contrainte d’itinéraire doit être enregistré auprès de l’application dans le conteneur de <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> service. Un `ConstraintMap` est un dictionnaire qui mappe les clés de contrainte d’itinéraire aux implémentations `IRouteConstraint` qui valident ces contraintes. Le `ConstraintMap` d’une application peut être mis à jour dans `Startup.ConfigureServices` dans le cadre d’un appel [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou en configurant <xref:Microsoft.AspNetCore.Routing.RouteOptions> directement avec `services.Configure<RouteOptions>`. Par exemple :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

La contrainte précédente est appliquée dans le code suivant :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

La mise `MyCustomConstraint` en `0` œuvre d’empêche d’être appliquée à un paramètre d’itinéraire :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Le code précédent :

* Empêche `0` dans `{id}` le segment de l’itinéraire.
* Est montré pour fournir un exemple de base de la mise en œuvre d’une contrainte personnalisée. Il ne doit pas être utilisé dans une application de production.

Le code suivant est une meilleure `id` approche `0` pour empêcher un contenant un d’être traité :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Le code précédent présente les `MyCustomConstraint` avantages suivants par rapport à l’approche :

* Il ne nécessite pas de contrainte personnalisée.
* Il renvoie une erreur descriptive `0`plus lorsque le paramètre d’itinéraire comprend .

## <a name="parameter-transformer-reference"></a>Informations de référence sur le transformateur de paramètre

Transformateurs de paramètre :

* Exécutez lors de <xref:Microsoft.AspNetCore.Routing.LinkGenerator>la génération d’un lien à l’aide de .
* Implémentez <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.
* Sont configurés à l’aide de <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Prennent la valeur de routage du paramètre et la convertissent en une nouvelle valeur de chaîne.
* Aboutissent à l’utilisation de la valeur transformée dans le lien généré.

Par exemple, un transformateur de paramètre `slugify` personnalisé dans le modèle d’itinéraire `blog\{article:slugify}` avec `Url.Action(new { article = "MyTestArticle" })` génère `blog\my-test-article`.

Considérez `IOutboundParameterTransformer` la mise en œuvre suivante :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Pour utiliser un transformateur de paramètres dans <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> `Startup.ConfigureServices`un modèle d’itinéraire, configurez-le en utilisant :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

Le cadre ASP.NET Core utilise des transformateurs de paramètres pour transformer l’URI lorsqu’un point de terminaison se résout. Par exemple, les transformateurs de paramètres `area`transforment `controller` `action`les `page`valeurs d’itinéraire utilisées pour correspondre à un , , , et .

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Avec le modèle d’itinéraire précédent, l’action `SubscriptionManagementController.GetAll` est jumelée à l’URI `/subscription-management/get-all`. Un transformateur de paramètre ne modifie pas les valeurs de routage utilisées pour générer un lien. Par exemple, `Url.Action("GetAll", "SubscriptionManagement")` produit `/subscription-management/get-all`.

ASP.NET Core fournit des conventions API pour l’utilisation de transformateurs de paramètres avec des itinéraires générés :

* La <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName> convention MVC applique un transformateur de paramètres spécifié à toutes les routes d’attribut dans l’application. Le transformateur de paramètre transforme les jetons de routage d’attribut quand ils sont remplacés. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> utilise la convention API. Cette convention applique un transformateur de paramètre spécifié à toutes les pages Razor découvertes automatiquement. Le transformateur de paramètre transforme les segments du nom de dossier et du nom de fichier des routes Razor Pages. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser les routages de pages](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Informations de référence sur la génération d’URL

Cette section contient une référence pour l’algorithme mis en œuvre par la génération d’URL. Dans la pratique, les exemples les plus complexes de génération d’URL utilisent des contrôleurs ou des pages Razor. Voir [l’itinéraire dans les contrôleurs](xref:mvc/controllers/routing) pour plus d’informations.

Le processus de génération d’URL commence par un appel à [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) ou une méthode similaire. La méthode est fournie avec une adresse, un ensemble de valeurs `HttpContext`d’itinéraire, et des informations d’option sur la demande actuelle de .

La première étape consiste à utiliser l’adresse pour résoudre [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1) un ensemble de paramètres de fin de candidat à l’aide d’un qui correspond au type d’adresse.

Une fois que l’ensemble des candidats est trouvé par le système d’adresse, les points de terminaison sont commandés et traités itérativement jusqu’à ce qu’une opération de génération d’URL réussisse. La génération d’URL ne vérifie **pas** les ambiguïtés, le premier résultat retourné est le résultat final.

### <a name="troubleshooting-url-generation-with-logging"></a>Dépannage de la génération d’URL avec l’enregistrement

La première étape dans la génération d’URL `Microsoft.AspNetCore.Routing` de `TRACE`dépannage est de définir le niveau d’enregistrement de . `LinkGenerator`enregistre de nombreux détails sur son traitement qui peuvent être utiles pour résoudre les problèmes.

Voir [la référence de génération d’URL](#ugr) pour plus de détails sur la génération d’URL.

### <a name="addresses"></a>Adresses

Les adresses sont le concept dans la génération d’URL utilisé pour lier un appel dans le générateur de lien à un ensemble de paramètres candidats.

Les adresses sont un concept extensible qui est livré avec deux implémentations par défaut:

* Utilisation *du nom de point de terminaison* (`string`) comme adresse :
    * Fournit des fonctionnalités similaires au nom de l’itinéraire de MVC.
    * Utilise <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata> le type de métadonnées.
    * Résout la chaîne fournie contre les métadonnées de tous les points de terminaison enregistrés.
    * Jetez une exception sur le démarrage si plusieurs points de terminaison utilisent le même nom.
    * Recommandé pour une utilisation générale en dehors des contrôleurs et des pages Razor.
* Utilisation des<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress> *valeurs d’itinéraire* ( ) comme adresse :
    * Fournit des fonctionnalités similaires aux contrôleurs et à la génération d’URL héritée de Razor Pages.
    * Très complexe à étendre et déboiffer.
    * Fournit l’implémentation utilisée par `IUrlHelper`, Tag Helpers, ASSISTANT HTML, Résultats d’action, etc.

Le rôle du système d’adresse est de faire de l’association entre l’adresse et les critères d’évaluation correspondants par des critères arbitraires :

* Le schéma de nom de point final effectue un suivi de base de dictionnaire.
* Le système de valeurs d’itinéraire a un sous-ensemble complexe meilleur d’algorithme d’ensemble.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Valeurs ambiantes et valeurs explicites

De la demande actuelle, l’itinéraire accède `HttpContext.Request.RouteValues`aux valeurs de l’itinéraire de la demande actuelle . Les valeurs associées à la demande actuelle sont appelées les **valeurs ambiantes**. Aux fins de la clarté, la documentation fait référence aux valeurs de route transmises aux méthodes comme **valeurs explicites**.

L’exemple suivant montre des valeurs ambiantes et des valeurs explicites. Il fournit des valeurs ambiantes `{ id = 17, }`à partir de la demande actuelle et des valeurs explicites : :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Le code précédent :

* Retourne `/Widget/Index/17`.
* Obtient <xref:Microsoft.AspNetCore.Routing.LinkGenerator> via [DI](xref:fundamentals/dependency-injection).

Le code suivant ne fournit aucune `{ controller = "Home", action = "Subscribe", id = 17, }`valeur ambiante et des valeurs explicites : :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

La méthode précédente revient`/Home/Subscribe/17`

Le code suivant `WidgetController` `/Widget/Subscribe/17`dans les retours :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Le code suivant fournit au contrôleur des valeurs ambiantes dans la demande actuelle et des valeurs explicites : `{ action = "Edit", id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

Dans le code précédent :

* `/Gadget/Edit/17`est retourné.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url>obtient <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>le .
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
génère une URL avec un chemin absolu pour une méthode d’action. L’URL contient `action` le `route` nom et les valeurs spécifiés.

Le code suivant fournit des valeurs ambiantes à partir de la demande actuelle et des valeurs explicites : `{ page = "./Edit, id = 17, }`

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Le code `url` précédent `/Edit/17` se fixe à l’époque où la page De rasoir Edit contient la directive de page suivante :

 `@page "{id:int}"`

Si la page Edit ne `"{id:int}"` contient `url` pas `/Edit?id=17`le modèle d’itinéraire, est .

Le comportement de MVC <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> ajoute une couche de complexité en plus des règles décrites ici:

* `IUrlHelper`fournit toujours les valeurs d’itinéraire de la demande actuelle en tant que valeurs ambiantes.
* [IUrlHelper.Action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) copie toujours `action` `controller` les valeurs actuelles et d’itinéraire comme des valeurs explicites à moins d’être dépassée par le développeur.
* [IUrlHelper.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) copie toujours `page` la valeur actuelle de l’itinéraire comme une valeur explicite à moins d’être annulée. <!--by the user-->
* `IUrlHelper.Page`remplace toujours la `handler` valeur actuelle `null` de l’itinéraire par des valeurs explicites, sauf fois dépassée.

Les utilisateurs sont souvent surpris par les détails comportementaux des valeurs ambiantes, parce que MVC ne semble pas suivre ses propres règles. Pour des raisons historiques et de compatibilité, certaines valeurs d’itinéraire `action`telles que `controller`, , `page`, et `handler` ont leur propre comportement de cas spécial.

La fonctionnalité équivalente `LinkGenerator.GetPathByAction` `LinkGenerator.GetPathByPage` fournie par et `IUrlHelper` double ces anomalies de compatibilité.

### <a name="url-generation-process"></a>Processus de génération d’URL

Une fois l’ensemble des paramètres des candidats trouvés, l’algorithme de génération d’URL :

* Traite les points de terminaison itérativement.
* Retourne le premier résultat réussi.

La première étape de ce processus est appelée **invalidation de la valeur d’itinéraire**.  L’invalidation de la valeur de l’itinéraire est le processus par lequel le routage décide quelles valeurs d’itinéraire des valeurs ambiantes doivent être utilisées et lesquelles doivent être ignorées. Chaque valeur ambiante est considérée et soit combinée avec les valeurs explicites, ou ignorée.

La meilleure façon de penser au rôle des valeurs ambiantes est qu’ils tentent d’enregistrer les développeurs d’applications tapant, dans certains cas communs. Traditionnellement, les scénarios où les valeurs ambiantes sont utiles sont liés à MVC :

* Lorsque vous vous connectez à une autre action dans le même contrôleur, le nom du contrôleur n’a pas besoin d’être spécifié.
* Lorsque vous vous connectez à un autre contrôleur dans la même zone, le nom de la zone n’a pas besoin d’être spécifié.
* Lorsque vous vous connectez à la même méthode d’action, les valeurs d’itinéraire n’ont pas besoin d’être spécifiées.
* Lorsque vous vous connectez à une autre partie de l’application, vous ne souhaitez pas reporter les valeurs d’itinéraire qui n’ont aucun sens dans cette partie de l’application.

Les `LinkGenerator` appels `IUrlHelper` à `null` ou ce retour sont généralement causés par le fait de ne pas comprendre l’invalidation de la valeur de l’itinéraire. Invalidation de la valeur de l’itinéraire de dépannage en spécifiant explicitement plus de valeurs de l’itinéraire pour voir si cela résout le problème.

L’invalidation de la valeur d’itinéraire fonctionne en supposant que le système d’URL de l’application est hiérarchique, avec une hiérarchie formée de gauche à droite. Considérez le modèle `{controller}/{action}/{id?}` d’itinéraire de base du contrôleur pour avoir une idée intuitive de la façon dont cela fonctionne dans la pratique. Une **modification** de la valeur **invalide** toutes les valeurs de l’itinéraire qui apparaissent à droite. Cela reflète l’hypothèse de la hiérarchie. Si l’application a `id`une valeur ambiante pour , `controller`et l’opération spécifie une valeur différente pour le :

* `id`ne sera pas réutilisé parce que c’est `{controller}` à gauche de `{id?}`.

Quelques exemples démontrant ce principe :

* Si les valeurs explicites `id`contiennent une `id` valeur pour , la valeur ambiante pour est ignorée. Les valeurs `controller` ambiantes pour et `action` peuvent être utilisées.
* Si les valeurs explicites `action`contiennent une `action` valeur pour , toute valeur ambiante pour est ignorée. Les valeurs `controller` ambiantes pour peuvent être utilisées. Si la valeur `action` explicite pour est `action`différente `id` de la valeur ambiante pour, la valeur ne sera pas utilisée.  Si la valeur `action` explicite pour est la `action`même `id` que la valeur ambiante pour , la valeur peut être utilisée.
* Si les valeurs explicites `controller`contiennent une `controller` valeur pour , toute valeur ambiante pour est ignorée. Si la valeur `controller` explicite pour est `controller`différente `action` de `id` la valeur ambiante pour, le et les valeurs ne seront pas utilisés. Si la valeur `controller` explicite pour est la `controller`même `action` `id` que la valeur ambiante pour, le et les valeurs peuvent être utilisés.

Ce processus est encore compliqué par l’existence d’itinéraires d’attributs et d’itinéraires conventionnels dédiés. Contrôlez les `{controller}/{action}/{id?}` itinéraires conventionnels tels que spécifier une hiérarchie à l’aide de paramètres d’itinéraire. Pour [les itinéraires conventionnels dédiés](xref:mvc/controllers/routing#dcr) et [les itinéraires d’attribution](xref:mvc/controllers/routing#ar) aux contrôleurs et aux pages Razor :

* Il y a une hiérarchie des valeurs de route.
* Ils n’apparaissent pas dans le modèle.

Pour ces cas, la génération d’URL définit le concept **de valeurs requise.** Les points de terminaison créés par les contrôleurs et les pages Razor ont exigé des valeurs spécifiées qui permettent l’invalidation de la valeur d’itinéraire au travail.

L’algorithme d’invalidation de la valeur d’itinéraire en détail :

* Les noms de valeur requis sont combinés avec les paramètres de l’itinéraire, puis traités de gauche à droite.
* Pour chaque paramètre, la valeur ambiante et la valeur explicite sont comparées :
    * Si la valeur ambiante et la valeur explicite sont les mêmes, le processus se poursuit.
    * Si la valeur ambiante est présente et que la valeur explicite ne l’est pas, la valeur ambiante est utilisée lors de la génération de l’URL.
    * Si la valeur ambiante n’est pas présente et que la valeur explicite est, rejetez la valeur ambiante et toutes les valeurs ambiantes ultérieures.
    * Si la valeur ambiante et la valeur explicite sont présentes, et que les deux valeurs sont différentes, rejetez la valeur ambiante et toutes les valeurs ambiantes ultérieures.

À ce stade, l’opération de génération d’URL est prête à évaluer les contraintes d’itinéraire. L’ensemble des valeurs acceptées est combiné avec les valeurs par défaut de paramètre, qui sont fournies aux contraintes. Si les contraintes passent toutes, l’opération continue.

Ensuite, les **valeurs acceptées** peuvent être utilisées pour élargir le modèle d’itinéraire. Le modèle d’itinéraire est traité :

* De gauche à droite.
* Chaque paramètre a sa valeur acceptée substituée.
* Avec les cas spéciaux suivants :
  * Si les valeurs acceptées manquent une valeur et que le paramètre a une valeur par défaut, la valeur par défaut est utilisée.
  * Si les valeurs acceptées manquent une valeur et que le paramètre est facultatif, le traitement se poursuit.
  * Si un paramètre d’itinéraire vers la droite d’un paramètre optionnel manquant a une valeur, l’opération échoue.
  * <!-- review default-valued parameters optional parameters --> Des paramètres contigus de valeur par défaut et des paramètres facultatifs sont effondrés dans la mesure du possible.

Des valeurs explicitement fournies qui ne correspondent pas à un segment de l’itinéraire sont ajoutées à la chaîne de requête. Le tableau suivant présente le résultat en cas d’utilisation du modèle de routage `{controller}/{action}/{id?}`.

| Valeurs ambiantes                     | Valeurs explicites                        | Résultats                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Problèmes d’invalidation de la valeur de l’itinéraire

En ce qui concerne ASP.NET Core 3.0, certains systèmes de génération d’URL utilisés dans les versions plus tôt ASP.NET Core ne fonctionnent pas bien avec la génération d’URL. L’équipe ASP.NET Core prévoit d’ajouter des fonctionnalités pour répondre à ces besoins dans une version future. Pour l’instant, la meilleure solution est d’utiliser le routage hérité.

Le code suivant montre un exemple d’un système de génération d’URL qui n’est pas pris en charge par le routage.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

Dans le code `culture` précédent, le paramètre d’itinéraire est utilisé pour la localisation. Le désir est `culture` d’avoir le paramètre toujours accepté comme une valeur ambiante. Toutefois, `culture` le paramètre n’est pas accepté comme valeur ambiante en raison du fonctionnement des valeurs requises :

* Dans `"default"` le modèle `culture` d’itinéraire, le `controller`paramètre d’itinéraire est à gauche de , ainsi les changements à `controller` ne sera pas invalider `culture`.
* Dans `"blog"` le modèle `culture` d’itinéraire, le paramètre `controller`d’itinéraire est considéré comme étant à droite de , qui apparaît dans les valeurs requises.

## <a name="configuring-endpoint-metadata"></a>Configurer les métadonnées de point de terminaison

Les liens suivants fournissent des informations sur la configuration des métadonnées de point de terminaison :

* [Activez Les Cors avec le routage de point de terminaison](xref:security/cors#enable-cors-with-endpoint-routing)
* [IAuthorizationPolicyProvider échantillon](https://github.com/dotnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider) à `[MinimumAgeAuthorize]` l’aide d’un attribut personnalisé
* [Testez l’authentification avec l’attribut [Autoriser]](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Sélection du régime avec l’attribut [Autoriser]](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Appliquer des politiques à l’aide de l’attribut [Autoriser]](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Hôte correspondant dans les itinéraires avec RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*>applique une contrainte à l’itinéraire qui nécessite l’hôte spécifié. Le `RequireHost` paramètre [ou [Hôte]](xref:Microsoft.AspNetCore.Routing.HostAttribute) peut être :

* Animateur: `www.domain.com`, `www.domain.com` correspond avec n’importe quel port.
* Hôte avec `*.domain.com`wildcard: `www.domain.com` `subdomain.domain.com`, `www.subdomain.domain.com` allumettes , , ou sur n’importe quel port.
* Port: `*:5000`, correspond port 5000 avec n’importe quel hôte.
* Hôte et `www.domain.com:5000` port: ou `*.domain.com:5000`, allume l’hôte et le port.

Plusieurs paramètres peuvent être `RequireHost` `[Host]`spécifiés à l’aide ou . La contrainte correspond aux hôtes valables pour l’un des paramètres. Par `[Host("domain.com", "*.domain.com")]` exemple, `domain.com` `www.domain.com`les `subdomain.domain.com`allumettes , , et .

Le code `RequireHost` suivant utilise pour exiger l’hôte spécifié sur l’itinéraire :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Le code suivant `[Host]` utilise l’attribut sur le contrôleur pour exiger l’un des hôtes spécifiés :

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Lorsque `[Host]` l’attribut est appliqué à la fois au contrôleur et à la méthode d’action :

* L’attribut sur l’action est utilisé.
* L’attribut du contrôleur est ignoré.

## <a name="performance-guidance-for-routing"></a>Orientation de performance pour le routage

La majeure partie du routage a été mise à jour dans ASP.NET Core 3.0 afin d’augmenter les performances.

Lorsqu’une application a des problèmes de performances, le routage est souvent suspecté comme le problème. La raison pour laquelle l’itinéraire est suspecté est que les cadres comme les contrôleurs et les pages Razor rapportent le temps passé à l’intérieur du cadre dans leurs messages d’enregistrement. Lorsqu’il y a une différence significative entre le temps rapporté par les contrôleurs et le temps total de la demande :

* Les développeurs éliminent leur code d’application comme source du problème.
* Il est courant de supposer que le routage en est la cause.

Routing est testé en fonction de milliers de points de terminaison. Il est peu probable qu’une application typique rencontre un problème de performances simplement en étant trop grand. La cause la plus commune de la performance de routage lent est généralement un middleware personnalisé mal-comportement.

Cet exemple de code suivant démontre une technique de base pour réduire la source de retard :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Au moment de l’acheminement:

* Interlendre chaque middleware avec une copie du middleware de synchronisation indiqué dans le code précédent.
* Ajoutez un identifiant unique pour corréler les données de synchronisation avec le code.

C’est une façon fondamentale de réduire le délai quand il `10ms`est significatif, par exemple, plus de .  `Time 2` Soustrayant `Time 1` des rapports le `UseRouting` temps passé à l’intérieur du middleware.

Le code suivant utilise une approche plus compacte du code de chronométrage précédent :

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Caractéristiques de routage potentiellement coûteuses

La liste suivante donne un aperçu des fonctionnalités de routage qui sont relativement coûteuses par rapport aux modèles d’itinéraire de base :

* Expressions régulières : Il est possible d’écrire des expressions régulières qui sont complexes ou qui ont beaucoup de temps avec une petite quantité d’entrée.

* Segments complexes (`{x}-{y}-{z}`): 
  * Sont beaucoup plus chers que l’analyse d’un segment de trajectoire URL régulière.
  * Faire en sorte qu’on lui attribue de nombreux autres sous-cordes.
  * La logique du segment complexe n’a pas été mise à jour dans ASP.NET mise à jour des performances de routage Core 3.0.

* Accès aux données synchrones : De nombreuses applications complexes ont accès à une base de données dans le cadre de leur itinéraire. ASP.NET Core 2.2 et un itinéraire plus tôt pourraient ne pas fournir les bons points d’extensibility pour prendre en charge le routage d’accès à la base de données. Par exemple, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> , et sont synchrones. Extésensibilité <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> points <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> tels que et sont asynchrones.

## <a name="guidance-for-library-authors"></a>Orientation pour les auteurs de bibliothèques

Cette section contient des conseils pour les auteurs de bibliothèques s’appuyant sur le routage. Ces détails visent à s’assurer que les développeurs d’applications ont une bonne expérience en utilisant les bibliothèques et les cadres qui prolongent le routage.

### <a name="define-endpoints"></a>Définir les points de terminaison

Pour créer un cadre qui utilise le routage pour l’appariement <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>des URL, commencez par définir une expérience utilisateur qui s’appuie sur le dessus de .

**NE** construire sur <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>le dessus de . Cela permet aux utilisateurs de composer votre cadre avec d’autres fonctionnalités ASP.NET Core sans confusion. Chaque ASP.NET modèle Core comprend le routage. Supposons que le routage est présent et familier pour les utilisateurs.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**NE** retourner un type de béton `MapMyFramework(...)` scellé <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder>à partir d’un appel à cet implément. La `Map...` plupart des méthodes-cadres suivent ce modèle. L’interface: `IEndpointConventionBuilder`

* Permet la composition des métadonnées.
* Est ciblé par une variété de méthodes d’extension.

Déclarer votre propre type vous permet d’ajouter votre propre fonctionnalité propre cadre au constructeur. Il est acceptable d’envelopper un constructeur déclaré par le cadre et d’y envoyer des appels.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**CONSIDER** écrire <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>votre propre . `EndpointDataSource`est le primitif de bas niveau pour déclarer et mettre à jour une collection de points de terminaison. `EndpointDataSource`est une API puissante utilisée par les contrôleurs et les pages Razor.

Les tests de routage sont un exemple de [base](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) d’une source de données non actualisante.

**NE PAS** tenter `EndpointDataSource` d’enregistrer un par défaut. Demandez aux utilisateurs d’enregistrer votre cadre dans <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>. La philosophie du routage est que rien `UseEndpoints` n’est inclus par défaut, et c’est l’endroit pour enregistrer les points de terminaison.

### <a name="creating-routing-integrated-middleware"></a>Création de middleware intégré au routage

**CONSIDER** définissant les types de métadonnées comme interface.

**Ne permettent** d’utiliser les types de métadonnées comme un attribut sur les classes et les méthodes.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Des cadres comme les contrôleurs et les pages Razor prennent en charge l’application d’attributs de métadonnées aux types et méthodes. Si vous déclarez les types de métadonnées :

* Les rendre accessibles comme [attributs](/dotnet/csharp/programming-guide/concepts/attributes/).
* La plupart des utilisateurs sont familiers avec l’application d’attributs.

Déclarer un type de métadonnées comme interface ajoute une autre couche de flexibilité :

* Les interfaces sont composables.
* Les développeurs peuvent déclarer leurs propres types qui combinent plusieurs stratégies.

**NE** permettent de remplacer les métadonnées, comme le montre l’exemple suivant :

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

La meilleure façon de suivre ces lignes directrices est d’éviter de définir des **métadonnées marqueurs**:

* Ne cherchez pas seulement la présence d’un type de métadonnées.
* Définissez une propriété sur les métadonnées et vérifiez la propriété.

La collecte des métadonnées est ordonnée et soutient la priorité. Dans le cas des contrôleurs, les métadonnées sur la méthode d’action sont les plus spécifiques.

**Ne** faire middleware utile avec et sans routage.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequrireAuthorization();
});
```

À titre d’exemple de `UseAuthorization` cette ligne directrice, considérez le middleware. L’autorisation middleware vous permet de passer dans une politique de repli. <!-- shown where?  (shown here) --> La politique de repli, si elle est spécifiée, s’applique aux deux :

* Points de terminaison sans stratégie spécifiée.
* Demandes qui ne correspondent pas à un critère d’évaluation.

Cela rend l’autorisation middleware utile en dehors du contexte de routage. Le middleware d’autorisation peut être utilisé pour la programmation middleware traditionnelle.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Routing est responsable de la cartographie des URL de demande aux points de terminaison et de l’envoi des demandes entrantes à ces points de terminaison. Les routes sont définies dans l’application et configurées au démarrage de l’application. Une route peut éventuellement extraire des valeurs de l’URL contenue dans la requête, et ces valeurs peuvent ensuite être utilisées pour le traitement de la requête. En utilisant les informations d’itinéraire de l’application, le routage est également en mesure de générer des URL qui cartographient jusqu’aux points de terminaison.

Pour utiliser les scénarios de routage les plus récents dans ASP.NET Core 2.2, spécifiez la [version de compatibilité](xref:mvc/compatibility-version) à l’inscription des services MVC dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

L’option <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> détermine si le routage doit utiliser en interne une logique basée sur les points de terminaison ou la logique basée sur <xref:Microsoft.AspNetCore.Routing.IRouter> d’ASP.NET Core 2.1 ou antérieur. Quand la version de compatibilité est définie sur 2.2 ou ultérieur, la valeur par défaut est `true`. Définissez la valeur sur `false` pour utiliser la logique de routage antérieure :

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Pour plus d’informations sur le routage basé sur <xref:Microsoft.AspNetCore.Routing.IRouter>, consultez la [version ASP.NET Core 2.1 de cette rubrique](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1).

> [!IMPORTANT]
> Ce document traite du routage ASP.NET Core de bas niveau. Pour plus d’informations sur le routage ASP.NET Core MVC, consultez <xref:mvc/controllers/routing>. Pour plus d’informations sur les conventions de routage dans Razor Pages, consultez <xref:razor-pages/razor-pages-conventions>.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Concepts de base du routage

La plupart des applications doivent choisir un schéma de routage de base et descriptif pour que les URL soient lisibles et explicites. La route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` :

* Prend en charge un schéma de routage de base et descriptif.
* Est un point de départ pratique pour les applications basées sur une interface utilisateur.

Les développeurs ajoutent généralement des itinéraires laconiques supplémentaires aux zones à fort trafic d’une application dans des situations spécialisées à l’aide [d’un itinéraire d’attributs](xref:mvc/controllers/routing#attribute-routing) ou d’itinéraires conventionnels dédiés. Les exemples de situations spécialisées incluent, les points de terminaison de blog et de commerce électronique.

Les API web doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources dans lequel les opérations sont représentées par des verbes HTTP. Cela signifie que de nombreuses opérations, par exemple, GET, et POST, sur la même ressource logique utilisent la même URL. Le routage d’attributs fournit le niveau de contrôle nécessaire pour concevoir avec soin la disposition des points de terminaison publics d’une API.

Les applications Razor Pages utilisent le routage conventionnel par défaut pour délivrer des ressources nommées dans le dossier *Pages* d’une application. Des conventions supplémentaires sont disponibles : elles vous permettent de personnaliser le comportement de routage de Razor Pages. Pour plus d’informations, consultez <xref:razor-pages/index> et <xref:razor-pages/razor-pages-conventions>.

La prise en charge de la génération d’URL permet de développer l’application sans coder en dur les URL pour lier l’application. Cette prise en charge permet de commencer avec une configuration de routage de base, puis de modifier les routes une fois que la disposition des ressources de l’application est déterminée.

Routing *endpoints* utilise des`Endpoint`paramètres () pour représenter les paramètres logiques d’une application.

Un point de terminaison définit un délégué pour traiter les requêtes et une collection de métadonnées arbitraires. Les métadonnées sont utilisées pour implémenter des problèmes transversaux basés sur des stratégies et une configuration attachées à chaque point de terminaison.

Le système de routage a les caractéristiques suivantes :

* Une syntaxe des modèles de route est utilisée pour définir des routes avec des paramètres de route tokenisés.
* La configuration de points de terminaison de style conventionnel et de style « attribut » est autorisée.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> est utilisé pour déterminer si un paramètre d’URL contient une valeur valide pour une contrainte de point de terminaison donné.
* Les modèles d’application, comme MVC/Razor Pages, inscrivent tous leurs points de terminaison, qui ont une implémentation prévisible de scénarios de routage.
* L’implémentation du routage prend des décisions de routage partout où vous le souhaitez dans le pipeline de middleware (intergiciel).
* Le middleware qui apparaît après un middleware de routage peut inspecter le résultat de la décision du middleware de routage quant au point de terminaison d’un URI de requête donné.
* Il est possible d’énumérer tous les points de terminaison dans l’application n’importe où dans le pipeline de middleware.
* Une application peut utiliser le routage pour générer des URL (par exemple pour la redirection ou pour des liens) en fonction des informations des points de terminaison et éviter ainsi les URL codées en dur, ce qui facilite la maintenance.
* La génération d’URL est basée sur des adresses, qui prennent en charge l’extensibilité arbitraire :

  * L’API du générateur de liens (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) peut être résolue partout avec [l’injection de dépendances](xref:fundamentals/dependency-injection) pour générer des URL.
  * Quand l’API du générateur de liens n’est pas disponible via l’injection de dépendances, <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offre des méthodes pour générer des URL.

> [!NOTE]
> Avec la publication du routage des points de terminaison dans ASP.NET Core 2.2, la liaison de point de terminaison est limitée aux actions et aux pages MVC/Razor Pages. Les expansions des fonctionnalités de liaison de point de terminaison sont prévues pour les versions futures.

Le routage est connecté au pipeline de [l’intergiciel (middleware)](xref:fundamentals/middleware/index) par la classe <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>. [ASP.NET Core MVC](xref:mvc/overview) ajoute le routage au pipeline de middleware dans le cadre de sa configuration, et gère le routage dans les applications MVC et Razor Pages. Pour découvrir comment utiliser le routage en tant que composant autonome, consultez la section [Utiliser le middleware de routage](#use-routing-middleware).

### <a name="url-matching"></a>Correspondance d’URL

La correspondance d’URL est le processus par lequel le routage distribue une requête entrante à un *point de terminaison*. Ce processus est basé sur des données présentes dans le chemin de l’URL, mais il peut être étendu pour prendre en compte toutes les données de la requête. La possibilité de distribuer des requêtes à des gestionnaires distincts est essentielle pour adapter la taille et la complexité d’une application.

Le système de routage dans le routage de point de terminaison est responsable de toutes les décisions de distribution. Comme le middleware applique des stratégies basées sur le point de terminaison sélectionné, il est important que les décisions susceptibles d’affecter la distribution ou l’application des stratégies de sécurité soient prises au sein du système de routage.

Quand le délégué du point de terminaison est exécuté, les propriétés de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) sont définies sur des valeurs appropriées en fonction du traitement des requêtes effectué jusqu’à présent.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) est un dictionnaire de *valeurs de route* produites à partir de la route. Ces valeurs sont généralement déterminées en décomposant l’URL en jetons. Elles peuvent être utilisées pour accepter l’entrée d’utilisateur ou pour prendre d’autres décisions relatives à la distribution à l’intérieur de l’application.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) est un conteneur de propriétés des données supplémentaires associées à la route mise en correspondance. Les <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> sont fournis pour prendre en charge l’association de données d’état à chaque route, de façon que l’application puisse prendre des décisions en fonction de la route avec laquelle la correspondance a été établie. Ces valeurs sont définies par le développeur et n’affectent **pas** le comportement du routage de quelque manière que ce soit. De plus, les valeurs dissimulées dans [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) peuvent être de n’importe quel type, contrairement aux valeurs [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), qui doivent être convertibles en chaînes et à partir de chaînes.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) est une liste des routes qui ont participé à la mise en correspondance correcte de la requête. Les routes peuvent être imbriquées les unes dans les autres. La propriété <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> reflète le chemin à travers l’arborescence logique des routes qui ont généré une correspondance. En général le premier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est la collection de routes et il doit être utilisé pour la génération d’URL. Le dernier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est le gestionnaire de routage avec lequel la correspondance a été établie.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>Génération d’URL avec LinkGenerator

La génération d’URL est le processus par lequel le routage peut créer un chemin d’URL basé sur un ensemble de valeurs de route. Ceci permet une séparation logique entre vos points de terminaison et les URL qui y accèdent.

Le routage des points de terminaison inclut l’API de générateur de liens (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator>est un service singleton qui peut être récupéré à partir de [DI](xref:fundamentals/dependency-injection). L’API peut être utilisée en dehors du contexte d’une requête en cours d’exécution. Le <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> de MVC et les scénarios qui s’appuient sur <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>, comme les [Tag Helpers](xref:mvc/views/tag-helpers/intro), les helpers HTML et les [résultats d’action](xref:mvc/controllers/actions), utilisent le générateur de liens pour fournir les fonctionnalités de création de liens.

Le générateur de liens est basé sur le concept d’une *adresse* et de *schémas d’adresse*. Un schéma d’adresse est un moyen de déterminer les points de terminaison à prendre en compte pour la génération de liens. Par exemple, les scénarios de nom de route et de valeurs de route que connaissent bien nombre d’utilisateurs dans les MVC/Razor Pages sont implémentés en tant que schémas d’adresse.

Le générateur de liens peut lier à des actions et à des pages MVC/Razor Pages via les méthodes d’extension suivantes :

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Une surcharge de ces méthodes accepte des arguments qui incluent le `HttpContext`. Ces méthodes sont fonctionnellement équivalentes à `Url.Action` et à `Url.Page`, mais elles offrent davantage de flexibilité et d’options.

Les méthodes `GetPath*` sont les plus proches de `Url.Action` et de `Url.Page` en ce qu’elles génèrent un URI contenant un chemin absolu. Les méthodes `GetUri*` génèrent toujours un URI absolu contenant un schéma et un hôte. Les méthodes qui acceptent un `HttpContext` génèrent un URI dans le contexte de la requête en cours d’exécution. Les valeurs de route ambiante, le chemin de base d’URL, le schéma et l’hôte de la requête en cours d’exécution sont utilisés, sauf s’ils sont remplacés.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> est appelé avec une adresse. La génération d’un URI se fait en deux étapes :

1. Une adresse est liée à une liste de points de terminaison qui correspondent à l’adresse.
1. Le `RoutePattern` de chaque point de terminaison est évalué jusqu’à ce qu’un modèle de route correspondant aux valeurs fournies soit trouvé. Le résultat obtenu est combiné avec d’autres parties de l’URI fournies par le générateur de liens, puis il est retourné.

Les méthodes fournies par <xref:Microsoft.AspNetCore.Routing.LinkGenerator> prennent en charge des fonctionnalités de génération de liens standard pour n’importe quel type d’adresse. La façon la plus pratique d’utiliser le générateur de liens est de le faire via des méthodes d’extension qui effectuent des opérations pour un type d’adresse spécifique.

| Méthode d'extension   | Description                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Génère un URI avec un chemin absolu basé sur les valeurs fournies. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Génère un URI absolu basé sur les valeurs fournies.             |

> [!WARNING]
> Faites attention aux implications suivantes de l’appel de méthodes <xref:Microsoft.AspNetCore.Routing.LinkGenerator> :
>
> * Utilisez les méthodes d’extension `GetUri*` avec précaution dans une configuration d’application qui ne valide pas l’en-tête `Host` des requêtes entrantes. Si l’en-tête `Host` des requêtes entrantes n’est pas validé, l’entrée de requête non approuvée peut être renvoyée au client dans les URI d’une page/vue. Nous recommandons que toutes les applications de production configurent leur serveur pour qu’il valide l’en-tête `Host` par rapport à des valeurs valides connues.
>
> * Utilisez <xref:Microsoft.AspNetCore.Routing.LinkGenerator> avec précaution dans le middleware en combinaison avec `Map` ou `MapWhen`. `Map*` modifie le chemin de base de la requête en cours d’exécution, ce qui affecte la sortie de la génération de liens. Toutes les API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> permettent la spécification d’un chemin de base. Spécifiez toujours un chemin de base vide pour annuler l’effet de `Map*` sur la génération de liens.

## <a name="differences-from-earlier-versions-of-routing"></a>Différences par rapport aux versions précédentes du routage

Il existe quelques différences entre le routage de points de terminaison d’ASP.NET Core 2.2 ou ultérieur et celui des versions antérieures d’ASP.NET Core :

* Le système de routage de points de terminaison ne prend pas en charge l’extensibilité basée sur <xref:Microsoft.AspNetCore.Routing.IRouter>, notamment l’héritage de <xref:Microsoft.AspNetCore.Routing.Route>.

* Le routage de points de terminaison ne prend pas en charge [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim). Utilisez la version de [compatibilité](xref:mvc/compatibility-version) `.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`2.1 ( ) pour continuer à utiliser le cale de compatibilité.

* Le routage de points de terminaison a un comportement différent pour la casse des URI générés lors de l’utilisation de routes conventionnelles.

  Considérez le modèle de route par défaut suivant :

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Supposez que vous générez un lien vers une action avec la route suivante :

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Avec le routage basé sur <xref:Microsoft.AspNetCore.Routing.IRouter>, ce code génère un URI `/blog/ReadPost/17`, qui respecte la casse de la valeur de la route fournie. Le routage de points de terminaison dans ASP.NET Core 2.2 ou ultérieur produit `/Blog/ReadPost/17` (« Blog » commence par une majuscule). Le routage de points de terminaison fournit l’interface `IOutboundParameterTransformer`, qui peut être utilisée pour personnaliser ce comportement de façon globale ou pour appliquer des conventions différentes pour le mappage d’URL.

  Pour plus d’informations, consultez la section [Informations de référence sur les transformateurs de paramètre](#parameter-transformer-reference).

* La génération de liens utilisée par MVC/Razor Pages avec des routes conventionnelles se comporte différemment quand vous tentez de lier à un contrôleur/action ou à une page qui n’existe pas.

  Considérez le modèle de route par défaut suivant :

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Supposez que vous générez un lien vers une action en utilisant le modèle par défaut avec ce qui suit :

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Avec le routage basé sur `IRouter`, le résultat est toujours `/Blog/ReadPost/17`, même si le `BlogController` n’existe pas ou n’a pas de méthode d’action `ReadPost`. Comme prévu, le routage de points de terminaison dans ASP.NET Core 2.2 ou ultérieur produit `/Blog/ReadPost/17` si la méthode d’action existe. *Cependant, le routage de points de terminaison produit une chaîne vide si l’action n’existe pas.* Sur le plan conceptuel, le routage de points de terminaison ne fait pas l’hypothèse que le point de terminaison existe si l’action n’existe pas.

* L’*algorithme d’invalidation de valeur ambiante* de la génération de liens se comporte différemment quand il est utilisé avec le routage de points de terminaison.

  L’*invalidation de valeur ambiante* est l’algorithme qui décide quelles valeurs de route provenant de la requête en cours d’exécution (les valeurs ambiantes) peuvent être utilisées dans les opérations de génération de liens. Le routage conventionnel a toujours invalidé les valeurs de route supplémentaires lors de la liaison à une action différente. Le routage d’attributs n’a pas ce comportement avant la publication d’ASP.NET Core 2.2. Dans les versions antérieures d’ASP.NET Core, les liens vers une autre action utilisant les mêmes noms de paramètre de route provoquaient des erreurs de génération de liens. Dans ASP.NET Core 2.2 ou ultérieur, les deux formes de routage invalident les valeurs lors de la liaison vers une autre action.

  Considérez l’exemple suivant dans ASP.NET Core 2.1 ou antérieur. Lors de la liaison à une autre action (ou une autre page), les valeurs de route peuvent être réutilisées de façon non souhaitée.

  Dans */Pages/Store/Product.cshtml* :

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  Dans */Pages/Login.cshtml* :

  ```cshtml
  @page "{id?}"
  ```

  Si l’URI est `/Store/Product/18` dans ASP.NET Core 2.1 ou antérieur, le lien généré sur la page Store/Info par `@Url.Page("/Login")` est `/Login/18`. La valeur 18 pour `id` est réutilisée, même si la destination du lien est une partie entièrement différente de l’application. La valeur de route pour `id` dans le contexte de la page `/Login` est probablement une valeur d’ID utilisateur, et non pas une valeur d’ID de produit de magasin.

  Dans le routage de points de terminaison avec ASP.NET Core 2.2 ou ultérieur, le résultat est `/Login`. Les valeurs ambiantes ne sont pas réutilisées quand la destination liée est une action ou une page différente.

* Syntaxe des paramètres de route avec aller-retour : les barres obliques ne sont pas encodées lors de l’utilisation d’une syntaxe de paramètre passe-partout avec double astérisque (`**`).

  Pendant la génération de liens, le système de routage encode la valeur capturée dans un paramètre passe-partout avec double astérisque (`**`) (par exemple `{**myparametername}`) sans les barres obliques. Le passe-partout avec double astérisque est pris en charge avec le routage basé sur `IRouter` dans ASP.NET Core 2.2 ou ultérieur.

  La syntaxe de paramètre passe-partout avec un seul astérisque dans les versions antérieures d’ASP.NET Core (`{*myparametername}`) reste prise en charge, et les barres obliques sont encodées.

  | Routage              | Lien généré avec<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (la barre oblique est encodée)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Exemple de middleware

Dans l’exemple suivant, un middleware utilise l’API <xref:Microsoft.AspNetCore.Routing.LinkGenerator> pour créer un lien vers une méthode d’action qui liste les produits d’un magasin. L’utilisation du générateur de liens en l’injectant dans une classe et en appelant `GenerateLink` est disponible pour n’importe quelle classe dans une application.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Créer des itinéraires

La plupart des applications créent des routes en appelant <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ou l’une des méthodes d’extension similaires définies sur <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Toutes les méthodes d’extension de <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> créent une instance de <xref:Microsoft.AspNetCore.Routing.Route> et l’ajoutent à la collection de routes.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> n’accepte pas de paramètre de gestionnaire de routage. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ajoute uniquement des routes gérées par <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Pour plus d’informations sur le routage dans MVC, consultez <xref:mvc/controllers/routing>.

L’exemple de code suivant est un exemple d’appel à <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> utilisé par une définition de route ASP.NET Core MVC classique :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ce modèle établit une correspondance avec un chemin d’URL et extrait les valeurs de route . Par exemple, le chemin `/Products/Details/17` génère les valeurs de route suivantes : `{ controller = Products, action = Details, id = 17 }`.

Les valeurs de route sont déterminées en divisant le chemin d’URL en segments et en mettant en correspondance chaque segment avec le nom des *paramètres de routage* dans le modèle de routage. Les paramètres de routage sont nommés. Vous définissez des paramètres en plaçant leur nom entre des accolades `{ ... }`.

Le modèle précédent peut également mettre en correspondance le chemin d’URL `/` et produire les valeurs `{ controller = Home, action = Index }`. Cela s’explique par le fait que les paramètres de routage `{controller}` et `{action}` ont des valeurs par défaut et que le paramètre de routage `id` est facultatif. Un signe égal (`=`) suivi d’une valeur après le nom du paramètre de routage définit une valeur par défaut pour le paramètre. Un point d’interrogation (`?`) après le nom du paramètre de routage définit un paramètre facultatif.

Les paramètres de routage ayant une valeur par défaut produisent *toujours* une valeur de routage quand la route correspond. Les paramètres facultatifs ne produisent pas de valeur d’itinéraire s’il n’y a pas de segment de trajectoire d’URL correspondant. Pour obtenir une description complète des scénarios et de la syntaxe des modèles de routage, consultez la section [Informations de référence sur les modèles de routage](#route-template-reference).

Dans l’exemple suivant, la définition du paramètre de route `{id:int}` définit une [contrainte de route](#route-constraint-reference) pour le paramètre de route `id` :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ce modèle établit une correspondance avec un chemin d’URL comme `/Products/Details/17`, mais pas `/Products/Details/Apples`. Les contraintes de routage implémentent <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> et inspectent les valeurs de route pour les vérifier. Dans cet exemple, la valeur de route `id` doit être convertible en entier. Pour obtenir une explication des contraintes de route fournies par le framework, consultez [Informations de référence sur les contraintes de route](#route-constraint-reference).

Des surcharges supplémentaires de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> acceptent des values pour `constraints`, `dataTokens` et `defaults`. L’utilisation classique de ces paramètres consiste à passer un objet typé anonymement, où les noms des propriétés du type anonyme correspondent aux noms de paramètre de routage.

Les exemples de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> suivants créent des routes équivalentes :

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> La syntaxe inline pour la définition des contraintes et des valeurs par défaut peut être pratique pour les routes simples. Cependant, certains scénarios, comme les jetons de données, ne sont pas pris en charge par la syntaxe inline.

L’exemple suivant montre quelques autres scénarios :

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/Blog/All-About-Routing/Introduction` et extrait les valeurs `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Les valeurs de route par défaut pour `controller` et `action` sont produites par la route, même s’il n’existe aucun paramètre de routage correspondant dans le modèle. Il est possible de spécifier des valeurs par défaut dans le modèle de route. Le paramètre de route `article` est défini comme *passe-partout * par la présence d’un double astérisque (`**`) avant le nom du paramètre de route. Les paramètres de routage fourre-tout capturent le reste du chemin d’URL et peuvent également établir une correspondance avec la chaîne vide.

L’exemple suivant ajoute des contraintes de route et des jetons de données :

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/en-US/Products/5`, et extrait les valeurs `{ controller = Products, action = Details, id = 5 }` et les jetons de données `{ locale = en-US }`.

![Jetons Windows de variables locales](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Génération d’URL de classe de route

La classe <xref:Microsoft.AspNetCore.Routing.Route> peut également effectuer une génération d’URL en combinant un ensemble de valeurs de route et son modèle de routage. Il s’agit logiquement du processus inverse de la mise en correspondance du chemin d’URL.

> [!TIP]
> Pour mieux comprendre la génération d’URL, imaginez l’URL que vous voulez générer, puis pensez à la façon dont un modèle de routage établirait une correspondance avec cette URL. Quelles valeurs seraient produites ? Cela équivaut approximativement à la façon dont la génération d’URL fonctionne dans la classe <xref:Microsoft.AspNetCore.Routing.Route>.

L’exemple suivant utilise une route par défaut ASP.NET Core MVC générale :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Avec les valeurs de route `{ controller = Products, action = List }`, l’URL `/Products/List` est générée. Les valeurs de route remplacent les paramètres de routage correspondant pour former le chemin d’URL. Dans la mesure où `id` est un paramètre de route facultatif, l’URL est générée correctement sans valeur pour `id`.

Avec les valeurs de route `{ controller = Home, action = Index }`, l’URL `/` est générée. Les valeurs de route fournies correspondent aux valeurs par défaut, et les segments correspondant aux valeurs par défaut peuvent être omis sans risque.

Les deux URL générées effectuent un aller-retour avec la définition de route suivante (`/Home/Index` et `/`), et produisent les mêmes valeurs de route que celles utilisées pour générer l’URL.

> [!NOTE]
> Pour générer des URL, une application utilisant ASP.NET Core MVC doit utiliser <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> au lieu d’effectuer un appel directement dans le routage.

Pour plus d’informations sur la génération d’URL, consultez la section [Informations de référence sur la génération d’URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Utilisation du middleware de routage

Référencez le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) dans le fichier projet de l’application.

Ajoutez le routage au conteneur de service dans `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Les routes doivent être configurées dans la méthode `Startup.Configure`. L’exemple d’application utilise les API suivantes :

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Correspondances uniquement les demandes HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Le tableau suivant montre les réponses avec les URI donnés.

| URI                    | response                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Valeurs de route : [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/`      | La requête passe à travers ceci, aucune correspondance.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | La requête passe à travers ceci, correspondance seulement avec HTTP GET. |
| `GET /hello/Joe/Smith` | La requête passe à travers ceci, aucune correspondance.              |

Le framework fournit un ensemble de méthodes d’extension pour la création de routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) :

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Les méthodes `Map[Verb]` utilisent des contraintes pour limiter la route au verbe HTTP dans le nom de la méthode. Par exemple, consultez <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> et <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Informations de référence sur les modèles de routage

Les jetons placés entre accolades (`{ ... }`) définissent des *paramètres de routage* qui sont liés si une correspondance est trouvée pour la route. Vous pouvez définir plusieurs paramètres de routage dans un segment de route, mais ils doivent être séparés par une valeur littérale. Par exemple `{controller=Home}{action=Index}` n’est pas une route valide, car il n’y a aucune valeur littérale entre `{controller}` et `{action}`. Ces paramètres de routage doivent avoir un nom, et ils autorisent la spécification d’attributs supplémentaires.

Un texte littéral autre que les paramètres de routage (par exemple, `{id}`) et le séparateur de chemin `/` doit correspondre au texte présent dans l’URL. La correspondance de texte ne respecte pas la casse et est basée sur la représentation décodée du chemin des URL. Pour mettre en correspondance un délimiteur de paramètre de route littéral (`{` ou `}`), placez-le dans une séquence d’échappement en répétant le caractère (`{{` ou `}}`).

Les modèles d’URL qui tentent de capturer un nom de fichier avec une extension de fichier facultative doivent faire l’objet de considérations supplémentaires. Prenez par exemple le modèle `files/{filename}.{ext?}`. Quand des valeurs existent à la fois pour `filename` et pour `ext`, les deux valeurs sont renseignées. Si seule une valeur existe pour `filename` dans l’URL, une correspondance est trouvée pour la route, car le point final (`.`) est facultatif. Les URL suivantes correspondent à cette route :

* `/files/myFile.txt`
* `/files/myFile`

Vous pouvez utiliser un astérisque (`*`) ou un double astérisque (`**`) comme préfixe d’un paramètre de route à lier au reste de l’URI. Ils sont appelés des paramètres *passe-partout*. Par exemple, `blog/{**slug}` établit une correspondance avec n’importe quel URI commençant par `/blog` et suivi de n’importe quelle valeur, qui est affectée à la valeur de route `slug`. Les paramètres fourre-tout peuvent également établir une correspondance avec la chaîne vide.

Le paramètre fourre-tout place les caractères appropriés dans une séquence d’échappement lorsque la route est utilisée pour générer une URL, y compris les caractères de séparation de chemin (`/`). Par exemple, la route `foo/{*path}` avec les valeurs de route `{ path = "my/path" }` génère `foo/my%2Fpath`. Notez la barre oblique d’échappement. Pour les séparateurs de chemin aller-retour, utilisez le préfixe de paramètre de routage `**`. La route `foo/{**path}` avec `{ path = "my/path" }` génère `foo/my/path`.

Les paramètres de route peuvent avoir des *valeurs par défaut*, désignées en spécifiant la valeur par défaut après le nom du paramètre, séparée par un signe égal (`=`). Par exemple, `{controller=Home}` définit `Home` comme valeur par défaut de `controller`. La valeur par défaut est utilisée si aucune valeur n’est présente dans l’URL pour le paramètre. Vous pouvez rendre facultatifs les paramètres de route en ajoutant un point d’interrogation (`?`) à la fin du nom du paramètre, comme dans `id?`. La différence entre les valeurs facultatives et les paramètres de route par défaut est qu’un paramètre de route ayant une valeur par défaut produit toujours une valeur, tandis qu’un paramètre facultatif a une valeur seulement quand celle-ci est fournie par l’URL de requête.

Les paramètres de route peuvent avoir des contraintes, qui doivent correspondre à la valeur de route liée à partir de l’URL. L’ajout d’un signe deux-points (`:`) et d’un nom de contrainte après le nom du paramètre de routage spécifie une *contrainte inline* sur un paramètre de routage. Si la contrainte nécessite des arguments, ils sont fournis entre parenthèses (`(...)`) après le nom de la contrainte. Il est possible de spécifier plusieurs contraintes inline en ajoutant un autre signe deux-points (`:`) et le nom d’une autre contrainte.

Le nom de la contrainte et les arguments sont passés au service <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> pour créer une instance de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> à utiliser dans le traitement des URL. Par exemple, le modèle de routage `blog/{article:minlength(10)}` spécifie une contrainte `minlength` avec l’argument `10`. Pour plus d’informations sur les contraintes de route et pour obtenir la liste des contraintes fournies par le framework, consultez la section [Informations de référence sur les contraintes de route](#route-constraint-reference).

Les paramètres de route peuvent également contenir des transformateurs de paramètre, qui transforment une valeur de paramètre lors de la génération des liens et de la mise en correspondance des actions et des pages avec les URL. À l’instar des contraintes, les transformateurs de paramètre peuvent être ajoutés inline à un paramètre de routage en ajoutant un signe deux-points (`:`) et le nom du transformateur après le nom du paramètre de routage. Par exemple, le modèle de routage `blog/{article:slugify}` spécifie un transformateur `slugify`. Pour plus d’informations sur les transformateurs de paramètre, consultez la section [Informations de référence sur les transformateurs de paramètre](#parameter-transformer-reference).

Le tableau suivant montre des exemples de modèles de route et leur comportement.

| Modèle de routage                           | Exemple d’URI en correspondance    | URI de la requête&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Correspond seulement au chemin unique `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Correspond à `Page` et le définit sur `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Correspond à `Page` et le définit sur `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mappe au contrôleur `Products` et à l’action `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mappe au contrôleur `Products` et à l’action `Details` (`id` défini sur 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mappe au contrôleur `Home` et à la méthode `Index` (`id` est ignoré).        |

L’utilisation d’un modèle est généralement l’approche la plus simple pour le routage. Il est également possible de spécifier des contraintes et des valeurs par défaut hors du modèle de routage.

> [!TIP]
> Activez la [journalisation](xref:fundamentals/logging/index) pour voir comment les implémentations de routage intégrées, comme <xref:Microsoft.AspNetCore.Routing.Route>, établissent des correspondances avec les requêtes.

## <a name="reserved-routing-names"></a>Noms de routage réservés

Les mots clés suivants sont des noms réservés qui ne peuvent pas être utilisés comme paramètres ou noms de routage :

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Informations de référence sur les contraintes de routage

Les contraintes de route s’exécutent quand une correspondance s’est produite pour l’URL entrante, et le chemin de l’URL est tokenisé en valeurs de route. En général, les contraintes de routage inspectent la valeur de route associée par le biais du modèle de routage, et créent une décision oui/non indiquant si la valeur est, ou non, acceptable. Certaines contraintes de routage utilisent des données hors de la valeur de route pour déterminer si la requête peut être routée. Par exemple, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> peut accepter ou rejeter une requête en fonction de son verbe HTTP. Les contraintes sont utilisées dans le routage des requêtes et la génération des liens.

> [!WARNING]
> N’utilisez pas de contraintes pour la **validation des entrées**. Si des contraintes sont utilisées pour la **validation des entrées**, une entrée non valide génère une réponse *404 - Introuvable* au lieu d’une réponse *400 - Requête incorrecte* avec un message d’erreur approprié. Les contraintes de route sont utilisées pour **lever l’ambiguïté** entre des routes similaires, et non pas pour valider les entrées d’une route particulière.

Le tableau suivant montre des exemples de contrainte de route et leur comportement attendu.

| contrainte | Exemple | Exemples de correspondances | Notes |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Correspond à n’importe quel intégrant. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Matchs `true` ou 'faux. Insensible aux cas. |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Correspond à `DateTime` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Correspond à `decimal` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Correspond à `double` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Correspond à `float` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Correspond à `Guid` une valeur valide. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Correspond à `long` une valeur valide. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La chaîne doit être d’au moins 4 caractères. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | La chaîne a un maximum de 8 caractères. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La chaîne doit être exactement 12 caractères de long. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La chaîne doit être d’au moins 8 et a un maximum de 16 caractères. |
| `min(value)` | `{age:min(18)}` | `19` | La valeur de l’intégré doit être d’au moins 18. |
| `max(value)` | `{age:max(120)}` | `91` | Valeur d’un intégrant maximale de 120. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | La valeur de l’intégré doit être d’au moins 18 et maximale de 120. |
| `alpha` | `{name:alpha}` | `Rick` | La chaîne doit se composer `a` - `z`d’un ou plusieurs caractères alphabétiques.  Insensible aux cas. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La ficelle doit correspondre à l’expression régulière. Voir des conseils sur la définition d’une expression régulière. |
| `required` | `{name:required}` | `Rick` | Utilisé pour faire respecter la présence d’une valeur non-paramètre pendant la génération d’URL. |

Il est possible d’appliquer plusieurs contraintes séparées par un point-virgule à un même paramètre. Par exemple, la contrainte suivante limite un paramètre à une valeur entière supérieure ou égale à 1 :

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant. ces contraintes partent du principe que l’URL n’est pas localisable. Les contraintes de routage fournies par le framework ne modifient pas les valeurs stockées dans les valeurs de route. Toutes les valeurs de route analysées à partir de l’URL sont stockées sous forme de chaînes. Par exemple, la contrainte `float` tente de convertir la valeur de route en valeur float, mais la valeur convertie est utilisée uniquement pour vérifier qu’elle peut être convertie en valeur float.

## <a name="regular-expressions"></a>Expressions régulières

Le framework ASP.NET Core ajoute `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` au constructeur d’expression régulière. Pour obtenir une description de ces membres, consultez <xref:System.Text.RegularExpressions.RegexOptions>.

Les expressions régulières utilisent des dlimiteurs et des jetons similaires à ceux utilisés par le routage et la langue C. Les jetons d’expression régulière doivent être placés dans une séquence d’échappement. Pour utiliser l’expression `^\d{3}-\d{2}-\d{4}$` régulière dans le routage :

* L’expression doit avoir les `\` caractères de barre oblique `\\` inverse unique fournis dans la chaîne comme doubles caractères de barre oblique inverse dans le code source.
* L’expression régulière `\\` doit nous `\` permettre d’échapper au caractère d’évasion de cordes.
* L’expression régulière ne `\\` nécessite pas lors de l’utilisation [de la chaîne textuelle littérale](/dotnet/csharp/language-reference/keywords/string).

Pour échapper à l’itinéraire `{` `}`paramètre delimiter caractères `{{` `}`, `[[` `]]`, `[`, `]`, le double des personnages dans l’expression , , , . Le tableau suivant montre une expression régulière et la version échappée:

| Expression régulière    | Expression régulière en échappement     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Les expressions régulières utilisées dans le `^` routage commencent souvent avec le caractère caret et correspondent à la position de départ de la chaîne. Les expressions se terminent `$` souvent avec le caractère de signe de dollar et la fin de match de la chaîne. Les caractères `^` et `$` garantissent que l’expression régulière établit une correspondance avec la totalité de la valeur du paramètre de route. Sans les caractères `^` et `$`, l’expression régulière peut correspondre à n’importe quelle sous-chaîne dans la chaîne, ce qui est souvent indésirable. Le tableau suivant contient des exemples et explique pourquoi ils établissent ou non une correspondance.

| Expression   | String    | Correspond | Commentaire               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | 123abc456 | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | mz        | Oui   | Correspondance avec l’expression    |
| `[a-z]{2}`   | MZ        | Oui   | Non-respect de la casse    |
| `^[a-z]{2}$` | hello     | Non    | Voir `^` et `$` ci-dessus |
| `^[a-z]{2}$` | 123abc456 | Non    | Voir `^` et `$` ci-dessus |

Pour plus d’informations sur la syntaxe des expressions régulières, consultez [Expressions régulières du .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Pour contraindre un paramètre à un ensemble connu de valeurs possibles, utilisez une expression régulière. Par exemple, `{action:regex(^(list|get|create)$)}` établit une correspondance avec la valeur de route `action` uniquement pour `list`, `get` ou `create`. Si elle est passée dans le dictionnaire de contraintes, la chaîne `^(list|get|create)$` est équivalente. Les contraintes passées dans le dictionnaire de contraintes (c’est-à-dire qui ne sont pas inline dans un modèle) qui ne correspondent pas à l’une des contraintes connues sont également traitées comme des expressions régulières.

## <a name="custom-route-constraints"></a>Contraintes d’itinéraire personnalisées

Outre les contraintes d’itinéraire intégré, les contraintes d’itinéraire personnalisé peuvent être créées en implémentant l’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. L’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contient une méthode unique, `Match`, qui retourne `true` si la contrainte est satisfaite et `false` dans le cas contraire.

Pour utiliser un <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personnalisé, le type de contrainte d’itinéraire doit être inscrit avec le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de l’application dans le conteneur de service de l’application. Un <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> est un dictionnaire qui mappe les clés de contrainte d’itinéraire aux implémentations <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> qui valident ces contraintes. Le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> d’une application peut être mis à jour dans `Startup.ConfigureServices` dans le cadre d’un appel [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou en configurant <xref:Microsoft.AspNetCore.Routing.RouteOptions> directement avec `services.Configure<RouteOptions>`. Par exemple :

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

La contrainte peut ensuite être appliquée aux itinéraires de la manière habituelle, en utilisant le nom spécifié lors de l’inscription du type de contrainte. Par exemple :

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Informations de référence sur le transformateur de paramètre

Transformateurs de paramètre :

* Sont exécutés lors de la génération d’un lien pour un <xref:Microsoft.AspNetCore.Routing.Route>.
* Implémentez `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Sont configurés à l’aide de <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>.
* Prennent la valeur de routage du paramètre et la convertissent en une nouvelle valeur de chaîne.
* Aboutissent à l’utilisation de la valeur transformée dans le lien généré.

Par exemple, un transformateur de paramètre `slugify` personnalisé dans le modèle d’itinéraire `blog\{article:slugify}` avec `Url.Action(new { article = "MyTestArticle" })` génère `blog\my-test-article`.

Pour utiliser un transformateur de paramètre dans un modèle d’itinéraire, configurez-le d’abord en utilisant <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> dans `Startup.ConfigureServices` :

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Les transformateurs de paramètre sont utilisés par le framework pour transformer l’URI où un point de terminaison est résolu. Par exemple, ASP.NET Core MVC utilise des transformateurs de paramètre pour convertir la valeur de routage utilisée et la faire correspondre à un `area`, `controller`, `action` et `page`.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Avec la route précédente, l’action `SubscriptionManagementController.GetAll` est mise en correspondance avec l’URI `/subscription-management/get-all`. Un transformateur de paramètre ne modifie pas les valeurs de routage utilisées pour générer un lien. Par exemple, `Url.Action("GetAll", "SubscriptionManagement")` produit `/subscription-management/get-all`.

ASP.NET Core fournit des conventions d’API pour l’utilisation des transformateurs de paramètre avec des routages générés :

* La convention de l’API `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` est fournie avec ASP.NET Core MVC. Cette convention applique un transformateur de paramètre spécifié à tous les routages d’attributs dans l’application. Le transformateur de paramètre transforme les jetons de routage d’attribut quand ils sont remplacés. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser le remplacement des jetons](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* Razor Pages a la convention d’API `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention`. Cette convention applique un transformateur de paramètre spécifié à toutes les pages Razor découvertes automatiquement. Le transformateur de paramètre transforme les segments du nom de dossier et du nom de fichier des routes Razor Pages. Pour plus d’informations, consultez [Utiliser un transformateur de paramètre pour personnaliser les routages de pages](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Informations de référence sur la génération d’URL

L’exemple suivant montre comment générer un lien vers une route selon un dictionnaire de valeurs de route et un <xref:Microsoft.AspNetCore.Routing.RouteCollection> données.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Le <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> généré à la fin de l’exemple précédent est `/package/create/123`. Le dictionnaire fournit les valeurs de route `operation` et `id` du modèle « Suivi de package de route », `package/{operation}/{id}`. Pour plus d’informations, consultez l’exemple de code dans la section [Utilisation du middleware de routage](#use-routing-middleware) ou l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Le deuxième paramètre pour le constructeur <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> est une collection de *valeurs ambiantes*. Les valeurs ambiantes sont pratiques à utiliser, car elles limitent le nombre de valeurs qu’un développeur doit spécifier dans un contexte de requête. Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans l’action `About` de `HomeController` d’une application ASP.NET Core MVC, vous n’avez pas besoin de spécifier la valeur de route du contrôleur pour créer un lien vers l’action `Index` : la valeur ambiante de `Home` est utilisée.

Les valeurs ambiantes qui ne correspondent pas à un paramètre sont ignorées. Les valeurs ambiantes sont également ignorées quand une valeur explicitement fournie remplace la valeur ambiante. La mise en correspondance se produit de gauche à droite dans l’URL.

Les valeurs fournies explicitement mais qui n’ont pas de correspondance avec un segment de la route sont ajoutées à la chaîne de requête. Le tableau suivant présente le résultat en cas d’utilisation du modèle de routage `{controller}/{action}/{id?}`.

| Valeurs ambiantes                     | Valeurs explicites                        | Résultats                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Si une route a une valeur par défaut qui ne correspond pas à un paramètre et que cette valeur est explicitement fournie, elle doit correspondre à la valeur par défaut :

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

La génération de liens génère un lien pour cette route seulement quand les valeurs correspondantes pour `controller` et pour `action` sont fournies.

## <a name="complex-segments"></a>Segments complexes

Les segments complexes (par exemple, `[Route("/x{token}y")]`) sont traités par la mise en correspondance des littéraux de droite à gauche de manière non gourmande. Consultez [ce code](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) pour obtenir une explication détaillée de la façon dont les segments complexes sont mis en correspondance. [L’exemple de code](https://github.com/dotnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) n’est pas utilisé par ASP.NET Core, mais il fournit une bonne explication des segments complexes.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Le routage est responsable du mappage des URI des requêtes aux gestionnaires de routage et de la distribution des requêtes entrantes. Les routes sont définies dans l’application et configurées au démarrage de l’application. Une route peut éventuellement extraire des valeurs de l’URL contenue dans la requête, et ces valeurs peuvent ensuite être utilisées pour le traitement de la requête. En utilisant des routes configurées à partir de l’application, le routage est en mesure de générer des URL qui mappent à des gestionnaires de routage.

Pour utiliser les scénarios de routage les plus récents dans ASP.NET Core 2.1, spécifiez la [version de compatibilité](xref:mvc/compatibility-version) à l’inscription des services MVC dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> Ce document traite du routage ASP.NET Core de bas niveau. Pour plus d’informations sur le routage ASP.NET Core MVC, consultez <xref:mvc/controllers/routing>. Pour plus d’informations sur les conventions de routage dans Razor Pages, consultez <xref:razor-pages/razor-pages-conventions>.

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([comment télécharger](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Concepts de base du routage

La plupart des applications doivent choisir un schéma de routage de base et descriptif pour que les URL soient lisibles et explicites. La route conventionnelle par défaut `{controller=Home}/{action=Index}/{id?}` :

* Prend en charge un schéma de routage de base et descriptif.
* Est un point de départ pratique pour les applications basées sur une interface utilisateur.

Les développeurs ajoutent fréquemment des routes laconiques supplémentaires aux zones à trafic élevé de l’application dans des situations particulières (par exemple des points de terminaison de blog ou d’e-commerce) en utilisant le [routage d’attributs](xref:mvc/controllers/routing#attribute-routing) ou des routes conventionnelles dédiées.

Les API web doivent utiliser le routage d’attributs pour modéliser les fonctionnalités de l’application sous la forme d’un ensemble de ressources dans lequel les opérations sont représentées par des verbes HTTP. Cela signifie que plusieurs opérations (comme GET et POST) sur la même ressource logique utilisent la même URL. Le routage d’attributs fournit le niveau de contrôle nécessaire pour concevoir avec soin la disposition des points de terminaison publics d’une API.

Les applications Razor Pages utilisent le routage conventionnel par défaut pour délivrer des ressources nommées dans le dossier *Pages* d’une application. Des conventions supplémentaires sont disponibles : elles vous permettent de personnaliser le comportement de routage de Razor Pages. Pour plus d’informations, consultez <xref:razor-pages/index> et <xref:razor-pages/razor-pages-conventions>.

La prise en charge de la génération d’URL permet de développer l’application sans coder en dur les URL pour lier l’application. Cette prise en charge permet de commencer avec une configuration de routage de base, puis de modifier les routes une fois que la disposition des ressources de l’application est déterminée.

Routing utilise les <xref:Microsoft.AspNetCore.Routing.IRouter> implémentations d’itinéraires de :

* Mapper les requêtes entrantes à des *gestionnaires de routage*.
* Générer les URL utilisées dans les réponses.

Par défaut, une application a une seule collection de routes. Quand une requête arrive, les routes de la collection sont traitées dans l’ordre où elles se trouvent dans la collection. Le framework tente de mettre en correspondance l’URL d’une requête entrante avec une route de la collection en appelant la méthode <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> sur chaque route de la collection. Une réponse peut utiliser le routage pour générer des URL (par exemple pour la redirection ou pour des liens) en fonction des informations des routes et éviter ainsi les URL codées en dur, ce qui facilite la maintenance.

Le système de routage a les caractéristiques suivantes :

* Une syntaxe des modèles de route est utilisée pour définir des routes avec des paramètres de route tokenisés.
* La configuration de points de terminaison de style conventionnel et de style « attribut » est autorisée.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> est utilisé pour déterminer si un paramètre d’URL contient une valeur valide pour une contrainte de point de terminaison donné.
* Les modèles d’application, comme MVC/Razor Pages, inscrivent toutes leurs routes, qui ont une implémentation prévisible de scénarios de routage.
* Une réponse peut utiliser le routage pour générer des URL (par exemple pour la redirection ou pour des liens) en fonction des informations des routes et éviter ainsi les URL codées en dur, ce qui facilite la maintenance.
* La génération d’URL est basée sur des routes, qui prennent en charge l’extensibilité arbitraire. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> offre des méthodes pour générer des URL.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Le routage est connecté au pipeline de [l’intergiciel (middleware)](xref:fundamentals/middleware/index) par la classe <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>. [ASP.NET Core MVC](xref:mvc/overview) ajoute le routage au pipeline de middleware dans le cadre de sa configuration, et gère le routage dans les applications MVC et Razor Pages. Pour découvrir comment utiliser le routage en tant que composant autonome, consultez la section [Utiliser le middleware de routage](#use-routing-middleware).

### <a name="url-matching"></a>Correspondance d’URL

La correspondance d’URL est le processus par lequel le routage distribue une requête entrante à un *gestionnaire*. Ce processus est basé sur des données présentes dans le chemin de l’URL, mais il peut être étendu pour prendre en compte toutes les données de la requête. La possibilité de distribuer des requêtes à des gestionnaires distincts est essentielle pour adapter la taille et la complexité d’une application.

Les requêtes entrantes entrent dans <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>, qui appelle la méthode <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> sur chaque route dans l’ordre. L’instance <xref:Microsoft.AspNetCore.Routing.IRouter> détermine s’il faut *gérer* la requête en affectant à [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) un <xref:Microsoft.AspNetCore.Http.RequestDelegate> non null. Si une route définit un gestionnaire pour la requête, le traitement de la route s’arrête et le gestionnaire est appelé pour traiter la requête. Si aucun gestionnaire de routage n’est trouvé pour traiter la requête, le middleware passe la requête au middleware suivant dans le pipeline de requête.

L’entrée principale de <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> est le [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*) associé à la requête actuelle. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) et [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) sont des sorties définies après la mise en correspondance d’une route.

Une correspondance qui appelle <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> définit également les propriétés de [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) sur des valeurs appropriées en fonction du traitement des requêtes effectué jusqu’à présent.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) est un dictionnaire de *valeurs de route* produites à partir de la route. Ces valeurs sont généralement déterminées en décomposant l’URL en jetons. Elles peuvent être utilisées pour accepter l’entrée d’utilisateur ou pour prendre d’autres décisions relatives à la distribution à l’intérieur de l’application.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) est un conteneur de propriétés des données supplémentaires associées à la route mise en correspondance. Les <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> sont fournis pour prendre en charge l’association de données d’état à chaque route, de façon que l’application puisse prendre des décisions en fonction de la route avec laquelle la correspondance a été établie. Ces valeurs sont définies par le développeur et n’affectent **pas** le comportement du routage de quelque manière que ce soit. De plus, les valeurs dissimulées dans [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) peuvent être de n’importe quel type, contrairement aux valeurs [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), qui doivent être convertibles en chaînes et à partir de chaînes.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) est une liste des routes qui ont participé à la mise en correspondance correcte de la requête. Les routes peuvent être imbriquées les unes dans les autres. La propriété <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> reflète le chemin à travers l’arborescence logique des routes qui ont généré une correspondance. En général le premier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est la collection de routes et il doit être utilisé pour la génération d’URL. Le dernier élément de <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> est le gestionnaire de routage avec lequel la correspondance a été établie.

<a name="lg"></a>

### <a name="url-generation"></a>Génération d’URL

La génération d’URL est le processus par lequel le routage peut créer un chemin d’URL basé sur un ensemble de valeurs de route. Ceci permet une séparation logique entre les gestionnaires de routes et les URL qui y accèdent.

La génération d’URL suit un processus itératif similaire, mais elle commence par un appel du code utilisateur ou de framework à la méthode <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> de la collection de routes. La méthode <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> de chaque *route* est appelée en séquence jusqu’à ce qu’un <xref:Microsoft.AspNetCore.Routing.VirtualPathData> non null soit retourné.

Les entrées principales dans <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> sont les suivantes :

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Les routes utilisent principalement les valeurs de routage fournies par <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> et par <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> pour décider où il est possible de générer une URL et quelles sont les valeurs à inclure. Les <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> sont l’ensemble des valeurs de route produites à partir de la mise en correspondance de la requête actuelle. En revanche, les <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> sont les valeurs de route qui spécifient la façon de générer l’URL souhaitée pour l’opération actuelle. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> est fourni au cas où une route aurait besoin d’obtenir des services ou des données supplémentaires associés au contexte actuel.

> [!TIP]
> Considérez [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) comme un ensemble de remplacements pour [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*). La génération d’URL tente de réutiliser des valeurs de route de la requête actuelle afin de générer les URL pour des liens utilisant la même route ou les mêmes valeurs de route.

La sortie de <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> est un <xref:Microsoft.AspNetCore.Routing.VirtualPathData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> est l’équivalent de <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> contient le <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> de l’URL de sortie et des propriétés supplémentaires qui doivent être définies par la route.

La propriété [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) contient le *chemin virtuel* produit par la route. Selon vos besoins, vous devrez peut-être traiter plus avant le chemin. Si vous souhaitez afficher l’URL générée au format HTML, ajoutez un préfixe au chemin de base de l’application.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) est une référence à la route qui a généré avec succès l’URL.

La propriété [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) est un dictionnaire de données supplémentaires relatives à la route qui a généré l’URL. Il s’agit de l’équivalent de [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Créer des itinéraires

Le routage fournit la classe <xref:Microsoft.AspNetCore.Routing.Route> comme implémentation standard d’<xref:Microsoft.AspNetCore.Routing.IRouter>. <xref:Microsoft.AspNetCore.Routing.Route> utilise la syntaxe des *modèles de routage* pour définir des modèles à mettre en correspondance avec le chemin d’URL quand <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> est appelé. <xref:Microsoft.AspNetCore.Routing.Route> utilise le même modèle de routage pour générer une URL quand <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> est appelé.

La plupart des applications créent des routes en appelant <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ou l’une des méthodes d’extension similaires définies sur <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>. Toutes les méthodes d’extension de <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> créent une instance de <xref:Microsoft.AspNetCore.Routing.Route> et l’ajoutent à la collection de routes.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> n’accepte pas de paramètre de gestionnaire de routage. <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> ajoute uniquement des routes gérées par <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>. Le gestionnaire par défaut est un `IRouter`, et le gestionnaire est susceptible de ne pas traiter la requête. Par exemple, ASP.NET Core MVC est généralement configuré comme gestionnaire par défaut qui gère uniquement les requêtes correspondant à un contrôleur et une action disponibles. Pour plus d’informations sur le routage dans MVC, consultez <xref:mvc/controllers/routing>.

L’exemple de code suivant est un exemple d’appel à <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> utilisé par une définition de route ASP.NET Core MVC classique :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Ce modèle établit une correspondance avec un chemin d’URL et extrait les valeurs de route . Par exemple, le chemin `/Products/Details/17` génère les valeurs de route suivantes : `{ controller = Products, action = Details, id = 17 }`.

Les valeurs de route sont déterminées en divisant le chemin d’URL en segments et en mettant en correspondance chaque segment avec le nom des *paramètres de routage* dans le modèle de routage. Les paramètres de routage sont nommés. Vous définissez des paramètres en plaçant leur nom entre des accolades `{ ... }`.

Le modèle précédent peut également mettre en correspondance le chemin d’URL `/` et produire les valeurs `{ controller = Home, action = Index }`. Cela s’explique par le fait que les paramètres de routage `{controller}` et `{action}` ont des valeurs par défaut et que le paramètre de routage `id` est facultatif. Un signe égal (`=`) suivi d’une valeur après le nom du paramètre de routage définit une valeur par défaut pour le paramètre. Un point d’interrogation (`?`) après le nom du paramètre de routage définit un paramètre facultatif.

Les paramètres de routage ayant une valeur par défaut produisent *toujours* une valeur de routage quand la route correspond. Les paramètres facultatifs ne produisent pas de valeur d’itinéraire s’il n’y a pas de segment de trajectoire d’URL correspondant. Pour obtenir une description complète des scénarios et de la syntaxe des modèles de routage, consultez la section [Informations de référence sur les modèles de routage](#route-template-reference).

Dans l’exemple suivant, la définition du paramètre de route `{id:int}` définit une [contrainte de route](#route-constraint-reference) pour le paramètre de route `id` :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Ce modèle établit une correspondance avec un chemin d’URL comme `/Products/Details/17`, mais pas `/Products/Details/Apples`. Les contraintes de routage implémentent <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> et inspectent les valeurs de route pour les vérifier. Dans cet exemple, la valeur de route `id` doit être convertible en entier. Pour obtenir une explication des contraintes de route fournies par le framework, consultez [Informations de référence sur les contraintes de route](#route-constraint-reference).

Des surcharges supplémentaires de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> acceptent des values pour `constraints`, `dataTokens` et `defaults`. L’utilisation classique de ces paramètres consiste à passer un objet typé anonymement, où les noms des propriétés du type anonyme correspondent aux noms de paramètre de routage.

Les exemples de <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> suivants créent des routes équivalentes :

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> La syntaxe inline pour la définition des contraintes et des valeurs par défaut peut être pratique pour les routes simples. Cependant, certains scénarios, comme les jetons de données, ne sont pas pris en charge par la syntaxe inline.

L’exemple suivant montre quelques autres scénarios :

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/Blog/All-About-Routing/Introduction` et extrait les valeurs `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`. Les valeurs de route par défaut pour `controller` et `action` sont produites par la route, même s’il n’existe aucun paramètre de routage correspondant dans le modèle. Il est possible de spécifier des valeurs par défaut dans le modèle de route. Le paramètre de route `article` est défini comme *passe-partout * par la présence d’un astérisque (`*`) avant le nom du paramètre de route. Les paramètres de routage fourre-tout capturent le reste du chemin d’URL et peuvent également établir une correspondance avec la chaîne vide.

L’exemple suivant ajoute des contraintes de route et des jetons de données :

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Le modèle précédent établit une correspondance avec un chemin d’URL comme `/en-US/Products/5`, et extrait les valeurs `{ controller = Products, action = Details, id = 5 }` et les jetons de données `{ locale = en-US }`.

![Jetons Windows de variables locales](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Génération d’URL de classe de route

La classe <xref:Microsoft.AspNetCore.Routing.Route> peut également effectuer une génération d’URL en combinant un ensemble de valeurs de route et son modèle de routage. Il s’agit logiquement du processus inverse de la mise en correspondance du chemin d’URL.

> [!TIP]
> Pour mieux comprendre la génération d’URL, imaginez l’URL que vous voulez générer, puis pensez à la façon dont un modèle de routage établirait une correspondance avec cette URL. Quelles valeurs seraient produites ? Cela équivaut approximativement à la façon dont la génération d’URL fonctionne dans la classe <xref:Microsoft.AspNetCore.Routing.Route>.

L’exemple suivant utilise une route par défaut ASP.NET Core MVC générale :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Avec les valeurs de route `{ controller = Products, action = List }`, l’URL `/Products/List` est générée. Les valeurs de route remplacent les paramètres de routage correspondant pour former le chemin d’URL. Dans la mesure où `id` est un paramètre de route facultatif, l’URL est générée correctement sans valeur pour `id`.

Avec les valeurs de route `{ controller = Home, action = Index }`, l’URL `/` est générée. Les valeurs de route fournies correspondent aux valeurs par défaut, et les segments correspondant aux valeurs par défaut peuvent être omis sans risque.

Les deux URL générées effectuent un aller-retour avec la définition de route suivante (`/Home/Index` et `/`), et produisent les mêmes valeurs de route que celles utilisées pour générer l’URL.

> [!NOTE]
> Pour générer des URL, une application utilisant ASP.NET Core MVC doit utiliser <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> au lieu d’effectuer un appel directement dans le routage.

Pour plus d’informations sur la génération d’URL, consultez la section [Informations de référence sur la génération d’URL](#url-generation-reference).

## <a name="use-routing-middleware"></a>Utiliser le middleware de routage

Référencez le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) dans le fichier projet de l’application.

Ajoutez le routage au conteneur de service dans `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Les routes doivent être configurées dans la méthode `Startup.Configure`. L’exemple d’application utilise les API suivantes :

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>&ndash; Correspondances uniquement les demandes HTTP GET.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

Le tableau suivant montre les réponses avec les URI donnés.

| URI                    | response                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Valeurs de route : [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Valeurs de route : [operation, track], [id, -3] |
| `/package/track/`      | La requête passe à travers ceci, aucune correspondance.              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | La requête passe à travers ceci, correspondance seulement avec HTTP GET. |
| `GET /hello/Joe/Smith` | La requête passe à travers ceci, aucune correspondance.              |

Si vous configurez une seule route, appelez <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> en passant une instance `IRouter`. Vous n’avez pas besoin d’utiliser <xref:Microsoft.AspNetCore.Routing.RouteBuilder>.

Le framework fournit un ensemble de méthodes d’extension pour la création de routes (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) :

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Certaines des méthodes listées, comme <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>, nécessitent un <xref:Microsoft.AspNetCore.Http.RequestDelegate>. <xref:Microsoft.AspNetCore.Http.RequestDelegate> est utilisé comme *gestionnaire de routage* quand une correspondance est trouvée pour la route. D’autres méthodes de cette famille permettent de configurer un pipeline de middleware utilisé comme gestionnaire de routage. Si la méthode `Map*` n’accepte pas de gestionnaire, comme <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>, elle utilise le <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

Les méthodes `Map[Verb]` utilisent des contraintes pour limiter la route au verbe HTTP dans le nom de la méthode. Par exemple, consultez <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> et <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Informations de référence sur les modèles de routage

Les jetons placés entre accolades (`{ ... }`) définissent des *paramètres de routage* qui sont liés si une correspondance est trouvée pour la route. Vous pouvez définir plusieurs paramètres de routage dans un segment de route, mais ils doivent être séparés par une valeur littérale. Par exemple `{controller=Home}{action=Index}` n’est pas une route valide, car il n’y a aucune valeur littérale entre `{controller}` et `{action}`. Ces paramètres de routage doivent avoir un nom, et ils autorisent la spécification d’attributs supplémentaires.

Un texte littéral autre que les paramètres de routage (par exemple, `{id}`) et le séparateur de chemin `/` doit correspondre au texte présent dans l’URL. La correspondance de texte ne respecte pas la casse et est basée sur la représentation décodée du chemin des URL. Pour mettre en correspondance un délimiteur de paramètre de route littéral (`{` ou `}`), placez-le dans une séquence d’échappement en répétant le caractère (`{{` ou `}}`).

Les modèles d’URL qui tentent de capturer un nom de fichier avec une extension de fichier facultative doivent faire l’objet de considérations supplémentaires. Prenez par exemple le modèle `files/{filename}.{ext?}`. Quand des valeurs existent à la fois pour `filename` et pour `ext`, les deux valeurs sont renseignées. Si seule une valeur existe pour `filename` dans l’URL, une correspondance est trouvée pour la route, car le point final (`.`) est facultatif. Les URL suivantes correspondent à cette route :

* `/files/myFile.txt`
* `/files/myFile`

Vous pouvez utiliser l’astérisque (`*`) comme préfixe d’un paramètre de route à lier au reste de l’URI. Cela s’appelle un paramètre *fourre-tout*. Par exemple, `blog/{*slug}` établit une correspondance avec n’importe quel URI commençant par `/blog` et suivi de n’importe quelle valeur, qui est affectée à la valeur de route `slug`. Les paramètres fourre-tout peuvent également établir une correspondance avec la chaîne vide.

Le paramètre fourre-tout place les caractères appropriés dans une séquence d’échappement lorsque la route est utilisée pour générer une URL, y compris les caractères de séparation de chemin (`/`). Par exemple, la route `foo/{*path}` avec les valeurs de route `{ path = "my/path" }` génère `foo/my%2Fpath`. Notez la barre oblique d’échappement.

Les paramètres de route peuvent avoir des *valeurs par défaut*, désignées en spécifiant la valeur par défaut après le nom du paramètre, séparée par un signe égal (`=`). Par exemple, `{controller=Home}` définit `Home` comme valeur par défaut de `controller`. La valeur par défaut est utilisée si aucune valeur n’est présente dans l’URL pour le paramètre. Vous pouvez rendre facultatifs les paramètres de route en ajoutant un point d’interrogation (`?`) à la fin du nom du paramètre, comme dans `id?`. La différence entre les valeurs facultatives et les paramètres de route par défaut est qu’un paramètre de route ayant une valeur par défaut produit toujours une valeur, tandis qu’un paramètre facultatif a une valeur seulement quand celle-ci est fournie par l’URL de requête.

Les paramètres de route peuvent avoir des contraintes, qui doivent correspondre à la valeur de route liée à partir de l’URL. L’ajout d’un signe deux-points (`:`) et d’un nom de contrainte après le nom du paramètre de routage spécifie une *contrainte inline* sur un paramètre de routage. Si la contrainte nécessite des arguments, ils sont fournis entre parenthèses (`(...)`) après le nom de la contrainte. Il est possible de spécifier plusieurs contraintes inline en ajoutant un autre signe deux-points (`:`) et le nom d’une autre contrainte.

Le nom de la contrainte et les arguments sont passés au service <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> pour créer une instance de <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> à utiliser dans le traitement des URL. Par exemple, le modèle de routage `blog/{article:minlength(10)}` spécifie une contrainte `minlength` avec l’argument `10`. Pour plus d’informations sur les contraintes de route et pour obtenir la liste des contraintes fournies par le framework, consultez la section [Informations de référence sur les contraintes de route](#route-constraint-reference).

Le tableau suivant montre des exemples de modèles de route et leur comportement.

| Modèle de routage                           | Exemple d’URI en correspondance    | URI de la requête&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Correspond seulement au chemin unique `/hello`.                                     |
| `{Page=Home}`                            | `/`                     | Correspond à `Page` et le définit sur `Home`.                                         |
| `{Page=Home}`                            | `/Contact`              | Correspond à `Page` et le définit sur `Contact`.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Mappe au contrôleur `Products` et à l’action `List`.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Mappe au contrôleur `Products` et à l’action `Details` (`id` défini sur 123). |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Mappe au contrôleur `Home` et à la méthode `Index` (`id` est ignoré).        |

L’utilisation d’un modèle est généralement l’approche la plus simple pour le routage. Il est également possible de spécifier des contraintes et des valeurs par défaut hors du modèle de routage.

> [!TIP]
> Activez la [journalisation](xref:fundamentals/logging/index) pour voir comment les implémentations de routage intégrées, comme <xref:Microsoft.AspNetCore.Routing.Route>, établissent des correspondances avec les requêtes.

## <a name="route-constraint-reference"></a>Informations de référence sur les contraintes de routage

Les contraintes de route s’exécutent quand une correspondance s’est produite pour l’URL entrante, et le chemin de l’URL est tokenisé en valeurs de route. En général, les contraintes de routage inspectent la valeur de route associée par le biais du modèle de routage, et créent une décision oui/non indiquant si la valeur est, ou non, acceptable. Certaines contraintes de routage utilisent des données hors de la valeur de route pour déterminer si la requête peut être routée. Par exemple, <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> peut accepter ou rejeter une requête en fonction de son verbe HTTP. Les contraintes sont utilisées dans le routage des requêtes et la génération des liens.

> [!WARNING]
> N’utilisez pas de contraintes pour la **validation des entrées**. Si des contraintes sont utilisées pour la **validation des entrées**, une entrée non valide génère une réponse *404 - Introuvable* au lieu d’une réponse *400 - Requête incorrecte* avec un message d’erreur approprié. Les contraintes de route sont utilisées pour **lever l’ambiguïté** entre des routes similaires, et non pas pour valider les entrées d’une route particulière.

Le tableau suivant montre des exemples de contrainte de route et leur comportement attendu.

| contrainte | Exemple | Exemples de correspondances | Notes |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Correspond à n’importe quel entier |
| `bool` | `{active:bool}` | `true`, `FALSE` | Correspond à `true` ou à `false` (non-respect de la casse) |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Correspond à `DateTime` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Correspond à `decimal` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Correspond à `double` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Correspond à `float` une valeur valable dans la culture invariante. Voir l’avertissement précédent.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Correspond à une valeur `Guid` valide |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Correspond à une valeur `long` valide |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | La chaîne doit comporter au moins 4 caractères |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | La chaîne ne doit pas comporter plus de 8 caractères |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | La chaîne doit comporter exactement 12 caractères |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | La chaîne doit comporter au moins 8 caractères et pas plus de 16 caractères |
| `min(value)` | `{age:min(18)}` | `19` | La valeur entière doit être au moins égale à 18 |
| `max(value)` | `{age:max(120)}` | `91` | La valeur entière ne doit pas être supérieure à 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | La valeur entière doit être au moins égale à 18 mais ne doit pas être supérieure à 120 |
| `alpha` | `{name:alpha}` | `Rick` | La chaîne doit se composer d’un ou de plusieurs caractères alphabétiques (`a`-`z`, non-respect de la casse) |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | La chaîne doit correspondre à l’expression régulière (voir les conseils relatifs à la définition d’une expression régulière) |
| `required` | `{name:required}` | `Rick` | Utilisé pour garantir qu’une valeur autre qu’un paramètre est présente pendant la génération de l’URL |

Il est possible d’appliquer plusieurs contraintes séparées par un point-virgule à un même paramètre. Par exemple, la contrainte suivante limite un paramètre à une valeur entière supérieure ou égale à 1 :

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Les contraintes de routage qui vérifient que l’URL peut être convertie en type CLR (comme `int` ou `DateTime`) utilisent toujours la culture invariant. ces contraintes partent du principe que l’URL n’est pas localisable. Les contraintes de routage fournies par le framework ne modifient pas les valeurs stockées dans les valeurs de route. Toutes les valeurs de route analysées à partir de l’URL sont stockées sous forme de chaînes. Par exemple, la contrainte `float` tente de convertir la valeur de route en valeur float, mais la valeur convertie est utilisée uniquement pour vérifier qu’elle peut être convertie en valeur float.

## <a name="regular-expressions"></a>Expressions régulières

Le framework ASP.NET Core ajoute `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` au constructeur d’expression régulière. Pour obtenir une description de ces membres, consultez <xref:System.Text.RegularExpressions.RegexOptions>.

Les expressions régulières utilisent les délimiteurs et des jetons semblables à ceux utilisés par le service de routage et le langage C#. Les jetons d’expression régulière doivent être placés dans une séquence d’échappement. Pour que l’expression régulière `^\d{3}-\d{2}-\d{4}$` puisse être utilisée dans le routage, il faut que les caractères `\` (une seule barre oblique inverse) soit fournis dans la chaîne sous la forme `\\` (double barre oblique inverse) dans le fichier source C#, pour placer en échappement le caractère d’échappement de chaîne `\` (sauf en cas d’utilisation de [littéraux de chaîne textuelle](/dotnet/csharp/language-reference/keywords/string)). Pour placer en échappement les caractères de délimiteur de paramètre de route (`{`, `}`, `[`, `]`), doublez les caractères dans l’expression (`{{`, `}`, `[[`, `]]`). Le tableau suivant montre une expression régulière et la version placée en échappement.

| Expression régulière    | Expression régulière en échappement     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Les expressions régulières utilisées dans le routage commencent souvent par un caret (`^`) et correspondent à la position de début de la chaîne. Les expressions se terminent souvent par le signe dollar (`$`) de caractère et correspondent à la fin de la chaîne. Les caractères `^` et `$` garantissent que l’expression régulière établit une correspondance avec la totalité de la valeur du paramètre de route. Sans les caractères `^` et `$`, l’expression régulière peut correspondre à n’importe quelle sous-chaîne dans la chaîne, ce qui est souvent indésirable. Le tableau suivant contient des exemples et explique pourquoi ils établissent ou non une correspondance.

| Expression   | String    | Correspond | Commentaire               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | 123abc456 | Oui   | Correspondances de sous-chaînes     |
| `[a-z]{2}`   | mz        | Oui   | Correspondance avec l’expression    |
| `[a-z]{2}`   | MZ        | Oui   | Non-respect de la casse    |
| `^[a-z]{2}$` | hello     | Non    | Voir `^` et `$` ci-dessus |
| `^[a-z]{2}$` | 123abc456 | Non    | Voir `^` et `$` ci-dessus |

Pour plus d’informations sur la syntaxe des expressions régulières, consultez [Expressions régulières du .NET Framework](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Pour contraindre un paramètre à un ensemble connu de valeurs possibles, utilisez une expression régulière. Par exemple, `{action:regex(^(list|get|create)$)}` établit une correspondance avec la valeur de route `action` uniquement pour `list`, `get` ou `create`. Si elle est passée dans le dictionnaire de contraintes, la chaîne `^(list|get|create)$` est équivalente. Les contraintes passées dans le dictionnaire de contraintes (c’est-à-dire qui ne sont pas inline dans un modèle) qui ne correspondent pas à l’une des contraintes connues sont également traitées comme des expressions régulières.

## <a name="custom-route-constraints"></a>Contraintes d’itinéraire personnalisé

Outre les contraintes d’itinéraire intégré, les contraintes d’itinéraire personnalisé peuvent être créées en implémentant l’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>. L’interface <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> contient une méthode unique, `Match`, qui retourne `true` si la contrainte est satisfaite et `false` dans le cas contraire.

Pour utiliser un <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> personnalisé, le type de contrainte d’itinéraire doit être inscrit avec le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> de l’application dans le conteneur de service de l’application. Un <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> est un dictionnaire qui mappe les clés de contrainte d’itinéraire aux implémentations <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> qui valident ces contraintes. Le <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> d’une application peut être mis à jour dans `Startup.ConfigureServices` dans le cadre d’un appel [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) ou en configurant <xref:Microsoft.AspNetCore.Routing.RouteOptions> directement avec `services.Configure<RouteOptions>`. Par exemple :

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

La contrainte peut ensuite être appliquée aux itinéraires de la manière habituelle, en utilisant le nom spécifié lors de l’inscription du type de contrainte. Par exemple :

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Informations de référence sur la génération d’URL

L’exemple suivant montre comment générer un lien vers une route selon un dictionnaire de valeurs de route et un <xref:Microsoft.AspNetCore.Routing.RouteCollection> données.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Le <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> généré à la fin de l’exemple précédent est `/package/create/123`. Le dictionnaire fournit les valeurs de route `operation` et `id` du modèle « Suivi de package de route », `package/{operation}/{id}`. Pour plus d’informations, consultez l’exemple de code dans la section [Utilisation du middleware de routage](#use-routing-middleware) ou l’[exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Le deuxième paramètre pour le constructeur <xref:Microsoft.AspNetCore.Routing.VirtualPathContext> est une collection de *valeurs ambiantes*. Les valeurs ambiantes sont pratiques à utiliser, car elles limitent le nombre de valeurs qu’un développeur doit spécifier dans un contexte de requête. Les valeurs de route actuelles de la requête actuelle sont considérées comme des valeurs ambiantes pour la génération de liens. Dans l’action `About` de `HomeController` d’une application ASP.NET Core MVC, vous n’avez pas besoin de spécifier la valeur de route du contrôleur pour créer un lien vers l’action `Index` : la valeur ambiante de `Home` est utilisée.

Les valeurs ambiantes qui ne correspondent pas à un paramètre sont ignorées. Les valeurs ambiantes sont également ignorées quand une valeur explicitement fournie remplace la valeur ambiante. La mise en correspondance se produit de gauche à droite dans l’URL.

Les valeurs fournies explicitement mais qui n’ont pas de correspondance avec un segment de la route sont ajoutées à la chaîne de requête. Le tableau suivant présente le résultat en cas d’utilisation du modèle de routage `{controller}/{action}/{id?}`.

| Valeurs ambiantes                     | Valeurs explicites                        | Résultats                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Si une route a une valeur par défaut qui ne correspond pas à un paramètre et que cette valeur est explicitement fournie, elle doit correspondre à la valeur par défaut :

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

La génération de liens génère un lien pour cette route seulement quand les valeurs correspondantes pour `controller` et pour `action` sont fournies.

## <a name="complex-segments"></a>Segments complexes

Les segments complexes (par exemple, `[Route("/x{token}y")]`) sont traités par la mise en correspondance des littéraux de droite à gauche de manière non gourmande. Consultez [ce code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) pour obtenir une explication détaillée de la façon dont les segments complexes sont mis en correspondance. [L’exemple de code](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) n’est pas utilisé par ASP.NET Core, mais il fournit une bonne explication des segments complexes.

::: moniker-end
