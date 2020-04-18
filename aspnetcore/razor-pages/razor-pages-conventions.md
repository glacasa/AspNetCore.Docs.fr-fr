---
title: Conventions de routes et d’applications pour les pages Razor dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les conventions du fournisseur de modèles de routes et d’applications vous permettent de gérer le routage, la découverte et le traitement des pages.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 6124554d5f9859179edfb5c545cf0b082369c0c9
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642732"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Conventions de routes et d’applications pour les pages Razor dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Découvrez comment utiliser les [conventions du fournisseur de modèles de routes et d’applications](xref:mvc/controllers/application-model#conventions) pour contrôler le routage, la découverte et le traitement des pages dans les applications à base de pages Razor.

Quand vous devez configurer des routages de pages personnalisés pour des pages individuelles, configurez le routage vers les pages avec la convention [AddPageRoute](#configure-a-page-route) décrite plus loin dans cette rubrique.

Pour spécifier un itinéraire de page, ajouter des segments d’itinéraire ou ajouter des paramètres à un itinéraire, utilisez la directive de `@page` la page. Pour plus d’informations, voir [itinéraires personnalisés](xref:razor-pages/index#custom-routes).

Il y a des mots réservés qui ne peuvent pas être utilisés comme segments d’itinéraire ou noms de paramètres. Pour plus d’informations, voir [Routing: Reserved routing names](xref:mvc/controllers/routing#reserved-routing-names).

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

| Scénario | L’exemple montre... |
| -------- | --------------------------- |
| [Conventions de modèle](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Ajouter un modèle de route et un en-tête aux pages d’une application. |
| [Conventions d’actions de routage de pages](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Ajouter un modèle de route aux pages d’un dossier et à une page unique. |
| [Conventions d’actions de modèle de page](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtre, expression lambda ou fabrique de filtres)</li></ul> | Ajouter un en-tête dans les pages d’un dossier, ajouter un en-tête dans une page unique et configurer une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) pour ajouter un en-tête dans les pages d’une application. |

Les conventions Razor Pages sont <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> ajoutées <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> et configurées `Startup` à l’aide de la méthode d’extension à la collection de services de la classe. Les exemples de convention suivants sont expliqués plus loin dans cette rubrique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordre d’itinéraire

Itinéraires <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> spécifier un traitement (correspondance de l’itinéraire).

| JSON            | Comportement |
| :--------------: | -------- |
| -1               | L’itinéraire est traité avant que d’autres itinéraires ne soient traités. |
| 0                | L’ordre n’est pas spécifié (valeur par défaut). Ne pas `Order` `Order = null`attribuer ( ) `Order` par défaut l’itinéraire à 0 (zéro) pour le traitement. |
| 1, 2, &hellip; n | Spécifie l’ordre de traitement de l’itinéraire. |

Le traitement des itinéraires est établi par convention :

* Les itinéraires sont traités par ordre séquentiel (-1, 0, 1, 2, &hellip; n).
* Lorsque les itinéraires ont le même `Order`, l’itinéraire le plus spécifique est égalé d’abord suivi par des itinéraires moins spécifiques.
* Lorsque les itinéraires avec le même `Order` nombre et le même nombre de paramètres correspondent à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>une URL de demande, les itinéraires sont traités dans l’ordre qu’ils sont ajoutés à la .

Si possible, évitez de dépendre d’une ordonnance de traitement des itinéraires établie. Généralement, le routage sélectionne l’itinéraire correct avec l’appariement de l’URL. Si vous devez `Order` définir correctement les propriétés d’itinéraire pour les demandes d’itinéraire, le système de routage de l’application est probablement déroutant pour les clients et fragile à entretenir. Cherchez à simplifier le système de routage de l’application. L’application d’échantillon nécessite un ordre explicite de traitement d’itinéraire pour démontrer plusieurs scénarios de routage à l’aide d’une seule application. Toutefois, vous devriez essayer d’éviter `Order` la pratique de définir l’itinéraire dans les applications de production.

Le routage de Razor Pages et celui du contrôleur MVC partagent une implémentation. Des informations sur l’ordre d’itinéraire dans les sujets MVC sont disponibles à [Routing pour les actions de contrôleur: Itinéraires d’attribut de commande](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Conventions de modèle

Ajoutez un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> délégué pour ajouter [des conventions modèles](xref:mvc/controllers/application-model#conventions) qui s’appliquent aux Pages Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Ajouter une convention de modèle de routage à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle d’itinéraire de page.

L’exemple d’application ajoute un modèle de routage `{globalTemplate?}` à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `1`. Cela garantit le comportement de correspondance d’itinéraire suivant dans l’application d’échantillon :

* Un modèle `TheContactPage/{text?}` d’itinéraire pour est ajouté plus tard dans le sujet. L’itinéraire De la Page `null` `Order = 0`De Contact a `{globalTemplate?}` une commande par défaut de (), de sorte qu’il correspond avant le modèle d’itinéraire.
* Un `{aboutTemplate?}` modèle d’itinéraire est ajouté plus tard dans le sujet. Le modèle `{aboutTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand la page About est demandée sur `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 1`) et non `RouteData.Values["aboutTemplate"]` (`Order = 2`) en raison de la définition de la propriété `Order`.
* Un `{otherPagesTemplate?}` modèle d’itinéraire est ajouté plus tard dans le sujet. Le modèle `{otherPagesTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Lorsque n’importe quelle page du dossier *Pages/OtherPages* est `/OtherPages/Page1/RouteDataValue`demandée avec un paramètre d’itinéraire (par`Order = 2`exemple, ), `Order` "RouteDataValue" est chargé dans (`Order = 1`) et non `RouteData.Values["otherPagesTemplate"]` ( ) en raison de la mise en `RouteData.Values["globalTemplate"]` place de la propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Les options De Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Razor, telles que l’ajout, `Startup.ConfigureServices`sont ajoutées lorsque MVC est ajouté à la collection de services en . Pour obtenir un exemple, consultez [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue` et examinez le résultat :

![La page About est demandée avec un segment de routage pour GlobalRouteValue. La page affichée montre que la valeur des données de routage est capturée dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Ajouter une convention de modèle d’application à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle d’application de page.

Pour illustrer cette convention et bien d’autres, plus loin dans cette rubrique, l’exemple d’application inclut une classe `AddHeaderAttribute`. Le constructeur de classe accepte une chaîne `name` et un tableau de chaînes `values`. Ces valeurs sont utilisées dans sa méthode `OnResultExecuting` pour définir un en-tête de réponse. La classe complète est affichée dans la section [Conventions d’actions de modèle de page](#page-model-action-conventions), plus loin dans cette rubrique.

L’exemple d’application utilise la classe `AddHeaderAttribute` pour ajouter un en-tête, `GlobalHeader`, à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que GlobalHeader a été ajouté.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Ajouter une convention de modèle de gestionnaire à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle de gestionnaire de page.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Conventions d’actions de routage de pages

Le fournisseur de modèles d’itinéraire par défaut qui dérive de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> conventions invoque qui sont conçus pour fournir des points d’extensibility pour configurer les itinéraires de page.

### <a name="folder-route-model-convention"></a>Convention de modèle de routage de dossier

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> invoque une action sur le pour toutes les pages sous le dossier spécifié.

L’exemple d’application utilise <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour ajouter un modèle de routage `{otherPagesTemplate?}` aux pages du dossier *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela garantit que le `{globalTemplate?}` modèle pour (fixé `1`plus tôt dans le sujet à ) est donné la priorité pour la position de valeur de données de première route quand une valeur d’itinéraire unique est fournie. Si une page du dossier *Pages/OtherPages* est demandée avec `/OtherPages/Page1/RouteDataValue`une valeur de paramètre d’itinéraire`Order = 1`(par `RouteData.Values["otherPagesTemplate"]` `Order = 2`exemple, ), `Order` "RouteDataValue" est chargé dans ( ) et non ( ) en raison de la mise en `RouteData.Values["globalTemplate"]` place de la propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` et examinez le résultat :

![La page Page1 du dossier OtherPages est demandée avec un segment de routage pour GlobalRouteValue et OtherPagesRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convention de modèle de routage de page

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> invoque une action sur la page pour le nom spécifié.

L’exemple d’application utilise `AddPageRouteModelConvention` pour ajouter un modèle de routage `{aboutTemplate?}` à la page About :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela garantit que le `{globalTemplate?}` modèle pour (fixé `1`plus tôt dans le sujet à ) est donné la priorité pour la position de valeur de données de première route quand une valeur d’itinéraire unique est fournie. Si la page About est demandée `/About/RouteDataValue`avec une valeur de paramètre `RouteData.Values["globalTemplate"]` `Order = 1`d’itinéraire `RouteData.Values["aboutTemplate"]` `Order = 2`à , "RouteDataValue" est chargé dans ( ) et non ( ) en raison de la mise en place de la `Order` propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue/AboutRouteValue` et examinez le résultat :

![La page About est demandée avec des segments de routage pour GlobalRouteValue et AboutRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Utilisez un transformateur de paramètres pour personnaliser les itinéraires des pages

Les itinéraires de pages générés par ASP.NET Core peuvent être personnalisés à l’aide d’un transformateur de paramètres. Un transformateur de paramètre implémente `IOutboundParameterTransformer` et transforme la valeur des paramètres. Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé transforme la valeur de la route `SubscriptionManagement` en `subscription-management`.

La `PageRouteTransformerConvention` convention de modèle d’itinéraire de page applique un transformateur de paramètres aux segments de dossier et de nom de fichier des itinéraires de page générés automatiquement dans une application. Par exemple, le fichier Razor Pages à */Pages/SubscriptionManagement/ViewAll.cshtml* aurait son itinéraire réécrit de `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`.

`PageRouteTransformerConvention`transforme uniquement les segments générés automatiquement d’un itinéraire de page provenant du dossier Et du nom du fichier Razor Pages. Il ne transforme pas les `@page` segments d’itinéraire ajoutés avec la directive. La convention ne transforme pas <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>non plus les itinéraires ajoutés par .

Le `PageRouteTransformerConvention` est enregistré comme `Startup.ConfigureServices`une option dans :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Configurer un routage de page

Utilisez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> pour configurer un itinéraire vers une page sur le parcours spécifié de la page. Les liens générés qui pointent vers la page utilisent le routage que vous avez spécifié. `AddPageRoute` utilise `AddPageRouteModelConvention` pour établir le routage.

L’exemple d’application crée un routage vers `/TheContactPage` pour *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

La page Contact est également accessible sur `/Contact` via son routage par défaut.

Le routage personnalisé de l’exemple d’application vers la page Contact permet un segment de routage `text` facultatif (`{text?}`). Cette page inclut également ce segment facultatif dans sa directive `@page`, au cas où le visiteur accèderait à la page via le routage `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Notez que l’URL générée pour le lien **Contact** dans la page affichée reflète le routage mis à jour :

![Lien Contact de l’exemple d’application dans la barre de navigation](razor-pages-conventions/_static/contact-link.png)

![Le lien Contact dans la page HTML affichée indique que l’attribut href a la valeur ’/TheContactPage’](razor-pages-conventions/_static/contact-link-source.png)

Visitez la page Contact via son routage ordinaire, `/Contact`, ou via le routage personnalisé, `/TheContactPage`. Si vous indiquez un segment de routage `text` supplémentaire, la page affiche le segment HTML que vous fournissez :

![Exemple dans le navigateur Edge d’un segment de routage ‘text’ facultatif fourni pour ‘TextValue’ dans l’URL. La page affichée montre la valeur du segment ‘text’.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Conventions d’actions de modèle de page

Le fournisseur de modèles <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> de pages par défaut qui implémente invoque des conventions qui sont conçues pour fournir des points d’extensibility pour configurer les modèles de page. Ces conventions sont utiles durant la génération et la modification de scénarios de découverte et de traitement de pages.

Pour les exemples de cette section, l’application d’échantillon utilise une `AddHeaderAttribute` classe, qui est un <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, qui applique un en-tête de réponse :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

À l’aide de conventions, l’exemple montre comment appliquer l’attribut à toutes les pages d’un dossier et à une seule page.

**Convention de modèle d’application de dossier**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> qui invoque une action sur les instances pour toutes les pages sous le dossier spécifié.

L’exemple illustre l’utilisation de `AddFolderApplicationModelConvention` en ajoutant un en-tête, `OtherPagesHeader`, aux pages situées dans le dossier *OtherPages* de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page OtherPages/Page1 montrent que OtherPagesHeader a été ajouté.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convention de modèle d’application de page**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> invoque une action sur la page pour le nom spécifié.

L’exemple illustre l’utilisation de `AddPageApplicationModelConvention` en ajoutant un en-tête, `AboutHeader`, à la page About :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que AboutHeader a été ajouté.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurer un filtre**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configure le filtre spécifié pour s’appliquer. Vous pouvez implémenter une classe de filtre. Toutefois, l’exemple d’application montre comment implémenter un filtre dans une expression lambda, laquelle est implémentée en arrière-plan en tant que fabrique qui retourne un filtre :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Le modèle d’application de page est utilisé pour vérifier le chemin relatif des segments qui mènent à la page Page2 dans le dossier *OtherPages*. Si la condition est satisfaite, un en-tête est ajouté. Sinon, `EmptyFilter` est appliqué.

`EmptyFilter` est un [filtre d’action](xref:mvc/controllers/filters#action-filters). Étant donné que les filtres `EmptyFilter` Action sont ignorés par Razor Pages, le n’a aucun effet comme prévu si le chemin ne contient `OtherPages/Page2`pas .

Demandez la page Page2 de l’exemple sur `localhost:5000/OtherPages/Page2` et examinez les en-têtes pour voir le résultat :

![OtherPagesPage2Header est ajouté à la réponse pour Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurer une fabrique de filtres**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configure l’usine spécifiée pour appliquer [des filtres](xref:mvc/controllers/filters) à toutes les pages Razor.

L’exemple d’application illustre l’utilisation d’une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) en ajoutant un en-tête, `FilterFactoryHeader`, et deux valeurs aux pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que deux en-têtes FilterFactoryHeader ont été ajoutés.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtres MVC et filtre de page (IPageFilter)

Les [filtres d’action](xref:mvc/controllers/filters#action-filters) MVC sont ignorés par les pages Razor, car les pages Razor utilisent des méthodes de gestionnaire. Vous pouvez utiliser d’autres types de filtre MVC : [Autorisation](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) et [Résultat](xref:mvc/controllers/filters#result-filters). Pour plus d’informations, consultez la rubrique [Filtres](xref:mvc/controllers/filters).

Le filtre<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>Page ( ) est un filtre qui s’applique aux Pages Razor. Pour plus d’informations, consultez [Méthodes de filtre pour les pages Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Découvrez comment utiliser les [conventions du fournisseur de modèles de routes et d’applications](xref:mvc/controllers/application-model#conventions) pour contrôler le routage, la découverte et le traitement des pages dans les applications à base de pages Razor.

Quand vous devez configurer des routages de pages personnalisés pour des pages individuelles, configurez le routage vers les pages avec la convention [AddPageRoute](#configure-a-page-route) décrite plus loin dans cette rubrique.

Pour spécifier un itinéraire de page, ajouter des segments d’itinéraire ou ajouter des paramètres à un itinéraire, utilisez la directive de `@page` la page. Pour plus d’informations, voir [itinéraires personnalisés](xref:razor-pages/index#custom-routes).

Il y a des mots réservés qui ne peuvent pas être utilisés comme segments d’itinéraire ou noms de paramètres. Pour plus d’informations, voir [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

| Scénario | L’exemple montre... |
| -------- | --------------------------- |
| [Conventions de modèle](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Ajouter un modèle de route et un en-tête aux pages d’une application. |
| [Conventions d’actions de routage de pages](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Ajouter un modèle de route aux pages d’un dossier et à une page unique. |
| [Conventions d’actions de modèle de page](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtre, expression lambda ou fabrique de filtres)</li></ul> | Ajouter un en-tête dans les pages d’un dossier, ajouter un en-tête dans une page unique et configurer une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) pour ajouter un en-tête dans les pages d’une application. |

Les conventions Razor Pages sont <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> ajoutées <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> et configurées `Startup` à l’aide de la méthode d’extension à la collection de services de la classe. Les exemples de convention suivants sont expliqués plus loin dans cette rubrique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordre d’itinéraire

Itinéraires <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> spécifier un traitement (correspondance de l’itinéraire).

| JSON            | Comportement |
| :--------------: | -------- |
| -1               | L’itinéraire est traité avant que d’autres itinéraires ne soient traités. |
| 0                | L’ordre n’est pas spécifié (valeur par défaut). Ne pas `Order` `Order = null`attribuer ( ) `Order` par défaut l’itinéraire à 0 (zéro) pour le traitement. |
| 1, 2, &hellip; n | Spécifie l’ordre de traitement de l’itinéraire. |

Le traitement des itinéraires est établi par convention :

* Les itinéraires sont traités par ordre séquentiel (-1, 0, 1, 2, &hellip; n).
* Lorsque les itinéraires ont le même `Order`, l’itinéraire le plus spécifique est égalé d’abord suivi par des itinéraires moins spécifiques.
* Lorsque les itinéraires avec le même `Order` nombre et le même nombre de paramètres correspondent à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>une URL de demande, les itinéraires sont traités dans l’ordre qu’ils sont ajoutés à la .

Si possible, évitez de dépendre d’une ordonnance de traitement des itinéraires établie. Généralement, le routage sélectionne l’itinéraire correct avec l’appariement de l’URL. Si vous devez `Order` définir correctement les propriétés d’itinéraire pour les demandes d’itinéraire, le système de routage de l’application est probablement déroutant pour les clients et fragile à entretenir. Cherchez à simplifier le système de routage de l’application. L’application d’échantillon nécessite un ordre explicite de traitement d’itinéraire pour démontrer plusieurs scénarios de routage à l’aide d’une seule application. Toutefois, vous devriez essayer d’éviter `Order` la pratique de définir l’itinéraire dans les applications de production.

Le routage de Razor Pages et celui du contrôleur MVC partagent une implémentation. Des informations sur l’ordre d’itinéraire dans les sujets MVC sont disponibles à [Routing pour les actions de contrôleur: Itinéraires d’attribut de commande](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Conventions de modèle

Ajoutez un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> délégué pour ajouter [des conventions modèles](xref:mvc/controllers/application-model#conventions) qui s’appliquent aux Pages Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Ajouter une convention de modèle de routage à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle d’itinéraire de page.

L’exemple d’application ajoute un modèle de routage `{globalTemplate?}` à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `1`. Cela garantit le comportement de correspondance d’itinéraire suivant dans l’application d’échantillon :

* Un modèle `TheContactPage/{text?}` d’itinéraire pour est ajouté plus tard dans le sujet. L’itinéraire De la Page `null` `Order = 0`De Contact a `{globalTemplate?}` une commande par défaut de (), de sorte qu’il correspond avant le modèle d’itinéraire.
* Un `{aboutTemplate?}` modèle d’itinéraire est ajouté plus tard dans le sujet. Le modèle `{aboutTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand la page About est demandée sur `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 1`) et non `RouteData.Values["aboutTemplate"]` (`Order = 2`) en raison de la définition de la propriété `Order`.
* Un `{otherPagesTemplate?}` modèle d’itinéraire est ajouté plus tard dans le sujet. Le modèle `{otherPagesTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Lorsque n’importe quelle page du dossier *Pages/OtherPages* est `/OtherPages/Page1/RouteDataValue`demandée avec un paramètre d’itinéraire (par`Order = 2`exemple, ), `Order` "RouteDataValue" est chargé dans (`Order = 1`) et non `RouteData.Values["otherPagesTemplate"]` ( ) en raison de la mise en `RouteData.Values["globalTemplate"]` place de la propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Les options De Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Razor, telles que l’ajout, `Startup.ConfigureServices`sont ajoutées lorsque MVC est ajouté à la collection de services en . Pour obtenir un exemple, consultez [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue` et examinez le résultat :

![La page About est demandée avec un segment de routage pour GlobalRouteValue. La page affichée montre que la valeur des données de routage est capturée dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Ajouter une convention de modèle d’application à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle d’application de page.

Pour illustrer cette convention et bien d’autres, plus loin dans cette rubrique, l’exemple d’application inclut une classe `AddHeaderAttribute`. Le constructeur de classe accepte une chaîne `name` et un tableau de chaînes `values`. Ces valeurs sont utilisées dans sa méthode `OnResultExecuting` pour définir un en-tête de réponse. La classe complète est affichée dans la section [Conventions d’actions de modèle de page](#page-model-action-conventions), plus loin dans cette rubrique.

L’exemple d’application utilise la classe `AddHeaderAttribute` pour ajouter un en-tête, `GlobalHeader`, à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que GlobalHeader a été ajouté.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Ajouter une convention de modèle de gestionnaire à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle de gestionnaire de page.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Conventions d’actions de routage de pages

Le fournisseur de modèles d’itinéraire par défaut qui dérive de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> conventions invoque qui sont conçus pour fournir des points d’extensibility pour configurer les itinéraires de page.

### <a name="folder-route-model-convention"></a>Convention de modèle de routage de dossier

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> invoque une action sur le pour toutes les pages sous le dossier spécifié.

L’exemple d’application utilise <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour ajouter un modèle de routage `{otherPagesTemplate?}` aux pages du dossier *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela garantit que le `{globalTemplate?}` modèle pour (fixé `1`plus tôt dans le sujet à ) est donné la priorité pour la position de valeur de données de première route quand une valeur d’itinéraire unique est fournie. Si une page du dossier *Pages/OtherPages* est demandée avec `/OtherPages/Page1/RouteDataValue`une valeur de paramètre d’itinéraire`Order = 1`(par `RouteData.Values["otherPagesTemplate"]` `Order = 2`exemple, ), `Order` "RouteDataValue" est chargé dans ( ) et non ( ) en raison de la mise en `RouteData.Values["globalTemplate"]` place de la propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` et examinez le résultat :

![La page Page1 du dossier OtherPages est demandée avec un segment de routage pour GlobalRouteValue et OtherPagesRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convention de modèle de routage de page

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> invoque une action sur la page pour le nom spécifié.

L’exemple d’application utilise `AddPageRouteModelConvention` pour ajouter un modèle de routage `{aboutTemplate?}` à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela garantit que le `{globalTemplate?}` modèle pour (fixé `1`plus tôt dans le sujet à ) est donné la priorité pour la position de valeur de données de première route quand une valeur d’itinéraire unique est fournie. Si la page About est demandée `/About/RouteDataValue`avec une valeur de paramètre `RouteData.Values["globalTemplate"]` `Order = 1`d’itinéraire `RouteData.Values["aboutTemplate"]` `Order = 2`à , "RouteDataValue" est chargé dans ( ) et non ( ) en raison de la mise en place de la `Order` propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue/AboutRouteValue` et examinez le résultat :

![La page About est demandée avec des segments de routage pour GlobalRouteValue et AboutRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Utilisez un transformateur de paramètres pour personnaliser les itinéraires des pages

Les itinéraires de pages générés par ASP.NET Core peuvent être personnalisés à l’aide d’un transformateur de paramètres. Un transformateur de paramètre implémente `IOutboundParameterTransformer` et transforme la valeur des paramètres. Par exemple, un transformateur de paramètre `SlugifyParameterTransformer` personnalisé transforme la valeur de la route `SubscriptionManagement` en `subscription-management`.

La `PageRouteTransformerConvention` convention de modèle d’itinéraire de page applique un transformateur de paramètres aux segments de dossier et de nom de fichier des itinéraires de page générés automatiquement dans une application. Par exemple, le fichier Razor Pages à */Pages/SubscriptionManagement/ViewAll.cshtml* aurait son itinéraire réécrit de `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`.

`PageRouteTransformerConvention`transforme uniquement les segments générés automatiquement d’un itinéraire de page provenant du dossier Et du nom du fichier Razor Pages. Il ne transforme pas les `@page` segments d’itinéraire ajoutés avec la directive. La convention ne transforme pas <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>non plus les itinéraires ajoutés par .

Le `PageRouteTransformerConvention` est enregistré comme `Startup.ConfigureServices`une option dans :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>Configurer un routage de page

Utilisez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> pour configurer un itinéraire vers une page sur le parcours spécifié de la page. Les liens générés qui pointent vers la page utilisent le routage que vous avez spécifié. `AddPageRoute` utilise `AddPageRouteModelConvention` pour établir le routage.

L’exemple d’application crée un routage vers `/TheContactPage` pour *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La page Contact est également accessible sur `/Contact` via son routage par défaut.

Le routage personnalisé de l’exemple d’application vers la page Contact permet un segment de routage `text` facultatif (`{text?}`). Cette page inclut également ce segment facultatif dans sa directive `@page`, au cas où le visiteur accèderait à la page via le routage `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Notez que l’URL générée pour le lien **Contact** dans la page affichée reflète le routage mis à jour :

![Lien Contact de l’exemple d’application dans la barre de navigation](razor-pages-conventions/_static/contact-link.png)

![Le lien Contact dans la page HTML affichée indique que l’attribut href a la valeur ’/TheContactPage’](razor-pages-conventions/_static/contact-link-source.png)

Visitez la page Contact via son routage ordinaire, `/Contact`, ou via le routage personnalisé, `/TheContactPage`. Si vous indiquez un segment de routage `text` supplémentaire, la page affiche le segment HTML que vous fournissez :

![Exemple dans le navigateur Edge d’un segment de routage ‘text’ facultatif fourni pour ‘TextValue’ dans l’URL. La page affichée montre la valeur du segment ‘text’.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Conventions d’actions de modèle de page

Le fournisseur de modèles <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> de pages par défaut qui implémente invoque des conventions qui sont conçues pour fournir des points d’extensibility pour configurer les modèles de page. Ces conventions sont utiles durant la génération et la modification de scénarios de découverte et de traitement de pages.

Pour les exemples de cette section, l’application d’échantillon utilise une `AddHeaderAttribute` classe, qui est un <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, qui applique un en-tête de réponse :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

À l’aide de conventions, l’exemple montre comment appliquer l’attribut à toutes les pages d’un dossier et à une seule page.

**Convention de modèle d’application de dossier**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> qui invoque une action sur les instances pour toutes les pages sous le dossier spécifié.

L’exemple illustre l’utilisation de `AddFolderApplicationModelConvention` en ajoutant un en-tête, `OtherPagesHeader`, aux pages situées dans le dossier *OtherPages* de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page OtherPages/Page1 montrent que OtherPagesHeader a été ajouté.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convention de modèle d’application de page**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> invoque une action sur la page pour le nom spécifié.

L’exemple illustre l’utilisation de `AddPageApplicationModelConvention` en ajoutant un en-tête, `AboutHeader`, à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que AboutHeader a été ajouté.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurer un filtre**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configure le filtre spécifié pour s’appliquer. Vous pouvez implémenter une classe de filtre. Toutefois, l’exemple d’application montre comment implémenter un filtre dans une expression lambda, laquelle est implémentée en arrière-plan en tant que fabrique qui retourne un filtre :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Le modèle d’application de page est utilisé pour vérifier le chemin relatif des segments qui mènent à la page Page2 dans le dossier *OtherPages*. Si la condition est satisfaite, un en-tête est ajouté. Sinon, `EmptyFilter` est appliqué.

`EmptyFilter` est un [filtre d’action](xref:mvc/controllers/filters#action-filters). Étant donné que les filtres `EmptyFilter` Action sont ignorés par Razor Pages, le n’a aucun effet comme prévu si le chemin ne contient `OtherPages/Page2`pas .

Demandez la page Page2 de l’exemple sur `localhost:5000/OtherPages/Page2` et examinez les en-têtes pour voir le résultat :

![OtherPagesPage2Header est ajouté à la réponse pour Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurer une fabrique de filtres**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configure l’usine spécifiée pour appliquer [des filtres](xref:mvc/controllers/filters) à toutes les pages Razor.

L’exemple d’application illustre l’utilisation d’une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) en ajoutant un en-tête, `FilterFactoryHeader`, et deux valeurs aux pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que deux en-têtes FilterFactoryHeader ont été ajoutés.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtres MVC et filtre de page (IPageFilter)

Les [filtres d’action](xref:mvc/controllers/filters#action-filters) MVC sont ignorés par les pages Razor, car les pages Razor utilisent des méthodes de gestionnaire. Vous pouvez utiliser d’autres types de filtre MVC : [Autorisation](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) et [Résultat](xref:mvc/controllers/filters#result-filters). Pour plus d’informations, consultez la rubrique [Filtres](xref:mvc/controllers/filters).

Le filtre<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>Page ( ) est un filtre qui s’applique aux Pages Razor. Pour plus d’informations, consultez [Méthodes de filtre pour les pages Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Découvrez comment utiliser les [conventions du fournisseur de modèles de routes et d’applications](xref:mvc/controllers/application-model#conventions) pour contrôler le routage, la découverte et le traitement des pages dans les applications à base de pages Razor.

Quand vous devez configurer des routages de pages personnalisés pour des pages individuelles, configurez le routage vers les pages avec la convention [AddPageRoute](#configure-a-page-route) décrite plus loin dans cette rubrique.

Pour spécifier un itinéraire de page, ajouter des segments d’itinéraire ou ajouter des paramètres à un itinéraire, utilisez la directive de `@page` la page. Pour plus d’informations, voir [itinéraires personnalisés](xref:razor-pages/index#custom-routes).

Il y a des mots réservés qui ne peuvent pas être utilisés comme segments d’itinéraire ou noms de paramètres. Pour plus d’informations, voir [Routing: Reserved routing names](xref:fundamentals/routing#reserved-routing-names).

[Afficher ou télécharger le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([comment télécharger](xref:index#how-to-download-a-sample))

| Scénario | L’exemple montre... |
| -------- | --------------------------- |
| [Conventions de modèle](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Ajouter un modèle de route et un en-tête aux pages d’une application. |
| [Conventions d’actions de routage de pages](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Ajouter un modèle de route aux pages d’un dossier et à une page unique. |
| [Conventions d’actions de modèle de page](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (classe de filtre, expression lambda ou fabrique de filtres)</li></ul> | Ajouter un en-tête dans les pages d’un dossier, ajouter un en-tête dans une page unique et configurer une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) pour ajouter un en-tête dans les pages d’une application. |

Les conventions Razor Pages sont <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> ajoutées <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> et configurées `Startup` à l’aide de la méthode d’extension à la collection de services de la classe. Les exemples de convention suivants sont expliqués plus loin dans cette rubrique :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Ordre d’itinéraire

Itinéraires <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> spécifier un traitement (correspondance de l’itinéraire).

| JSON            | Comportement |
| :--------------: | -------- |
| -1               | L’itinéraire est traité avant que d’autres itinéraires ne soient traités. |
| 0                | L’ordre n’est pas spécifié (valeur par défaut). Ne pas `Order` `Order = null`attribuer ( ) `Order` par défaut l’itinéraire à 0 (zéro) pour le traitement. |
| 1, 2, &hellip; n | Spécifie l’ordre de traitement de l’itinéraire. |

Le traitement des itinéraires est établi par convention :

* Les itinéraires sont traités par ordre séquentiel (-1, 0, 1, 2, &hellip; n).
* Lorsque les itinéraires ont le même `Order`, l’itinéraire le plus spécifique est égalé d’abord suivi par des itinéraires moins spécifiques.
* Lorsque les itinéraires avec le même `Order` nombre et le même nombre de paramètres correspondent à <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>une URL de demande, les itinéraires sont traités dans l’ordre qu’ils sont ajoutés à la .

Si possible, évitez de dépendre d’une ordonnance de traitement des itinéraires établie. Généralement, le routage sélectionne l’itinéraire correct avec l’appariement de l’URL. Si vous devez `Order` définir correctement les propriétés d’itinéraire pour les demandes d’itinéraire, le système de routage de l’application est probablement déroutant pour les clients et fragile à entretenir. Cherchez à simplifier le système de routage de l’application. L’application d’échantillon nécessite un ordre explicite de traitement d’itinéraire pour démontrer plusieurs scénarios de routage à l’aide d’une seule application. Toutefois, vous devriez essayer d’éviter `Order` la pratique de définir l’itinéraire dans les applications de production.

Le routage de Razor Pages et celui du contrôleur MVC partagent une implémentation. Des informations sur l’ordre d’itinéraire dans les sujets MVC sont disponibles à [Routing pour les actions de contrôleur: Itinéraires d’attribut de commande](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Conventions de modèle

Ajoutez un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> délégué pour ajouter [des conventions modèles](xref:mvc/controllers/application-model#conventions) qui s’appliquent aux Pages Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Ajouter une convention de modèle de routage à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle d’itinéraire de page.

L’exemple d’application ajoute un modèle de routage `{globalTemplate?}` à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `1`. Cela garantit le comportement de correspondance d’itinéraire suivant dans l’application d’échantillon :

* Un modèle `TheContactPage/{text?}` d’itinéraire pour est ajouté plus tard dans le sujet. L’itinéraire De la Page `null` `Order = 0`De Contact a `{globalTemplate?}` une commande par défaut de (), de sorte qu’il correspond avant le modèle d’itinéraire.
* Un `{aboutTemplate?}` modèle d’itinéraire est ajouté plus tard dans le sujet. Le modèle `{aboutTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Quand la page About est demandée sur `/About/RouteDataValue`, « RouteDataValue » est chargé dans `RouteData.Values["globalTemplate"]` (`Order = 1`) et non `RouteData.Values["aboutTemplate"]` (`Order = 2`) en raison de la définition de la propriété `Order`.
* Un `{otherPagesTemplate?}` modèle d’itinéraire est ajouté plus tard dans le sujet. Le modèle `{otherPagesTemplate?}` reçoit l’ordre `Order` avec la valeur `2`. Lorsque n’importe quelle page du dossier *Pages/OtherPages* est `/OtherPages/Page1/RouteDataValue`demandée avec un paramètre d’itinéraire (par`Order = 2`exemple, ), `Order` "RouteDataValue" est chargé dans (`Order = 1`) et non `RouteData.Values["otherPagesTemplate"]` ( ) en raison de la mise en `RouteData.Values["globalTemplate"]` place de la propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Les options De Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>Razor, telles que l’ajout, `Startup.ConfigureServices`sont ajoutées lorsque MVC est ajouté à la collection de services en . Pour obtenir un exemple, consultez [l’exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue` et examinez le résultat :

![La page About est demandée avec un segment de routage pour GlobalRouteValue. La page affichée montre que la valeur des données de routage est capturée dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Ajouter une convention de modèle d’application à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle d’application de page.

Pour illustrer cette convention et bien d’autres, plus loin dans cette rubrique, l’exemple d’application inclut une classe `AddHeaderAttribute`. Le constructeur de classe accepte une chaîne `name` et un tableau de chaînes `values`. Ces valeurs sont utilisées dans sa méthode `OnResultExecuting` pour définir un en-tête de réponse. La classe complète est affichée dans la section [Conventions d’actions de modèle de page](#page-model-action-conventions), plus loin dans cette rubrique.

L’exemple d’application utilise la classe `AddHeaderAttribute` pour ajouter un en-tête, `GlobalHeader`, à toutes les pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que GlobalHeader a été ajouté.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Ajouter une convention de modèle de gestionnaire à toutes les pages

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> et ajouter <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> une collection d’instances qui sont appliquées lors de la construction du modèle de gestionnaire de page.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Conventions d’actions de routage de pages

Le fournisseur de modèles d’itinéraire par défaut qui dérive de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> conventions invoque qui sont conçus pour fournir des points d’extensibility pour configurer les itinéraires de page.

### <a name="folder-route-model-convention"></a>Convention de modèle de routage de dossier

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> invoque une action sur le pour toutes les pages sous le dossier spécifié.

L’exemple d’application utilise <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> pour ajouter un modèle de routage `{otherPagesTemplate?}` aux pages du dossier *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela garantit que le `{globalTemplate?}` modèle pour (fixé `1`plus tôt dans le sujet à ) est donné la priorité pour la position de valeur de données de première route quand une valeur d’itinéraire unique est fournie. Si une page du dossier *Pages/OtherPages* est demandée avec `/OtherPages/Page1/RouteDataValue`une valeur de paramètre d’itinéraire`Order = 1`(par `RouteData.Values["otherPagesTemplate"]` `Order = 2`exemple, ), `Order` "RouteDataValue" est chargé dans ( ) et non ( ) en raison de la mise en `RouteData.Values["globalTemplate"]` place de la propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` et examinez le résultat :

![La page Page1 du dossier OtherPages est demandée avec un segment de routage pour GlobalRouteValue et OtherPagesRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Convention de modèle de routage de page

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> invoque une action sur la page pour le nom spécifié.

L’exemple d’application utilise `AddPageRouteModelConvention` pour ajouter un modèle de routage `{aboutTemplate?}` à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

La propriété <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> de <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> a la valeur `2`. Cela garantit que le `{globalTemplate?}` modèle pour (fixé `1`plus tôt dans le sujet à ) est donné la priorité pour la position de valeur de données de première route quand une valeur d’itinéraire unique est fournie. Si la page About est demandée `/About/RouteDataValue`avec une valeur de paramètre `RouteData.Values["globalTemplate"]` `Order = 1`d’itinéraire `RouteData.Values["aboutTemplate"]` `Order = 2`à , "RouteDataValue" est chargé dans ( ) et non ( ) en raison de la mise en place de la `Order` propriété.

Dans la mesure du `Order`possible, ne `Order = 0`définissez pas le , ce qui se traduit par . Comptez sur le routage pour sélectionner l’itinéraire correct.

Demandez la page About de l’exemple sur `localhost:5000/About/GlobalRouteValue/AboutRouteValue` et examinez le résultat :

![La page About est demandée avec des segments de routage pour GlobalRouteValue et AboutRouteValue. La page affichée montre que les valeurs des données de routage sont capturées dans la méthode OnGet de la page.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Configurer un routage de page

Utilisez <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> pour configurer un itinéraire vers une page sur le parcours spécifié de la page. Les liens générés qui pointent vers la page utilisent le routage que vous avez spécifié. `AddPageRoute` utilise `AddPageRouteModelConvention` pour établir le routage.

L’exemple d’application crée un routage vers `/TheContactPage` pour *Contact.cshtml* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

La page Contact est également accessible sur `/Contact` via son routage par défaut.

Le routage personnalisé de l’exemple d’application vers la page Contact permet un segment de routage `text` facultatif (`{text?}`). Cette page inclut également ce segment facultatif dans sa directive `@page`, au cas où le visiteur accèderait à la page via le routage `/Contact` :

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Notez que l’URL générée pour le lien **Contact** dans la page affichée reflète le routage mis à jour :

![Lien Contact de l’exemple d’application dans la barre de navigation](razor-pages-conventions/_static/contact-link.png)

![Le lien Contact dans la page HTML affichée indique que l’attribut href a la valeur ’/TheContactPage’](razor-pages-conventions/_static/contact-link-source.png)

Visitez la page Contact via son routage ordinaire, `/Contact`, ou via le routage personnalisé, `/TheContactPage`. Si vous indiquez un segment de routage `text` supplémentaire, la page affiche le segment HTML que vous fournissez :

![Exemple dans le navigateur Edge d’un segment de routage ‘text’ facultatif fourni pour ‘TextValue’ dans l’URL. La page affichée montre la valeur du segment ‘text’.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Conventions d’actions de modèle de page

Le fournisseur de modèles <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> de pages par défaut qui implémente invoque des conventions qui sont conçues pour fournir des points d’extensibility pour configurer les modèles de page. Ces conventions sont utiles durant la génération et la modification de scénarios de découverte et de traitement de pages.

Pour les exemples de cette section, l’application d’échantillon utilise une `AddHeaderAttribute` classe, qui est un <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, qui applique un en-tête de réponse :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

À l’aide de conventions, l’exemple montre comment appliquer l’attribut à toutes les pages d’un dossier et à une seule page.

**Convention de modèle d’application de dossier**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter un <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> qui invoque une action sur les instances pour toutes les pages sous le dossier spécifié.

L’exemple illustre l’utilisation de `AddFolderApplicationModelConvention` en ajoutant un en-tête, `OtherPagesHeader`, aux pages situées dans le dossier *OtherPages* de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Demandez la page Page1 de l’exemple sur `localhost:5000/OtherPages/Page1` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page OtherPages/Page1 montrent que OtherPagesHeader a été ajouté.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Convention de modèle d’application de page**

Utilisez <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> pour créer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> et ajouter un qui <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> invoque une action sur la page pour le nom spécifié.

L’exemple illustre l’utilisation de `AddPageApplicationModelConvention` en ajoutant un en-tête, `AboutHeader`, à la page About :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que AboutHeader a été ajouté.](razor-pages-conventions/_static/about-page-about-header.png)

**Configurer un filtre**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configure le filtre spécifié pour s’appliquer. Vous pouvez implémenter une classe de filtre. Toutefois, l’exemple d’application montre comment implémenter un filtre dans une expression lambda, laquelle est implémentée en arrière-plan en tant que fabrique qui retourne un filtre :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Le modèle d’application de page est utilisé pour vérifier le chemin relatif des segments qui mènent à la page Page2 dans le dossier *OtherPages*. Si la condition est satisfaite, un en-tête est ajouté. Sinon, `EmptyFilter` est appliqué.

`EmptyFilter` est un [filtre d’action](xref:mvc/controllers/filters#action-filters). Étant donné que les filtres `EmptyFilter` Action sont ignorés par Razor Pages, le n’a aucun effet comme prévu si le chemin ne contient `OtherPages/Page2`pas .

Demandez la page Page2 de l’exemple sur `localhost:5000/OtherPages/Page2` et examinez les en-têtes pour voir le résultat :

![OtherPagesPage2Header est ajouté à la réponse pour Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Configurer une fabrique de filtres**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>configure l’usine spécifiée pour appliquer [des filtres](xref:mvc/controllers/filters) à toutes les pages Razor.

L’exemple d’application illustre l’utilisation d’une [fabrique de filtres](xref:mvc/controllers/filters#ifilterfactory) en ajoutant un en-tête, `FilterFactoryHeader`, et deux valeurs aux pages de l’application :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Demandez la page About de l’exemple sur `localhost:5000/About` et examinez les en-têtes pour voir le résultat :

![Les en-têtes de réponse de la page About montrent que deux en-têtes FilterFactoryHeader ont été ajoutés.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtres MVC et filtre de page (IPageFilter)

Les [filtres d’action](xref:mvc/controllers/filters#action-filters) MVC sont ignorés par les pages Razor, car les pages Razor utilisent des méthodes de gestionnaire. Vous pouvez utiliser d’autres types de filtre MVC : [Autorisation](xref:mvc/controllers/filters#authorization-filters), [Exception](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) et [Résultat](xref:mvc/controllers/filters#result-filters). Pour plus d’informations, consultez la rubrique [Filtres](xref:mvc/controllers/filters).

Le filtre<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>Page ( ) est un filtre qui s’applique aux Pages Razor. Pour plus d’informations, consultez [Méthodes de filtre pour les pages Razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
