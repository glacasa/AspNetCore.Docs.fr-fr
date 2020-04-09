---
title: Présentation des pages Razor dans ASP.NET Core
author: Rick-Anderson
description: Découvrez comment les pages Razor dans ASP.NET Core permettent de développer des scénarios orientés page de façon plus simple et plus productive qu’avec MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 42ffb0d4d2e49663dd53ffeee5d9fa2a931ee5b7
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667579"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a>Présentation des pages Razor dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)

Razor Pages peut faciliter et rendre les scénarios axés sur le codage axés sur les pages que l’utilisation de contrôleurs et de vues.

Si vous cherchez un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ce document fournit une introduction aux pages Razor. Il ne s’agit pas d’un didacticiel pas à pas. Si certaines sections vous semblent trop techniques, consultez [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start). Pour une vue d’ensemble d’ASP.NET Core, consultez [Introduction à ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Créer un projet Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour obtenir des instructions sur la création d’un projet Razor Pages, consultez [Bien démarrer avec Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez `dotnet new webapp` à partir de la ligne de commande.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Exécutez `dotnet new webapp` à partir de la ligne de commande.

Ouvrez le fichier *.csproj* généré à partir de Visual Studio pour Mac.

---

## <a name="razor-pages"></a>Pages Razor

La fonctionnalité Pages Razor est activée dans *Startup.cs* :

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Considérez une page de base : <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Le code précédent ressemble beaucoup à un [fichier vue Razor](xref:tutorials/first-mvc-app/adding-view) utilisé dans une application ASP.NET Core avec des contrôleurs et des vues. Ce qui le [`@page`](xref:mvc/views/razor#page) rend différent, c’est la directive. `@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur. `@page` doit être la première directive Razor sur une page. `@page`affecte le comportement d’autres constructions [Razor.](xref:mvc/views/razor) Les noms de fichiers Razor Pages ont un suffixe *.cshtml.*

Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants. Le fichier *Pages/Index2.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Le modèle de page *Pages/Index2.cshtml.cs* :

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Par convention, le fichier de classe `PageModel` a le même nom que le fichier Page Razor, avec *.cs* en plus. Par exemple, la page Razor précédente est *Pages/Index2.cshtml*. Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.

Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers. Le tableau suivant montre un chemin Page Razor et l’URL correspondante :

| Nom et chemin de fichier               | URL correspondante |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` ou `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` ou `/Store/Index` |

Remarques :

* Le runtime recherche les fichiers Pages Razor dans le dossier *Pages* par défaut.
* `Index` est la page par défaut quand une URL n’inclut pas de page.

## <a name="write-a-basic-form"></a>Écrire un formulaire de base

Les pages Razor sont conçues pour que les modèles courants utilisés avec les navigateurs web soient faciles à implémenter lors de la création d’une application. La [liaison de modèle](xref:mvc/models/model-binding), les [Tag Helpers](xref:mvc/views/tag-helpers/intro)et les assistances HTML *fonctionnent tous* avec les propriétés définies dans une classe Page Razor. Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :

Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Le modèle de données :

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Le contexte de la base de données :

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Le fichier vue *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Le modèle de page *Pages/Create.cshtml.cs* :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.

La classe `PageModel` permet de séparer la logique d’une page de sa présentation. Elle définit des gestionnaires de page pour les demandes envoyées à la page et les données utilisées pour l’afficher. Cette séparation permet :

* Gestion des dépendances de page par [injection de dépendance](xref:fundamentals/dependency-injection).
* [Test unitaire](xref:test/razor-pages-tests)

La page a une  *méthode de gestionnaire*`OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire). Des méthodes de manutention pour n’importe quel verbe HTTP peuvent être ajoutées. Les gestionnaires les plus courants sont :

* `OnGet` pour initialiser l’état nécessaire pour la page. Dans le code `OnGet` précédent, la méthode affiche la page de rasoir *CreateModel.cshtml.*
* `OnPost` pour gérer les envois de formulaire.

Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones. Le code précédent est typique des pages Razor.

Si vous connaissez ASP.NET applications à l’aide de contrôleurs et de vues :

* Le `OnPostAsync` code dans l’exemple précédent ressemble au code de contrôle typique.
* La plupart des primitifs MVC comme la [liaison de modèle,](xref:mvc/models/model-binding) [la validation,](xref:mvc/models/validation)et les résultats d’action fonctionnent de la même façon avec les contrôleurs et les pages de rasoir. 

La méthode `OnPostAsync` précédente :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Le flux de base de `OnPostAsync` :

Recherchez les erreurs de validation.

* S’il n’y a aucune erreur, enregistrez les données et redirigez.
* S’il y a des erreurs, réaffichez la page avec des messages de validation. Dans de nombreux cas, les erreurs de validation sont détectées sur le client et jamais envoyées au serveur.

Le fichier vue *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Le HTML rendu de *Pages/Create.cshtml*:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

Dans le code précédent, l’affichage du formulaire:

* Avec des données valides :

  * La `OnPostAsync` méthode du <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> gestionnaire appelle la méthode de l’aide. `RedirectToPage` retourne une instance de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>. `RedirectToPage`:

    * Est un résultat d’action.
    * Est similaire `RedirectToAction` `RedirectToRoute` ou (utilisé dans les contrôleurs et les vues).
    * Est personnalisé pour les pages. Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`). `RedirectToPage`est détaillé dans la génération URL pour la section [Pages.](#url_gen)

* Avec des erreurs de validation qui sont transmises au serveur :

  * La `OnPostAsync` méthode du <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> gestionnaire appelle la méthode de l’aide. `Page` retourne une instance de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>. Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`. `PageResult`est le type de retour par défaut pour une méthode de gestionnaire. Une méthode de gestionnaire qui retourne `void` restitue la page.
  * Dans l’exemple précédent, l’affichage du formulaire sans valeur donne à [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) retour faux. Dans cet échantillon, aucune erreur de validation n’est affichée sur le client. La remise d’erreurs de validation est couverte plus tard dans ce document.

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Avec les erreurs de validation détectées par la validation côté client :

  * Les données ne sont **pas** affichées sur le serveur.
  * La validation côté client est expliquée plus tard dans ce document.

La `Customer` propriété [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) utilise l’attribut pour opter pour la liaison de modèle :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]`ne doit **pas** être utilisé sur des modèles contenant des propriétés qui ne devraient pas être modifiées par le client. Pour plus d’informations, voir [Overposting](xref:data/ef-rp/crud#overposting).

Par défaut, Razor Pages lie les propriétés seulement avec des verbes non-`GET`. La liaison vers les propriétés élimine la nécessité d’écrire du code pour convertir les données HTTP au type de modèle. Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name">`) et accepter l’entrée.

[!INCLUDE[](~/includes/bind-get.md)]

Examen du fichier De vue *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* Dans le code précédent, l’aide [à l’étiquette d’entrée](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` lie l’élément HTML `<input>` à l’expression du `Customer.Name` modèle.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available)rend Tag Helpers disponible.

### <a name="the-home-page"></a>La page d’accueil

*Index.cshtml* est la page d’accueil:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

La classe `PageModel` associée (*Index.cshtml.cs*) :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Le fichier *Index.cshtml* contient la balisage suivante :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

`<a /a>` [L’aide d’étiquette d’ancrage](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a utilisé l’attribut `asp-route-{value}` pour générer un lien vers la page Modifier. Le lien contient des données d’itinéraire avec l’ID de contact. Par exemple : `https://localhost:5001/Edit/1`. Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor.

Le fichier *Index.cshtml* contient une balisage pour créer un bouton de suppression pour chaque contact client :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Le HTML rendu:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Lorsque le bouton de suppression est rendu en HTML, son [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) inclut des paramètres pour :

* L’ID de contact `asp-route-id` client, spécifié par l’attribut.
* Le `handler`, spécifié `asp-page-handler` par l’attribut.

Quand le bouton est sélectionné, une demande `POST` de forumaire est envoyée au serveur. Par convention, le nom de la méthode de gestionnaire est sélectionné en fonction de la valeur du paramètre `handler` conformément au schéma `OnPost[handler]Async`.

Étant donné que le `handler` est `delete` dans cet exemple, la méthode de gestionnaire `OnPostDeleteAsync` est utilisée pour traiter la demande `POST`. Si `asp-page-handler` est défini sur une autre valeur, comme `remove`, une méthode de gestionnaire avec le nom `OnPostRemoveAsync` est sélectionnée.

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

La méthode `OnPostDeleteAsync` :

* Obtient `id` le de la chaîne de requête.
* Interroge la base de données pour le contact client avec `FindAsync`.
* Si le contact client est trouvé, il est supprimé et la base de données est mise à jour.
* Appelle <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pour rediriger vers la page Index racine (`/Index`).

### <a name="the-editcshtml-file"></a>Le fichier Edit.cshtml

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

La première ligne contient la directive `@page "{id:int}"`. La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`. Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable). Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Le *fichier Edit.cshtml.cs* :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Validation

Règles de validation:

* Sont spécifiés de façon déclarative dans la classe modèle.
* Sont appliqués partout dans l’application.

L’espace <xref:System.ComponentModel.DataAnnotations> de nom fournit un ensemble d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou une propriété. DataAnnotations contient également des [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attributs de formatage comme celui qui aident au formatage et ne fournissent aucune validation.

Considérez `Customer` le modèle:

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Utilisation du fichier de vue *Create.cshtml* suivant :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Le code précédent :

* Inclut les scripts de validation jQuery et jQuery.
* Utilise `<div />` les `<span />` [Helpers](xref:mvc/views/tag-helpers/intro) et Tag pour activer :

  * Validation côté client.
  * Rendu d’erreur de validation.

* Génère le code HTML suivant :

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

L’affichage du formulaire Créer sans valeur de nom affiche le message d’erreur «Le champ de nom est nécessaire." sur le formulaire. Si JavaScript est activé sur le client, le navigateur affiche l’erreur sans l’afficher sur le serveur.

L’attribut `[StringLength(10)]` `data-val-length-max="10"` génère sur le HTML rendu. `data-val-length-max`empêche les navigateurs d’entrer plus que la longueur maximale spécifiée. Si un outil tel que [Fiddler](https://www.telerik.com/fiddler) est utilisé pour modifier et rejouer le message :

* Avec le nom plus long que 10.
* Le message d’erreur "Le nom de champ doit être une chaîne avec une longueur maximale de 10." » est renvoyé.

Considérez `Movie` le modèle suivant :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Les attributs de validation spécifient le comportement à appliquer sur les propriétés du modèle à laquelle ils sont appliqués :

* Les `Required` `MinimumLength` attributs et les attributs indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer dans l’espace blanc pour satisfaire cette validation.
* L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés. Dans le code précédent, « Genre » :

  * Doit utiliser seulement des lettres.
  * La première lettre doit être une majuscule. Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.

* L’expression `RegularExpression` « Rating » :

  * Nécessite que le premier caractère soit une lettre majuscule.
  * Permet des caractères et des nombres spéciaux dans les espaces suivants. « PG-13 » est valide pour une évaluation, mais échoue pour un « Genre ».

* L’attribut `Range` contraint une valeur à une plage spécifiée.
* L’attribut `StringLength` définit la longueur maximale d’une propriété à cordes, et d’option sa longueur minimale.
* Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.

La page Créer `Movie` pour le modèle affiche des erreurs avec des valeurs invalides :

![Formulaire de vue Movie avec plusieurs erreurs de validation jQuery côté client](~/tutorials/razor-pages/validation/_static/val.png)

Pour plus d'informations, consultez les pages suivantes :

* [Ajouter la validation à l’application Movie](xref:tutorials/razor-pages/validation)
* [Validation du modèle dans ASP.NET Core](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Gérer les requêtes HEAD avec un gestionnaire OnGet de secours

`HEAD`les demandes permettent de récupérer les en-têtes pour une ressource spécifique. Contrairement aux requêtes `GET`, les requêtes `HEAD` ne retournent pas un corps de réponse.

En règle générale, un gestionnaire `OnHead` est créé et appelé pour les requêtes `HEAD` :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages revient `OnGet` à appeler `OnHead` le gestionnaire si aucun gestionnaire n’est défini.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF et pages Razor

Les pages razor sont protégées par [la validation Antiforgery](xref:security/anti-request-forgery). Le [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte des jetons antiforgery dans des éléments de forme HTML.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Utilisation de dispositions, partiels, modèles et Tag Helpers avec les pages Razor

Les pages Razor fonctionnent avec toutes les fonctionnalités du moteur de vue Razor. Les mises en page, les partielles, les modèles, tag Helpers, *_ViewStart.cshtml,* et *_ViewImports.cshtml* fonctionnent de la même manière qu’ils le font pour les vues classiques razor.

Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.

Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/Shared/_Layout.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

La [mise en page](xref:mvc/views/layout):

* Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).
* Importe des structures HTML telles que JavaScript et les feuilles de style.
* Le contenu de la page `@RenderBody()` Razor est rendu là où on l’appelle.

Pour plus d’informations, voir [page de mise en page](xref:mvc/views/layout).

La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

La disposition est dans le dossier *Pages/Shared*. Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active. Une disposition dans le dossier *Pages/Shared* peut être utilisée à partir de n’importe quelle page Razor sous le dossier *Pages*.

Le fichier de disposition doit être placé dans le dossier *Pages/Shared*.

Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*. *Views/Shared* est un modèle de vues MVC. Les pages Razor sont censées s’appuyer sur la hiérarchie des dossiers, pas sur les conventions de chemins.

La recherche de vue à partir d’une page Razor inclut le dossier *Pages*. Les mises en page, les modèles et les partielles utilisés avec les contrôleurs MVC et les vues de Razor conventionnelles *fonctionnent simplement*.

Ajoutez un fichier *Pages/_ViewImports.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` est expliqué plus loin dans le didacticiel. La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.

<a name="namespace"></a>

La `@namespace` directive fixée sur une page :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La `@namespace` directive définit l’espace de nom pour la page. La directive `@model` n’a pas besoin d’inclure l’espace de noms.

Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml*, l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`. Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.

Par exemple, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

L’espace de noms généré pour la page Razor *Pages/Customers/Edit.cshtml* est identique à la classe `PageModel`.

`@namespace` *fonctionne également avec les vues Razor classiques.*

Considérez le fichier De vue *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Le fichier de vue *Pages/Create.cshtml* mis à jour avec *_ViewImports.cshtml* et le fichier de mise en page précédent :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

Dans le code précédent, le *_ViewImports.cshtml* importé le namespace et Tag Helpers. Le fichier de mise en page a importé les fichiers JavaScript.

Le [projet de démarrage de pages Razor](#rpvs17) contient *Pages/_ValidationScriptsPartial.cshtml*, qui connecte la validation côté client.

Pour plus d'informations sur les affichages partiels, consultez <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Génération d’URL pour les pages

La page `Create`, illustrée précédemment, utilise `RedirectToPage` :

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

L’application a la structure de fichiers/dossiers suivante :

* */Pages*

  * *Index.cshtml*
  * *Privacy.cshtml (en anglais)*
  * */Clients*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Les *pages Pages/Clients/Create.cshtml* et *Pages/Customers/Edit.cshtml* pages rediriger vers *Pages/Customers/Index.cshtml* après le succès. La `./Index` chaîne est un nom de page relatif utilisé pour accéder à la page précédente. Il est utilisé pour générer des URL à la page *Pages/Clients/Index.cshtml.* Par exemple :

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Le nom `/Index` absolu de la page est utilisé pour générer des URL sur la page *Pages/Index.cshtml.* Par exemple :

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Le nom de la page est le chemin de la page à partir du dossier racine */Pages* avec un `/` devant (par exemple, `/Index`). Les échantillons de génération d’URL précédents offrent des options améliorées et des capacités fonctionnelles sur le codage dur d’une URL. La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.

La génération d’URL pour les pages prend en charge les noms relatifs. Le tableau suivant indique quelle page `RedirectToPage` Index est sélectionnée à l’aide de paramètres différents dans *Pages/Customers/Create.cshtml*.

| RedirectToPage(x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")`, `RedirectToPage("../Index")` et sont *des noms relatifs*. Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.

La liaison de nom relatif est utile lors de la création de sites avec une structure complexe. Lorsque des noms relatifs sont utilisés pour relier les pages d’un dossier :

* Le changement de nom d’un dossier ne brise pas les liens relatifs.
* Les liens ne sont pas cassés parce qu’ils n’incluent pas le nom du dossier.

Pour rediriger vers une page située dans une autre [Zone](xref:mvc/controllers/areas), spécifiez la zone :

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Pour plus d’informations, consultez <xref:mvc/controllers/areas> et <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Attribut ViewData

Les données peuvent être <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>transmises à une page avec . Propriétés `[ViewData]` avec l’attribut ont leurs <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>valeurs stockées et chargées à partir de la .

Dans l’exemple `AboutModel` suivant, `[ViewData]` l’attribut s’applique à la `Title` propriété :

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Dans la page À propos de, accédez à la propriété `Title` en tant que propriété de modèle :

```cshtml
<h1>@Model.Title</h1>
```

Dans la disposition, le titre est lu à partir du dictionnaire ViewData :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core expose le <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>. Cette propriété stocke les données jusqu’à ce qu’elles soient lues. Vous pouvez utiliser les méthodes <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> et <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> pour examiner les données sans suppression. `TempData`est utile pour la redirection, lorsque des données sont nécessaires pour plus d’une seule demande.

Le code suivant définit la valeur de `Message` à l’aide de `TempData` :

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Le modèle de page *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Pour plus d’informations, voir [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Plusieurs gestionnaires par page

La page suivante génère un balisage pour deux gestionnaires en utilisant le Tag Helper `asp-page-handler` :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente. L’attribut `asp-page-handler` est un complément de `asp-page`. `asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page. `asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.

Le modèle de page :

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Le code précédent utilise des *méthodes de gestionnaire nommées*. Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant). Dans l’exemple précédent, les méthodes de page sont OnPost**JoinList**Async et OnPost**JoinListUC**Async. Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Itinéraires personnalisés

Utilisez la directive `@page` pour :

* Spécifier une route personnalisée vers une page. Par exemple, la route vers la page À propos peut être définie sur `/Some/Other/Path` avec `@page "/Some/Other/Path"`.
* Ajouter des segments à la route par défaut d’une page. Par exemple, un segment « item » peut être ajouté à la route par défaut d’une page avec `@page "item"`.
* Ajouter des paramètres à la route par défaut d’une page. Par exemple, un paramètre d’ID, `id`, peut être nécessaire pour une page avec `@page "{id}"`.

Un chemin relatif racine désigné par un tilde (`~`) au début du chemin est pris en charge. Par exemple, `@page "~/Some/Other/Path"` est identique à `@page "/Some/Other/Path"`.

Si vous n’aimez pas `?handler=JoinList` la chaîne de requête dans l’URL, modifiez l’itinéraire pour mettre le nom du gestionnaire dans la partie de chemin de l’URL. L’itinéraire peut être personnalisé en ajoutant un modèle `@page` d’itinéraire inclus dans des citations doubles après la directive.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH/JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.

## <a name="advanced-configuration-and-settings"></a>Configuration et paramètres avancés

La configuration et les paramètres dans les sections suivantes ne sont pas requis par la plupart des applications.

Pour configurer les options <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>avancées, utilisez la méthode d’extension :

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> le pour définir l’annuaire racine pour les pages, ou ajouter des conventions de modèle d’application pour les pages. Pour plus d’informations sur les conventions, voir [les conventions d’autorisation de Razor Pages](xref:security/authorization/razor-pages-authorization).

Pour précomposer les vues, voir [la compilation Razor view](xref:mvc/views/view-compilation).

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Spécifier que les pages Razor se trouvent à la racine du contenu

Par défaut, les pages Razor sont associées à la racine */Pages*. Ajoutez <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> à préciser que vos Pages Razor<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>sont à la racine du [contenu](xref:fundamentals/index#content-root) ( ) de l’application :

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Spécifier que les pages Razor se trouvent dans un répertoire racine personnalisé

Ajoutez <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> à préciser que Razor Pages est à un répertoire racine personnalisé dans l’application (fournir un chemin relatif):

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Ressources supplémentaires

* Voir [Get started avec Razor Pages](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction
* [Télécharger ou afficher le code de l’échantillon](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)

Les pages Razor constituent un nouvel aspect d’ASP.NET Core MVC qui permet de développer des scénarios orientés page de façon plus simple et plus productive.

Si vous cherchez un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).

Ce document fournit une introduction aux pages Razor. Il ne s’agit pas d’un didacticiel pas à pas. Si certaines sections vous semblent trop techniques, consultez [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start). Pour une vue d’ensemble d’ASP.NET Core, consultez [Introduction à ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Prérequis

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a>Créer un projet Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Pour obtenir des instructions sur la création d’un projet Razor Pages, consultez [Bien démarrer avec Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Exécutez `dotnet new webapp` à partir de la ligne de commande.

Ouvrez le fichier *.csproj* généré à partir de Visual Studio pour Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez `dotnet new webapp` à partir de la ligne de commande.

---

## <a name="razor-pages"></a>Pages Razor

La fonctionnalité Pages Razor est activée dans *Startup.cs* :

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Considérez une page de base : <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Le code précédent ressemble beaucoup à un [fichier vue Razor](xref:tutorials/first-mvc-app/adding-view) utilisé dans une application ASP.NET Core avec des contrôleurs et des vues. Ce qui le rend différent est la directive `@page`. `@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur. `@page` doit être la première directive Razor sur une page. `@page` affecte le comportement d’autres constructions Razor.

Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants. Le fichier *Pages/Index2.cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Le modèle de page *Pages/Index2.cshtml.cs* :

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Par convention, le fichier de classe `PageModel` a le même nom que le fichier Page Razor, avec *.cs* en plus. Par exemple, la page Razor précédente est *Pages/Index2.cshtml*. Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.

Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers. Le tableau suivant montre un chemin Page Razor et l’URL correspondante :

| Nom et chemin de fichier               | URL correspondante |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` ou `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` ou `/Store/Index` |

Remarques :

* Le runtime recherche les fichiers Pages Razor dans le dossier *Pages* par défaut.
* `Index` est la page par défaut quand une URL n’inclut pas de page.

## <a name="write-a-basic-form"></a>Écrire un formulaire de base

Les pages Razor sont conçues pour que les modèles courants utilisés avec les navigateurs web soient faciles à implémenter lors de la création d’une application. La [liaison de modèle](xref:mvc/models/model-binding), les [Tag Helpers](xref:mvc/views/tag-helpers/intro)et les assistances HTML *fonctionnent tous* avec les propriétés définies dans une classe Page Razor. Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :

Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Le modèle de données :

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Le contexte de la base de données :

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Le fichier vue *Pages/Create.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Le modèle de page *Pages/Create.cshtml.cs* :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.

La classe `PageModel` permet de séparer la logique d’une page de sa présentation. Elle définit des gestionnaires de page pour les demandes envoyées à la page et les données utilisées pour l’afficher. Cette séparation permet :

* Gestion des dépendances de page par [injection de dépendance](xref:fundamentals/dependency-injection).
* [Unité testant](xref:test/razor-pages-tests) les pages.

La page a une  *méthode de gestionnaire*`OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire). Vous pouvez ajouter des méthodes de gestionnaire pour n’importe quel verbe HTTP. Les gestionnaires les plus courants sont :

* `OnGet` pour initialiser l’état nécessaire pour la page. Exemple [OnGet](#OnGet).
* `OnPost` pour gérer les envois de formulaire.

Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones. Le code précédent est typique des pages Razor.

Si vous connaissez ASP.NET applications à l’aide de contrôleurs et de vues :

* Le `OnPostAsync` code dans l’exemple précédent ressemble au code de contrôle typique.
* La plupart des primitifs MVC comme [la liaison de modèle,](xref:mvc/models/model-binding) [la validation,](xref:mvc/models/validation) [la validation,](xref:mvc/models/validation)et les résultats d’action sont partagés.

La méthode `OnPostAsync` précédente :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Le flux de base de `OnPostAsync` :

Recherchez les erreurs de validation.

* S’il n’y a aucune erreur, enregistrez les données et redirigez.
* S’il y a des erreurs, réaffichez la page avec des messages de validation. La validation côté client est identique à celle des applications ASP.NET Core MVC traditionnelles. Dans de nombreux cas, les erreurs de validation sont détectées sur le client et jamais envoyées au serveur.

Quand les données sont entrées correctement, la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `RedirectToPage` pour retourner une instance de `RedirectToPageResult`. `RedirectToPage` est un nouveau résultat d’action, semblable à `RedirectToAction` ou `RedirectToRoute`, mais personnalisé pour les pages. Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`). `RedirectToPage`est détaillé dans la génération URL pour la section [Pages.](#url_gen)

Quand le formulaire envoyé comporte des erreurs de validation (qui sont passées au serveur), la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `Page`. `Page` retourne une instance de `PageResult`. Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`. `PageResult`est le type de retour par défaut pour une méthode de gestionnaire. Une méthode de gestionnaire qui retourne `void` restitue la page.

La propriété `Customer` utilise l’attribut `[BindProperty]` pour accepter la liaison de modèle.

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Par défaut, Razor Pages lie les propriétés seulement avec des verbes non-`GET`. La liaison aux propriétés peut réduire la quantité de code à écrire. Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name">`) et accepter l’entrée.

[!INCLUDE[](~/includes/bind-get.md)]

La page d’accueil (*Index.cshtml*) :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

La classe `PageModel` associée (*Index.cshtml.cs*) :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Le fichier *Index.cshtml* contient le balisage suivant pour créer un lien d’édition pour chaque contact :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

`<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [L’aide d’étiquette d’ancrage](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a utilisé l’attribut `asp-route-{value}` pour générer un lien vers la page Modifier. Le lien contient des données d’itinéraire avec l’ID de contact. Par exemple : `https://localhost:5001/Edit/1`. Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les fichiers Razor. Les Tag Helpers sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Le fichier *Pages/Edit.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

La première ligne contient la directive `@page "{id:int}"`. La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`. Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable). Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :

 ```cshtml
@page "{id:int?}"
```

Le fichier *Pages/Edit.cshtml.cs* :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Le fichier *Index.cshtml* contient également le balisage pour créer un bouton Supprimer pour chaque contact client :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Lorsque le bouton Supprimer est rendu en HTML, son `formaction` comprend des paramètres pour :

* L’ID du contact client spécifié par l’attribut `asp-route-id`.
* Le `handler` spécifié par l’attribut `asp-page-handler`.

Voici un exemple de bouton Supprimer rendu avec un ID de contact client de `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Quand le bouton est sélectionné, une demande `POST` de forumaire est envoyée au serveur. Par convention, le nom de la méthode de gestionnaire est sélectionné en fonction de la valeur du paramètre `handler` conformément au schéma `OnPost[handler]Async`.

Étant donné que le `handler` est `delete` dans cet exemple, la méthode de gestionnaire `OnPostDeleteAsync` est utilisée pour traiter la demande `POST`. Si `asp-page-handler` est défini sur une autre valeur, comme `remove`, une méthode de gestionnaire avec le nom `OnPostRemoveAsync` est sélectionnée. Le code suivant `OnPostDeleteAsync` montre le gestionnaire :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

La méthode `OnPostDeleteAsync` :

* Accepte l’`id` de la chaîne de requête. Si la directive de page *Index.cshtml* contenait la contrainte `"{id:int?}"`de routage, `id` proviendrait des données d’itinéraire. Les données `id` d’itinéraire pour est `https://localhost:5001/Customers/2`spécifiée dans l’URI tels que .
* Interroge la base de données pour le contact client avec `FindAsync`.
* Si le contact client est trouvé, il est supprimé de la liste des contacts client. La base de données est mise à jour.
* Appelle `RedirectToPage` pour rediriger vers la page Index racine (`/Index`).

## <a name="mark-page-properties-as-required"></a>Marquer les propriétés de page comme Required

Propriétés `PageModel` sur un peut être marqué avec l’attribut [requis:](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Pour plus d’informations, consultez [Validation de modèle](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Gérer les requêtes HEAD avec un gestionnaire OnGet de secours

Les requêtes `HEAD` vous permettent de récupérer les en-têtes pour une ressource spécifique. Contrairement aux requêtes `GET`, les requêtes `HEAD` ne retournent pas un corps de réponse.

En règle générale, un gestionnaire `OnHead` est créé et appelé pour les requêtes `HEAD` : 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

Dans ASP.NET Core 2.1 ou ultérieur, Razor Pages se rabat sur un appel du gestionnaire `OnGet` si aucun gestionnaire `OnHead` n’est défini. Ce comportement est activé par l’appel à [SetCompatibilityVersion](xref:mvc/compatibility-version) dans `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Les modèles par défaut génèrent l'appel `SetCompatibilityVersion` dans ASP.NET Core 2.1 et 2.2. `SetCompatibilityVersion` définit efficacement l’option Pages Razor `AllowMappingHeadRequestsToGetHandler` sur `true`.

Au lieu d’adhérer à tous les comportements avec `SetCompatibilityVersion`, vous pouvez adhérer explicitement à des comportements *spécifiques*. Le code suivant adhère à l’autorisation de mappage des requêtes `HEAD` au gestionnaire `OnGet` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a>XSRF/CSRF et pages Razor

Vous n’avez aucun code à écrire pour la [validation anti-contrefaçon](xref:security/anti-request-forgery). La validation et la génération de jetons anti-contrefaçon sont automatiquement incluses dans les pages Razor.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a>Utilisation de dispositions, partiels, modèles et Tag Helpers avec les pages Razor

Les pages Razor fonctionnent avec toutes les fonctionnalités du moteur de vue Razor. Les dispositions, partiels, modèles, Tag Helpers, *_ViewStart.cshtml* et *_ViewImports.cshtml* fonctionnent de la même façon que pour les vues Razor classiques.

Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.

Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/Shared/_Layout.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

La [mise en page](xref:mvc/views/layout):

* Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).
* Importe des structures HTML telles que JavaScript et les feuilles de style.

Pour plus d’informations, consultez [Page de disposition](xref:mvc/views/layout).

La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

La disposition est dans le dossier *Pages/Shared*. Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active. Une disposition dans le dossier *Pages/Shared* peut être utilisée à partir de n’importe quelle page Razor sous le dossier *Pages*.

Le fichier de disposition doit être placé dans le dossier *Pages/Shared*.

Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*. *Views/Shared* est un modèle de vues MVC. Les pages Razor sont censées s’appuyer sur la hiérarchie des dossiers, pas sur les conventions de chemins.

La recherche de vue à partir d’une page Razor inclut le dossier *Pages*. Les dispositions, modèles et partiels que vous utilisez avec les contrôleurs MVC et les vues Razor conventionnelles *fonctionnent simplement*.

Ajoutez un fichier *Pages/_ViewImports.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` est expliqué plus loin dans le didacticiel. La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.

<a name="namespace"></a>

Quand la directive `@namespace` est utilisée explicitement sur une page :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

La directive définit l’espace de noms pour la page. La directive `@model` n’a pas besoin d’inclure l’espace de noms.

Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml*, l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`. Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.

Par exemple, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

L’espace de noms généré pour la page Razor *Pages/Customers/Edit.cshtml* est identique à la classe `PageModel`.

`@namespace` *fonctionne également avec les vues Razor classiques.*

Le fichier de vue *Pages/Create.cshtml* d’origine :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Le fichier vue *Pages/Create.cshtml* mis à jour :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

Le [projet de démarrage de pages Razor](#rpvs17) contient *Pages/_ValidationScriptsPartial.cshtml*, qui connecte la validation côté client.

Pour plus d'informations sur les affichages partiels, consultez <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>Génération d’URL pour les pages

La page `Create`, illustrée précédemment, utilise `RedirectToPage` :

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

L’application a la structure de fichiers/dossiers suivante :

* */Pages*

  * *Index.cshtml*
  * */Clients*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Une fois l’opération réussie, les pages *Pages/Customers/Create.cshtml* et *Pages/Customers/Edit.cshtml* redirigent vers *Pages/Index.cshtml*. La chaîne `/Index` fait partie de l’URI pour accéder à la page précédente. La chaîne `/Index` peut être utilisée pour générer l’URI de la page *Pages/Index.cshtml*. Par exemple :

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Le nom de la page est le chemin de la page à partir du dossier racine */Pages* avec un `/` devant (par exemple, `/Index`). Les exemples de génération d’URL précédents offrent des options améliorées et des capacités fonctionnelles sur le codage en dur d’une URL. La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.

La génération d’URL pour les pages prend en charge les noms relatifs. Le tableau suivant montre la page Index sélectionnée avec différents paramètres `RedirectToPage` à partir de *Pages/Customers/Create.cshtml* :

| RedirectToPage(x)| Page |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")`, `RedirectToPage("../Index")` et sont *des noms relatifs*. Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

La liaison de nom relatif est utile lors de la création de sites avec une structure complexe. Si vous utilisez des noms relatifs pour établir une liaison entre les pages d’un dossier, vous pouvez renommer ce dossier. Tous les liens fonctionneront encore (car ils n’incluent pas le nom du dossier).

Pour rediriger vers une page située dans une autre [Zone](xref:mvc/controllers/areas), spécifiez la zone :

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Pour plus d’informations, consultez <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Attribut ViewData

Les données peuvent être passées à une page avec [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute). Propriétés sur les contrôleurs `[ViewData]` ou les modèles Razor Page avec l’attribut ont leurs valeurs stockées et chargées à partir de la [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).

Dans l’exemple `AboutModel` suivant, `Title` le `[ViewData]`contient une propriété marquée avec . La propriété `Title` a pour valeur le titre de la page À propos de :

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Dans la page À propos de, accédez à la propriété `Title` en tant que propriété de modèle :

```cshtml
<h1>@Model.Title</h1>
```

Dans la disposition, le titre est lu à partir du dictionnaire ViewData :

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core expose la propriété [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) sur un [contrôleur](/dotnet/api/microsoft.aspnetcore.mvc.controller). Cette propriété stocke les données jusqu’à ce qu’elles soient lues. Vous pouvez utiliser les méthodes `Keep` et `Peek` pour examiner les données sans suppression. `TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.

Le code suivant définit la valeur de `Message` à l’aide de `TempData` :

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Le modèle de page *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.

```csharp
[TempData]
public string Message { get; set; }
```

Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Plusieurs gestionnaires par page

La page suivante génère un balisage pour deux gestionnaires en utilisant le Tag Helper `asp-page-handler` :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente. L’attribut `asp-page-handler` est un complément de `asp-page`. `asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page. `asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.

Le modèle de page :

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Le code précédent utilise des *méthodes de gestionnaire nommées*. Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant). Dans l’exemple précédent, les méthodes de page sont OnPost**JoinList**Async et OnPost**JoinListUC**Async. Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Itinéraires personnalisés

Utilisez la directive `@page` pour :

* Spécifier une route personnalisée vers une page. Par exemple, la route vers la page À propos peut être définie sur `/Some/Other/Path` avec `@page "/Some/Other/Path"`.
* Ajouter des segments à la route par défaut d’une page. Par exemple, un segment « item » peut être ajouté à la route par défaut d’une page avec `@page "item"`.
* Ajouter des paramètres à la route par défaut d’une page. Par exemple, un paramètre d’ID, `id`, peut être nécessaire pour une page avec `@page "{id}"`.

Un chemin relatif racine désigné par un tilde (`~`) au début du chemin est pris en charge. Par exemple, `@page "~/Some/Other/Path"` est identique à `@page "/Some/Other/Path"`.

Si vous n’aimez pas `?handler=JoinList` la chaîne de requête dans l’URL, modifiez l’itinéraire pour mettre le nom du gestionnaire dans la partie de chemin de l’URL. L’itinéraire peut être personnalisé en ajoutant un modèle `@page` d’itinéraire inclus dans des citations doubles après la directive.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH/JoinList`. Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.

## <a name="configuration-and-settings"></a>Configuration et paramètres

Pour configurer les options avancées, utilisez la méthode d’extension `AddRazorPagesOptions` sur le générateur MVC :

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Actuellement, vous pouvez utiliser `RazorPagesOptions` pour définir le répertoire racine pour les pages, ou ajouter des conventions de modèle d’application pour les pages. Nous permettrons à l’avenir une plus grande extensibilité en ce sens.

Pour précompiler des vues, consultez [Compilation de vue Razor](xref:mvc/views/view-compilation).

[Téléchargez ou affichez des exemples de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Consultez [Bien démarrer avec les pages Razor](xref:tutorials/razor-pages/razor-pages-start) qui s’appuie sur cette introduction.

### <a name="specify-that-razor-pages-are-at-the-content-root"></a>Spécifier que les pages Razor se trouvent à la racine du contenu

Par défaut, les pages Razor sont associées à la racine */Pages*. Ajoutez [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos pages Razor sont à la racine du [contenu](xref:fundamentals/index#content-root) [(ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de l’application :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a>Spécifier que les pages Razor se trouvent dans un répertoire racine personnalisé

Ajoutez [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos pages Razor se trouvent dans le répertoire racine personnalisé de l’application (fournissez un chemin relatif) :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
