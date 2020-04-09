> [!WARNING]
> Pour des raisons de sécurité, vous devez choisir de lier les données de requête `GET` aux propriétés du modèle de page. Vérifiez l’entrée utilisateur avant de la mapper à des propriétés. Opter `GET` pour la fixation est utile pour aborder des scénarios qui reposent sur des valeurs de chaîne ou d’itinéraires.
>
> Pour lier `GET` une propriété [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) sur les `SupportsGet` demandes, définissez la propriété de l’attribut à `true`:
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Pour plus d’informations, voir [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
