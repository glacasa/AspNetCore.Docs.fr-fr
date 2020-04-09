# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly Exemple App

Cet échantillon illustre l’utilisation des scénarios Blazor décrits dans la documentation Blazor.

## <a name="call-web-api-example"></a>Appelez l’exemple de l’API web

L’exemple de l’API Web nécessite une API Web en cours d’exécution basée sur l’application d’échantillon pour la <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">création d’une API web avec ASP.NET</a> sujet de base, qui par défaut utilise le même port HTTPS (5001) que l’application d’échantillon Blazor. Pour utiliser les deux applications sur la même machine en même temps, modifiez le port de l’API web (par exemple, utilisez le port 10000). L’application d’échantillon fait des `https://localhost:10000/api/TodoItems`demandes à l’API web à . Si une adresse API Web différente `ServiceEndpoint` est utilisée, mettez `@code` à jour la valeur constante du bloc du composant Razor.</p>

L’application échantillonne fait une demande de partage `http://localhost:5000` `https://localhost:5001` de <a href="https://docs.microsoft.com/aspnet/core/security/cors">ressources inter-origine (CORS)</a> de ou vers l’API Web. Les informations d’identification (cookies d’autorisation/en-têtes) sont autorisées. Ajoutez la configuration suivante du middleware CORS `Startup.Configure` à la méthode de l’API web :</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Ajustez les domaines `WithOrigins` et les ports de ce que nécessaire pour l’application Blazor.

L’API Web est configurée pour CORS pour permettre aux cookies d’autorisation / en-têtes et les demandes à partir du code client, mais l’API web tel que créé par le tutoriel n’autorise pas réellement les demandes. Consultez les <a href="https://docs.microsoft.com/aspnet/core/security/">articles ASP.NET Core Security and Identity</a> pour les directives de mise en œuvre.
