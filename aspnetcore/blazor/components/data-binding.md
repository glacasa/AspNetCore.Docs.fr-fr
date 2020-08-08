---
title: BlazorLiaison de données ASP.net Core
author: guardrex
description: En savoir plus sur les fonctionnalités de liaison de données pour les composants et les éléments DOM dans les Blazor applications.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
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
ms.openlocfilehash: 6f5ad6b8f225834c92d6e33d8bcf608b56678e67
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014670"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="32bba-103">BlazorLiaison de données ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="32bba-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="32bba-104">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="32bba-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="32bba-105">Razorles composants fournissent des fonctionnalités de liaison de données via un attribut d’élément HTML nommé [`@bind`](xref:mvc/views/razor#bind) avec une valeur de champ, de propriété ou d' Razor expression.</span><span class="sxs-lookup"><span data-stu-id="32bba-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="32bba-106">L’exemple suivant lie la `CurrentValue` propriété à la valeur de la zone de texte :</span><span class="sxs-lookup"><span data-stu-id="32bba-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="32bba-107">Lorsque la zone de texte perd le focus, la valeur de la propriété est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="32bba-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="32bba-108">La zone de texte est mise à jour dans l’interface utilisateur uniquement lorsque le composant est rendu, et non en réponse à la modification de la valeur de la propriété.</span><span class="sxs-lookup"><span data-stu-id="32bba-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="32bba-109">Étant donné que les composants s’affichent après l’exécution du code du gestionnaire d’événements, les mises à jour de propriétés sont *généralement* reflétées dans l’interface utilisateur immédiatement après le déclenchement d’un gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="32bba-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="32bba-110">L’utilisation [`@bind`](xref:mvc/views/razor#bind) de avec la `CurrentValue` propriété ( `<input @bind="CurrentValue" />` ) équivaut essentiellement à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="32bba-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="32bba-111">Lors du rendu du composant, le `value` de l’élément d’entrée provient de la `CurrentValue` propriété.</span><span class="sxs-lookup"><span data-stu-id="32bba-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="32bba-112">Lorsque l’utilisateur tape dans la zone de texte et modifie le focus de l’élément, l' `onchange` événement est déclenché et la `CurrentValue` propriété est définie sur la valeur modifiée.</span><span class="sxs-lookup"><span data-stu-id="32bba-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="32bba-113">En réalité, la génération de code est plus complexe, car [`@bind`](xref:mvc/views/razor#bind) gère les cas où les conversions de types sont effectuées.</span><span class="sxs-lookup"><span data-stu-id="32bba-113">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="32bba-114">En principe, [`@bind`](xref:mvc/views/razor#bind) associe la valeur actuelle d’une expression à un `value` attribut et gère les modifications à l’aide du gestionnaire inscrit.</span><span class="sxs-lookup"><span data-stu-id="32bba-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="32bba-115">Liez une propriété ou un champ à d’autres événements en incluant également un `@bind:event` attribut avec un `event` paramètre.</span><span class="sxs-lookup"><span data-stu-id="32bba-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="32bba-116">L’exemple suivant lie la `CurrentValue` propriété à l' `oninput` événement :</span><span class="sxs-lookup"><span data-stu-id="32bba-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="32bba-117">Contrairement à `onchange` , qui se déclenche lorsque l’élément perd le focus, `oninput` se déclenche lorsque la valeur de la zone de texte change.</span><span class="sxs-lookup"><span data-stu-id="32bba-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="32bba-118">Utilisez `@bind-{ATTRIBUTE}` avec la `@bind-{ATTRIBUTE}:event` syntaxe pour lier des attributs d’élément autres que `value` .</span><span class="sxs-lookup"><span data-stu-id="32bba-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="32bba-119">Dans l’exemple suivant, le style du paragraphe est mis à jour lorsque la `paragraphStyle` valeur change :</span><span class="sxs-lookup"><span data-stu-id="32bba-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

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

<span data-ttu-id="32bba-120">La liaison d’attribut respecte la casse :</span><span class="sxs-lookup"><span data-stu-id="32bba-120">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="32bba-121">`@bind` est valide.</span><span class="sxs-lookup"><span data-stu-id="32bba-121">`@bind` is valid.</span></span>
* <span data-ttu-id="32bba-122">`@Bind`et `@BIND` ne sont pas valides.</span><span class="sxs-lookup"><span data-stu-id="32bba-122">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="32bba-123">Valeurs inanalysables</span><span class="sxs-lookup"><span data-stu-id="32bba-123">Unparsable values</span></span>

<span data-ttu-id="32bba-124">Quand un utilisateur fournit une valeur non analysable à un élément DataBound, la valeur unanalysable est automatiquement rétablie à sa valeur précédente lorsque l’événement de liaison est déclenché.</span><span class="sxs-lookup"><span data-stu-id="32bba-124">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="32bba-125">Examinez le cas suivant :</span><span class="sxs-lookup"><span data-stu-id="32bba-125">Consider the following scenario:</span></span>

* <span data-ttu-id="32bba-126">Un `<input>` élément est lié à un `int` type dont la valeur initiale est `123` :</span><span class="sxs-lookup"><span data-stu-id="32bba-126">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="32bba-127">L’utilisateur met à jour la valeur de l’élément à `123.45` dans la page et modifie le focus de l’élément.</span><span class="sxs-lookup"><span data-stu-id="32bba-127">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="32bba-128">Dans le scénario précédent, la valeur de l’élément est rétablie à `123` .</span><span class="sxs-lookup"><span data-stu-id="32bba-128">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="32bba-129">Lorsque la valeur `123.45` est rejetée en faveur de la valeur d’origine de `123` , l’utilisateur sait que sa valeur n’a pas été acceptée.</span><span class="sxs-lookup"><span data-stu-id="32bba-129">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="32bba-130">Par défaut, la liaison s’applique à l’événement de l’élément `onchange` ( `@bind="{PROPERTY OR FIELD}"` ).</span><span class="sxs-lookup"><span data-stu-id="32bba-130">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="32bba-131">Utilisez `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` pour déclencher la liaison sur un événement différent.</span><span class="sxs-lookup"><span data-stu-id="32bba-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="32bba-132">Pour l' `oninput` événement ( `@bind:event="oninput"` ), la réversion se produit après toute séquence de touches qui introduit une valeur non analysable.</span><span class="sxs-lookup"><span data-stu-id="32bba-132">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="32bba-133">Lorsque vous ciblez l' `oninput` événement avec un `int` type lié, un utilisateur ne peut pas taper un `.` caractère.</span><span class="sxs-lookup"><span data-stu-id="32bba-133">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="32bba-134">Un `.` caractère est immédiatement supprimé, de sorte que l’utilisateur reçoit des commentaires immédiats que seuls des nombres entiers sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="32bba-134">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="32bba-135">Il existe des scénarios dans lesquels le rétablissement de la valeur de l' `oninput` événement n’est pas idéal, par exemple lorsque l’utilisateur doit être autorisé à effacer une valeur non analysable `<input>` .</span><span class="sxs-lookup"><span data-stu-id="32bba-135">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="32bba-136">Les alternatives sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="32bba-136">Alternatives include:</span></span>

* <span data-ttu-id="32bba-137">N’utilisez pas l' `oninput` événement.</span><span class="sxs-lookup"><span data-stu-id="32bba-137">Don't use the `oninput` event.</span></span> <span data-ttu-id="32bba-138">Utilisez l’événement par défaut `onchange` (spécifiez uniquement `@bind="{PROPERTY OR FIELD}"` ), où une valeur non valide n’est pas rétablie tant que l’élément n’a pas perdu le focus.</span><span class="sxs-lookup"><span data-stu-id="32bba-138">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="32bba-139">Effectuer une liaison à un type Nullable, tel que `int?` ou `string` , et fournir une logique personnalisée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="32bba-139">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="32bba-140">Utilisez un [composant de validation de formulaire](xref:blazor/forms-validation), tel que <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> ou <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="32bba-140">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="32bba-141">Les composants de validation de formulaire offrent une prise en charge intégrée pour gérer les entrées non valides.</span><span class="sxs-lookup"><span data-stu-id="32bba-141">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="32bba-142">Composants de validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="32bba-142">Form validation components:</span></span>
  * <span data-ttu-id="32bba-143">Permet à l’utilisateur de fournir une entrée non valide et de recevoir des erreurs de validation sur le associé <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="32bba-143">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="32bba-144">Affichez les erreurs de validation dans l’interface utilisateur sans interférer avec l’utilisateur qui saisit des données Webform supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="32bba-144">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="32bba-145">Chaînes de format</span><span class="sxs-lookup"><span data-stu-id="32bba-145">Format strings</span></span>

<span data-ttu-id="32bba-146">La liaison de données fonctionne avec les <xref:System.DateTime> chaînes de format à l’aide de `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="32bba-146">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="32bba-147">D’autres expressions de mise en forme, telles que les formats de devise ou de nombre, ne sont pas disponibles pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="32bba-147">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="32bba-148">Dans le code précédent, le `<input>` type de champ de l’élément ( `type` ) est défini par défaut sur `text` .</span><span class="sxs-lookup"><span data-stu-id="32bba-148">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="32bba-149">`@bind:format`est pris en charge pour la liaison des types .NET suivants :</span><span class="sxs-lookup"><span data-stu-id="32bba-149">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="32bba-150"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="32bba-150"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="32bba-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="32bba-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="32bba-152">L' `@bind:format` attribut spécifie le format de date à appliquer au `value` de l' `<input>` élément.</span><span class="sxs-lookup"><span data-stu-id="32bba-152">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="32bba-153">Le format est également utilisé pour analyser la valeur lorsqu’un `onchange` événement se produit.</span><span class="sxs-lookup"><span data-stu-id="32bba-153">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="32bba-154">La spécification d’un format pour le `date` type de champ n’est pas recommandée, car Blazor dispose d’une prise en charge intégrée pour mettre en forme les dates.</span><span class="sxs-lookup"><span data-stu-id="32bba-154">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="32bba-155">Malgré la recommandation, utilisez uniquement le `yyyy-MM-dd` format de date pour que la liaison fonctionne correctement si un format est fourni avec le `date` type de champ :</span><span class="sxs-lookup"><span data-stu-id="32bba-155">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="32bba-156">Liaison parent-enfant avec les paramètres de composant</span><span class="sxs-lookup"><span data-stu-id="32bba-156">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="32bba-157">La liaison reconnaît les paramètres du composant, où `@bind-{PROPERTY}` peut lier une valeur de propriété d’un composant parent à un composant enfant.</span><span class="sxs-lookup"><span data-stu-id="32bba-157">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="32bba-158">La liaison d’un enfant à un parent est couverte dans la [liaison enfant-parent avec la section de liaison chaînée](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="32bba-158">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="32bba-159">Le composant enfant suivant ( `ChildComponent` ) a un `Year` paramètre de composant et un `YearChanged` Rappel :</span><span class="sxs-lookup"><span data-stu-id="32bba-159">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="32bba-160">Le <xref:Microsoft.AspNetCore.Components.EventCallback%601> doit être nommé en tant que nom de paramètre de composant suivi du `Changed` suffixe ( `{PARAMETER NAME}Changed` ), `YearChanged` dans l’exemple précédent.</span><span class="sxs-lookup"><span data-stu-id="32bba-160">The <xref:Microsoft.AspNetCore.Components.EventCallback%601> must be named as the component parameter name followed by the `Changed` suffix (`{PARAMETER NAME}Changed`), `YearChanged` in the preceding example.</span></span> <span data-ttu-id="32bba-161">Pour plus d'informations sur le <xref:Microsoft.AspNetCore.Components.EventCallback%601>, consultez <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="32bba-161">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="32bba-162">Le composant parent suivant utilise :</span><span class="sxs-lookup"><span data-stu-id="32bba-162">The following parent component uses:</span></span>

* <span data-ttu-id="32bba-163">`ChildComponent`et lie le `ParentYear` paramètre du parent au `Year` paramètre sur le composant enfant.</span><span class="sxs-lookup"><span data-stu-id="32bba-163">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="32bba-164">L' `onclick` événement est utilisé pour déclencher la `ChangeTheYear` méthode.</span><span class="sxs-lookup"><span data-stu-id="32bba-164">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="32bba-165">Pour plus d'informations, consultez <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="32bba-165">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="32bba-166">Le chargement de `ParentComponent` génère le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="32bba-166">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="32bba-167">Si la valeur de la `ParentYear` propriété est modifiée en sélectionnant le bouton dans le `ParentComponent` , la `Year` propriété de `ChildComponent` est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="32bba-167">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="32bba-168">La nouvelle valeur de `Year` est rendue dans l’interface utilisateur lorsque le est restitué à nouveau `ParentComponent` :</span><span class="sxs-lookup"><span data-stu-id="32bba-168">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="32bba-169">Le `Year` paramètre peut être lié, car il a un `YearChanged` événement auxiliaire qui correspond au type du `Year` paramètre.</span><span class="sxs-lookup"><span data-stu-id="32bba-169">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="32bba-170">Par Convention, `<ChildComponent @bind-Year="ParentYear" />` est essentiellement équivalent à l’écriture :</span><span class="sxs-lookup"><span data-stu-id="32bba-170">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="32bba-171">En général, une propriété peut être liée à un gestionnaire d’événements correspondant en incluant un `@bind-{PROPRETY}:event` attribut.</span><span class="sxs-lookup"><span data-stu-id="32bba-171">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="32bba-172">Par exemple, la propriété `MyProp` peut être liée à à `MyEventHandler` l’aide des deux attributs suivants :</span><span class="sxs-lookup"><span data-stu-id="32bba-172">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="32bba-173">Liaison enfant-parent avec liaison chaînée</span><span class="sxs-lookup"><span data-stu-id="32bba-173">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="32bba-174">Un scénario courant consiste à chaîner un paramètre lié aux données à un élément de page dans la sortie du composant.</span><span class="sxs-lookup"><span data-stu-id="32bba-174">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="32bba-175">Ce scénario est appelé *liaison chaînée* car plusieurs niveaux de liaison se produisent simultanément.</span><span class="sxs-lookup"><span data-stu-id="32bba-175">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="32bba-176">Une liaison chaînée ne peut pas être implémentée avec [`@bind`](xref:mvc/views/razor#bind) la syntaxe dans l’élément de la page.</span><span class="sxs-lookup"><span data-stu-id="32bba-176">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="32bba-177">Le gestionnaire d’événements et la valeur doivent être spécifiés séparément.</span><span class="sxs-lookup"><span data-stu-id="32bba-177">The event handler and value must be specified separately.</span></span> <span data-ttu-id="32bba-178">Toutefois, un composant parent peut utiliser [`@bind`](xref:mvc/views/razor#bind) la syntaxe avec le paramètre du composant.</span><span class="sxs-lookup"><span data-stu-id="32bba-178">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="32bba-179">Le `PasswordField` composant suivant ( `PasswordField.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="32bba-179">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="32bba-180">Définit `<input>` la valeur d’un élément sur `Password` une propriété.</span><span class="sxs-lookup"><span data-stu-id="32bba-180">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="32bba-181">Expose les modifications de la `Password` propriété à un composant parent avec un [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="32bba-181">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="32bba-182">Utilise l' `onclick` événement pour déclencher la `ToggleShowPassword` méthode.</span><span class="sxs-lookup"><span data-stu-id="32bba-182">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="32bba-183">Pour plus d'informations, consultez <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="32bba-183">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="32bba-184">Le `PasswordField` composant est utilisé dans un autre composant :</span><span class="sxs-lookup"><span data-stu-id="32bba-184">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="32bba-185">Pour effectuer des vérifications ou des erreurs d’interruption sur le mot de passe dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="32bba-185">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="32bba-186">Créez un champ de stockage pour `Password` ( `password` dans l’exemple de code suivant).</span><span class="sxs-lookup"><span data-stu-id="32bba-186">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="32bba-187">Effectuez les vérifications ou les erreurs d’interruption dans la méthode `Password` Setter.</span><span class="sxs-lookup"><span data-stu-id="32bba-187">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="32bba-188">L’exemple suivant fournit un retour immédiat à l’utilisateur si un espace est utilisé dans la valeur du mot de passe :</span><span class="sxs-lookup"><span data-stu-id="32bba-188">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="32bba-189">Ressources complémentaires</span><span class="sxs-lookup"><span data-stu-id="32bba-189">Additional resources</span></span>

* [<span data-ttu-id="32bba-190">Lier à des cases d’option dans un formulaire</span><span class="sxs-lookup"><span data-stu-id="32bba-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="32bba-191">Liaison `<select>` d’options d’éléments à `null` des valeurs d’objet C# dans un formulaire</span><span class="sxs-lookup"><span data-stu-id="32bba-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
