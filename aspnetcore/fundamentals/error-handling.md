---
title: Gérer les erreurs dans ASP.NET Core
author: rick-anderson
description: Découvrez comment gérer les erreurs dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: bbd16e2e045a24f018eb59b67dfdf50d9bed6f32
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775035"
---
# <a name="handle-errors-in-aspnet-core"></a>Gérer les erreurs dans ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra/) et [Steve Smith](https://ardalis.com/)

Cet article décrit les approches courantes de gestion des erreurs dans ASP.NET Core Web Apps. Consultez <xref:web-api/handle-errors> pour les API Web.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Comment télécharger](xref:index#how-to-download-a-sample).) L’article contient des instructions sur la façon de définir des directives de`#if`préprocesseur (, `#endif`, `#define`) dans l’exemple d’application pour activer différents scénarios.

## <a name="developer-exception-page"></a>Page d’exceptions du développeur

La *Page d’exceptions du développeur* affiche des informations détaillées sur les exceptions des demandes. Elle est mise à disposition par le package [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), qui se trouve dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Ajoutez du code à la méthode `Startup.Configure` pour activer la page lorsque l’application s’exécute dans [l’environnement](xref:fundamentals/environments) de développement :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Placez l’appel à <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> avant tous les middlewares dont vous souhaitez intercepter les exceptions.

> [!WARNING]
> Activez la page exception du développeur **uniquement lorsque l’application s’exécute dans l’environnement de développement**. Il n’est pas souhaitable de partager publiquement des informations détaillées sur les exceptions quand l’application s’exécute en production. Pour plus d’informations sur la configuration des environnements, consultez <xref:fundamentals/environments>.

Cette page inclut les informations suivantes sur l’exception et la demande :

* Arborescence des appels de procédure
* Paramètres de la chaîne de requête (le cas échéant)
* Cookies (le cas échéant)
* headers

Pour afficher la Page d’exceptions du développeur dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez la directive de préprocesseur `DevEnvironment` et sélectionnez **Déclencher une exception** sur la page d’accueil.

## <a name="exception-handler-page"></a>Page Gestionnaire d’exceptions

Pour configurer une page de gestion des erreurs personnalisée en fonction de l’environnement de production, utilisez le middleware de gestion des exceptions. Le middleware :

* Intercepte et consigne les exceptions.
* Réexécute la requête dans un autre pipeline pour la page ou le contrôleur indiqué(e). La demande n’est pas réexécutée si la réponse a démarré.

Dans l’exemple suivant, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> ajoute le middleware de gestion des exceptions dans des environnements autres que les environnements de développement :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Le Razor modèle d’application pages fournit une page d’erreur (*. cshtml*) <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> et`ErrorModel`une classe () dans le dossier *pages* . Pour une application MVC, le modèle de projet comprend une méthode d’action d’erreur et un affichage correspondant. Voici la méthode d’action :

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Ne Marquez pas la méthode d’action du gestionnaire d’erreurs avec des `HttpGet`attributs de méthode http, tels que. Les verbes explicites empêchent certaines demandes d’atteindre la méthode. Autorisez l’accès anonyme à la méthode afin que les utilisateurs non authentifiés puissent recevoir la vue des erreurs.

### <a name="access-the-exception"></a>Accéder à l'exception

Utilisez <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> pour accéder à l’exception et au chemin de la demande d’origine dans une page ou un contrôleur de gestionnaire d’erreurs :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Ne communiquez **pas** d’informations sensibles sur les erreurs aux clients. Cela représenterait un risque de sécurité.

Pour afficher la page de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerPage` et sélectionnez **Déclencher une exception** sur la page d’accueil.

## <a name="exception-handler-lambda"></a>Expression lambda Gestionnaire d’exceptions

En dehors d’une [page de gestionnaire d’exceptions personnalisée](#exception-handler-page), il est possible de fournir une expression lambda à <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>, ce qui permet d’accéder à l’erreur avant de retourner la réponse.

Voici un exemple d’utilisation d’une expression lambda pour la gestion des exceptions :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

Dans le code précédent, `await context.Response.WriteAsync(new string(' ', 512));` est ajouté afin que le navigateur Internet Explorer affiche le message d’erreur plutôt qu’un message d’erreur IE. Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> Ne distribuez **pas** d’informations sensibles sur les erreurs de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> ou de <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> aux clients. Cela représenterait un risque de sécurité.

Pour afficher le résultat de l’expression lambda de gestion des exceptions dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez les directive de préprocesseur `ProdEnvironment` et `ErrorHandlerLambda` et sélectionnez **Déclencher une exception** sur la page d’accueil.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Par défaut, une application ASP.NET Core ne fournit pas une page de codes d’état pour les codes d’état HTTP, comme *404 - Introuvable*. L’application retourne un code d’état et un corps de réponse vide. Pour fournir des pages de codes d’état, utilisez le middleware Pages de codes d’état.

Le middleware est mis à disposition par le package [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/), qui se trouve dans le [métapackage Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Pour activer les gestionnaires exclusivement textuels par défaut des codes d’état d’erreur courants, appelez <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> dans la méthode `Startup.Configure` :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Appelez `UseStatusCodePages` avant les middlewares de gestion des demandes (par exemple, le middleware de fichiers statiques et le middleware MVC).

Voici un exemple du texte affiché par les gestionnaires par défaut :

```
Status Code: 404; Not Found
```

Pour voir l’un des formats de pages de codes d’état dans [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), utilisez une des directives de préprocesseur qui commencent par `StatusCodePages`, puis sélectionnez **Déclencher une erreur 404** sur la page d’accueil.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages avec chaîne de format

Pour personnaliser le texte et le type de contenu de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> qui prend un type de contenu et une chaîne de format :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages avec expression lambda

Pour spécifier un code personnalisé de gestion des erreurs et d’écriture de la réponse, utilisez la surcharge de <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> qui prend une expression lambda :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> :

* Envoie un code d’état *302 - Trouvé* au client.
* Redirige le client à l’emplacement fourni dans le modèle d’URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Le modèle d’URL peut comporter un espace réservé `{0}` pour le code d’état, comme dans l’exemple. S’il commence par un tilde (~), celui-ci est remplacé par le `PathBase` de l’application. Si vous pointez vers un point de terminaison au sein de l’application, Razor créez une vue ou une page MVC pour le point de terminaison. Pour obtenir Razor un exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Cette méthode est généralement utilisée lorsque l’application :

* Doit rediriger le client vers un autre point de terminaison, généralement dans les cas où une autre application traite l’erreur. Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison redirigé.
* Ne doit pas conserver ni retourner le code d’état d’origine avec la réponse de redirection initiale.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

La méthode d’extension <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> :

* Retourne le code d’état d’origine au client.
* Génère le corps de la réponse en réexécutant le pipeline de requête avec un autre chemin.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Si vous pointez vers un point de terminaison au sein de l’application, Razor créez une vue ou une page MVC pour le point de terminaison. Pour obtenir Razor un exemple de pages, consultez *pages/StatusCode. cshtml* dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Cette méthode est généralement utilisée lorsque l’application doit :

* Traiter la demande sans la rediriger vers un autre point de terminaison. Pour les applications web, la barre d’adresses du navigateur client reflète le point de terminaison demandé à l’origine.
* Conserver et retourner le code d’état d’origine avec la réponse.

Les modèles d’URL et de chaîne de requête peuvent comporter un espace réservé (`{0}`) pour le code d’état. Le modèle d’URL doit commencer par une barre oblique (`/`). Si vous utilisez un espace réservé dans le chemin, vérifiez que le point de terminaison (page ou contrôleur) peut traiter le segment de chemin. Par exemple, une Razor page pour les erreurs doit accepter la valeur de segment de chemin `@page` d’accès facultative avec la directive :

```cshtml
@page "{code?}"
```

Le point de terminaison qui traite l’erreur peut récupérer l’URL d’origine qui a généré l’erreur, comme dans l’exemple suivant :

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Désactiver les pages de codes d’état

Pour désactiver les pages de codes d’État pour un contrôleur MVC ou une méthode [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) d’action, utilisez l’attribut.

Pour désactiver les pages de codes d’État pour des Razor demandes spécifiques dans une méthode de gestionnaire de pages ou <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>dans un contrôleur MVC, utilisez :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Code de gestion des exceptions

Le code dans les pages de gestion des exceptions peut lever des exceptions. Il est souvent judicieux de mettre dans les pages d’erreur de production du contenu purement statique.

### <a name="response-headers"></a>En-têtes de réponse

Une fois les en-têtes d’une réponse envoyés :

* L’application ne peut pas modifier le code d’état de la réponse.
* Il est impossible d’exécuter les pages d’exception ou les gestionnaires. La réponse doit être accomplie ou la connexion abandonnée.

## <a name="server-exception-handling"></a>Gestion des exceptions de serveur

En plus de la logique de gestion des exceptions de votre application, [l’implémentation de serveur HTTP](xref:fundamentals/servers/index) peut gérer certaines exceptions. Si le serveur intercepte une exception avant que les en-têtes de réponse ne soient envoyés, le serveur envoie une réponse *500 Erreur interne du serveur* sans corps de réponse. Si le serveur intercepte une exception une fois que les en-têtes de réponse ont été envoyés, il ferme la connexion. Les demandes qui ne sont pas gérées par votre application sont gérées par le serveur. Toute exception qui se produit tandis que le serveur traite la demande est gérée par le dispositif de gestion des exceptions du serveur. Ni les pages d’erreur personnalisées de l’application, ni les intergiciels (middleware) de gestion des exceptions, ni les filtres n’ont d’incidence sur ce comportement.

## <a name="startup-exception-handling"></a>Gestion des exceptions de démarrage

Seule la couche d’hébergement peut gérer les exceptions qui se produisent au démarrage de l’application. L’hôte peut être configuré pour [capturer les erreurs de démarrage](xref:fundamentals/host/web-host#capture-startup-errors) et [capturer les erreurs détaillées](xref:fundamentals/host/web-host#detailed-errors).

La couche d’hébergement ne peut afficher la page d’une erreur de démarrage capturée que si celle-ci se produit une fois la liaison établie entre l’adresse d’hôte et le port. Si la liaison échoue :

* La couche d’hébergement journalise une exception critique.
* Le processus dotnet tombe en panne.
* Aucune page d’erreur ne s’affiche si le serveur HTTP est [Kestrel](xref:fundamentals/servers/kestrel).

En cas d’exécution sur [IIS](/iis) (ou Azure App Service) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), une réponse *502.5 - Échec du processus* est retournée par le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) si le processus ne peut pas démarrer. Pour plus d’informations, consultez <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Page d’erreur de base de données

L’intergiciel (middleware) de page d’erreur de base de données capture des exceptions liées à la base de données qui peuvent être résolues à l’aide de Entity Framework migrations. Lorsque ces exceptions se produisent, une réponse HTML comportant le détail des actions possibles pour résoudre le problème est générée. Cette page ne doit être activée que dans l’environnement de développement. Pour cela, ajoutez du code à `Startup.Configure` :

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtres d’exceptions

Dans les applications MVC, vous pouvez configurer les filtres d’exception globalement, contrôleur par contrôleur ou action par action. Dans Razor les applications pages, elles peuvent être configurées globalement ou par modèle de page. Ces filtres gèrent les exceptions non prises en charge qui se produisent pendant l’exécution d’une action de contrôleur ou d’un autre filtre. Pour plus d’informations, consultez <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Les filtres d’exceptions sont utiles pour intercepter les exceptions qui se produisent dans les actions MVC, mais n’offrent pas la même souplesse que le middleware de gestion des exceptions. Nous vous recommandons d’utiliser le middleware. N’utilisez des filtres que si vous devez gérer les erreurs différemment en fonction de l’action MVC choisie.

## <a name="model-state-errors"></a>Erreurs d’état de modèle

Pour plus d’informations sur la gestion des erreurs d’état de modèle, voir [Liaison de modèles](xref:mvc/models/model-binding) et [Validation de modèles](xref:mvc/models/validation).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
