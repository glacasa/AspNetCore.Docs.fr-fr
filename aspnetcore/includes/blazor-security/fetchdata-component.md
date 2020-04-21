<span data-ttu-id="f68f6-101">Le `FetchData` composant montre comment :</span><span class="sxs-lookup"><span data-stu-id="f68f6-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="f68f6-102">Fournir un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="f68f6-102">Provision an access token.</span></span>
* <span data-ttu-id="f68f6-103">Utilisez le jeton d’accès pour appeler une API de ressource protégée dans l’application *Server.*</span><span class="sxs-lookup"><span data-stu-id="f68f6-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="f68f6-104">La `@attribute [Authorize]` directive indique au système d’autorisation WebAssembly Blazor que l’utilisateur doit être autorisé pour visiter ce composant.</span><span class="sxs-lookup"><span data-stu-id="f68f6-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="f68f6-105">La présence de l’attribut dans l’application *Client* n’empêche pas l’API sur le serveur d’être appelé sans informations d’identification appropriées.</span><span class="sxs-lookup"><span data-stu-id="f68f6-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="f68f6-106">L’application *Server* `[Authorize]` doit également être utilisée sur les paramètres appropriés pour les protéger correctement.</span><span class="sxs-lookup"><span data-stu-id="f68f6-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="f68f6-107">`AuthenticationService.RequestAccessToken();`prend soin de demander un jeton d’accès qui peut être ajouté à la demande d’appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="f68f6-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="f68f6-108">Si le jeton est mis en cache ou si le service est en mesure de fournir un nouveau jeton d’accès sans interaction utilisateur, la demande symbolique réussit.</span><span class="sxs-lookup"><span data-stu-id="f68f6-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="f68f6-109">Dans le cas contraire, la demande symbolique échoue.</span><span class="sxs-lookup"><span data-stu-id="f68f6-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="f68f6-110">Afin d’obtenir le jeton réel à inclure dans la demande, `tokenResult.TryGetToken(out var token)`l’application doit vérifier que la demande a réussi en appelant .</span><span class="sxs-lookup"><span data-stu-id="f68f6-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="f68f6-111">Si la demande a été acceptée, la variable symbolique est peuplée du jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="f68f6-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="f68f6-112">La `Value` propriété du jeton expose la chaîne littérale `Authorization` à inclure dans l’en-tête de demande.</span><span class="sxs-lookup"><span data-stu-id="f68f6-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="f68f6-113">Si la demande a échoué parce que le jeton ne pouvait pas être fourni sans l’interaction de l’utilisateur, le résultat symbolique contient une URL rediriger.</span><span class="sxs-lookup"><span data-stu-id="f68f6-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="f68f6-114">Naviguer vers cette URL amène l’utilisateur à la page de connexion et de retour à la page actuelle après une authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="f68f6-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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

<span data-ttu-id="f68f6-115">Pour plus d’informations, voir [l’état de l’application Enregistrer avant une opération d’authentification](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span><span class="sxs-lookup"><span data-stu-id="f68f6-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
