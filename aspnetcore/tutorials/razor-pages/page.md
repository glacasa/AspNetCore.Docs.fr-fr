---
title: Partie 3, Razor pages de génération de modèles automatique dans ASP.net Core
author: rick-anderson
description: Partie 3 de la série de didacticiels sur les Razor pages.
ms.author: riande
ms.date: 08/17/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/page
ms.openlocfilehash: 03febbd71df19cd3524d26e229a8bd8798a874b5
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865123"
---
# <a name="part-3-scaffolded-no-locrazor-pages-in-aspnet-core"></a>Partie 3, Razor pages de génération de modèles automatique dans ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel examine les Razor pages créées par génération de modèles automatique dans le [didacticiel précédent](xref:tutorials/razor-pages/model).

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="the-create-delete-details-and-edit-pages"></a>Pages Create, Delete, Details et Edit

Examinez le modèle de page *Pages/Movies/Index.cshtml.cs* : 

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

Razor Les pages sont dérivées de `PageModel` . Par convention, la classe dérivée de `PageModel` s’appelle `<PageName>Model`. Le constructeur utilise l’[injection de dépendances](xref:fundamentals/dependency-injection) pour ajouter `RazorPagesMovieContext` à la page. Toutes les pages obtenues par génération de modèles automatique suivent ce modèle. Consultez [Code asynchrone](xref:data/ef-rp/intro#asynchronous-code) pour plus d’informations sur la programmation asynchrone avec Entity Framework.

Quand une demande est effectuée pour la page, la `OnGetAsync` méthode retourne une liste de films à la Razor page. `OnGetAsync` ou `OnGet` est appelé pour initialiser l’état de la page. Dans ce cas, `OnGetAsync` obtient une liste de films et les affiche.

Lorsque `OnGet` retourne `void` ou `OnGetAsync` retourne une valeur `Task` , aucune instruction return n’est utilisée. Lorsque le type de retour est `IActionResult` ou `Task<IActionResult>`, une instruction de retour doit être spécifiée. Par exemple, la méthode *Pages/Movies/Create.cshtml.cs* `OnPostAsync` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Examinez la page *pages/movies/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

Razor peut passer du code HTML au langage C# ou à Razor un balisage spécifique. Quand un `@` symbole est suivi d’un [ Razor mot clé réservé](xref:mvc/views/razor#razor-reserved-keywords), il passe dans le Razor balisage spécifique, sinon il passe en C#.

### <a name="the-page-directive"></a>Directive @page

La `@page` Razor directive fait du fichier une action MVC, ce qui signifie qu’elle peut gérer les demandes. `@page` doit être la première Razor directive sur une page. `@page` est un exemple de transition vers un Razor balisage spécifique. Pour plus d’informations, consultez [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .

Examinez l’expression lambda utilisée dans le HTML Helper suivant :

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Le HTML Helper `DisplayNameFor` inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage. L’expression lambda est inspectée plutôt qu’évaluée. Cela signifie qu’il n’y a aucune violation d’accès quand `model` , `model.Movie` ou `model.Movie[0]` est `null` ou est vide. Quand l’expression lambda est évaluée (par exemple avec `@Html.DisplayFor(modelItem => item.Title)`), les valeurs de propriété du modèle sont évaluées.

<a name="md"></a>

### <a name="the-model-directive"></a>Directive @model

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

La `@model` directive spécifie le type du modèle passé à la Razor page. Dans l’exemple précédent, la `@model` ligne rend la `PageModel` classe dérivée de disponible sur la Razor page. Le modèle est utilisé dans les  [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` et `@Html.DisplayFor` de la page.

### <a name="the-layout-page"></a>La page de disposition

Sélectionnez les liens de menu (** Razor PagesMovie**, **début**et **confidentialité**). Chaque page affiche la même disposition de menu. La disposition du menu est implémentée dans le fichier *Pages/Shared/_Layout.cshtml*. Ouvrez le fichier *Pages/Shared/_Layout.cshtml*.

Les modèles de [Disposition](xref:mvc/views/layout) permettent que la disposition du conteneur HTML soit :

* spécifiée à un seul endroit ;
* appliquée à plusieurs pages du site.

Recherchez la ligne `@RenderBody()`. `RenderBody` est un espace réservé dans lequel toutes les vues propres à la page s’affichent, *encapsulées* dans la page de disposition. Par exemple, sélectionnez le lien **Confidentialité** et la vue *Pages/Privacy.cshtml* est restituée dans la méthode `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData et disposition

Considérez la balise suivante du fichier *Pages/Movies/Index.cshtml* :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Le balisage en surbrillance précédent est un exemple de Razor transition en C#. Les caractères `{` et `}` délimitent un bloc de code C#.

La `PageModel` classe de base contient une `ViewData` propriété de dictionnaire qui peut être utilisée pour passer des données à une vue. Des objets sont ajoutés au dictionnaire `ViewData` à l’aide d’un modèle clé/valeur. Dans l’exemple précédent, la propriété `"Title"` est ajoutée au dictionnaire `ViewData`.

La propriété `"Title"` est utilisée dans le fichier * Pages/Shared/_Layout.cshtml*. La balise suivante montre les premières lignes du fichier *_Layout.cshtml*.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

La ligne `@*Markup removed for brevity.*@` est un Razor commentaire. Contrairement aux commentaires HTML ( `<!-- -->` ), Razor les commentaires ne sont pas envoyés au client.

### <a name="update-the-layout"></a>Mettre à jour la disposition

Modifiez l' `<title>` élément dans le fichier *pages/Shared/_Layout. cshtml* pour afficher **Movie** plutôt que ** Razor PagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Recherchez l’élément anchor suivant dans le fichier *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Remplacez l’élément précédent par la balise suivante :

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

L’élément anchor précédent est un [Tag Helper](xref:mvc/views/tag-helpers/intro). Dans le cas présent, il s’agit du [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). L' `asp-page="/Movies/Index"` attribut et la valeur tag Helper crée un lien vers la `/Movies/Index` Razor page. La valeur de l’attribut `asp-area` est vide : la zone n’est donc pas utilisée dans le lien. Pour plus d’informations, consultez [Zones](xref:mvc/controllers/areas).

Enregistrez vos changements, puis testez l’application en cliquant sur le lien **RpMovie**. Consultez le fichier [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) dans GitHub si vous rencontrez des problèmes.

Testez les autres liens (**Home**, **RpMovie**, **Create**, **Edit** et **Delete**). Chaque page définit le titre, que vous pouvez voir dans l’onglet navigateur. Lorsque vous ajoutez une page à un signet, le titre est utilisé pour le signet.

> [!NOTE]
> Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que l’anglais qui utilisent une virgule (« , ») comme décimale et des formats de date autres que l’anglais des États-Unis, vous devez effectuer des étapes pour localiser votre application. Consultez cette page [GitHub problème 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) pour savoir comment ajouter une virgule décimale.

La propriété `Layout` est définie dans le fichier *Pages/_ViewStart.cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

Le balisage précédent définit le fichier de disposition sur *pages/Shared/_Layout. cshtml* pour tous les Razor fichiers sous le dossier *pages* . Pour plus d’informations, consultez [Disposition](xref:razor-pages/index#layout).

### <a name="the-create-page-model"></a>Le modèle de page Create

Examinez le modèle de page *Pages/Movies/Create.cshtml.cs* :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

La méthode `OnGet` initialise l’état nécessaire pour la page. La page Create n’ayant aucun état à initialiser, `Page` est retourné. Plus loin dans le tutoriel, un exemple d’état d’initialisation `OnGet` est affiché. La méthode `Page` crée un objet `PageResult` qui affiche la page *Create.cshtml*.

La propriété `Movie` utilise l’attribut `[BindProperty]` pour adhérer à la [liaison de données](xref:mvc/models/model-binding). Quand le formulaire Create publie les valeurs de formulaire, le runtime ASP.NET Core lie les valeurs publiées au modèle `Movie`.

La méthode `OnPostAsync` est exécutée quand la page publie les données de formulaire :

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

S’il existe des erreurs liées au modèle, le formulaire est réaffiché, ainsi que toutes les données de formulaire publiées. La plupart des erreurs de modèle peuvent être interceptées côté client avant la publication du formulaire. Voici un exemple d’erreur de modèle : la publication pour le champ de date d’une valeur qui ne peut pas être convertie en date. La validation côté client et la validation du modèle sont présentées plus loin dans le tutoriel.

S’il n’existe pas d’erreurs de modèle, les données sont enregistrées et le navigateur est redirigé vers la page Index.

### <a name="the-create-no-locrazor-page"></a>La Razor page créer

Examinez le fichier de page *pages/movies/Create. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio affiche la balise suivante dans une police différenciée en gras utilisée pour les Tag Helpers :

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Vue VS17 de la page Create.cshtml](page/_static/th3.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Les Tag Helpers suivants sont affichées dans la balise précédente :

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Visual Studio affiche la balise suivante dans une police différenciée en gras utilisée pour les Tag Helpers :

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

L’élément `<form method="post">` est un [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper). Le Tag Helper de formulaire inclut automatiquement un [jeton de protection contre les falsifications](xref:security/anti-request-forgery).

Le moteur de génération de modèles automatique crée un Razor balisage pour chaque champ du modèle (à l’exception de l’ID) semblable à ce qui suit :

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

Les [tag helpers de validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` et `<span asp-validation-for` ) affichent des erreurs de validation. La validation est traitée de manière plus détaillée plus loin dans cette série.

Le [tag Helper étiquette](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) génère la légende et l' `for` attribut de l’étiquette pour la `Title` propriété.

Le [tag Helper d’entrée](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.

Pour plus d’informations sur les Tag Helpers, comme `<form method="post">`, consultez [Tag Helpers dans ASP.NET Core](xref:mvc/views/tag-helpers/intro).

## <a name="additional-resources"></a>Ressources supplémentaires

> [!div class="step-by-step"]
> [Précédent : ajout d’un modèle](xref:tutorials/razor-pages/model) 
>  [Suivant : base de données](xref:tutorials/razor-pages/sql)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel examine les Razor pages créées par génération de modèles automatique dans le [didacticiel précédent](xref:tutorials/razor-pages/model).

[Affichez ou téléchargez](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) l’exemple de code.

## <a name="the-create-delete-details-and-edit-pages"></a>Pages Create, Delete, Details et Edit

Examinez le modèle de page *Pages/Movies/Index.cshtml.cs* : 

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

Razor Les pages sont dérivées de `PageModel` . Par convention, la classe dérivée de `PageModel` s’appelle `<PageName>Model`. Le constructeur utilise l’[injection de dépendances](xref:fundamentals/dependency-injection) pour ajouter `RazorPagesMovieContext` à la page. Toutes les pages obtenues par génération de modèles automatique suivent ce modèle. Consultez [Code asynchrone](xref:data/ef-rp/intro#asynchronous-code) pour plus d’informations sur la programmation asynchrone avec Entity Framework.

Quand une demande est effectuée pour la page, la `OnGetAsync` méthode retourne une liste de films à la Razor page. `OnGetAsync` ou `OnGet` est appelé sur une Razor page pour initialiser l’état de la page. Dans ce cas, `OnGetAsync` obtient une liste de films et les affiche.

Si `OnGet` retourne `void` ou que `OnGetAsync` retourne `Task`, aucune méthode de retour n’est utilisée. Lorsque le type de retour est `IActionResult` ou `Task<IActionResult>`, une instruction de retour doit être spécifiée. Par exemple, la méthode *Pages/Movies/Create.cshtml.cs* `OnPostAsync` :

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> Examinez la page *pages/movies/index. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

Razor peut passer du code HTML au langage C# ou à Razor un balisage spécifique. Quand un `@` symbole est suivi d’un [ Razor mot clé réservé](xref:mvc/views/razor#razor-reserved-keywords), il passe dans le Razor balisage spécifique, sinon il passe en C#.

La `@page` Razor directive convertit le fichier en action MVC, ce qui signifie qu’il peut gérer les demandes. `@page` doit être la première Razor directive sur une page. `@page` est un exemple de transition vers un Razor balisage spécifique. Pour plus d’informations, consultez [ Razor syntaxe](xref:mvc/views/razor#razor-syntax) .

Examinez l’expression lambda utilisée dans le HTML Helper suivant :

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

Le HTML Helper `DisplayNameFor` inspecte la propriété `Title` référencée dans l’expression lambda pour déterminer le nom d’affichage. L’expression lambda est inspectée plutôt qu’évaluée. Cela signifie qu’il n’existe pas de violation d’accès quand `model`, `model.Movie` ou `model.Movie[0]` ont une valeur `null` ou vide. Quand l’expression lambda est évaluée (par exemple avec `@Html.DisplayFor(modelItem => item.Title)`), les valeurs de propriété du modèle sont évaluées.

<a name="md"></a>

### <a name="the-model-directive"></a>Directive @model

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

La `@model` directive spécifie le type du modèle passé à la Razor page. Dans l’exemple précédent, la `@model` ligne rend la `PageModel` classe dérivée de disponible sur la Razor page. Le modèle est utilisé dans les  [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)`@Html.DisplayNameFor` et `@Html.DisplayFor` de la page.

### <a name="the-layout-page"></a>La page de disposition

Sélectionnez les liens de menu (** Razor PagesMovie**, **début**et **confidentialité**). Chaque page affiche la même disposition de menu. La disposition du menu est implémentée dans le fichier *Pages/Shared/_Layout.cshtml*. Ouvrez le fichier *Pages/Shared/_Layout.cshtml*.

Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site. Recherchez la ligne `@RenderBody()`. `RenderBody` est un espace réservé dans lequel tous les affichages spécifiques à la page que vous créez s’affichent, *inclus* dans la page de disposition. Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Pages/Privacy.cshtml** est restituée dans la méthode `RenderBody`.

<a name="vd"></a>

### <a name="viewdata-and-layout"></a>ViewData et disposition

Considérez le code suivant du fichier *Pages/Movies/Index.cshtml*:

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

Le code en surbrillance précédent est un exemple de Razor transition en C#. Les caractères `{` et `}` délimitent un bloc de code C#.

La classe de base `PageModel` a une propriété de dictionnaire `ViewData` qui permet d’ajouter des données à passer à une vue. Vous pouvez ajouter des objets au dictionnaire `ViewData` à l’aide d’un modèle clé/valeur. Dans l’exemple précédent, la propriété « Title » est ajoutée au dictionnaire `ViewData`.

La propriété « Title » est utilisée dans le fichier *Pages/Shared/_Layout.cshtml*. La balise suivante montre les premières lignes du fichier *_Layout.cshtml*.

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

La ligne `@*Markup removed for brevity.*@` est un Razor commentaire qui n’apparaît pas dans votre fichier de disposition. Contrairement aux commentaires HTML ( `<!-- -->` ), Razor les commentaires ne sont pas envoyés au client.

### <a name="update-the-layout"></a>Mettre à jour la disposition

Modifiez l' `<title>` élément dans le fichier *pages/Shared/_Layout. cshtml* pour afficher **Movie** plutôt que ** Razor PagesMovie**.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

Recherchez l’élément anchor suivant dans le fichier *Pages/Shared/_Layout.cshtml*.

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

Remplacez l’élément précédent par le code suivant.

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

L’élément anchor précédent est un [Tag Helper](xref:mvc/views/tag-helpers/intro). Dans le cas présent, il s’agit du [Tag Helper d’ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper). L' `asp-page="/Movies/Index"` attribut et la valeur tag Helper crée un lien vers la `/Movies/Index` Razor page. La valeur de l’attribut `asp-area` est vide : la zone n’est donc pas utilisée dans le lien. Pour plus d’informations, consultez [Zones](xref:mvc/controllers/areas).

Enregistrez vos changements, puis testez l’application en cliquant sur le lien **RpMovie**. Consultez le fichier [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) dans GitHub si vous rencontrez des problèmes.

Testez les autres liens (**Home**, **RpMovie**, **Create**, **Edit** et **Delete**). Chaque page définit le titre, que vous pouvez voir dans l’onglet navigateur. Lorsque vous ajoutez une page à un signet, le titre est utilisé pour le signet.

> [!NOTE]
> Vous ne pourrez peut-être pas entrer de virgules décimales dans le champ `Price`. Pour prendre en charge la [validation jQuery](https://jqueryvalidation.org/) pour les paramètres régionaux autres que l’anglais qui utilisent une virgule (« , ») comme décimale et des formats de date autres que l’anglais des États-Unis, vous devez effectuer des étapes pour localiser votre application. Consultez la page [GitHub problème 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) pour savoir comment ajouter une virgule décimale.

La propriété `Layout` est définie dans le fichier *Pages/_ViewStart.cshtml* :

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

Le balisage précédent définit le fichier de disposition sur *pages/Shared/_Layout. cshtml* pour tous les Razor fichiers sous le dossier *pages* . Pour plus d’informations, consultez [Disposition](xref:razor-pages/index#layout).

### <a name="the-create-page-model"></a>Le modèle de page Create

Examinez le modèle de page *Pages/Movies/Create.cshtml.cs* :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

La méthode `OnGet` initialise l’état nécessaire pour la page. La page Create n’ayant aucun état à initialiser, `Page` est retourné. Ce tutoriel illustre plus loin l’initialisation de l’état par la méthode `OnGet`. La méthode `Page` crée un objet `PageResult` qui affiche la page *Create.cshtml*.

La propriété `Movie` utilise l’attribut `[BindProperty]` pour adhérer à la [liaison de données](xref:mvc/models/model-binding). Quand le formulaire Create publie les valeurs de formulaire, le runtime ASP.NET Core lie les valeurs publiées au modèle `Movie`.

La méthode `OnPostAsync` est exécutée quand la page publie les données de formulaire :

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

S’il existe des erreurs liées au modèle, le formulaire est réaffiché, ainsi que toutes les données de formulaire publiées. La plupart des erreurs de modèle peuvent être interceptées côté client avant la publication du formulaire. Voici un exemple d’erreur de modèle : la publication pour le champ de date d’une valeur qui ne peut pas être convertie en date. La validation côté client et la validation du modèle sont présentées plus loin dans le tutoriel.

S’il n’existe pas d’erreurs de modèle, les données sont enregistrées et le navigateur est redirigé vers la page Index.

### <a name="the-create-no-locrazor-page"></a>La Razor page créer

Examinez le fichier de page *pages/movies/Create. cshtml* Razor :

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio affiche la balise `<form method="post">` dans une police différenciée en gras utilisée pour les Tag Helpers :

![Vue VS17 de la page Create.cshtml](page/_static/th.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Pour plus d’informations sur les Tag Helpers, comme `<form method="post">`, consultez [Tag Helpers dans ASP.NET Core](xref:mvc/views/tag-helpers/intro).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

Visual Studio pour Mac affiche la balise `<form method="post">` dans une police différenciée en gras utilisée pour les Tag Helpers.

---

L’élément `<form method="post">` est un [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper). Le Tag Helper de formulaire inclut automatiquement un [jeton de protection contre les falsifications](xref:security/anti-request-forgery).

Le moteur de génération de modèles automatique crée un Razor balisage pour chaque champ du modèle (à l’exception de l’ID) semblable à ce qui suit :

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Les [tag helpers de validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) ( `<div asp-validation-summary` et `<span asp-validation-for` ) affichent des erreurs de validation. La validation est traitée de manière plus détaillée plus loin dans cette série.

Le [tag Helper étiquette](xref:mvc/views/working-with-forms#the-label-tag-helper) ( `<label asp-for="Movie.Title" class="control-label"></label>` ) génère la légende et l' `for` attribut de l’étiquette pour la `Title` propriété.

Le [tag Helper d’entrée](xref:mvc/views/working-with-forms) ( `<input asp-for="Movie.Title" class="form-control">` ) utilise les attributs [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Version YouTube de ce tutoriel](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> [Précédent : ajout d’un modèle](xref:tutorials/razor-pages/model) 
>  [Suivant : base de données](xref:tutorials/razor-pages/sql)

::: moniker-end
