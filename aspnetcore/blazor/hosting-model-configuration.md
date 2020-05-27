---
<span data-ttu-id="3de7b-101">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-103">'Blazor'</span></span>
- <span data-ttu-id="3de7b-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-104">'Identity'</span></span>
- <span data-ttu-id="3de7b-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-106">'Razor'</span></span>
- <span data-ttu-id="3de7b-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="3de7b-108">BlazorConfiguration du modèle d’hébergement ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="3de7b-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="3de7b-109">Par [Daniel Roth](https://github.com/danroth27) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3de7b-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3de7b-110">Cet article traite de l’hébergement de la configuration du modèle.</span><span class="sxs-lookup"><span data-stu-id="3de7b-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="3de7b-111">Webassembly</span><span class="sxs-lookup"><span data-stu-id="3de7b-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="3de7b-112">Environnement</span><span class="sxs-lookup"><span data-stu-id="3de7b-112">Environment</span></span>

<span data-ttu-id="3de7b-113">Lors de l’exécution locale d’une application, l’environnement est par défaut développement.</span><span class="sxs-lookup"><span data-stu-id="3de7b-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="3de7b-114">Lorsque l’application est publiée, l’environnement prend par défaut la valeur production.</span><span class="sxs-lookup"><span data-stu-id="3de7b-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="3de7b-115">Une Blazor application Webassembly hébergée récupère l’environnement à partir du serveur via un intergiciel qui communique l’environnement au navigateur en ajoutant l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="3de7b-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="3de7b-116">La valeur de l’en-tête est l’environnement.</span><span class="sxs-lookup"><span data-stu-id="3de7b-116">The value of the header is the environment.</span></span> <span data-ttu-id="3de7b-117">L’application hébergée Blazor et l’application serveur partagent le même environnement.</span><span class="sxs-lookup"><span data-stu-id="3de7b-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="3de7b-118">Pour plus d’informations, notamment sur la configuration de l’environnement, consultez <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="3de7b-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3de7b-119">Pour une application autonome exécutée localement, le serveur de développement ajoute l' `blazor-environment` en-tête pour spécifier l’environnement de développement.</span><span class="sxs-lookup"><span data-stu-id="3de7b-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="3de7b-120">Pour spécifier l’environnement pour d’autres environnements d’hébergement, ajoutez l' `blazor-environment` en-tête.</span><span class="sxs-lookup"><span data-stu-id="3de7b-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="3de7b-121">Dans l’exemple suivant pour IIS, ajoutez l’en-tête personnalisé au fichier *Web. config* publié.</span><span class="sxs-lookup"><span data-stu-id="3de7b-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="3de7b-122">Le fichier *Web. config* se trouve dans le dossier *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="3de7b-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="3de7b-123">Pour utiliser un fichier *Web. config* personnalisé pour IIS qui n’est pas remplacé lorsque l’application est publiée dans le dossier de *publication* , consultez <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="3de7b-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="3de7b-124">Obtenez l’environnement de l’application dans un composant en injectant <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> et en lisant la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> propriété :</span><span class="sxs-lookup"><span data-stu-id="3de7b-124">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="3de7b-125">Au démarrage, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> expose le <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> via la <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> propriété, qui permet aux développeurs d’avoir une logique spécifique à l’environnement dans leur code :</span><span class="sxs-lookup"><span data-stu-id="3de7b-125">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="3de7b-126">Les méthodes d’extension suivantes permettent de vérifier l’environnement actuel pour les noms d’environnement de développement, de production, intermédiaires et personnalisés :</span><span class="sxs-lookup"><span data-stu-id="3de7b-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

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

<span data-ttu-id="3de7b-127">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> propriété peut être utilisée au démarrage lorsque le <xref:Microsoft.AspNetCore.Components.NavigationManager> service n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="3de7b-127">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="3de7b-128">Configuration</span><span class="sxs-lookup"><span data-stu-id="3de7b-128">Configuration</span></span>

Blazor<span data-ttu-id="3de7b-129">Webassembly charge la configuration à partir de :</span><span class="sxs-lookup"><span data-stu-id="3de7b-129"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="3de7b-130">Fichiers de paramètres d’application par défaut :</span><span class="sxs-lookup"><span data-stu-id="3de7b-130">App settings files by default:</span></span>
  * <span data-ttu-id="3de7b-131">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="3de7b-131">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="3de7b-132">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="3de7b-132">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="3de7b-133">Autres [fournisseurs de configuration](xref:fundamentals/configuration/index) inscrits par l’application.</span><span class="sxs-lookup"><span data-stu-id="3de7b-133">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="3de7b-134">Tous les fournisseurs ne sont pas appropriés pour les Blazor applications Webassembly.</span><span class="sxs-lookup"><span data-stu-id="3de7b-134">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="3de7b-135">La clarification des fournisseurs pris en charge pour Blazor Webassembly est suivie par [clarifier les fournisseurs de configuration pour Blazor WASM (dotnet/AspNetCore. docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="3de7b-135">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="3de7b-136">La configuration dans une Blazor application Webassembly est visible pour les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="3de7b-136">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="3de7b-137">**Ne stockez pas les secrets ou les informations d’identification de l’application dans la configuration.**</span><span class="sxs-lookup"><span data-stu-id="3de7b-137">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="3de7b-138">Pour plus d’informations sur les fournisseurs de configuration, consultez <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="3de7b-138">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="3de7b-139">Configuration des paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="3de7b-139">App settings configuration</span></span>

<span data-ttu-id="3de7b-140">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3de7b-140">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="3de7b-141">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="3de7b-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="3de7b-142">Configuration du fournisseur</span><span class="sxs-lookup"><span data-stu-id="3de7b-142">Provider configuration</span></span>

<span data-ttu-id="3de7b-143">L’exemple suivant utilise un <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> pour fournir une configuration supplémentaire :</span><span class="sxs-lookup"><span data-stu-id="3de7b-143">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="3de7b-144">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3de7b-144">`Program.Main`:</span></span>

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

<span data-ttu-id="3de7b-145">Injecter une <xref:Microsoft.Extensions.Configuration.IConfiguration> instance dans un composant pour accéder aux données de configuration :</span><span class="sxs-lookup"><span data-stu-id="3de7b-145">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

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

<span data-ttu-id="3de7b-146">Pour lire d’autres fichiers de configuration du dossier *wwwroot* dans la configuration, utilisez un <xref:System.Net.Http.HttpClient> pour obtenir le contenu du fichier.</span><span class="sxs-lookup"><span data-stu-id="3de7b-146">To read other configuration files from the *wwwroot* folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="3de7b-147">Lors de l’utilisation de cette approche, l' <xref:System.Net.Http.HttpClient> inscription de service existante peut utiliser le client local créé pour lire le fichier, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="3de7b-147">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="3de7b-148">*wwwroot/cars. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3de7b-148">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="3de7b-149">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3de7b-149">`Program.Main`:</span></span>

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

#### <a name="authentication-configuration"></a><span data-ttu-id="3de7b-150">Configuration de l’authentification</span><span class="sxs-lookup"><span data-stu-id="3de7b-150">Authentication configuration</span></span>

<span data-ttu-id="3de7b-151">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3de7b-151">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="3de7b-152">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3de7b-152">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="3de7b-153">Configuration de la journalisation</span><span class="sxs-lookup"><span data-stu-id="3de7b-153">Logging configuration</span></span>

<span data-ttu-id="3de7b-154">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="3de7b-154">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="3de7b-155">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3de7b-155">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="3de7b-156">Configuration du générateur d’ordinateurs hôtes</span><span class="sxs-lookup"><span data-stu-id="3de7b-156">Host builder configuration</span></span>

<span data-ttu-id="3de7b-157">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="3de7b-157">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="3de7b-158">Configuration mise en cache</span><span class="sxs-lookup"><span data-stu-id="3de7b-158">Cached configuration</span></span>

<span data-ttu-id="3de7b-159">Les fichiers de configuration sont mis en cache pour une utilisation hors connexion.</span><span class="sxs-lookup"><span data-stu-id="3de7b-159">Configuration files are cached for offline use.</span></span> <span data-ttu-id="3de7b-160">Avec les [applications Web progressives (PWA)](xref:blazor/progressive-web-app), vous pouvez uniquement mettre à jour les fichiers de configuration lors de la création d’un déploiement.</span><span class="sxs-lookup"><span data-stu-id="3de7b-160">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="3de7b-161">La modification des fichiers de configuration entre les déploiements n’a aucun effet, car :</span><span class="sxs-lookup"><span data-stu-id="3de7b-161">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="3de7b-162">Les utilisateurs ont des versions mises en cache des fichiers qu’ils continuent d’utiliser.</span><span class="sxs-lookup"><span data-stu-id="3de7b-162">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="3de7b-163">Les fichiers *Service-Worker. js* et *Service-Worker-Assets. js* de PWA doivent être reconstruits lors de la compilation, qui signalent à l’application à la prochaine visite en ligne de l’utilisateur que l’application a été redéployée.</span><span class="sxs-lookup"><span data-stu-id="3de7b-163">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="3de7b-164">Pour plus d’informations sur la façon dont les mises à jour en arrière-plan sont gérées par PWA, consultez <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="3de7b-164">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="3de7b-165">Journalisation</span><span class="sxs-lookup"><span data-stu-id="3de7b-165">Logging</span></span>

<span data-ttu-id="3de7b-166">Pour plus d’informations sur la Blazor prise en charge de la journalisation Webassembly, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="3de7b-166">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="3de7b-167">Serveurs</span><span class="sxs-lookup"><span data-stu-id="3de7b-167"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="3de7b-168">Refléter l’état de la connexion dans l’interface utilisateur</span><span class="sxs-lookup"><span data-stu-id="3de7b-168">Reflect the connection state in the UI</span></span>

<span data-ttu-id="3de7b-169">Lorsque le client détecte que la connexion a été perdue, une interface utilisateur par défaut est affichée à l’utilisateur pendant que le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="3de7b-169">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="3de7b-170">En cas d’échec de la reconnexion, l’utilisateur a la possibilité de réessayer.</span><span class="sxs-lookup"><span data-stu-id="3de7b-170">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="3de7b-171">Pour personnaliser l’interface utilisateur, définissez un élément avec un `id` de `components-reconnect-modal` dans le `<body>` de la page *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="3de7b-171">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="3de7b-172">Le tableau suivant décrit les classes CSS appliquées à l' `components-reconnect-modal` élément.</span><span class="sxs-lookup"><span data-stu-id="3de7b-172">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="3de7b-173">Classe CSS</span><span class="sxs-lookup"><span data-stu-id="3de7b-173">CSS class</span></span>                       | <span data-ttu-id="3de7b-174">Détermine&hellip;</span><span class="sxs-lookup"><span data-stu-id="3de7b-174">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="3de7b-175">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-175">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-176">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-176">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-177">'Blazor'</span></span>
- <span data-ttu-id="3de7b-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-178">'Identity'</span></span>
- <span data-ttu-id="3de7b-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-180">'Razor'</span></span>
- <span data-ttu-id="3de7b-181">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-182">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-182">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-183">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-183">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-184">'Blazor'</span></span>
- <span data-ttu-id="3de7b-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-185">'Identity'</span></span>
- <span data-ttu-id="3de7b-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-187">'Razor'</span></span>
- <span data-ttu-id="3de7b-188">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-189">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-189">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-190">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-190">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-191">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-191">'Blazor'</span></span>
- <span data-ttu-id="3de7b-192">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-192">'Identity'</span></span>
- <span data-ttu-id="3de7b-193">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-193">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-194">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-194">'Razor'</span></span>
- <span data-ttu-id="3de7b-195">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-195">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-196">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-196">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-197">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-197">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-198">'Blazor'</span></span>
- <span data-ttu-id="3de7b-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-199">'Identity'</span></span>
- <span data-ttu-id="3de7b-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-201">'Razor'</span></span>
- <span data-ttu-id="3de7b-202">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-203">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-203">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-204">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-204">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-205">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-205">'Blazor'</span></span>
- <span data-ttu-id="3de7b-206">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-206">'Identity'</span></span>
- <span data-ttu-id="3de7b-207">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-207">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-208">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-208">'Razor'</span></span>
- <span data-ttu-id="3de7b-209">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-209">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-210">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-210">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-211">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-211">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-212">'Blazor'</span></span>
- <span data-ttu-id="3de7b-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-213">'Identity'</span></span>
- <span data-ttu-id="3de7b-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-215">'Razor'</span></span>
- <span data-ttu-id="3de7b-216">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-217">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-217">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-218">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-218">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-219">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-219">'Blazor'</span></span>
- <span data-ttu-id="3de7b-220">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-220">'Identity'</span></span>
- <span data-ttu-id="3de7b-221">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-221">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-222">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-222">'Razor'</span></span>
- <span data-ttu-id="3de7b-223">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-223">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-224">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-224">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-225">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-225">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-226">'Blazor'</span></span>
- <span data-ttu-id="3de7b-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-227">'Identity'</span></span>
- <span data-ttu-id="3de7b-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-229">'Razor'</span></span>
- <span data-ttu-id="3de7b-230">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-231">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-231">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-232">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-232">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-233">'Blazor'</span></span>
- <span data-ttu-id="3de7b-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-234">'Identity'</span></span>
- <span data-ttu-id="3de7b-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-236">'Razor'</span></span>
- <span data-ttu-id="3de7b-237">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-237">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-238">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-238">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-239">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-239">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-240">'Blazor'</span></span>
- <span data-ttu-id="3de7b-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-241">'Identity'</span></span>
- <span data-ttu-id="3de7b-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-243">'Razor'</span></span>
- <span data-ttu-id="3de7b-244">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-245">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-245">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-246">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-246">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-247">'Blazor'</span></span>
- <span data-ttu-id="3de7b-248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-248">'Identity'</span></span>
- <span data-ttu-id="3de7b-249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-249">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-250">'Razor'</span></span>
- <span data-ttu-id="3de7b-251">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-252">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-252">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-253">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-253">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-254">'Blazor'</span></span>
- <span data-ttu-id="3de7b-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-255">'Identity'</span></span>
- <span data-ttu-id="3de7b-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-257">'Razor'</span></span>
- <span data-ttu-id="3de7b-258">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-259">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-259">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-260">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-260">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-261">'Blazor'</span></span>
- <span data-ttu-id="3de7b-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-262">'Identity'</span></span>
- <span data-ttu-id="3de7b-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-264">'Razor'</span></span>
- <span data-ttu-id="3de7b-265">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-265">'SignalR' uid:</span></span> 

<span data-ttu-id="3de7b-266">---------------- | titre de la--- : « ASP.NET Core Blazor de l’hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-266">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-267">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-267">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-268">'Blazor'</span></span>
- <span data-ttu-id="3de7b-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-269">'Identity'</span></span>
- <span data-ttu-id="3de7b-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-271">'Razor'</span></span>
- <span data-ttu-id="3de7b-272">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-273">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-273">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-274">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-274">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-275">'Blazor'</span></span>
- <span data-ttu-id="3de7b-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-276">'Identity'</span></span>
- <span data-ttu-id="3de7b-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-278">'Razor'</span></span>
- <span data-ttu-id="3de7b-279">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-280">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-280">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-281">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-281">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-282">'Blazor'</span></span>
- <span data-ttu-id="3de7b-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-283">'Identity'</span></span>
- <span data-ttu-id="3de7b-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-285">'Razor'</span></span>
- <span data-ttu-id="3de7b-286">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-287">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-287">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-288">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-288">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-289">'Blazor'</span></span>
- <span data-ttu-id="3de7b-290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-290">'Identity'</span></span>
- <span data-ttu-id="3de7b-291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-291">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-292">'Razor'</span></span>
- <span data-ttu-id="3de7b-293">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-293">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-294">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-294">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-295">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-295">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-296">'Blazor'</span></span>
- <span data-ttu-id="3de7b-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-297">'Identity'</span></span>
- <span data-ttu-id="3de7b-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-299">'Razor'</span></span>
- <span data-ttu-id="3de7b-300">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="3de7b-301">titre : « ASP.NET Core Blazor Hébergement de la configuration du modèle » auteur : Description : « en savoir plus sur Blazor l’hébergement de la configuration du modèle, y compris l’intégration Razor des composants dans Razor les pages et les applications MVC. »</span><span class="sxs-lookup"><span data-stu-id="3de7b-301">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="3de7b-302">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="3de7b-302">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="3de7b-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-303">'Blazor'</span></span>
- <span data-ttu-id="3de7b-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="3de7b-304">'Identity'</span></span>
- <span data-ttu-id="3de7b-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="3de7b-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="3de7b-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="3de7b-306">'Razor'</span></span>
- <span data-ttu-id="3de7b-307">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="3de7b-307">'SignalR' uid:</span></span> 

<span data-ttu-id="3de7b-308">--------- | | `components-reconnect-show`     | Connexion perdue.</span><span class="sxs-lookup"><span data-stu-id="3de7b-308">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="3de7b-309">Le client tente de se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="3de7b-309">The client is attempting to reconnect.</span></span> <span data-ttu-id="3de7b-310">Affichez le modal.</span><span class="sxs-lookup"><span data-stu-id="3de7b-310">Show the modal.</span></span> <span data-ttu-id="3de7b-311">| | `components-reconnect-hide`     | Une connexion active est rétablie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="3de7b-311">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="3de7b-312">Masquez le modal.</span><span class="sxs-lookup"><span data-stu-id="3de7b-312">Hide the modal.</span></span> <span data-ttu-id="3de7b-313">| | `components-reconnect-failed`   | Échec de la reconnexion, probablement en raison d’une défaillance du réseau.</span><span class="sxs-lookup"><span data-stu-id="3de7b-313">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="3de7b-314">Pour tenter une reconnexion, appelez `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="3de7b-314">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="3de7b-315">| | `components-reconnect-rejected` | Reconnexion refusée.</span><span class="sxs-lookup"><span data-stu-id="3de7b-315">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="3de7b-316">Le serveur a été atteint mais a refusé la connexion, et l’état de l’utilisateur sur le serveur est perdu.</span><span class="sxs-lookup"><span data-stu-id="3de7b-316">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="3de7b-317">Pour recharger l’application, appelez `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="3de7b-317">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="3de7b-318">Cet état de connexion peut se produire dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="3de7b-318">This connection state may result when:</span></span><ul><li><span data-ttu-id="3de7b-319">Un blocage dans le circuit côté serveur se produit.</span><span class="sxs-lookup"><span data-stu-id="3de7b-319">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="3de7b-320">Le client est déconnecté suffisamment longtemps pour que le serveur supprime l’état de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3de7b-320">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="3de7b-321">Les instances des composants avec lesquels l’utilisateur interagit sont supprimées.</span><span class="sxs-lookup"><span data-stu-id="3de7b-321">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="3de7b-322">Le serveur est redémarré ou le processus de travail de l’application est recyclé.</span><span class="sxs-lookup"><span data-stu-id="3de7b-322">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="3de7b-323">Mode d’affichage</span><span class="sxs-lookup"><span data-stu-id="3de7b-323">Render mode</span></span>

Blazor<span data-ttu-id="3de7b-324">Les applications serveur sont configurées par défaut pour prérestituer l’interface utilisateur sur le serveur avant que la connexion cliente au serveur soit établie.</span><span class="sxs-lookup"><span data-stu-id="3de7b-324"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="3de7b-325">Elle est configurée dans la page *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="3de7b-325">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="3de7b-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>Configure si le composant :</span><span class="sxs-lookup"><span data-stu-id="3de7b-326"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="3de7b-327">Est prérendu dans la page.</span><span class="sxs-lookup"><span data-stu-id="3de7b-327">Is prerendered into the page.</span></span>
* <span data-ttu-id="3de7b-328">Est rendu en HTML statique sur la page ou s’il contient les informations nécessaires pour démarrer une Blazor application à partir de l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="3de7b-328">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> | <span data-ttu-id="3de7b-329">Description</span><span class="sxs-lookup"><span data-stu-id="3de7b-329">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="3de7b-330">Génère le rendu du composant en HTML statique et comprend un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="3de7b-330">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3de7b-331">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="3de7b-331">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="3de7b-332">Restitue un marqueur pour une Blazor application serveur.</span><span class="sxs-lookup"><span data-stu-id="3de7b-332">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3de7b-333">La sortie du composant n’est pas incluse.</span><span class="sxs-lookup"><span data-stu-id="3de7b-333">Output from the component isn't included.</span></span> <span data-ttu-id="3de7b-334">Au démarrage de l’agent utilisateur, ce marqueur est utilisé pour démarrer une Blazor application.</span><span class="sxs-lookup"><span data-stu-id="3de7b-334">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="3de7b-335">Génère le rendu du composant en HTML statique.</span><span class="sxs-lookup"><span data-stu-id="3de7b-335">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3de7b-336">Le rendu des composants serveur à partir d’une page HTML statique n’est pas pris en charge.</span><span class="sxs-lookup"><span data-stu-id="3de7b-336">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="3de7b-337">Configurer le SignalR client pour les Blazor applications serveur</span><span class="sxs-lookup"><span data-stu-id="3de7b-337">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="3de7b-338">Parfois, vous devez configurer le SignalR client utilisé par les Blazor applications serveur.</span><span class="sxs-lookup"><span data-stu-id="3de7b-338">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="3de7b-339">Par exemple, vous pouvez configurer la journalisation sur le SignalR client pour diagnostiquer un problème de connexion.</span><span class="sxs-lookup"><span data-stu-id="3de7b-339">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="3de7b-340">Pour configurer le SignalR client dans le fichier *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3de7b-340">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="3de7b-341">Ajoutez un `autostart="false"` attribut à la `<script>` balise pour le `blazor.server.js` script.</span><span class="sxs-lookup"><span data-stu-id="3de7b-341">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="3de7b-342">Appelez `Blazor.start` et transmettez un objet de configuration qui spécifie le SignalR Générateur.</span><span class="sxs-lookup"><span data-stu-id="3de7b-342">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="3de7b-343">Journalisation</span><span class="sxs-lookup"><span data-stu-id="3de7b-343">Logging</span></span>

<span data-ttu-id="3de7b-344">Pour plus d’informations sur la Blazor prise en charge de la journalisation du serveur, consultez <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="3de7b-344">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
