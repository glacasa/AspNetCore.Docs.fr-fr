---
<span data-ttu-id="5facc-101">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-103">'Blazor'</span></span>
- <span data-ttu-id="5facc-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-104">'Identity'</span></span>
- <span data-ttu-id="5facc-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-106">'Razor'</span></span>
- <span data-ttu-id="5facc-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="5facc-108">BlazorGestion des événements ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5facc-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="5facc-109">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5facc-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="5facc-110">les composants fournissent des fonctionnalités de gestion des événements.</span><span class="sxs-lookup"><span data-stu-id="5facc-110"> components provide event handling features.</span></span> <span data-ttu-id="5facc-111">Pour un attribut d’élément HTML nommé [`@on{EVENT}`](xref:mvc/views/razor#onevent) (par exemple, `@onclick` ) avec une valeur de type délégué, un Razor composant traite la valeur de l’attribut en tant que gestionnaire d’événements.</span><span class="sxs-lookup"><span data-stu-id="5facc-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="5facc-112">Le code suivant appelle la `UpdateHeading` méthode lorsque le bouton est sélectionné dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="5facc-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="5facc-113">Le code suivant appelle la `CheckChanged` méthode lorsque la case à cocher est modifiée dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="5facc-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="5facc-114">Les gestionnaires d’événements peuvent également être asynchrones et retourner un <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="5facc-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="5facc-115">Il n’est pas nécessaire d’appeler manuellement [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="5facc-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="5facc-116">Les exceptions sont journalisées lorsqu’elles se produisent.</span><span class="sxs-lookup"><span data-stu-id="5facc-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="5facc-117">Dans l’exemple suivant, `UpdateHeading` est appelé de façon asynchrone quand le bouton est sélectionné :</span><span class="sxs-lookup"><span data-stu-id="5facc-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="5facc-118">Types d’arguments d’événement</span><span class="sxs-lookup"><span data-stu-id="5facc-118">Event argument types</span></span>

<span data-ttu-id="5facc-119">Pour certains événements, les types d’arguments d’événement sont autorisés.</span><span class="sxs-lookup"><span data-stu-id="5facc-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="5facc-120">La spécification d’un type d’événement dans l’appel de méthode n’est nécessaire que si le type d’événement est utilisé dans la méthode.</span><span class="sxs-lookup"><span data-stu-id="5facc-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="5facc-121">Les informations prises en charge <xref:System.EventArgs> sont indiquées dans le tableau suivant.</span><span class="sxs-lookup"><span data-stu-id="5facc-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="5facc-122">Événement</span><span class="sxs-lookup"><span data-stu-id="5facc-122">Event</span></span>            | <span data-ttu-id="5facc-123">Classe</span><span class="sxs-lookup"><span data-stu-id="5facc-123">Class</span></span>                | <span data-ttu-id="5facc-124">Remarques et événements DOM</span><span class="sxs-lookup"><span data-stu-id="5facc-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="5facc-125">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-126">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-127">'Blazor'</span></span>
- <span data-ttu-id="5facc-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-128">'Identity'</span></span>
- <span data-ttu-id="5facc-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-130">'Razor'</span></span>
- <span data-ttu-id="5facc-131">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-132">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-133">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-134">'Blazor'</span></span>
- <span data-ttu-id="5facc-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-135">'Identity'</span></span>
- <span data-ttu-id="5facc-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-137">'Razor'</span></span>
- <span data-ttu-id="5facc-138">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-139">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-140">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-141">'Blazor'</span></span>
- <span data-ttu-id="5facc-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-142">'Identity'</span></span>
- <span data-ttu-id="5facc-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-144">'Razor'</span></span>
- <span data-ttu-id="5facc-145">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-146">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-147">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-148">'Blazor'</span></span>
- <span data-ttu-id="5facc-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-149">'Identity'</span></span>
- <span data-ttu-id="5facc-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-151">'Razor'</span></span>
- <span data-ttu-id="5facc-152">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-153">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-154">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-155">'Blazor'</span></span>
- <span data-ttu-id="5facc-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-156">'Identity'</span></span>
- <span data-ttu-id="5facc-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-158">'Razor'</span></span>
- <span data-ttu-id="5facc-159">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-160">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-161">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-162">'Blazor'</span></span>
- <span data-ttu-id="5facc-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-163">'Identity'</span></span>
- <span data-ttu-id="5facc-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-165">'Razor'</span></span>
- <span data-ttu-id="5facc-166">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-166">'SignalR' uid:</span></span> 

<span data-ttu-id="5facc-167">-------- | titre de la--- : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-168">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-169">'Blazor'</span></span>
- <span data-ttu-id="5facc-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-170">'Identity'</span></span>
- <span data-ttu-id="5facc-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-172">'Razor'</span></span>
- <span data-ttu-id="5facc-173">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-174">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-175">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-176">'Blazor'</span></span>
- <span data-ttu-id="5facc-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-177">'Identity'</span></span>
- <span data-ttu-id="5facc-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-179">'Razor'</span></span>
- <span data-ttu-id="5facc-180">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-181">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-182">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-183">'Blazor'</span></span>
- <span data-ttu-id="5facc-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-184">'Identity'</span></span>
- <span data-ttu-id="5facc-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-186">'Razor'</span></span>
- <span data-ttu-id="5facc-187">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-188">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-189">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-190">'Blazor'</span></span>
- <span data-ttu-id="5facc-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-191">'Identity'</span></span>
- <span data-ttu-id="5facc-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-193">'Razor'</span></span>
- <span data-ttu-id="5facc-194">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-195">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-196">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-197">'Blazor'</span></span>
- <span data-ttu-id="5facc-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-198">'Identity'</span></span>
- <span data-ttu-id="5facc-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-200">'Razor'</span></span>
- <span data-ttu-id="5facc-201">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-202">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-203">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-204">'Blazor'</span></span>
- <span data-ttu-id="5facc-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-205">'Identity'</span></span>
- <span data-ttu-id="5facc-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-207">'Razor'</span></span>
- <span data-ttu-id="5facc-208">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-209">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-210">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-211">'Blazor'</span></span>
- <span data-ttu-id="5facc-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-212">'Identity'</span></span>
- <span data-ttu-id="5facc-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-214">'Razor'</span></span>
- <span data-ttu-id="5facc-215">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-216">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-217">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-218">'Blazor'</span></span>
- <span data-ttu-id="5facc-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-219">'Identity'</span></span>
- <span data-ttu-id="5facc-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-221">'Razor'</span></span>
- <span data-ttu-id="5facc-222">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-222">'SignalR' uid:</span></span> 

<span data-ttu-id="5facc-223">---------- | titre de la--- : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-224">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-225">'Blazor'</span></span>
- <span data-ttu-id="5facc-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-226">'Identity'</span></span>
- <span data-ttu-id="5facc-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-228">'Razor'</span></span>
- <span data-ttu-id="5facc-229">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-230">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-231">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-232">'Blazor'</span></span>
- <span data-ttu-id="5facc-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-233">'Identity'</span></span>
- <span data-ttu-id="5facc-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-235">'Razor'</span></span>
- <span data-ttu-id="5facc-236">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-237">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-238">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-239">'Blazor'</span></span>
- <span data-ttu-id="5facc-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-240">'Identity'</span></span>
- <span data-ttu-id="5facc-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-242">'Razor'</span></span>
- <span data-ttu-id="5facc-243">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-244">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-245">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-246">'Blazor'</span></span>
- <span data-ttu-id="5facc-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-247">'Identity'</span></span>
- <span data-ttu-id="5facc-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-249">'Razor'</span></span>
- <span data-ttu-id="5facc-250">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-251">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-252">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-253">'Blazor'</span></span>
- <span data-ttu-id="5facc-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-254">'Identity'</span></span>
- <span data-ttu-id="5facc-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-256">'Razor'</span></span>
- <span data-ttu-id="5facc-257">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-258">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-259">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-260">'Blazor'</span></span>
- <span data-ttu-id="5facc-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-261">'Identity'</span></span>
- <span data-ttu-id="5facc-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-263">'Razor'</span></span>
- <span data-ttu-id="5facc-264">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-265">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-266">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-267">'Blazor'</span></span>
- <span data-ttu-id="5facc-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-268">'Identity'</span></span>
- <span data-ttu-id="5facc-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-270">'Razor'</span></span>
- <span data-ttu-id="5facc-271">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5facc-272">title : 'ASP.NET Core Blazor gestion des événements’auteur : Description : 'en savoir plus sur Blazor les fonctionnalités de gestion des événements de, y compris les types d’arguments d’événement, les rappels d’événements et la gestion des événements de navigateur par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="5facc-273">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5facc-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5facc-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5facc-274">'Blazor'</span></span>
- <span data-ttu-id="5facc-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5facc-275">'Identity'</span></span>
- <span data-ttu-id="5facc-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5facc-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="5facc-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5facc-277">'Razor'</span></span>
- <span data-ttu-id="5facc-278">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5facc-278">'SignalR' uid:</span></span> 

<span data-ttu-id="5facc-279">---------- | | Presse-papiers | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Faire glisser | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`</span><span class="sxs-lookup"><span data-stu-id="5facc-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="5facc-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>et <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> contiennent des données d’élément glissées.</span><span class="sxs-lookup"><span data-stu-id="5facc-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="5facc-281">| | Erreur | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Événement | <xref:System.EventArgs>  |  *Général*</span><span class="sxs-lookup"><span data-stu-id="5facc-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="5facc-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="5facc-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="5facc-283">*Presse-papiers*</span><span class="sxs-lookup"><span data-stu-id="5facc-283">*Clipboard*</span></span><br><span data-ttu-id="5facc-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="5facc-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="5facc-285">*Input*</span><span class="sxs-lookup"><span data-stu-id="5facc-285">*Input*</span></span><br><span data-ttu-id="5facc-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="5facc-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="5facc-287">*Média*</span><span class="sxs-lookup"><span data-stu-id="5facc-287">*Media*</span></span><br><span data-ttu-id="5facc-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="5facc-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="5facc-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>contient des attributs permettant de configurer les mappages entre les noms d’événements et les types d’arguments d’événement.</span><span class="sxs-lookup"><span data-stu-id="5facc-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="5facc-290">| | Focus | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`</span><span class="sxs-lookup"><span data-stu-id="5facc-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="5facc-291">N’inclut pas la prise en charge de `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="5facc-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="5facc-292">| | Entrée | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Clavier | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Souris | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Pointeur de la souris | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Roulette de la souris | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progression | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Toucher | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="5facc-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="5facc-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>représente un point de contact unique sur un appareil tactile.</span><span class="sxs-lookup"><span data-stu-id="5facc-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="5facc-294">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="5facc-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="5facc-295">[Classes EventArgs dans la source de référence ASP.net Core (branche dotnet/aspnetcore ou version 3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="5facc-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="5facc-296">[MDN Web docs : GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): contient des informations sur les éléments HTML qui prennent en charge chaque événement DOM.</span><span class="sxs-lookup"><span data-stu-id="5facc-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="5facc-297">Expressions lambda</span><span class="sxs-lookup"><span data-stu-id="5facc-297">Lambda expressions</span></span>

<span data-ttu-id="5facc-298">Les [expressions lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) peuvent également être utilisées :</span><span class="sxs-lookup"><span data-stu-id="5facc-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="5facc-299">Il est souvent pratique de se rapprocher de valeurs supplémentaires, par exemple lors de l’itération sur un ensemble d’éléments.</span><span class="sxs-lookup"><span data-stu-id="5facc-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="5facc-300">L’exemple suivant crée trois boutons, chacun d’entre eux qui `UpdateHeading` passe un argument d’événement ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) et son numéro de bouton ( `buttonNumber` ) lorsqu’ils sont sélectionnés dans l’interface utilisateur :</span><span class="sxs-lookup"><span data-stu-id="5facc-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="5facc-301">N’utilisez **pas** la variable de boucle ( `i` ) dans une `for` boucle directement dans une expression lambda.</span><span class="sxs-lookup"><span data-stu-id="5facc-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="5facc-302">Dans le cas contraire, la même variable est utilisée par toutes les expressions lambda, ce qui signifie que la valeur de est `i` identique dans toutes les expressions lambda.</span><span class="sxs-lookup"><span data-stu-id="5facc-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="5facc-303">Capturez toujours sa valeur dans une variable locale ( `buttonNumber` dans l’exemple précédent), puis utilisez-la.</span><span class="sxs-lookup"><span data-stu-id="5facc-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="5facc-304">EventCallback suivante</span><span class="sxs-lookup"><span data-stu-id="5facc-304">EventCallback</span></span>

<span data-ttu-id="5facc-305">Un scénario courant avec des composants imbriqués est le désir d’exécuter la méthode d’un composant parent lorsqu’un événement de composant enfant se produit.</span><span class="sxs-lookup"><span data-stu-id="5facc-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="5facc-306">Un `onclick` événement qui se produit dans le composant enfant est un cas d’usage courant.</span><span class="sxs-lookup"><span data-stu-id="5facc-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="5facc-307">Pour exposer des événements entre les composants, utilisez un <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="5facc-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="5facc-308">Un composant parent peut affecter une méthode de rappel à un composant enfant <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="5facc-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="5facc-309">`ChildComponent`Dans l’exemple d’application (*Components/ChildComponent. Razor*), vous montre comment un gestionnaire de bouton `onclick` est configuré pour recevoir un <xref:Microsoft.AspNetCore.Components.EventCallback> délégué de l’exemple de `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="5facc-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="5facc-310">Le <xref:Microsoft.AspNetCore.Components.EventCallback> est typé avec `MouseEventArgs` , ce qui est approprié pour un `onclick` événement à partir d’un périphérique :</span><span class="sxs-lookup"><span data-stu-id="5facc-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="5facc-311">`ParentComponent`Définit le () de l’enfant <xref:Microsoft.AspNetCore.Components.EventCallback%601> `OnClickCallback` sur sa `ShowMessage` méthode.</span><span class="sxs-lookup"><span data-stu-id="5facc-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="5facc-312">*Pages/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="5facc-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="5facc-313">Lorsque le bouton est sélectionné dans le `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="5facc-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="5facc-314">La `ParentComponent` `ShowMessage` méthode de est appelée.</span><span class="sxs-lookup"><span data-stu-id="5facc-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="5facc-315">`messageText`est mis à jour et affiché dans le `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="5facc-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="5facc-316">Un appel à [StateHasChanged](xref:blazor/lifecycle#state-changes) n’est pas requis dans la méthode du rappel ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="5facc-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="5facc-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>est appelé automatiquement pour rerestituer le `ParentComponent` , tout comme les événements enfants déclenchent le rerendu des composants dans les gestionnaires d’événements qui s’exécutent dans l’enfant.</span><span class="sxs-lookup"><span data-stu-id="5facc-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="5facc-318"><xref:Microsoft.AspNetCore.Components.EventCallback>et <xref:Microsoft.AspNetCore.Components.EventCallback%601> autorisent les délégués asynchrones.</span><span class="sxs-lookup"><span data-stu-id="5facc-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="5facc-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601>est fortement typé et requiert un type d’argument spécifique.</span><span class="sxs-lookup"><span data-stu-id="5facc-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="5facc-320"><xref:Microsoft.AspNetCore.Components.EventCallback>est faiblement typé et autorise tout type d’argument.</span><span class="sxs-lookup"><span data-stu-id="5facc-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="5facc-321">Appelez <xref:Microsoft.AspNetCore.Components.EventCallback> ou <xref:Microsoft.AspNetCore.Components.EventCallback%601> avec <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> et en attente de <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="5facc-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="5facc-322">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> et <xref:Microsoft.AspNetCore.Components.EventCallback%601> pour la gestion des événements et les paramètres de composant de liaison.</span><span class="sxs-lookup"><span data-stu-id="5facc-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="5facc-323">Préférez le fortement typé <xref:Microsoft.AspNetCore.Components.EventCallback%601> <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="5facc-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="5facc-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601>fournit un meilleur retour d’erreur aux utilisateurs du composant.</span><span class="sxs-lookup"><span data-stu-id="5facc-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="5facc-325">Comme pour d’autres gestionnaires d’événements d’interface utilisateur, la spécification du paramètre d’événement est facultative.</span><span class="sxs-lookup"><span data-stu-id="5facc-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="5facc-326">Utilisez <xref:Microsoft.AspNetCore.Components.EventCallback> quand aucune valeur n’est passée au rappel.</span><span class="sxs-lookup"><span data-stu-id="5facc-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="5facc-327">Empêcher les actions par défaut</span><span class="sxs-lookup"><span data-stu-id="5facc-327">Prevent default actions</span></span>

<span data-ttu-id="5facc-328">Utilisez l' [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) attribut directive pour empêcher l’action par défaut d’un événement.</span><span class="sxs-lookup"><span data-stu-id="5facc-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="5facc-329">Quand une clé est sélectionnée sur un appareil d’entrée et que le focus de l’élément se trouve sur une zone de texte, un navigateur affiche normalement le caractère de la clé dans la zone de texte.</span><span class="sxs-lookup"><span data-stu-id="5facc-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="5facc-330">Dans l’exemple suivant, le comportement par défaut est évité en spécifiant l' `@onkeypress:preventDefault` attribut directive.</span><span class="sxs-lookup"><span data-stu-id="5facc-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="5facc-331">Le compteur est incrémenté et la **+** clé n’est pas capturée dans la `<input>` valeur de l’élément :</span><span class="sxs-lookup"><span data-stu-id="5facc-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="5facc-332">La spécification de l' `@on{EVENT}:preventDefault` attribut sans valeur équivaut à `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="5facc-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="5facc-333">La valeur de l’attribut peut également être une expression.</span><span class="sxs-lookup"><span data-stu-id="5facc-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="5facc-334">Dans l’exemple suivant, `shouldPreventDefault` est un `bool` champ défini sur `true` ou `false` :</span><span class="sxs-lookup"><span data-stu-id="5facc-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="5facc-335">Un gestionnaire d’événements n’est pas nécessaire pour empêcher l’action par défaut.</span><span class="sxs-lookup"><span data-stu-id="5facc-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="5facc-336">Le gestionnaire d’événements et les scénarios d’action par défaut peuvent être utilisés indépendamment.</span><span class="sxs-lookup"><span data-stu-id="5facc-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="5facc-337">Arrêter la propagation des événements</span><span class="sxs-lookup"><span data-stu-id="5facc-337">Stop event propagation</span></span>

<span data-ttu-id="5facc-338">Utilisez l' [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) attribut directive pour arrêter la propagation des événements.</span><span class="sxs-lookup"><span data-stu-id="5facc-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="5facc-339">Dans l’exemple suivant, la sélection de la case à cocher empêche les événements Click du deuxième enfant `<div>` de se propager vers le parent `<div>` :</span><span class="sxs-lookup"><span data-stu-id="5facc-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
