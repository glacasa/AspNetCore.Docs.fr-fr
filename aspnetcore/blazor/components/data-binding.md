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
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628558"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="61acf-103">BlazorLiaison de données ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="61acf-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="61acf-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="61acf-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="61acf-105">Razor les composants fournissent des fonctionnalités de liaison de données via un attribut d’élément HTML nommé [`@bind`](xref:mvc/views/razor#bind) avec une valeur de champ, de propriété ou d' Razor expression.</span><span class="sxs-lookup"><span data-stu-id="61acf-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="61acf-106">L’exemple suivant lie un `<input>` élément au `currentValue` champ et un `<input>` élément à la `CurrentValue` propriété :</span><span class="sxs-lookup"><span data-stu-id="61acf-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="61acf-107">Lorsque l’un des éléments perd le focus, le champ ou la propriété liés est mis à jour.</span><span class="sxs-lookup"><span data-stu-id="61acf-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="61acf-108">La zone de texte est mise à jour dans l’interface utilisateur uniquement lorsque le composant est rendu, et non en réponse à la modification de la valeur du champ ou de la propriété.</span><span class="sxs-lookup"><span data-stu-id="61acf-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="61acf-109">Étant donné que les composants s’affichent après l’exécution du code du gestionnaire d’événements, les mises à jour des champs et des propriétés sont *généralement* reflétées dans l’interface utilisateur immédiatement après le déclenchement d’un gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="61acf-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="61acf-110">L’utilisation [`@bind`](xref:mvc/views/razor#bind) de avec la `CurrentValue` propriété ( `<input @bind="CurrentValue" />` ) équivaut essentiellement à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="61acf-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="61acf-111">Lors du rendu du composant, le `value` de l’élément d’entrée provient de la `CurrentValue` propriété.</span><span class="sxs-lookup"><span data-stu-id="61acf-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="61acf-112">Lorsque l’utilisateur tape dans la zone de texte et modifie le focus de l’élément, l' `onchange` événement est déclenché et la `CurrentValue` propriété est définie sur la valeur modifiée.</span><span class="sxs-lookup"><span data-stu-id="61acf-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="61acf-113">En réalité, la génération de code est plus complexe que cela car [`@bind`](xref:mvc/views/razor#bind) gère les cas où les conversions de type sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="61acf-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="61acf-114">En principe, [`@bind`](xref:mvc/views/razor#bind) associe la valeur actuelle d’une expression à un `value` attribut et gère les modifications à l’aide du gestionnaire inscrit.</span><span class="sxs-lookup"><span data-stu-id="61acf-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="61acf-115">Liez une propriété ou un champ à d’autres événements en incluant également un `@bind:event` attribut avec un `event` paramètre.</span><span class="sxs-lookup"><span data-stu-id="61acf-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="61acf-116">L’exemple suivant lie la `CurrentValue` propriété à l' `oninput` événement :</span><span class="sxs-lookup"><span data-stu-id="61acf-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="61acf-117">Contrairement à `onchange` , qui se déclenche lorsque l’élément perd le focus, `oninput` se déclenche lorsque la valeur de la zone de texte change.</span><span class="sxs-lookup"><span data-stu-id="61acf-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="61acf-118">Utilisez `@bind-{ATTRIBUTE}` avec la `@bind-{ATTRIBUTE}:event` syntaxe pour lier des attributs d’élément autres que `value` .</span><span class="sxs-lookup"><span data-stu-id="61acf-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="61acf-119">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="61acf-119">In the following example:</span></span>

* <span data-ttu-id="61acf-120">Le style du paragraphe est **rouge** lorsque le composant se charge ( `style="color:red"` ).</span><span class="sxs-lookup"><span data-stu-id="61acf-120">The paragraph's style is **red** when the component loads (`style="color:red"`).</span></span>
* <span data-ttu-id="61acf-121">L’utilisateur modifie la valeur de la zone de texte pour qu’elle reflète un style de couleur CSS différent et modifie le focus de l’élément de la page.</span><span class="sxs-lookup"><span data-stu-id="61acf-121">The user changes the value of the text box to reflect a different CSS color style and changes the page's element focus.</span></span> <span data-ttu-id="61acf-122">Par exemple, l’utilisateur modifie la valeur de la zone de texte en `color:blue` et appuie sur la touche <kbd>Tab</kbd> du clavier.</span><span class="sxs-lookup"><span data-stu-id="61acf-122">For example, the user changes the text box value to `color:blue` and presses the <kbd>Tab</kbd> key on the keyboard.</span></span>
* <span data-ttu-id="61acf-123">Lorsque le focus de l’élément est modifié :</span><span class="sxs-lookup"><span data-stu-id="61acf-123">When the element focus changes:</span></span>
  * <span data-ttu-id="61acf-124">La valeur de `paragraphStyle` est assignée à partir de la valeur de l' `<input>` élément.</span><span class="sxs-lookup"><span data-stu-id="61acf-124">The value of `paragraphStyle` is assigned from the `<input>` element's value.</span></span>
  * <span data-ttu-id="61acf-125">Le style de paragraphe est mis à jour pour refléter le nouveau style dans `paragraphStyle` .</span><span class="sxs-lookup"><span data-stu-id="61acf-125">The paragraph style is updated to reflect the new style in `paragraphStyle`.</span></span> <span data-ttu-id="61acf-126">Si le style est mis à jour vers `color:blue` , la couleur du texte devient **bleue**.</span><span class="sxs-lookup"><span data-stu-id="61acf-126">If the style is updated to `color:blue`, the text color changes to **blue**.</span></span>

```razor
<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="61acf-127">La liaison d’attribut respecte la casse :</span><span class="sxs-lookup"><span data-stu-id="61acf-127">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="61acf-128">`@bind` est valide.</span><span class="sxs-lookup"><span data-stu-id="61acf-128">`@bind` is valid.</span></span>
* <span data-ttu-id="61acf-129">`@Bind` et `@BIND` ne sont pas valides.</span><span class="sxs-lookup"><span data-stu-id="61acf-129">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="61acf-130">Valeurs inanalysables</span><span class="sxs-lookup"><span data-stu-id="61acf-130">Unparsable values</span></span>

<span data-ttu-id="61acf-131">Quand un utilisateur fournit une valeur non analysable à un élément DataBound, la valeur unanalysable est automatiquement rétablie à sa valeur précédente lorsque l’événement de liaison est déclenché.</span><span class="sxs-lookup"><span data-stu-id="61acf-131">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="61acf-132">Examinez le cas suivant :</span><span class="sxs-lookup"><span data-stu-id="61acf-132">Consider the following scenario:</span></span>

* <span data-ttu-id="61acf-133">Un `<input>` élément est lié à un `int` type dont la valeur initiale est `123` :</span><span class="sxs-lookup"><span data-stu-id="61acf-133">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="61acf-134">L’utilisateur met à jour la valeur de l’élément à `123.45` dans la page et modifie le focus de l’élément.</span><span class="sxs-lookup"><span data-stu-id="61acf-134">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="61acf-135">Dans le scénario précédent, la valeur de l’élément est rétablie à `123` .</span><span class="sxs-lookup"><span data-stu-id="61acf-135">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="61acf-136">Lorsque la valeur `123.45` est rejetée en faveur de la valeur d’origine de `123` , l’utilisateur sait que sa valeur n’a pas été acceptée.</span><span class="sxs-lookup"><span data-stu-id="61acf-136">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="61acf-137">Par défaut, la liaison s’applique à l’événement de l’élément `onchange` ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="61acf-137">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="61acf-138">Utilisez `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent.</span><span class="sxs-lookup"><span data-stu-id="61acf-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="61acf-139">Pour l' `oninput` événement ( `@bind:event="oninput"` ), la réversion se produit après toute séquence de touches qui introduit une valeur non analysable.</span><span class="sxs-lookup"><span data-stu-id="61acf-139">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="61acf-140">Lorsque vous ciblez l' `oninput` événement avec un `int` type lié, un utilisateur ne peut pas taper un `.` caractère.</span><span class="sxs-lookup"><span data-stu-id="61acf-140">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="61acf-141">Un `.` caractère est immédiatement supprimé, de sorte que l’utilisateur reçoit des commentaires immédiats que seuls des nombres entiers sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="61acf-141">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="61acf-142">Il existe des scénarios dans lesquels le rétablissement de la valeur de l' `oninput` événement n’est pas idéal, par exemple lorsque l’utilisateur doit être autorisé à effacer une valeur non analysable `<input>` .</span><span class="sxs-lookup"><span data-stu-id="61acf-142">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="61acf-143">Les alternatives sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="61acf-143">Alternatives include:</span></span>

* <span data-ttu-id="61acf-144">N’utilisez pas l' `oninput` événement.</span><span class="sxs-lookup"><span data-stu-id="61acf-144">Don't use the `oninput` event.</span></span> <span data-ttu-id="61acf-145">Utilisez l’événement par défaut `onchange` (spécifiez uniquement `@bind="{PROPERTY OR FIELD}"` ), où une valeur non valide n’est pas rétablie tant que l’élément n’a pas perdu le focus.</span><span class="sxs-lookup"><span data-stu-id="61acf-145">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="61acf-146">Effectuer une liaison à un type Nullable, tel que `int?` ou, `string` et fournir une logique personnalisée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="61acf-146">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="61acf-147">Utilisez un [composant de validation de formulaire](xref:blazor/forms-validation), tel que <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="61acf-147">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="61acf-148">Les composants de validation de formulaire offrent une prise en charge intégrée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="61acf-148">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="61acf-149">Pour plus d'informations, consultez <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="61acf-149">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="61acf-150">Composants de validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="61acf-150">Form validation components:</span></span>
  * <span data-ttu-id="61acf-151">Permet à l’utilisateur de fournir une entrée non valide et de recevoir des erreurs de validation sur le associé <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="61acf-151">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="61acf-152">Affichez les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur qui saisit des données Webform supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="61acf-152">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="61acf-153">Chaînes de format</span><span class="sxs-lookup"><span data-stu-id="61acf-153">Format strings</span></span>

<span data-ttu-id="61acf-154">La liaison de données fonctionne avec les <xref:System.DateTime> chaînes de format à l’aide de `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="61acf-154">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="61acf-155">D’autres expressions de mise en forme, telles que les formats de devise ou de nombre, ne sont pas disponibles pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="61acf-155">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="61acf-156">Dans le code précédent, le `<input>` type de champ de l’élément ( `type` ) est défini par défaut sur `text` .</span><span class="sxs-lookup"><span data-stu-id="61acf-156">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="61acf-157">`@bind:format` est pris en charge pour la liaison des types .NET suivants :</span><span class="sxs-lookup"><span data-stu-id="61acf-157">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="61acf-158"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="61acf-158"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="61acf-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="61acf-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="61acf-160">L' `@bind:format` attribut spécifie le format de date à appliquer au `value` de l' `<input>` élément.</span><span class="sxs-lookup"><span data-stu-id="61acf-160">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="61acf-161">Le format est également utilisé pour analyser la valeur lorsqu’un `onchange` événement se produit.</span><span class="sxs-lookup"><span data-stu-id="61acf-161">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="61acf-162">La spécification d’un format pour le `date` type de champ n’est pas recommandée, car Blazor dispose d’une prise en charge intégrée pour mettre en forme les dates.</span><span class="sxs-lookup"><span data-stu-id="61acf-162">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="61acf-163">Malgré la recommandation, utilisez uniquement le `yyyy-MM-dd` format de date pour que la liaison fonctionne correctement si un format est fourni avec le `date` type de champ :</span><span class="sxs-lookup"><span data-stu-id="61acf-163">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="61acf-164">Liaison parent-enfant avec les paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="61acf-164">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="61acf-165">Les paramètres de composant autorisent les propriétés de liaison et les champs d’un composant parent avec la `@bind-{PROPERTY OR FIELD}` syntaxe.</span><span class="sxs-lookup"><span data-stu-id="61acf-165">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="61acf-166">Le `Child` composant suivant ( `Child.razor` ) a un `Year` paramètre de composant et un `YearChanged` Rappel :</span><span class="sxs-lookup"><span data-stu-id="61acf-166">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="61acf-167">Le rappel ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ) doit être nommé en tant que nom de paramètre de composant suivi du `Changed` suffixe «» ( `{PARAMETER NAME}Changed` ).</span><span class="sxs-lookup"><span data-stu-id="61acf-167">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="61acf-168">Dans l’exemple précédent, le rappel est nommé `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="61acf-168">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="61acf-169">Pour plus d'informations sur le <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consultez <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="61acf-169">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="61acf-170">Dans le `Parent` composant suivant ( `Parent.razor` ), le `year` champ est lié au `Year` paramètre du composant enfant :</span><span class="sxs-lookup"><span data-stu-id="61acf-170">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="61acf-171">Le `Year` paramètre peut être lié, car il a un `YearChanged` événement auxiliaire qui correspond au type du `Year` paramètre.</span><span class="sxs-lookup"><span data-stu-id="61acf-171">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="61acf-172">Par Convention, une propriété peut être liée à un gestionnaire d’événements correspondant en incluant un `@bind-{PROPERTY}:event` attribut assigné au gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="61acf-172">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="61acf-173">`<Child @bind-Year="year" />` équivaut à écrire :</span><span class="sxs-lookup"><span data-stu-id="61acf-173">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="61acf-174">Liaison enfant-parent avec liaison chaînée</span><span class="sxs-lookup"><span data-stu-id="61acf-174">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="61acf-175">Un scénario courant consiste à chaîner un paramètre lié aux données à un élément de page dans la sortie du composant.</span><span class="sxs-lookup"><span data-stu-id="61acf-175">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="61acf-176">Ce scénario est appelé *liaison chaînée* car plusieurs niveaux de liaison se produisent simultanément.</span><span class="sxs-lookup"><span data-stu-id="61acf-176">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="61acf-177">Une liaison chaînée ne peut pas être implémentée avec [`@bind`](xref:mvc/views/razor#bind) la syntaxe dans le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="61acf-177">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="61acf-178">Le gestionnaire d’événements et la valeur doivent être spécifiés séparément.</span><span class="sxs-lookup"><span data-stu-id="61acf-178">The event handler and value must be specified separately.</span></span> <span data-ttu-id="61acf-179">Toutefois, un composant parent peut utiliser [`@bind`](xref:mvc/views/razor#bind) la syntaxe avec le paramètre du composant enfant.</span><span class="sxs-lookup"><span data-stu-id="61acf-179">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="61acf-180">Le `PasswordField` composant suivant ( `PasswordField.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="61acf-180">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="61acf-181">Définit `<input>` la valeur d’un élément sur `Password` une propriété.</span><span class="sxs-lookup"><span data-stu-id="61acf-181">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="61acf-182">Expose les modifications de la `Password` propriété à un composant parent avec un [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="61acf-182">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="61acf-183">Utilise l' `onclick` événement pour déclencher la `ToggleShowPassword` méthode.</span><span class="sxs-lookup"><span data-stu-id="61acf-183">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="61acf-184">Pour plus d'informations, consultez <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="61acf-184">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="61acf-185">Le `PasswordField` composant est utilisé dans un autre composant :</span><span class="sxs-lookup"><span data-stu-id="61acf-185">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="61acf-186">Pour effectuer des vérifications ou des erreurs d’interruption sur le mot de passe dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="61acf-186">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="61acf-187">Créez un champ de stockage pour `Password` ( `password` dans l’exemple de code suivant).</span><span class="sxs-lookup"><span data-stu-id="61acf-187">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="61acf-188">Effectuez les vérifications ou les erreurs d’interruption dans la méthode `Password` Setter.</span><span class="sxs-lookup"><span data-stu-id="61acf-188">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="61acf-189">L’exemple suivant fournit un retour immédiat à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :</span><span class="sxs-lookup"><span data-stu-id="61acf-189">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="61acf-190">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="61acf-190">Additional resources</span></span>

* [<span data-ttu-id="61acf-191">Lier à des cases d’option dans un formulaire</span><span class="sxs-lookup"><span data-stu-id="61acf-191">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="61acf-192">Liaison `<select>` d’options d’éléments à `null` des valeurs d’objet C# dans un formulaire</span><span class="sxs-lookup"><span data-stu-id="61acf-192">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
