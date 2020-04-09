> [!WARNING]
> <span data-ttu-id="b3adc-101">Pour des raisons de sécurité, vous devez choisir de lier les données de requête `GET` aux propriétés du modèle de page.</span><span class="sxs-lookup"><span data-stu-id="b3adc-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="b3adc-102">Vérifiez l’entrée utilisateur avant de la mapper à des propriétés.</span><span class="sxs-lookup"><span data-stu-id="b3adc-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="b3adc-103">Opter `GET` pour la fixation est utile pour aborder des scénarios qui reposent sur des valeurs de chaîne ou d’itinéraires.</span><span class="sxs-lookup"><span data-stu-id="b3adc-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="b3adc-104">Pour lier `GET` une propriété [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) sur les `SupportsGet` demandes, définissez la propriété de l’attribut à `true`:</span><span class="sxs-lookup"><span data-stu-id="b3adc-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="b3adc-105">Pour plus d’informations, voir [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="b3adc-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
