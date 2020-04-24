<span data-ttu-id="a018b-101">Le `App` composant (*app. Razor*) est semblable au `App` composant des applications serveur éblouissantes :</span><span class="sxs-lookup"><span data-stu-id="a018b-101">The `App` component (*App.razor*) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="a018b-102">Le `CascadingAuthenticationState` composant gère l' `AuthenticationState` exposition de au reste de l’application.</span><span class="sxs-lookup"><span data-stu-id="a018b-102">The `CascadingAuthenticationState` component manages exposing the `AuthenticationState` to the rest of the app.</span></span>
* <span data-ttu-id="a018b-103">Le `AuthorizeRouteView` composant permet de s’assurer que l’utilisateur actuel est autorisé à accéder à une page donnée ou à `RedirectToLogin` effectuer un rendu du composant.</span><span class="sxs-lookup"><span data-stu-id="a018b-103">The `AuthorizeRouteView` component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="a018b-104">Le `RedirectToLogin` composant gère la redirection des utilisateurs non autorisés vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="a018b-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
