---
title: ASP.NET Core des formulaires et des validations éblouissants
author: guardrex
description: Découvrez comment utiliser des formulaires et des scénarios de validation de champ dans éblouissant.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030332"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="80661-103">ASP.NET Core des formulaires et des validations éblouissants</span><span class="sxs-lookup"><span data-stu-id="80661-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="80661-104">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="80661-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="80661-105">Les formulaires et la validation sont pris en charge dans éblouissant à l’aide d’annotations de [données](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="80661-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="80661-106">Les formulaires et les scénarios de validation sont susceptibles de changer avec chaque version préliminaire.</span><span class="sxs-lookup"><span data-stu-id="80661-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="80661-107">Le type `ExampleModel` suivant définit la logique de validation à l’aide d’annotations de données:</span><span class="sxs-lookup"><span data-stu-id="80661-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="80661-108">Un formulaire est défini à l' `EditForm` aide du composant.</span><span class="sxs-lookup"><span data-stu-id="80661-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="80661-109">Le formulaire suivant illustre des éléments typiques, des composants et du code Razor:</span><span class="sxs-lookup"><span data-stu-id="80661-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

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

* <span data-ttu-id="80661-110">Le formulaire valide l’entrée utilisateur dans le `name` champ à l’aide de la validation `ExampleModel` définie dans le type.</span><span class="sxs-lookup"><span data-stu-id="80661-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="80661-111">Le modèle est créé dans le bloc du `@code` composant et conservé dans un champ privé (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="80661-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="80661-112">Le champ est assigné à l' `Model` attribut de l' `<EditForm>` élément.</span><span class="sxs-lookup"><span data-stu-id="80661-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="80661-113">Le `DataAnnotationsValidator` composant attache la prise en charge de la validation à l’aide d’annotations de données.</span><span class="sxs-lookup"><span data-stu-id="80661-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="80661-114">Le `ValidationSummary` composant résume les messages de validation.</span><span class="sxs-lookup"><span data-stu-id="80661-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="80661-115">`HandleValidSubmit`est déclenché quand le formulaire est envoyé avec succès (réussit la validation).</span><span class="sxs-lookup"><span data-stu-id="80661-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="80661-116">Un ensemble de composants d’entrée intégrés est disponible pour recevoir et valider les entrées utilisateur.</span><span class="sxs-lookup"><span data-stu-id="80661-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="80661-117">Les entrées sont validées lorsqu’elles sont modifiées et lorsqu’un formulaire est envoyé.</span><span class="sxs-lookup"><span data-stu-id="80661-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="80661-118">Les composants d’entrée disponibles sont répertoriés dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="80661-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="80661-119">Composant d’entrée</span><span class="sxs-lookup"><span data-stu-id="80661-119">Input component</span></span> | <span data-ttu-id="80661-120">Rendu comme&hellip;</span><span class="sxs-lookup"><span data-stu-id="80661-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="80661-121">Tous les composants d’entrée, y `EditForm`compris, prennent en charge les attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="80661-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="80661-122">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté à l’élément HTML rendu.</span><span class="sxs-lookup"><span data-stu-id="80661-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="80661-123">Les composants d’entrée fournissent le comportement par défaut pour la validation lors de la modification et la modification de leur classe CSS pour refléter l’état du champ.</span><span class="sxs-lookup"><span data-stu-id="80661-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="80661-124">Certains composants incluent une logique d’analyse utile.</span><span class="sxs-lookup"><span data-stu-id="80661-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="80661-125">Par exemple, `InputDate` et `InputNumber` gèrent correctement les valeurs non analysables en les inscrivant en tant qu’erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="80661-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="80661-126">Les types qui peuvent accepter des valeurs NULL prennent également en charge la possibilité de valeur null du `int?`champ cible (par exemple,).</span><span class="sxs-lookup"><span data-stu-id="80661-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="80661-127">Le type `Starship` suivant définit la logique de validation à l’aide d’un plus grand ensemble de propriétés et `ExampleModel`d’annotations de données que la précédente:</span><span class="sxs-lookup"><span data-stu-id="80661-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="80661-128">Dans l’exemple précédent, `Description` est facultatif, car aucune annotation de données n’est présente.</span><span class="sxs-lookup"><span data-stu-id="80661-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="80661-129">La forme suivante valide l’entrée d’utilisateur à l’aide de la `Starship` validation définie dans le modèle:</span><span class="sxs-lookup"><span data-stu-id="80661-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
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

<span data-ttu-id="80661-130">Crée un `EditContext` comme valeur en [cascade](xref:blazor/components#cascading-values-and-parameters) qui effectue le suivi des métadonnées relatives au processus de modification, notamment les champs qui ont été modifiés et les messages de validation actuels. `EditForm`</span><span class="sxs-lookup"><span data-stu-id="80661-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="80661-131">Le `EditForm` fournit également des événements pratiques pour les envois valides`OnValidSubmit`et `OnInvalidSubmit`non valides (,).</span><span class="sxs-lookup"><span data-stu-id="80661-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="80661-132">Vous pouvez également utiliser `OnSubmit` pour déclencher la validation et vérifier les valeurs de champ avec un code de validation personnalisé.</span><span class="sxs-lookup"><span data-stu-id="80661-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="80661-133">Le `DataAnnotationsValidator` composant attache la prise en charge de la validation à l’aide d' `EditContext`annotations de données à la Cascaded.</span><span class="sxs-lookup"><span data-stu-id="80661-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="80661-134">L’activation de la prise en charge de la validation à l’aide des annotations de données requiert actuellement ce geste explicite, mais nous envisageons d’en faire le comportement par défaut que vous pouvez ensuite remplacer.</span><span class="sxs-lookup"><span data-stu-id="80661-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="80661-135">Pour utiliser un système de validation différent des annotations de données `DataAnnotationsValidator` , remplacez par une implémentation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="80661-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="80661-136">L’implémentation de ASP.NET Core est disponible pour l’inspection dans la source de référence: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="80661-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="80661-137">*L’implémentation de ASP.NET Core est soumise à des mises à jour rapides pendant la période de version préliminaire.*</span><span class="sxs-lookup"><span data-stu-id="80661-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="80661-138">Le `ValidationSummary` composant résume tous les messages de validation, ce qui est similaire au [tag Helper de résumé de validation](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="80661-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="80661-139">Le `ValidationMessage` composant affiche des messages de validation pour un champ spécifique, qui est similaire au [tag Helper de message de validation](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="80661-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="80661-140">Spécifiez le champ pour la validation `For` avec l’attribut et une expression lambda qui nomme la propriété de modèle:</span><span class="sxs-lookup"><span data-stu-id="80661-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="80661-141">Les `ValidationMessage` composants `ValidationSummary` et prennent en charge des attributs arbitraires.</span><span class="sxs-lookup"><span data-stu-id="80661-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="80661-142">Tout attribut qui ne correspond pas à un paramètre de composant est ajouté `<div>` à `<ul>` l’élément ou généré.</span><span class="sxs-lookup"><span data-stu-id="80661-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>