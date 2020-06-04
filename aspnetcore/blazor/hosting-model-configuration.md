---
<span data-ttu-id="e43fa-101">titre : « ASP.NET Core Blazor Hébergement de la configuration de modèle » auteur : guardrex Description : « en savoir plus sur Blazor l’hébergement de la configuration de modèle, notamment comment intégrer Razor des composants dans Razor des pages et des applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="e43fa-101">title: 'ASP.NET Core Blazor hosting model configuration' author: guardrex description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="e43fa-102">monikerRange : ' >= aspnetcore-3,1 'ms. Author : Riande ms. Custom : MVC ms. Date : 05/28/2020 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="e43fa-102">monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/28/2020 no-loc:</span></span>
- <span data-ttu-id="e43fa-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e43fa-103">'Blazor'</span></span>
- <span data-ttu-id="e43fa-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e43fa-104">'Identity'</span></span>
- <span data-ttu-id="e43fa-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e43fa-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e43fa-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e43fa-106">'Razor'</span></span>
- <span data-ttu-id="e43fa-107">' SignalR 'UID : éblouissant/Hosting-Model-Configuration</span><span class="sxs-lookup"><span data-stu-id="e43fa-107">'SignalR' uid: blazor/hosting-model-configuration</span></span>

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="e43fa-108">BlazorConfiguration du modèle d’hébergement ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e43fa-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="e43fa-109">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e43fa-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e43fa-110">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="e43fa-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="e43fa-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="e43fa-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="e43fa-112">Environnement</span><span class="sxs-lookup"><span data-stu-id="e43fa-112">Environment</span></span>

<span data-ttu-id="e43fa-113">Lors de l’exécution locale d’une application, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="e43fa-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="e43fa-114">Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.</span><span class="sxs-lookup"><span data-stu-id="e43fa-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="e43fa-115">Une Blazor application Webassembly hébergée récupère l’environnement à partir du serveur via un intergiciel qui communique l’environnement au navigateur en ajoutant l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="e43fa-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="e43fa-116">La valeur de l’en-tête est l’environnement.</span><span class="sxs-lookup"><span data-stu-id="e43fa-116">The value of the header is the environment.</span></span> <span data-ttu-id="e43fa-117">L’application hébergée Blazor et l’application serveur partagent le même environnement.</span><span class="sxs-lookup"><span data-stu-id="e43fa-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="e43fa-118">Pour plus d’informations, notamment sur la configuration de l’environnement, consultez <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="e43fa-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="e43fa-119">Pour une application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="e43fa-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="e43fa-120">Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="e43fa-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="e43fa-121">Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *Web. config* publié.</span><span class="sxs-lookup"><span data-stu-id="e43fa-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="e43fa-122">Le fichier *Web. config* se trouve dans le dossier *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="e43fa-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="e43fa-123">Pour utiliser un fichier *Web. config* personnalisé pour IIS qui n’est pas remplacé lorsque l’application est publiée dans le dossier de *publication* , consultez <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="e43fa-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="e43fa-124">Obtenez l’environnement de l’application dans un composant en injectant <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> et en lisant la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriété :</span><span class="sxs-lookup"><span data-stu-id="e43fa-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="e43fa-125">Au démarrage, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expose le <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> via la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :</span><span class="sxs-lookup"><span data-stu-id="e43fa-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="e43fa-126">Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :</span><span class="sxs-lookup"><span data-stu-id="e43fa-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="e43fa-127">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriété peut être utilisée au démarrage lorsque le <xref:Microsoft.AspNetCore.Components.NavigationManager> service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="e43fa-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="e43fa-128">Configuration</span><span class="sxs-lookup"><span data-stu-id="e43fa-128">Configuration</span></span>

Blazor<span data-ttu-id="e43fa-129">Webassembly charge la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="e43fa-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="e43fa-130">Fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="e43fa-130">App settings files by default:</span></span>
  * <span data-ttu-id="e43fa-131">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="e43fa-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="e43fa-132">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="e43fa-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="e43fa-133">Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application.</span><span class="sxs-lookup"><span data-stu-id="e43fa-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="e43fa-134">Tous les fournisseurs ne sont pas appropriés pour les Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="e43fa-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="e43fa-135">La clarification des fournisseurs pris en charge pour Blazor Webassembly est suivie par [clarifier les fournisseurs de configuration pour Blazor WASM (dotnet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="e43fa-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="e43fa-136">La configuration dans une Blazor application Webassembly est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="e43fa-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="e43fa-137">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="e43fa-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="e43fa-138">Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="e43fa-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="e43fa-139">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="e43fa-139">App settings configuration</span></span>

<span data-ttu-id="e43fa-140">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e43fa-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="e43fa-141">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="e43fa-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="e43fa-142">Configuration du fournisseur</span><span class="sxs-lookup"><span data-stu-id="e43fa-142">Provider configuration</span></span>

<span data-ttu-id="e43fa-143">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="e43fa-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="e43fa-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e43fa-144">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="e43fa-145">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="e43fa-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="e43fa-146">Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="e43fa-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="e43fa-147">Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="e43fa-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="e43fa-148">*wwwroot/cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e43fa-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="e43fa-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e43fa-149">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="e43fa-150">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="e43fa-150">Authentication configuration</span></span>

<span data-ttu-id="e43fa-151">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e43fa-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="e43fa-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e43fa-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

#### <a name="logging-configuration"></a><span data-ttu-id="e43fa-153">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="e43fa-153">Logging configuration</span></span>

<span data-ttu-id="e43fa-154">Ajoutez une référence de package pour [Microsoft. extensions. Logging. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span><span class="sxs-lookup"><span data-stu-id="e43fa-154">Add a package reference for [Microsoft.Extensions.Logging.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration/):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="e43fa-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e43fa-155">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="e43fa-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e43fa-156">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="e43fa-157">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="e43fa-157">Host builder configuration</span></span>

<span data-ttu-id="e43fa-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e43fa-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="e43fa-159">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="e43fa-159">Cached configuration</span></span>

<span data-ttu-id="e43fa-160">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="e43fa-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="e43fa-161">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="e43fa-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="e43fa-162">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="e43fa-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="e43fa-163">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="e43fa-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="e43fa-164">Les fichiers *Service-Worker. js* et *Service-Worker-Assets. js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="e43fa-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="e43fa-165">Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="e43fa-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="e43fa-166">Journalisation</span><span class="sxs-lookup"><span data-stu-id="e43fa-166">Logging</span></span>

<span data-ttu-id="e43fa-167">Pour plus d’informations sur la Blazor prise en charge de la journalisation Webassembly, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="e43fa-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="e43fa-168">Serveurs</span><span class="sxs-lookup"><span data-stu-id="e43fa-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="e43fa-169">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="e43fa-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="e43fa-170">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="e43fa-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="e43fa-171">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="e43fa-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="e43fa-172">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la page *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="e43fa-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="e43fa-173">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="e43fa-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="e43fa-174">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="e43fa-174">CSS class</span></span>                       | <span data-ttu-id="e43fa-175">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="e43fa-175">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="e43fa-176">Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="e43fa-176">A lost connection.</span></span> <span data-ttu-id="e43fa-177">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="e43fa-177">The client is attempting to reconnect.</span></span> <span data-ttu-id="e43fa-178">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="e43fa-178">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="e43fa-179">Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="e43fa-179">An active connection is re-established to the server.</span></span> <span data-ttu-id="e43fa-180">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="e43fa-180">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="e43fa-181">Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="e43fa-181">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="e43fa-182">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="e43fa-182">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="e43fa-183">Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="e43fa-183">Reconnection rejected.</span></span> <span data-ttu-id="e43fa-184">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="e43fa-184">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="e43fa-185">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="e43fa-185">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="e43fa-186">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="e43fa-186">This connection state may result when:</span></span><ul><li><span data-ttu-id="e43fa-187">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="e43fa-187">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="e43fa-188">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e43fa-188">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="e43fa-189">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="e43fa-189">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="e43fa-190">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="e43fa-190">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="e43fa-191">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="e43fa-191">Render mode</span></span>

Blazor<span data-ttu-id="e43fa-192">Les applications serveur sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="e43fa-192"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="e43fa-193">Elle est configurée dans la page *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="e43fa-193">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="e43fa-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="e43fa-194"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="e43fa-195">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="e43fa-195">Is prerendered into the page.</span></span>
* <span data-ttu-id="e43fa-196">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e43fa-196">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="e43fa-197">Description</span><span class="sxs-lookup"><span data-stu-id="e43fa-197">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e43fa-198">Génère le rendu du composant en HTML statique et comprend un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="e43fa-198">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e43fa-199">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="e43fa-199">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e43fa-200">Restitue un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="e43fa-200">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e43fa-201">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="e43fa-201">Output from the component isn't included.</span></span> <span data-ttu-id="e43fa-202">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="e43fa-202">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e43fa-203">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="e43fa-203">Renders the component into static HTML.</span></span> |

<span data-ttu-id="e43fa-204">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="e43fa-204">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="e43fa-205">Configurer le SignalR client pour les Blazor applications serveur</span><span class="sxs-lookup"><span data-stu-id="e43fa-205">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="e43fa-206">Parfois, vous devez configurer le SignalR client utilisé par les Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="e43fa-206">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="e43fa-207">Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="e43fa-207">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="e43fa-208">Pour configurer le SignalR client dans le fichier *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="e43fa-208">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="e43fa-209">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="e43fa-209">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="e43fa-210">Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR Générateur.</span><span class="sxs-lookup"><span data-stu-id="e43fa-210">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="e43fa-211">Journalisation</span><span class="sxs-lookup"><span data-stu-id="e43fa-211">Logging</span></span>

<span data-ttu-id="e43fa-212">Pour plus d’informations sur la Blazor prise en charge de la journalisation du serveur, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="e43fa-212">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
