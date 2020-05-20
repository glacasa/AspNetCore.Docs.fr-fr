---
<span data-ttu-id="5a2f8-101">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-103">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-104">'Identity'</span></span>
- <span data-ttu-id="5a2f8-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-106">'Razor'</span></span>
- <span data-ttu-id="5a2f8-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="5a2f8-108">BlazorConfiguration du modèle d’hébergement ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5a2f8-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="5a2f8-109">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5a2f8-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5a2f8-110">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="5a2f8-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="5a2f8-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="5a2f8-112">Environnement</span><span class="sxs-lookup"><span data-stu-id="5a2f8-112">Environment</span></span>

<span data-ttu-id="5a2f8-113">Lors de l’exécution locale d’une application, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="5a2f8-114">Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="5a2f8-115">Une Blazor application Webassembly hébergée récupère l’environnement à partir du serveur via un intergiciel qui communique l’environnement au navigateur en ajoutant l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="5a2f8-116">La valeur de l’en-tête est l’environnement.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-116">The value of the header is the environment.</span></span> <span data-ttu-id="5a2f8-117">L’application hébergée Blazor et l’application serveur partagent le même environnement.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="5a2f8-118">Pour plus d’informations, notamment sur la configuration de l’environnement, consultez <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="5a2f8-119">Pour une application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="5a2f8-120">Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="5a2f8-121">Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *Web. config* publié.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="5a2f8-122">Le fichier *Web. config* se trouve dans le dossier *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="5a2f8-123">Pour utiliser un fichier *Web. config* personnalisé pour IIS qui n’est pas remplacé lorsque l’application est publiée dans le dossier de *publication* , consultez <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="5a2f8-124">Obtenez l’environnement de l’application dans un composant en injectant `IWebAssemblyHostEnvironment` et en lisant la `Environment` propriété :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-124">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="5a2f8-125">Au démarrage, `WebAssemblyHostBuilder` expose le `IWebAssemblyHostEnvironment` via la `HostEnvironment` propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-125">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="5a2f8-126">Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="5a2f8-127">'IsEnvironment ("{nom d’environnement}")</span><span class="sxs-lookup"><span data-stu-id="5a2f8-127">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="5a2f8-128">La `IWebAssemblyHostEnvironment.BaseAddress` propriété peut être utilisée au démarrage lorsque le `NavigationManager` service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-128">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="5a2f8-129">Configuration</span><span class="sxs-lookup"><span data-stu-id="5a2f8-129">Configuration</span></span>

Blazor<span data-ttu-id="5a2f8-130">Webassembly charge la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-130"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="5a2f8-131">Fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-131">App settings files by default:</span></span>
  * <span data-ttu-id="5a2f8-132">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="5a2f8-132">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="5a2f8-133">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="5a2f8-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="5a2f8-134">Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-134">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="5a2f8-135">Tous les fournisseurs ne sont pas appropriés pour les Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-135">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="5a2f8-136">La clarification des fournisseurs pris en charge pour Blazor Webassembly est suivie par [clarifier les fournisseurs de configuration pour Blazor WASM (dotnet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="5a2f8-136">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="5a2f8-137">La configuration dans une Blazor application Webassembly est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-137">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="5a2f8-138">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="5a2f8-138">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="5a2f8-139">Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-139">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="5a2f8-140">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="5a2f8-140">App settings configuration</span></span>

<span data-ttu-id="5a2f8-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="5a2f8-142">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-142">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="5a2f8-143">Configuration du fournisseur</span><span class="sxs-lookup"><span data-stu-id="5a2f8-143">Provider configuration</span></span>

<span data-ttu-id="5a2f8-144">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-144">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="5a2f8-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-145">`Program.Main`:</span></span>

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

<span data-ttu-id="5a2f8-146">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-146">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="5a2f8-147">Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, utilisez un `HttpClient` pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-147">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="5a2f8-148">Lors de l’utilisation de cette approche, l' `HttpClient` inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-148">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="5a2f8-149">*wwwroot/cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-149">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="5a2f8-150">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-150">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="5a2f8-151">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="5a2f8-151">Authentication configuration</span></span>

<span data-ttu-id="5a2f8-152">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-152">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="5a2f8-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-153">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="5a2f8-154">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="5a2f8-154">Logging configuration</span></span>

<span data-ttu-id="5a2f8-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-155">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="5a2f8-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-156">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="5a2f8-157">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="5a2f8-157">Host builder configuration</span></span>

<span data-ttu-id="5a2f8-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5a2f8-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="5a2f8-159">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="5a2f8-159">Cached configuration</span></span>

<span data-ttu-id="5a2f8-160">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="5a2f8-161">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="5a2f8-162">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="5a2f8-163">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="5a2f8-164">Les fichiers *Service-Worker. js* et *Service-Worker-Assets. js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="5a2f8-165">Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="5a2f8-166">Journalisation</span><span class="sxs-lookup"><span data-stu-id="5a2f8-166">Logging</span></span>

<span data-ttu-id="5a2f8-167">Pour plus d’informations sur la Blazor prise en charge de la journalisation Webassembly, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="5a2f8-168">Serveurs</span><span class="sxs-lookup"><span data-stu-id="5a2f8-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="5a2f8-169">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="5a2f8-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="5a2f8-170">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="5a2f8-171">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="5a2f8-172">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la page *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="5a2f8-173">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="5a2f8-174">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="5a2f8-174">CSS class</span></span>                       | <span data-ttu-id="5a2f8-175">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="5a2f8-175">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="5a2f8-176">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-176">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-177">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-178">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-179">'Identity'</span></span>
- <span data-ttu-id="5a2f8-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-181">'Razor'</span></span>
- <span data-ttu-id="5a2f8-182">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-183">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-183">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-184">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-185">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-186">'Identity'</span></span>
- <span data-ttu-id="5a2f8-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-188">'Razor'</span></span>
- <span data-ttu-id="5a2f8-189">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-190">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-190">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-191">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-192">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-193">'Identity'</span></span>
- <span data-ttu-id="5a2f8-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-195">'Razor'</span></span>
- <span data-ttu-id="5a2f8-196">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-197">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-197">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-198">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-199">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-200">'Identity'</span></span>
- <span data-ttu-id="5a2f8-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-202">'Razor'</span></span>
- <span data-ttu-id="5a2f8-203">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-204">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-204">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-205">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-206">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-207">'Identity'</span></span>
- <span data-ttu-id="5a2f8-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-209">'Razor'</span></span>
- <span data-ttu-id="5a2f8-210">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-211">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-211">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-212">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-213">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-214">'Identity'</span></span>
- <span data-ttu-id="5a2f8-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-216">'Razor'</span></span>
- <span data-ttu-id="5a2f8-217">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-218">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-218">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-219">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-220">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-221">'Identity'</span></span>
- <span data-ttu-id="5a2f8-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-223">'Razor'</span></span>
- <span data-ttu-id="5a2f8-224">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-225">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-225">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-226">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-227">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-228">'Identity'</span></span>
- <span data-ttu-id="5a2f8-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-230">'Razor'</span></span>
- <span data-ttu-id="5a2f8-231">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-232">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-232">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-233">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-234">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-235">'Identity'</span></span>
- <span data-ttu-id="5a2f8-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-237">'Razor'</span></span>
- <span data-ttu-id="5a2f8-238">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-239">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-239">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-240">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-241">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-242">'Identity'</span></span>
- <span data-ttu-id="5a2f8-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-244">'Razor'</span></span>
- <span data-ttu-id="5a2f8-245">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-246">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-246">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-247">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-248">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-249">'Identity'</span></span>
- <span data-ttu-id="5a2f8-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-251">'Razor'</span></span>
- <span data-ttu-id="5a2f8-252">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-253">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-253">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-254">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-255">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-256">'Identity'</span></span>
- <span data-ttu-id="5a2f8-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-258">'Razor'</span></span>
- <span data-ttu-id="5a2f8-259">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-260">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-260">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-261">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-262">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-263">'Identity'</span></span>
- <span data-ttu-id="5a2f8-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-265">'Razor'</span></span>
- <span data-ttu-id="5a2f8-266">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-266">'SignalR' uid:</span></span> 

<span data-ttu-id="5a2f8-267">---------------- | titre de la--- : « ASP.NET Core Blazor de l’hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-267">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-268">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-269">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-270">'Identity'</span></span>
- <span data-ttu-id="5a2f8-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-272">'Razor'</span></span>
- <span data-ttu-id="5a2f8-273">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-274">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-274">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-275">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-276">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-277">'Identity'</span></span>
- <span data-ttu-id="5a2f8-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-279">'Razor'</span></span>
- <span data-ttu-id="5a2f8-280">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-281">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-281">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-282">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-283">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-284">'Identity'</span></span>
- <span data-ttu-id="5a2f8-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-286">'Razor'</span></span>
- <span data-ttu-id="5a2f8-287">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-288">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-288">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-289">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-290">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-291">'Identity'</span></span>
- <span data-ttu-id="5a2f8-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-293">'Razor'</span></span>
- <span data-ttu-id="5a2f8-294">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-295">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-295">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-296">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-297">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-298">'Identity'</span></span>
- <span data-ttu-id="5a2f8-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-300">'Razor'</span></span>
- <span data-ttu-id="5a2f8-301">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-302">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-302">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-303">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-304">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-305">'Identity'</span></span>
- <span data-ttu-id="5a2f8-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-307">'Razor'</span></span>
- <span data-ttu-id="5a2f8-308">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-308">'SignalR' uid:</span></span> 

<span data-ttu-id="5a2f8-309">--------- | | `components-reconnect-show`     | Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-309">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="5a2f8-310">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-310">The client is attempting to reconnect.</span></span> <span data-ttu-id="5a2f8-311">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-311">Show the modal.</span></span> <span data-ttu-id="5a2f8-312">| | `components-reconnect-hide`     | Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-312">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="5a2f8-313">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-313">Hide the modal.</span></span> <span data-ttu-id="5a2f8-314">| | `components-reconnect-failed`   | Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-314">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="5a2f8-315">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-315">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="5a2f8-316">| | `components-reconnect-rejected` | Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-316">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="5a2f8-317">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-317">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="5a2f8-318">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-318">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="5a2f8-319">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-319">This connection state may result when:</span></span><ul><li><span data-ttu-id="5a2f8-320">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-320">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="5a2f8-321">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-321">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="5a2f8-322">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-322">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="5a2f8-323">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-323">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="5a2f8-324">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="5a2f8-324">Render mode</span></span>

Blazor<span data-ttu-id="5a2f8-325">Les applications serveur sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-325"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="5a2f8-326">Elle est configurée dans la page *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-326">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="5a2f8-327">`RenderMode`Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-327">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="5a2f8-328">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-328">Is prerendered into the page.</span></span>
* <span data-ttu-id="5a2f8-329">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-329">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="5a2f8-330">Description</span><span class="sxs-lookup"><span data-stu-id="5a2f8-330">Description</span></span> |
| ---
<span data-ttu-id="5a2f8-331">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-331">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-332">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-333">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-334">'Identity'</span></span>
- <span data-ttu-id="5a2f8-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-336">'Razor'</span></span>
- <span data-ttu-id="5a2f8-337">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-338">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-338">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-339">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-340">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-341">'Identity'</span></span>
- <span data-ttu-id="5a2f8-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-343">'Razor'</span></span>
- <span data-ttu-id="5a2f8-344">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-345">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-345">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-346">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-347">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-348">'Identity'</span></span>
- <span data-ttu-id="5a2f8-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-350">'Razor'</span></span>
- <span data-ttu-id="5a2f8-351">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-352">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-352">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-353">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-354">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-355">'Identity'</span></span>
- <span data-ttu-id="5a2f8-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-357">'Razor'</span></span>
- <span data-ttu-id="5a2f8-358">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-359">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-359">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-360">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-361">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-362">'Identity'</span></span>
- <span data-ttu-id="5a2f8-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-364">'Razor'</span></span>
- <span data-ttu-id="5a2f8-365">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-366">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-366">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-367">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-368">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-369">'Identity'</span></span>
- <span data-ttu-id="5a2f8-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-371">'Razor'</span></span>
- <span data-ttu-id="5a2f8-372">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-372">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-373">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-373">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-374">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-375">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-376">'Identity'</span></span>
- <span data-ttu-id="5a2f8-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-378">'Razor'</span></span>
- <span data-ttu-id="5a2f8-379">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-379">'SignalR' uid:</span></span> 

<span data-ttu-id="5a2f8-380">---------- | titre de la--- : « ASP.NET Core Blazor de l’hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-380">---------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-381">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-382">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-383">'Identity'</span></span>
- <span data-ttu-id="5a2f8-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-385">'Razor'</span></span>
- <span data-ttu-id="5a2f8-386">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-387">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-387">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-388">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-389">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-390">'Identity'</span></span>
- <span data-ttu-id="5a2f8-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-392">'Razor'</span></span>
- <span data-ttu-id="5a2f8-393">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a2f8-394">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="5a2f8-394">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="5a2f8-395">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a2f8-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-396">'Blazor'</span></span>
- <span data-ttu-id="5a2f8-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-397">'Identity'</span></span>
- <span data-ttu-id="5a2f8-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a2f8-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a2f8-399">'Razor'</span></span>
- <span data-ttu-id="5a2f8-400">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-400">'SignalR' uid:</span></span> 

<span data-ttu-id="5a2f8-401">------ | | `ServerPrerendered` | Génère le rendu du composant en HTML statique et comprend un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-401">------ | | `ServerPrerendered` | Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5a2f8-402">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-402">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="5a2f8-403">| | `Server`            | Restitue un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-403">| | `Server`            | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5a2f8-404">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-404">Output from the component isn't included.</span></span> <span data-ttu-id="5a2f8-405">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-405">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="5a2f8-406">| | `Static`            | Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-406">| | `Static`            | Renders the component into static HTML.</span></span> |

<span data-ttu-id="5a2f8-407">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-407">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="5a2f8-408">Configurer le SignalR client pour les Blazor applications serveur</span><span class="sxs-lookup"><span data-stu-id="5a2f8-408">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="5a2f8-409">Parfois, vous devez configurer le SignalR client utilisé par les Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-409">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="5a2f8-410">Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-410">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="5a2f8-411">Pour configurer le SignalR client dans le fichier *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5a2f8-411">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="5a2f8-412">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-412">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="5a2f8-413">Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR Générateur.</span><span class="sxs-lookup"><span data-stu-id="5a2f8-413">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="5a2f8-414">Journalisation</span><span class="sxs-lookup"><span data-stu-id="5a2f8-414">Logging</span></span>

<span data-ttu-id="5a2f8-415">Pour plus d’informations sur la Blazor prise en charge de la journalisation du serveur, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="5a2f8-415">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
