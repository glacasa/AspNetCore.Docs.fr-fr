Le `FetchData` composant montre comment :

* Fournir un jeton d’accès.
* Utilisez le jeton d’accès pour appeler une API de ressource protégée dans l’application *Server.*

La `@attribute [Authorize]` directive indique au système d’autorisation WebAssembly Blazor que l’utilisateur doit être autorisé pour visiter ce composant. La présence de l’attribut dans l’application *Client* n’empêche pas l’API sur le serveur d’être appelé sans informations d’identification appropriées. L’application *Server* `[Authorize]` doit également être utilisée sur les paramètres appropriés pour les protéger correctement.

`AuthenticationService.RequestAccessToken();`prend soin de demander un jeton d’accès qui peut être ajouté à la demande d’appel de l’API. Si le jeton est mis en cache ou si le service est en mesure de fournir un nouveau jeton d’accès sans interaction utilisateur, la demande symbolique réussit. Dans le cas contraire, la demande symbolique échoue.

Afin d’obtenir le jeton réel à inclure dans la demande, `tokenResult.TryGetToken(out var token)`l’application doit vérifier que la demande a réussi en appelant . 

Si la demande a été acceptée, la variable symbolique est peuplée du jeton d’accès. La `Value` propriété du jeton expose la chaîne littérale `Authorization` à inclure dans l’en-tête de demande.

Si la demande a échoué parce que le jeton ne pouvait pas être fourni sans l’interaction de l’utilisateur, le résultat symbolique contient une URL rediriger. Naviguer vers cette URL amène l’utilisateur à la page de connexion et de retour à la page actuelle après une authentification réussie.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

Pour plus d’informations, voir [l’état de l’application Enregistrer avant une opération d’authentification](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).
