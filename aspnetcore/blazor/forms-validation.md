---
title: ASP.NET formulaires Blazor de base et validation
author: guardrex
description: Apprenez à utiliser les formulaires Blazoret les scénarios de validation sur le terrain dans .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218958"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="80c48-103">ASP.NET formulaires et validations Core Blazor</span><span class="sxs-lookup"><span data-stu-id="80c48-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="80c48-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="80c48-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="80c48-105">Les formulaires et la validation sont pris en charge dans Blazor à [l’aide d’annotations de données](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="80c48-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="80c48-106">Le `ExampleModel` type suivant définit la logique de validation à l’aide d’annotations de données :</span><span class="sxs-lookup"><span data-stu-id="80c48-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="80c48-107">Un formulaire est `EditForm` défini à l’aide du composant.</span><span class="sxs-lookup"><span data-stu-id="80c48-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="80c48-108">Le formulaire suivant montre des éléments typiques, des composants et du code Razor :</span><span class="sxs-lookup"><span data-stu-id="80c48-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="80c48-109">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="80c48-109">In the preceding example:</span></span>

* <span data-ttu-id="80c48-110">Le formulaire valide l’entrée de l’utilisateur sur le `name` terrain à l’aide de la validation définie dans le `ExampleModel` type.</span><span class="sxs-lookup"><span data-stu-id="80c48-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="80c48-111">Le modèle est créé dans `@code` le bloc du composant`_exampleModel`et tenu dans un champ privé ( ).</span><span class="sxs-lookup"><span data-stu-id="80c48-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="80c48-112">Le champ est `Model` attribué à `<EditForm>` l’attribut de l’élément.</span><span class="sxs-lookup"><span data-stu-id="80c48-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="80c48-113">Le `InputText` composant `@bind-Value` lie :</span><span class="sxs-lookup"><span data-stu-id="80c48-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="80c48-114">La propriété`_exampleModel.Name`du modèle `InputText` ( `Value` ) à la propriété du composant.</span><span class="sxs-lookup"><span data-stu-id="80c48-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="80c48-115">Un rendez-vous `InputText` de changement `ValueChanged` délégué à la propriété du composant.</span><span class="sxs-lookup"><span data-stu-id="80c48-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="80c48-116">Le `DataAnnotationsValidator` composant attache le support de validation à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="80c48-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="80c48-117">Le `ValidationSummary` composant résume les messages de validation.</span><span class="sxs-lookup"><span data-stu-id="80c48-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="80c48-118">`HandleValidSubmit`est déclenché lorsque le formulaire se soumet avec succès (validation de la passe).</span><span class="sxs-lookup"><span data-stu-id="80c48-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="80c48-119">Un ensemble de composants d’entrée intégrés sont disponibles pour recevoir et valider l’entrée de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="80c48-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="80c48-120">Les entrées sont validées lorsqu’elles sont modifiées et lorsqu’un formulaire est soumis.</span><span class="sxs-lookup"><span data-stu-id="80c48-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="80c48-121">Les composants d’entrée disponibles sont indiqués dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="80c48-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="80c48-122">Composant d’entrée</span><span class="sxs-lookup"><span data-stu-id="80c48-122">Input component</span></span> | <span data-ttu-id="80c48-123">Rendu comme&hellip;</span><span class="sxs-lookup"><span data-stu-id="80c48-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="80c48-124">Tous les composants d’entrée, y compris, `EditForm`prennent en charge les attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="80c48-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="80c48-125">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l’élément HTML rendu.</span><span class="sxs-lookup"><span data-stu-id="80c48-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="80c48-126">Les composants d’entrée fournissent un comportement par défaut pour valider sur modifier et modifier leur classe CSS pour refléter l’état de champ.</span><span class="sxs-lookup"><span data-stu-id="80c48-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="80c48-127">Certains composants incluent la logique d’analyse utile.</span><span class="sxs-lookup"><span data-stu-id="80c48-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="80c48-128">Par `InputDate` exemple, `InputNumber` et gérer les valeurs impardonnables gracieusement en les enregistrant comme erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="80c48-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="80c48-129">Les types qui peuvent accepter des valeurs nulles soutiennent `int?`également l’annulation du champ cible (par exemple, ).</span><span class="sxs-lookup"><span data-stu-id="80c48-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="80c48-130">Le `Starship` type suivant définit la logique de validation à l’aide `ExampleModel`d’un ensemble plus large de propriétés et d’annotations de données que les précédents :</span><span class="sxs-lookup"><span data-stu-id="80c48-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="80c48-131">Dans l’exemple `Description` précédent, est facultatif parce qu’aucune annotation de données n’est présente.</span><span class="sxs-lookup"><span data-stu-id="80c48-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="80c48-132">Le formulaire suivant valide l’entrée de `Starship` l’utilisateur à l’aide de la validation définie dans le modèle :</span><span class="sxs-lookup"><span data-stu-id="80c48-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="80c48-133">Le `EditForm` crée `EditContext` une valeur en [cascade](xref:blazor/components#cascading-values-and-parameters) qui suit les métadonnées sur le processus de modification, y compris les champs ont été modifiés et les messages de validation en cours.</span><span class="sxs-lookup"><span data-stu-id="80c48-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="80c48-134">Le `EditForm` fournit également des événements pratiques`OnValidSubmit`pour `OnInvalidSubmit`valide et invalide soumet ( , ).</span><span class="sxs-lookup"><span data-stu-id="80c48-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="80c48-135">Vous pouvez `OnSubmit` également utiliser pour déclencher la validation et vérifier les valeurs de champ avec le code de validation personnalisé.</span><span class="sxs-lookup"><span data-stu-id="80c48-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="80c48-136">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="80c48-136">In the following example:</span></span>

* <span data-ttu-id="80c48-137">La `HandleSubmit` méthode s’exécute lorsque le bouton **Soumettre** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="80c48-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="80c48-138">Le formulaire est validé à `EditContext`l’aide de la forme .</span><span class="sxs-lookup"><span data-stu-id="80c48-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="80c48-139">Le formulaire est également validé `EditContext` en `ServerValidate` passant la méthode qui appelle un point de terminaison API web sur le serveur *(non affiché*).</span><span class="sxs-lookup"><span data-stu-id="80c48-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="80c48-140">Le code supplémentaire est exécuté en fonction du résultat de `isValid`la validation côté client et serveur en vérifiant .</span><span class="sxs-lookup"><span data-stu-id="80c48-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="80c48-141">InputText basé sur l’événement d’entrée</span><span class="sxs-lookup"><span data-stu-id="80c48-141">InputText based on the input event</span></span>

<span data-ttu-id="80c48-142">Utilisez `InputText` le composant pour créer un `input` composant personnalisé `change` qui utilise l’événement au lieu de l’événement.</span><span class="sxs-lookup"><span data-stu-id="80c48-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="80c48-143">Créez un composant avec la balisage suivante `InputText` et utilisez le composant comme il est utilisé :</span><span class="sxs-lookup"><span data-stu-id="80c48-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="80c48-144">Travailler avec des boutons radio</span><span class="sxs-lookup"><span data-stu-id="80c48-144">Work with radio buttons</span></span>

<span data-ttu-id="80c48-145">Lorsque vous travaillez avec des boutons radio sous une forme, la liaison des données est traitée différemment des autres éléments parce que les boutons radio sont évalués en groupe.</span><span class="sxs-lookup"><span data-stu-id="80c48-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="80c48-146">La valeur de chaque bouton radio est fixe, mais la valeur du groupe de boutons radio est la valeur du bouton radio sélectionné.</span><span class="sxs-lookup"><span data-stu-id="80c48-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="80c48-147">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="80c48-147">The following example shows how to:</span></span>

* <span data-ttu-id="80c48-148">Gérer la liaison de données pour un groupe de boutons radio.</span><span class="sxs-lookup"><span data-stu-id="80c48-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="80c48-149">Validation de prise `InputRadio` en charge à l’aide d’un composant personnalisé.</span><span class="sxs-lookup"><span data-stu-id="80c48-149">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="80c48-150">Ce `EditForm` qui suit `InputRadio` utilise le composant précédent pour obtenir et valider une note de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="80c48-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="80c48-151">Soutien à la validation</span><span class="sxs-lookup"><span data-stu-id="80c48-151">Validation support</span></span>

<span data-ttu-id="80c48-152">Le `DataAnnotationsValidator` composant attache le support de validation à `EditContext`l’aide d’annotations de données à la cascade .</span><span class="sxs-lookup"><span data-stu-id="80c48-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="80c48-153">Permettre le support de validation à l’aide d’annotations de données nécessite ce geste explicite.</span><span class="sxs-lookup"><span data-stu-id="80c48-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="80c48-154">Pour utiliser un système de validation différent des `DataAnnotationsValidator` annotations de données, remplacez-le par une implémentation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="80c48-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="80c48-155">La mise en œuvre de base ASP.NET est disponible pour inspection dans la source de référence: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="80c48-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="80c48-156">effectue deux types de validation :</span><span class="sxs-lookup"><span data-stu-id="80c48-156"> performs two types of validation:</span></span>

* <span data-ttu-id="80c48-157">*La validation de champ* est effectuée lorsque l’utilisateur s’œil hors d’un champ.</span><span class="sxs-lookup"><span data-stu-id="80c48-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="80c48-158">Lors de la `DataAnnotationsValidator` validation sur le terrain, le composant associe tous les résultats de validation signalés sur le terrain.</span><span class="sxs-lookup"><span data-stu-id="80c48-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="80c48-159">*La validation du modèle* est effectuée lorsque l’utilisateur soumet le formulaire.</span><span class="sxs-lookup"><span data-stu-id="80c48-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="80c48-160">Lors de la `DataAnnotationsValidator` validation du modèle, le composant tente de déterminer le champ en fonction du nom du membre que le résultat de validation indique.</span><span class="sxs-lookup"><span data-stu-id="80c48-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="80c48-161">Les résultats de validation qui ne sont pas associés à un membre individuel sont associés au modèle plutôt qu’à un champ.</span><span class="sxs-lookup"><span data-stu-id="80c48-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="80c48-162">Validation Résumé et Validation Des composants du message</span><span class="sxs-lookup"><span data-stu-id="80c48-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="80c48-163">Le `ValidationSummary` composant résume tous les messages de validation, qui est similaire à [l’aide à l’étiquette résumée de validation](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="80c48-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="80c48-164">Messages de validation de sortie `Model` pour un modèle spécifique avec le paramètre :</span><span class="sxs-lookup"><span data-stu-id="80c48-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="80c48-165">Le `ValidationMessage` composant affiche des messages de validation pour un champ spécifique, qui est similaire à [l’aide à l’étiquette de message de validation](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="80c48-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="80c48-166">Spécifier le `For` champ de validation avec l’attribut et une expression lambda nommant la propriété modèle :</span><span class="sxs-lookup"><span data-stu-id="80c48-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="80c48-167">Les `ValidationMessage` `ValidationSummary` éléments et les composants sont à l’appui d’attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="80c48-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="80c48-168">Tout attribut qui ne correspond pas à un `<div>` `<ul>` paramètre de composant est ajouté au produit ou à l’élément.</span><span class="sxs-lookup"><span data-stu-id="80c48-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="80c48-169">Attributs de validation personnalisés</span><span class="sxs-lookup"><span data-stu-id="80c48-169">Custom validation attributes</span></span>

<span data-ttu-id="80c48-170">Pour s’assurer qu’un résultat de validation est correctement associé à un <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> champ lors <xref:System.ComponentModel.DataAnnotations.ValidationResult>de l’utilisation d’un attribut de [validation personnalisé,](xref:mvc/models/validation#custom-attributes)passez le contexte de validation lors de la création de la :</span><span class="sxs-lookup"><span data-stu-id="80c48-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="80c48-171">paquet de validation des annotations de données</span><span class="sxs-lookup"><span data-stu-id="80c48-171"> data annotations validation package</span></span>

<span data-ttu-id="80c48-172">Le [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) est un paquet qui comble les lacunes de l’expérience de validation à l’aide du `DataAnnotationsValidator` composant.</span><span class="sxs-lookup"><span data-stu-id="80c48-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="80c48-173">Le paquet est actuellement *expérimental*.</span><span class="sxs-lookup"><span data-stu-id="80c48-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="80c48-174">[CompareProperty] attribut</span><span class="sxs-lookup"><span data-stu-id="80c48-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="80c48-175">Le <xref:System.ComponentModel.DataAnnotations.CompareAttribute> ne fonctionne pas `DataAnnotationsValidator` bien avec le composant, car il n’associe pas le résultat de validation avec un membre spécifique.</span><span class="sxs-lookup"><span data-stu-id="80c48-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="80c48-176">Cela peut entraîner un comportement incohérent entre la validation au niveau du terrain et lorsque l’ensemble du modèle est validé sur un soumettre.</span><span class="sxs-lookup"><span data-stu-id="80c48-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="80c48-177">Le paquet expérimental [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* introduit un `ComparePropertyAttribute`attribut de validation supplémentaire, qui fonctionne autour de ces limitations.</span><span class="sxs-lookup"><span data-stu-id="80c48-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="80c48-178">Dans Blazor une `[CompareProperty]` application, est un `[Compare]` remplacement direct de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="80c48-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="80c48-179">Modèles imbriqués, types de collecte et types complexes</span><span class="sxs-lookup"><span data-stu-id="80c48-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="80c48-180">fournit un support pour valider l’entrée de formulaire `DataAnnotationsValidator`à l’aide d’annotations de données avec le intégré .</span><span class="sxs-lookup"><span data-stu-id="80c48-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="80c48-181">Toutefois, `DataAnnotationsValidator` le seul valide les propriétés de haut niveau du modèle liées à la forme qui ne sont pas des propriétés de type collection ou complexe.</span><span class="sxs-lookup"><span data-stu-id="80c48-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="80c48-182">Pour valider l’ensemble du graphique d’objets du modèle lié, y compris les propriétés de type collecte et complexe, utilisez le `ObjectGraphDataAnnotationsValidator` produit fourni par le package *expérimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="80c48-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="80c48-183">Annoter les `[ValidateComplexType]`propriétés du modèle avec .</span><span class="sxs-lookup"><span data-stu-id="80c48-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="80c48-184">Dans les classes de `ShipDescription` modèles suivantes, la classe contient des annotations de données supplémentaires à valider lorsque le modèle est lié à la forme :</span><span class="sxs-lookup"><span data-stu-id="80c48-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="80c48-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="80c48-185">*Starship.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="80c48-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="80c48-186">*ShipDescription.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="80c48-187">Activez le bouton soumettre en fonction de la validation du formulaire</span><span class="sxs-lookup"><span data-stu-id="80c48-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="80c48-188">Pour activer et désactiver le bouton soumettre en fonction de la validation du formulaire :</span><span class="sxs-lookup"><span data-stu-id="80c48-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="80c48-189">Utilisez le formulaire `EditContext` pour attribuer le modèle lorsque le composant est initialisé.</span><span class="sxs-lookup"><span data-stu-id="80c48-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="80c48-190">Validez le formulaire dans `OnFieldChanged` le rappel du contexte pour activer et désactiver le bouton soumettre.</span><span class="sxs-lookup"><span data-stu-id="80c48-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="80c48-191">Débranchez le gestionnaire `Dispose` d’événements dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="80c48-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="80c48-192">Pour plus d’informations, consultez <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="80c48-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="80c48-193">Dans l’exemple `_formInvalid` précédent, définissez `false` si :</span><span class="sxs-lookup"><span data-stu-id="80c48-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="80c48-194">Le formulaire est préchargé avec des valeurs valides par défaut.</span><span class="sxs-lookup"><span data-stu-id="80c48-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="80c48-195">Vous voulez que le bouton soumettre soit activé lorsque le formulaire se charge.</span><span class="sxs-lookup"><span data-stu-id="80c48-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="80c48-196">Un effet secondaire de l’approche précédente est qu’un `ValidationSummary` composant est peuplé de champs invalides après que l’utilisateur interagit avec n’importe quel champ.</span><span class="sxs-lookup"><span data-stu-id="80c48-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="80c48-197">Ce scénario peut être abordé de l’une ou l’autre des façons suivantes :</span><span class="sxs-lookup"><span data-stu-id="80c48-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="80c48-198">N’utilisez pas `ValidationSummary` de composant sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="80c48-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="80c48-199">Rendre `ValidationSummary` le composant visible lorsque le bouton soumettre `HandleValidSubmit` est sélectionné (par exemple, dans une méthode).</span><span class="sxs-lookup"><span data-stu-id="80c48-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
