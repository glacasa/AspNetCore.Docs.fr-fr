Le `App` composant (*app. Razor*) est semblable au `App` composant des applications serveur éblouissantes :

* Le `CascadingAuthenticationState` composant gère l' `AuthenticationState` exposition de au reste de l’application.
* Le `AuthorizeRouteView` composant permet de s’assurer que l’utilisateur actuel est autorisé à accéder à une page donnée ou à `RedirectToLogin` effectuer un rendu du composant.
* Le `RedirectToLogin` composant gère la redirection des utilisateurs non autorisés vers la page de connexion.

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
