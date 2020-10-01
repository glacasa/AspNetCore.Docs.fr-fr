---
title: BlazorLiaison de données ASP.net Core
author: guardrex
description: En savoir plus sur les fonctionnalités de liaison de données pour les composants et les éléments DOM dans les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3f823ca9cf96b7ff439ade59f946db222b7f7e60
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606699"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="d8f59-103">BlazorLiaison de données ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="d8f59-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="d8f59-104">Par [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)et [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="d8f59-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="d8f59-105">Razor les composants fournissent des fonctionnalités de liaison de données via un attribut d’élément HTML nommé [`@bind`](xref:mvc/views/razor#bind) avec une valeur de champ, de propriété ou d' Razor expression.</span><span class="sxs-lookup"><span data-stu-id="d8f59-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="d8f59-106">L’exemple suivant lie un `<input>` élément au `currentValue` champ et un `<input>` élément à la `CurrentValue` propriété :</span><span class="sxs-lookup"><span data-stu-id="d8f59-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="d8f59-107">Lorsque l’un des éléments perd le focus, le champ ou la propriété liés est mis à jour.</span><span class="sxs-lookup"><span data-stu-id="d8f59-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="d8f59-108">La zone de texte est mise à jour dans l’interface utilisateur uniquement lorsque le composant est rendu, et non en réponse à la modification de la valeur du champ ou de la propriété.</span><span class="sxs-lookup"><span data-stu-id="d8f59-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="d8f59-109">Étant donné que les composants s’affichent après l’exécution du code du gestionnaire d’événements, les mises à jour des champs et des propriétés sont *généralement* reflétées dans l’interface utilisateur immédiatement après le déclenchement d’un gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="d8f59-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="d8f59-110">L’utilisation [`@bind`](xref:mvc/views/razor#bind) de avec la `CurrentValue` propriété ( `<input @bind="CurrentValue" />` ) équivaut essentiellement à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d8f59-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="d8f59-111">Lors du rendu du composant, le `value` de l’élément d’entrée provient de la `CurrentValue` propriété.</span><span class="sxs-lookup"><span data-stu-id="d8f59-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="d8f59-112">Lorsque l’utilisateur tape dans la zone de texte et modifie le focus de l’élément, l' `onchange` événement est déclenché et la `CurrentValue` propriété est définie sur la valeur modifiée.</span><span class="sxs-lookup"><span data-stu-id="d8f59-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="d8f59-113">En réalité, la génération de code est plus complexe que cela car [`@bind`](xref:mvc/views/razor#bind) gère les cas où les conversions de type sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="d8f59-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="d8f59-114">En principe, [`@bind`](xref:mvc/views/razor#bind) associe la valeur actuelle d’une expression à un `value` attribut et gère les modifications à l’aide du gestionnaire inscrit.</span><span class="sxs-lookup"><span data-stu-id="d8f59-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="d8f59-115">Liez une propriété ou un champ à d’autres événements en incluant également un `@bind:event` attribut avec un `event` paramètre.</span><span class="sxs-lookup"><span data-stu-id="d8f59-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="d8f59-116">L’exemple suivant lie la `CurrentValue` propriété à l' `oninput` événement :</span><span class="sxs-lookup"><span data-stu-id="d8f59-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="d8f59-117">Contrairement à `onchange` , qui se déclenche lorsque l’élément perd le focus, `oninput` se déclenche lorsque la valeur de la zone de texte change.</span><span class="sxs-lookup"><span data-stu-id="d8f59-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="d8f59-118">La liaison d’attribut respecte la casse :</span><span class="sxs-lookup"><span data-stu-id="d8f59-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="d8f59-119">`@bind` est valide.</span><span class="sxs-lookup"><span data-stu-id="d8f59-119">`@bind` is valid.</span></span>
* <span data-ttu-id="d8f59-120">`@Bind` et `@BIND` ne sont pas valides.</span><span class="sxs-lookup"><span data-stu-id="d8f59-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="d8f59-121">Valeurs inanalysables</span><span class="sxs-lookup"><span data-stu-id="d8f59-121">Unparsable values</span></span>

<span data-ttu-id="d8f59-122">Quand un utilisateur fournit une valeur non analysable à un élément DataBound, la valeur unanalysable est automatiquement rétablie à sa valeur précédente lorsque l’événement de liaison est déclenché.</span><span class="sxs-lookup"><span data-stu-id="d8f59-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="d8f59-123">Examinez le cas suivant :</span><span class="sxs-lookup"><span data-stu-id="d8f59-123">Consider the following scenario:</span></span>

* <span data-ttu-id="d8f59-124">Un `<input>` élément est lié à un `int` type dont la valeur initiale est `123` :</span><span class="sxs-lookup"><span data-stu-id="d8f59-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="d8f59-125">L’utilisateur met à jour la valeur de l’élément à `123.45` dans la page et modifie le focus de l’élément.</span><span class="sxs-lookup"><span data-stu-id="d8f59-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="d8f59-126">Dans le scénario précédent, la valeur de l’élément est rétablie à `123` .</span><span class="sxs-lookup"><span data-stu-id="d8f59-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="d8f59-127">Lorsque la valeur `123.45` est rejetée en faveur de la valeur d’origine de `123` , l’utilisateur sait que sa valeur n’a pas été acceptée.</span><span class="sxs-lookup"><span data-stu-id="d8f59-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="d8f59-128">Par défaut, la liaison s’applique à l’événement de l’élément `onchange` ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="d8f59-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="d8f59-129">Utilisez `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent.</span><span class="sxs-lookup"><span data-stu-id="d8f59-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="d8f59-130">Pour l' `oninput` événement ( `@bind:event="oninput"` ), la réversion se produit après toute séquence de touches qui introduit une valeur non analysable.</span><span class="sxs-lookup"><span data-stu-id="d8f59-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="d8f59-131">Lorsque vous ciblez l' `oninput` événement avec un `int` type lié, un utilisateur ne peut pas taper un `.` caractère.</span><span class="sxs-lookup"><span data-stu-id="d8f59-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="d8f59-132">Un `.` caractère est immédiatement supprimé, de sorte que l’utilisateur reçoit des commentaires immédiats que seuls des nombres entiers sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="d8f59-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="d8f59-133">Il existe des scénarios dans lesquels le rétablissement de la valeur de l' `oninput` événement n’est pas idéal, par exemple lorsque l’utilisateur doit être autorisé à effacer une valeur non analysable `<input>` .</span><span class="sxs-lookup"><span data-stu-id="d8f59-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="d8f59-134">Les alternatives sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="d8f59-134">Alternatives include:</span></span>

* <span data-ttu-id="d8f59-135">N’utilisez pas l' `oninput` événement.</span><span class="sxs-lookup"><span data-stu-id="d8f59-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="d8f59-136">Utilisez l’événement par défaut `onchange` (spécifiez uniquement `@bind="{PROPERTY OR FIELD}"` ), où une valeur non valide n’est pas rétablie tant que l’élément n’a pas perdu le focus.</span><span class="sxs-lookup"><span data-stu-id="d8f59-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="d8f59-137">Effectuer une liaison à un type Nullable, tel que `int?` ou, `string` et fournir une logique personnalisée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="d8f59-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="d8f59-138">Utilisez un [composant de validation de formulaire](xref:blazor/forms-validation), tel que <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="d8f59-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="d8f59-139">Les composants de validation de formulaire offrent une prise en charge intégrée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="d8f59-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="d8f59-140">Pour plus d'informations, consultez <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="d8f59-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="d8f59-141">Composants de validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="d8f59-141">Form validation components:</span></span>
  * <span data-ttu-id="d8f59-142">Permet à l’utilisateur de fournir une entrée non valide et de recevoir des erreurs de validation sur le associé <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="d8f59-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="d8f59-143">Affichez les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur qui saisit des données Webform supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="d8f59-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="d8f59-144">Chaînes de format</span><span class="sxs-lookup"><span data-stu-id="d8f59-144">Format strings</span></span>

<span data-ttu-id="d8f59-145">La liaison de données fonctionne avec les <xref:System.DateTime> chaînes de format à l’aide de `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="d8f59-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="d8f59-146">D’autres expressions de mise en forme, telles que les formats de devise ou de nombre, ne sont pas disponibles pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="d8f59-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="d8f59-147">Dans le code précédent, le `<input>` type de champ de l’élément ( `type` ) est défini par défaut sur `text` .</span><span class="sxs-lookup"><span data-stu-id="d8f59-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="d8f59-148">`@bind:format` est pris en charge pour la liaison des types .NET suivants :</span><span class="sxs-lookup"><span data-stu-id="d8f59-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="d8f59-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="d8f59-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="d8f59-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="d8f59-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="d8f59-151">L' `@bind:format` attribut spécifie le format de date à appliquer au `value` de l' `<input>` élément.</span><span class="sxs-lookup"><span data-stu-id="d8f59-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="d8f59-152">Le format est également utilisé pour analyser la valeur lorsqu’un `onchange` événement se produit.</span><span class="sxs-lookup"><span data-stu-id="d8f59-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="d8f59-153">La spécification d’un format pour le `date` type de champ n’est pas recommandée, car Blazor dispose d’une prise en charge intégrée pour mettre en forme les dates.</span><span class="sxs-lookup"><span data-stu-id="d8f59-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="d8f59-154">Malgré la recommandation, utilisez uniquement le `yyyy-MM-dd` format de date pour que la liaison fonctionne correctement si un format est fourni avec le `date` type de champ :</span><span class="sxs-lookup"><span data-stu-id="d8f59-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="d8f59-155">Liaison parent-enfant avec les paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="d8f59-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="d8f59-156">Les paramètres de composant autorisent les propriétés de liaison et les champs d’un composant parent avec la `@bind-{PROPERTY OR FIELD}` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="d8f59-156">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="d8f59-157">Le `Child` composant suivant ( `Shared/Child.razor` ) a un `Year` paramètre de composant et un `YearChanged` Rappel :</span><span class="sxs-lookup"><span data-stu-id="d8f59-157">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="d8f59-158">Le rappel ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) doit être nommé en tant que nom de paramètre de composant suivi du `Changed` suffixe «» ( `{PARAMETER NAME}Changed` ).</span><span class="sxs-lookup"><span data-stu-id="d8f59-158">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="d8f59-159">Dans l’exemple précédent, le rappel est nommé `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="d8f59-159">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="d8f59-160">Pour plus d'informations sur le <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consultez <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="d8f59-160">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="d8f59-161">Dans le `Parent` composant suivant ( `Parent.razor` ), le `year` champ est lié au `Year` paramètre du composant enfant :</span><span class="sxs-lookup"><span data-stu-id="d8f59-161">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="d8f59-162">Le `Year` paramètre peut être lié, car il a un `YearChanged` événement auxiliaire qui correspond au type du `Year` paramètre.</span><span class="sxs-lookup"><span data-stu-id="d8f59-162">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="d8f59-163">Par Convention, une propriété peut être liée à un gestionnaire d’événements correspondant en incluant un `@bind-{PROPERTY}:event` attribut assigné au gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="d8f59-163">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="d8f59-164">`<Child @bind-Year="year" />` équivaut à écrire :</span><span class="sxs-lookup"><span data-stu-id="d8f59-164">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="d8f59-165">Liaison enfant-parent avec liaison chaînée</span><span class="sxs-lookup"><span data-stu-id="d8f59-165">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="d8f59-166">Un scénario courant consiste à chaîner un paramètre lié aux données à un élément de page dans la sortie du composant.</span><span class="sxs-lookup"><span data-stu-id="d8f59-166">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="d8f59-167">Ce scénario est appelé *liaison chaînée* car plusieurs niveaux de liaison se produisent simultanément.</span><span class="sxs-lookup"><span data-stu-id="d8f59-167">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="d8f59-168">Une liaison chaînée ne peut pas être implémentée avec [`@bind`](xref:mvc/views/razor#bind) la syntaxe dans le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="d8f59-168">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="d8f59-169">Le gestionnaire d’événements et la valeur doivent être spécifiés séparément.</span><span class="sxs-lookup"><span data-stu-id="d8f59-169">The event handler and value must be specified separately.</span></span> <span data-ttu-id="d8f59-170">Toutefois, un composant parent peut utiliser [`@bind`](xref:mvc/views/razor#bind) la syntaxe avec le paramètre du composant enfant.</span><span class="sxs-lookup"><span data-stu-id="d8f59-170">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="d8f59-171">Le `PasswordField` composant suivant ( `PasswordField.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="d8f59-171">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="d8f59-172">Définit `<input>` la valeur d’un élément sur `password` un champ.</span><span class="sxs-lookup"><span data-stu-id="d8f59-172">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="d8f59-173">Expose les modifications d’une `Password` propriété à un composant parent avec un [`EventCallback`](xref:blazor/components/event-handling#eventcallback) qui passe la valeur actuelle du champ de l’enfant `password` comme argument.</span><span class="sxs-lookup"><span data-stu-id="d8f59-173">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="d8f59-174">Utilise l' `onclick` événement pour déclencher la `ToggleShowPassword` méthode.</span><span class="sxs-lookup"><span data-stu-id="d8f59-174">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="d8f59-175">Pour plus d'informations, consultez <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="d8f59-175">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="d8f59-176">Le `PasswordField` composant est utilisé dans un autre composant :</span><span class="sxs-lookup"><span data-stu-id="d8f59-176">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="d8f59-177">Effectue des vérifications ou des erreurs d’interruption dans la méthode qui appelle le délégué de la liaison.</span><span class="sxs-lookup"><span data-stu-id="d8f59-177">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="d8f59-178">L’exemple suivant fournit un retour immédiat à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :</span><span class="sxs-lookup"><span data-stu-id="d8f59-178">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="d8f59-179">Liaison entre plus de deux composants</span><span class="sxs-lookup"><span data-stu-id="d8f59-179">Bind across more than two components</span></span>

<span data-ttu-id="d8f59-180">Vous pouvez lier n’importe quel nombre de composants imbriqués, mais vous devez respecter le déroulement unidirectionnel des données :</span><span class="sxs-lookup"><span data-stu-id="d8f59-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="d8f59-181">Les notifications de modifications sont *transmises dans la hiérarchie*.</span><span class="sxs-lookup"><span data-stu-id="d8f59-181">Change notifications *flow up the hierarchy*.</span></span>
* <span data-ttu-id="d8f59-182">Les nouvelles valeurs de paramètre *circulent dans la hiérarchie*.</span><span class="sxs-lookup"><span data-stu-id="d8f59-182">New parameter values *flow down the hierarchy*.</span></span>

<span data-ttu-id="d8f59-183">Une approche courante et recommandée consiste à stocker uniquement les données sous-jacentes dans le composant parent afin d’éviter toute confusion quant à l’État qui doit être mis à jour.</span><span class="sxs-lookup"><span data-stu-id="d8f59-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="d8f59-184">Les composants suivants illustrent les concepts précédents :</span><span class="sxs-lookup"><span data-stu-id="d8f59-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="d8f59-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d8f59-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="d8f59-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d8f59-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="d8f59-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="d8f59-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private Task ChangeValue()
    {
        return PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="d8f59-188">Pour une autre approche adaptée au partage des données en mémoire entre des composants qui ne sont pas nécessairement imbriqués, consultez <xref:blazor/state-management#in-memory-state-container-service> .</span><span class="sxs-lookup"><span data-stu-id="d8f59-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8f59-189">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d8f59-189">Additional resources</span></span>

* [<span data-ttu-id="d8f59-190">Lier à des cases d’option dans un formulaire</span><span class="sxs-lookup"><span data-stu-id="d8f59-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="d8f59-191">Liaison `<select>` d’options d’éléments à `null` des valeurs d’objet C# dans un formulaire</span><span class="sxs-lookup"><span data-stu-id="d8f59-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
