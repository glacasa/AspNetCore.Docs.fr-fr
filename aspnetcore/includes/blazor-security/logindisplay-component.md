<span data-ttu-id="a1f70-101">Le `LoginDisplay` composant (*Shared/LoginDisplay. Razor*) est affiché dans `MainLayout` le composant (*Shared/MainLayout. Razor*) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="a1f70-101">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="a1f70-102">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="a1f70-102">For authenticated users:</span></span>
  * <span data-ttu-id="a1f70-103">Affiche le nom d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="a1f70-103">Displays the current username.</span></span>
  * <span data-ttu-id="a1f70-104">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="a1f70-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="a1f70-105">Pour les utilisateurs anonymes, offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="a1f70-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
