La page produite par le `Authentication` composant (*pages/Authentication. Razor*) définit les itinéraires requis pour la gestion des différentes étapes d’authentification.

Le `RemoteAuthenticatorView` composant :

* Est fourni par le package [Microsoft. AspNetCore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .
* Gère l’exécution des actions appropriées à chaque étape de l’authentification.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
