---
title: ASP.NET Core Blazor les formulaires et la validation
author: guardrex
description: Découvrez comment utiliser des formulaires et des scénarios de validation Blazorde champ dans.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: ec2bc2867acdd1c9be42f77cb38be36abb8c8108
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967478"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="ca051-103">ASP.NET Core des formulaires et des validations éblouissants</span><span class="sxs-lookup"><span data-stu-id="ca051-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="ca051-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ca051-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ca051-105">Les formulaires et la validation sont pris en charge dans éblouissant à l’aide d' [Annotations de données](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="ca051-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="ca051-106">Le type `ExampleModel` suivant définit la logique de validation à l’aide d’annotations de données :</span><span class="sxs-lookup"><span data-stu-id="ca051-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="ca051-107">Un formulaire est défini à l' `EditForm` aide du composant.</span><span class="sxs-lookup"><span data-stu-id="ca051-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="ca051-108">Le formulaire suivant illustre des éléments typiques, des composants et du code Razor :</span><span class="sxs-lookup"><span data-stu-id="ca051-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="ca051-109">Dans l'exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="ca051-109">In the preceding example:</span></span>

* <span data-ttu-id="ca051-110">Le formulaire valide l’entrée utilisateur dans le `name` champ à l’aide de la validation `ExampleModel` définie dans le type.</span><span class="sxs-lookup"><span data-stu-id="ca051-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="ca051-111">Le modèle est créé dans le bloc du `@code` composant et conservé dans un champ privé (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="ca051-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="ca051-112">Le champ est assigné à l' `Model` attribut de l' `<EditForm>` élément.</span><span class="sxs-lookup"><span data-stu-id="ca051-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="ca051-113">Liaisons `InputText` du `@bind-Value` composant :</span><span class="sxs-lookup"><span data-stu-id="ca051-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="ca051-114">Propriété de modèle (`exampleModel.Name`) de la `InputText` propriété du `Value` composant.</span><span class="sxs-lookup"><span data-stu-id="ca051-114">The model property (`exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="ca051-115">Délégué d’événement de modification à `InputText` la propriété `ValueChanged` du composant.</span><span class="sxs-lookup"><span data-stu-id="ca051-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="ca051-116">Le `DataAnnotationsValidator` composant attache la prise en charge de la validation à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="ca051-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="ca051-117">Le `ValidationSummary` composant résume les messages de validation.</span><span class="sxs-lookup"><span data-stu-id="ca051-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="ca051-118">`HandleValidSubmit`est déclenché quand le formulaire est envoyé avec succès (réussit la validation).</span><span class="sxs-lookup"><span data-stu-id="ca051-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="ca051-119">Un ensemble de composants d’entrée intégrés est disponible pour recevoir et valider les entrées utilisateur.</span><span class="sxs-lookup"><span data-stu-id="ca051-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="ca051-120">Les entrées sont validées lorsqu’elles sont modifiées et lorsqu’un formulaire est envoyé.</span><span class="sxs-lookup"><span data-stu-id="ca051-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="ca051-121">Les composants d’entrée disponibles sont répertoriés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="ca051-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="ca051-122">Composant d’entrée</span><span class="sxs-lookup"><span data-stu-id="ca051-122">Input component</span></span> | <span data-ttu-id="ca051-123">Rendu comme&hellip;</span><span class="sxs-lookup"><span data-stu-id="ca051-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="ca051-124">Tous les composants d’entrée, y `EditForm`compris, prennent en charge les attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="ca051-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="ca051-125">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l’élément HTML rendu.</span><span class="sxs-lookup"><span data-stu-id="ca051-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="ca051-126">Les composants d’entrée fournissent le comportement par défaut pour la validation lors de la modification et la modification de leur classe CSS pour refléter l’état du champ.</span><span class="sxs-lookup"><span data-stu-id="ca051-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="ca051-127">Certains composants incluent une logique d’analyse utile.</span><span class="sxs-lookup"><span data-stu-id="ca051-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="ca051-128">Par exemple, `InputDate` et `InputNumber` gèrent correctement les valeurs non analysables en les inscrivant en tant qu’erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="ca051-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="ca051-129">Les types qui peuvent accepter des valeurs NULL prennent également en charge la possibilité de valeur null du `int?`champ cible (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="ca051-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="ca051-130">Le type `Starship` suivant définit la logique de validation à l’aide d’un plus grand ensemble de propriétés et `ExampleModel`d’annotations de données que la précédente :</span><span class="sxs-lookup"><span data-stu-id="ca051-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="ca051-131">Dans l’exemple précédent, `Description` est facultatif, car aucune annotation de données n’est présente.</span><span class="sxs-lookup"><span data-stu-id="ca051-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="ca051-132">La forme suivante valide l’entrée d’utilisateur à l’aide de la `Starship` validation définie dans le modèle :</span><span class="sxs-lookup"><span data-stu-id="ca051-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
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
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="ca051-133">`EditForm` Crée un `EditContext` comme valeur en [cascade](xref:blazor/components#cascading-values-and-parameters) qui effectue le suivi des métadonnées relatives au processus de modification, notamment les champs qui ont été modifiés et les messages de validation actuels.</span><span class="sxs-lookup"><span data-stu-id="ca051-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="ca051-134">Le `EditForm` fournit également des événements pratiques pour les envois valides`OnValidSubmit`et `OnInvalidSubmit`non valides (,).</span><span class="sxs-lookup"><span data-stu-id="ca051-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="ca051-135">Vous pouvez également utiliser `OnSubmit` pour déclencher la validation et vérifier les valeurs de champ avec un code de validation personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ca051-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="ca051-136">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="ca051-136">In the following example:</span></span>

* <span data-ttu-id="ca051-137">La `HandleSubmit` méthode s’exécute lorsque le bouton **Envoyer** est sélectionné.</span><span class="sxs-lookup"><span data-stu-id="ca051-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="ca051-138">Le formulaire est validé à l’aide de `EditContext`la.</span><span class="sxs-lookup"><span data-stu-id="ca051-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="ca051-139">Le formulaire est validé en passant `EditContext` à la `ServerValidate` méthode qui appelle un point de terminaison d’API Web sur le serveur (*non affiché*).</span><span class="sxs-lookup"><span data-stu-id="ca051-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="ca051-140">Du code supplémentaire est exécuté en fonction du résultat de la validation côté client et côté serveur en vérifiant `isValid`.</span><span class="sxs-lookup"><span data-stu-id="ca051-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="ca051-141">InputText en fonction de l’événement d’entrée</span><span class="sxs-lookup"><span data-stu-id="ca051-141">InputText based on the input event</span></span>

<span data-ttu-id="ca051-142">Utilisez le `InputText` composant pour créer un composant personnalisé qui utilise l' `input` événement au lieu de `change` l’événement.</span><span class="sxs-lookup"><span data-stu-id="ca051-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="ca051-143">Créez un composant avec le balisage suivant et utilisez le composant de la `InputText` même façon que :</span><span class="sxs-lookup"><span data-stu-id="ca051-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="ca051-144">Utiliser des cases d’option</span><span class="sxs-lookup"><span data-stu-id="ca051-144">Work with radio buttons</span></span>

<span data-ttu-id="ca051-145">Lorsque vous utilisez des cases d’option dans un formulaire, la liaison de données est gérée différemment des autres éléments, car les cases d’option sont évaluées en tant que groupe.</span><span class="sxs-lookup"><span data-stu-id="ca051-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="ca051-146">La valeur de chaque case d’option est fixe, mais la valeur du groupe de cases d’option est la valeur de la case d’option sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="ca051-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="ca051-147">L’exemple suivant montre comment :</span><span class="sxs-lookup"><span data-stu-id="ca051-147">The following example shows how to:</span></span>

* <span data-ttu-id="ca051-148">Gérer la liaison de données pour un groupe de cases d’option.</span><span class="sxs-lookup"><span data-stu-id="ca051-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="ca051-149">Prendre en charge la validation `InputRadio` à l’aide d’un composant personnalisé.</span><span class="sxs-lookup"><span data-stu-id="ca051-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="ca051-150">L’exemple `EditForm` suivant utilise le `InputRadio` composant précédent pour obtenir et valider une évaluation de l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="ca051-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

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

## <a name="validation-support"></a><span data-ttu-id="ca051-151">Prise en charge de la validation</span><span class="sxs-lookup"><span data-stu-id="ca051-151">Validation support</span></span>

<span data-ttu-id="ca051-152">Le `DataAnnotationsValidator` composant attache la prise en charge de la validation à l’aide d' `EditContext`annotations de données à la Cascaded.</span><span class="sxs-lookup"><span data-stu-id="ca051-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="ca051-153">L’activation de la prise en charge de la validation à l’aide d’annotations de données requiert ce geste explicite.</span><span class="sxs-lookup"><span data-stu-id="ca051-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="ca051-154">Pour utiliser un système de validation différent des annotations de données `DataAnnotationsValidator` , remplacez par une implémentation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="ca051-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="ca051-155">L’implémentation de ASP.net Core est disponible pour l’inspection dans la source de référence : [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="ca051-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="ca051-156">effectue deux types de validation :</span><span class="sxs-lookup"><span data-stu-id="ca051-156"> performs two types of validation:</span></span>

* <span data-ttu-id="ca051-157">La *validation de champ* est effectuée lorsque l’utilisateur quitte un champ.</span><span class="sxs-lookup"><span data-stu-id="ca051-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="ca051-158">Pendant la validation de champ `DataAnnotationsValidator` , le composant associe tous les résultats de validation signalés au champ.</span><span class="sxs-lookup"><span data-stu-id="ca051-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="ca051-159">La *validation du modèle* est effectuée lorsque l’utilisateur envoie le formulaire.</span><span class="sxs-lookup"><span data-stu-id="ca051-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="ca051-160">Lors de la validation du `DataAnnotationsValidator` modèle, le composant tente de déterminer le champ en fonction du nom du membre que le résultat de validation signale.</span><span class="sxs-lookup"><span data-stu-id="ca051-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="ca051-161">Les résultats de validation qui ne sont pas associés à un membre individuel sont associés au modèle plutôt qu’à un champ.</span><span class="sxs-lookup"><span data-stu-id="ca051-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="ca051-162">Résumé des validations et composants de message de validation</span><span class="sxs-lookup"><span data-stu-id="ca051-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="ca051-163">Le `ValidationSummary` composant résume tous les messages de validation, ce qui est similaire au [tag Helper de résumé de validation](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="ca051-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="ca051-164">Messages de validation de sortie pour un modèle spécifique `Model` avec le paramètre :</span><span class="sxs-lookup"><span data-stu-id="ca051-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="ca051-165">Le `ValidationMessage` composant affiche des messages de validation pour un champ spécifique, qui est similaire au [tag Helper de message de validation](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="ca051-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="ca051-166">Spécifiez le champ pour la validation `For` avec l’attribut et une expression lambda qui nomme la propriété de modèle :</span><span class="sxs-lookup"><span data-stu-id="ca051-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="ca051-167">Les `ValidationMessage` composants `ValidationSummary` et prennent en charge des attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="ca051-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="ca051-168">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté `<div>` à `<ul>` l’élément ou généré.</span><span class="sxs-lookup"><span data-stu-id="ca051-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="ca051-169">Attributs de validation personnalisés</span><span class="sxs-lookup"><span data-stu-id="ca051-169">Custom validation attributes</span></span>

<span data-ttu-id="ca051-170">Pour vous assurer qu’un résultat de validation est correctement associé à un champ lors de l’utilisation d’un [attribut de validation personnalisé](xref:mvc/models/validation#custom-attributes), transmettez le contexte de validation lors de <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> la création du <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="ca051-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="ca051-171">package de validation des annotations de données</span><span class="sxs-lookup"><span data-stu-id="ca051-171"> data annotations validation package</span></span>

<span data-ttu-id="ca051-172">[Microsoft. AspNetCore. Components. DataAnnotations. validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) est un package qui remplit les intervalles d’expérience de validation `DataAnnotationsValidator` à l’aide du composant.</span><span class="sxs-lookup"><span data-stu-id="ca051-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="ca051-173">Le package est actuellement *expérimental*.</span><span class="sxs-lookup"><span data-stu-id="ca051-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="ca051-174">Attribut [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="ca051-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="ca051-175">Le <xref:System.ComponentModel.DataAnnotations.CompareAttribute> ne fonctionne pas correctement avec `DataAnnotationsValidator` le composant, car il n’associe pas le résultat de la validation à un membre spécifique.</span><span class="sxs-lookup"><span data-stu-id="ca051-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="ca051-176">Cela peut entraîner un comportement incohérent entre la validation au niveau du champ et le moment où la totalité du modèle est validée sur une soumission.</span><span class="sxs-lookup"><span data-stu-id="ca051-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="ca051-177">Le package *expérimental* [Microsoft. AspNetCore. Components. DataAnnotations. validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduit un attribut de `ComparePropertyAttribute`validation supplémentaire,, qui contourne ces limitations.</span><span class="sxs-lookup"><span data-stu-id="ca051-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="ca051-178">Dans une Blazor application, `[CompareProperty]` est un remplacement direct de l' `[Compare]` attribut.</span><span class="sxs-lookup"><span data-stu-id="ca051-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="ca051-179">Modèles imbriqués, types de collection et types complexes</span><span class="sxs-lookup"><span data-stu-id="ca051-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="ca051-180">prend en charge la validation de l’entrée de formulaire à l’aide d’annotations de données avec le intégré `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="ca051-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="ca051-181">Toutefois, l `DataAnnotationsValidator` 'valide uniquement les propriétés de niveau supérieur du modèle lié au formulaire qui ne sont pas des propriétés de type collection ou complexe.</span><span class="sxs-lookup"><span data-stu-id="ca051-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="ca051-182">Pour valider le graphique d’objets entier du modèle lié, y compris les propriétés de type collection et de type `ObjectGraphDataAnnotationsValidator` complexe, utilisez le fourni par le package *expérimental* [Microsoft. AspNetCore. Components. DataAnnotations. validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="ca051-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="ca051-183">Annotez les propriétés `[ValidateComplexType]`de modèle avec.</span><span class="sxs-lookup"><span data-stu-id="ca051-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="ca051-184">Dans les classes de modèle suivantes, `ShipDescription` la classe contient des annotations de données supplémentaires à valider lorsque le modèle est lié au formulaire :</span><span class="sxs-lookup"><span data-stu-id="ca051-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="ca051-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca051-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="ca051-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca051-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="ca051-187">Activer le bouton Envoyer en fonction de la validation de formulaire</span><span class="sxs-lookup"><span data-stu-id="ca051-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="ca051-188">Pour activer et désactiver le bouton Envoyer en fonction de la validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="ca051-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="ca051-189">Utilisez le du `EditContext` formulaire pour assigner le modèle lorsque le composant est initialisé.</span><span class="sxs-lookup"><span data-stu-id="ca051-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="ca051-190">Validez le formulaire dans le rappel `OnFieldChanged` du contexte pour activer et désactiver le bouton Envoyer.</span><span class="sxs-lookup"><span data-stu-id="ca051-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="ca051-191">Décrochez le gestionnaire d’événements dans `Dispose` la méthode.</span><span class="sxs-lookup"><span data-stu-id="ca051-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="ca051-192">Pour plus d'informations, consultez <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="ca051-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="ca051-193">Dans l’exemple précédent, `formInvalid` affectez `false` à la valeur si :</span><span class="sxs-lookup"><span data-stu-id="ca051-193">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="ca051-194">Le formulaire est préchargé avec des valeurs par défaut valides.</span><span class="sxs-lookup"><span data-stu-id="ca051-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="ca051-195">Vous voulez que le bouton envoyer soit activé lors du chargement du formulaire.</span><span class="sxs-lookup"><span data-stu-id="ca051-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="ca051-196">L’un des effets secondaires de l’approche précédente est `ValidationSummary` qu’un composant est rempli avec des champs non valides une fois que l’utilisateur interagit avec un champ quelconque.</span><span class="sxs-lookup"><span data-stu-id="ca051-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="ca051-197">Ce scénario peut être traité de l’une des manières suivantes :</span><span class="sxs-lookup"><span data-stu-id="ca051-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="ca051-198">N’utilisez pas `ValidationSummary` un composant sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="ca051-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="ca051-199">Rendre le `ValidationSummary` composant visible lorsque le bouton Envoyer est sélectionné (par exemple, dans une `HandleValidSubmit` méthode).</span><span class="sxs-lookup"><span data-stu-id="ca051-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```
