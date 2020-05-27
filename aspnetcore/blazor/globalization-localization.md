---
<span data-ttu-id="27de4-101">titre : « ASP.NET Core Blazor globalisation et localisation » auteur : Description : « Découvrez comment rendre des Razor composants accessibles aux utilisateurs dans plusieurs cultures et langues. »</span><span class="sxs-lookup"><span data-stu-id="27de4-101">title: 'ASP.NET Core Blazor globalization and localization' author: description: 'Learn how to make Razor components accessible to users in multiple cultures and languages.'</span></span>
<span data-ttu-id="27de4-102">monikerRange : ms. Author : ms. Custom : ms. Date : No-Loc :</span><span class="sxs-lookup"><span data-stu-id="27de4-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="27de4-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="27de4-103">'Blazor'</span></span>
- <span data-ttu-id="27de4-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="27de4-104">'Identity'</span></span>
- <span data-ttu-id="27de4-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="27de4-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="27de4-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="27de4-106">'Razor'</span></span>
- <span data-ttu-id="27de4-107">SignalRUID :</span><span class="sxs-lookup"><span data-stu-id="27de4-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="27de4-108">BlazorGlobalisation et localisation ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="27de4-108">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="27de4-109">Par [Luke Latham](https://github.com/guardrex) et [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="27de4-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="27de4-110">les composants peuvent être rendus accessibles aux utilisateurs dans plusieurs cultures et langues.</span><span class="sxs-lookup"><span data-stu-id="27de4-110"> components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="27de4-111">Les scénarios de globalisation et de localisation .NET suivants sont disponibles :</span><span class="sxs-lookup"><span data-stu-id="27de4-111">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="27de4-112">. Système de ressources du réseau</span><span class="sxs-lookup"><span data-stu-id="27de4-112">.NET's resources system</span></span>
* <span data-ttu-id="27de4-113">Mise en forme des nombres et des dates spécifiques à la culture</span><span class="sxs-lookup"><span data-stu-id="27de4-113">Culture-specific number and date formatting</span></span>

<span data-ttu-id="27de4-114">Un ensemble limité de scénarios de localisation de ASP.NET Core est actuellement pris en charge :</span><span class="sxs-lookup"><span data-stu-id="27de4-114">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="27de4-115"><xref:Microsoft.Extensions.Localization.IStringLocalizer>et <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *sont pris en charge* dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="27de4-115"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="27de4-116"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>la <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> localisation des annotations de données, et est ASP.net Core les scénarios MVC et **non pris en charge** dans les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="27de4-116"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="27de4-117">Pour plus d'informations, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="27de4-117">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="27de4-118">Globalisation</span><span class="sxs-lookup"><span data-stu-id="27de4-118">Globalization</span></span>

Blazor<span data-ttu-id="27de4-119">[`@bind`](xref:mvc/views/razor#bind)la fonctionnalité de effectue des mises en forme et analyse les valeurs pour l’affichage en fonction de la culture actuelle de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="27de4-119">'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="27de4-120">La culture actuelle est accessible à partir de la <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> propriété.</span><span class="sxs-lookup"><span data-stu-id="27de4-120">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="27de4-121"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>est utilisé pour les types de champ suivants ( `<input type="{TYPE}" />` ) :</span><span class="sxs-lookup"><span data-stu-id="27de4-121"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="27de4-122">Les types de champ précédents :</span><span class="sxs-lookup"><span data-stu-id="27de4-122">The preceding field types:</span></span>

* <span data-ttu-id="27de4-123">Sont affichés à l’aide des règles de mise en forme appropriées basées sur le navigateur.</span><span class="sxs-lookup"><span data-stu-id="27de4-123">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="27de4-124">Ne peut pas contenir de texte de forme libre.</span><span class="sxs-lookup"><span data-stu-id="27de4-124">Can't contain free-form text.</span></span>
* <span data-ttu-id="27de4-125">Fournir des caractéristiques d’interaction de l’utilisateur en fonction de l’implémentation du navigateur.</span><span class="sxs-lookup"><span data-stu-id="27de4-125">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="27de4-126">Les types de champs suivants ont des exigences de mise en forme spécifiques et ne sont pas actuellement pris en charge par Blazor , car ils ne sont pas pris en charge par tous les principaux navigateurs :</span><span class="sxs-lookup"><span data-stu-id="27de4-126">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="27de4-127">[`@bind`](xref:mvc/views/razor#bind)prend en charge le `@bind:culture` paramètre pour fournir un <xref:System.Globalization.CultureInfo?displayProperty=fullName> pour l’analyse et la mise en forme d’une valeur.</span><span class="sxs-lookup"><span data-stu-id="27de4-127">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="27de4-128">La spécification d’une culture n’est pas recommandée lors de l’utilisation des `date` `number` types de champ et.</span><span class="sxs-lookup"><span data-stu-id="27de4-128">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="27de4-129">`date`et `number` disposent d’une Blazor prise en charge intégrée qui fournit la culture requise.</span><span class="sxs-lookup"><span data-stu-id="27de4-129">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="27de4-130">Localisation</span><span class="sxs-lookup"><span data-stu-id="27de4-130">Localization</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="27de4-131">Webassembly</span><span class="sxs-lookup"><span data-stu-id="27de4-131"> WebAssembly</span></span>

Blazor<span data-ttu-id="27de4-132">Les applications webassembly définissent la culture à l’aide de la [préférence de langue](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="27de4-132"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="27de4-133">Pour configurer explicitement la culture, définissez <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> et <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> dans `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="27de4-133">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="27de4-134">Par défaut, Blazor la configuration de l’éditeur de liens pour les Blazor applications webassembly supprime les informations d’internationalisation, à l’exception des paramètres régionaux demandés explicitement.</span><span class="sxs-lookup"><span data-stu-id="27de4-134">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="27de4-135">Pour plus d’informations et de conseils sur le contrôle du comportement de l’éditeur de liens, consultez <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization> .</span><span class="sxs-lookup"><span data-stu-id="27de4-135">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="27de4-136">Alors que la culture qui Blazor sélectionne par défaut peut être suffisante pour la plupart des utilisateurs, envisagez d’offrir aux utilisateurs un moyen de spécifier leurs paramètres régionaux préférés.</span><span class="sxs-lookup"><span data-stu-id="27de4-136">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="27de4-137">Pour obtenir un Blazor exemple d’application Webassembly avec un sélecteur de culture, consultez l’exemple d’application de localisation [LocSample](https://github.com/pranavkm/LocSample) .</span><span class="sxs-lookup"><span data-stu-id="27de4-137">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="27de4-138">Serveurs</span><span class="sxs-lookup"><span data-stu-id="27de4-138"> Server</span></span>

Blazor<span data-ttu-id="27de4-139">Les applications serveur sont localisées à l’aide de l' [intergiciel (middleware](xref:fundamentals/localization#localization-middleware)) de localisation.</span><span class="sxs-lookup"><span data-stu-id="27de4-139"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="27de4-140">L’intergiciel sélectionne la culture appropriée pour les utilisateurs qui demandent des ressources à partir de l’application.</span><span class="sxs-lookup"><span data-stu-id="27de4-140">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="27de4-141">La culture peut être définie à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="27de4-141">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="27de4-142">Cookies</span><span class="sxs-lookup"><span data-stu-id="27de4-142">Cookies</span></span>](#cookies)
* [<span data-ttu-id="27de4-143">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="27de4-143">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="27de4-144">Pour plus d’informations et d’exemples, consultez <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="27de4-144">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="27de4-145">Cookies</span><span class="sxs-lookup"><span data-stu-id="27de4-145">Cookies</span></span>

<span data-ttu-id="27de4-146">Un cookie de culture de localisation peut conserver la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="27de4-146">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="27de4-147">Le cookie est créé par la `OnGet` méthode de la page hôte de l’application (*pages/Host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="27de4-147">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="27de4-148">L’intergiciel (middleware) de localisation lit le cookie sur les demandes suivantes pour définir la culture de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="27de4-148">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="27de4-149">L’utilisation d’un cookie garantit que la connexion WebSocket peut propager correctement la culture.</span><span class="sxs-lookup"><span data-stu-id="27de4-149">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="27de4-150">Si les schémas de localisation sont basés sur le chemin d’URL ou la chaîne de requête, il est possible que le schéma ne soit pas en mesure de fonctionner avec les WebSockets, et donc de ne pas conserver la culture.</span><span class="sxs-lookup"><span data-stu-id="27de4-150">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="27de4-151">Par conséquent, l’utilisation d’un cookie de culture de localisation est l’approche recommandée.</span><span class="sxs-lookup"><span data-stu-id="27de4-151">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="27de4-152">Toute technique peut être utilisée pour assigner une culture si la culture est rendue persistante dans un cookie de localisation.</span><span class="sxs-lookup"><span data-stu-id="27de4-152">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="27de4-153">Si l’application a déjà un schéma de localisation établi pour les ASP.NET Core côté serveur, continuez à utiliser l’infrastructure de localisation existante de l’application et à définir le cookie de la culture de localisation dans le schéma de l’application.</span><span class="sxs-lookup"><span data-stu-id="27de4-153">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="27de4-154">L’exemple suivant montre comment définir la culture actuelle dans un cookie qui peut être lu par l’intergiciel (middleware) de localisation.</span><span class="sxs-lookup"><span data-stu-id="27de4-154">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="27de4-155">Créez un fichier *pages/_Host. cshtml. cs* avec le contenu suivant dans l' Blazor application serveur :</span><span class="sxs-lookup"><span data-stu-id="27de4-155">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="27de4-156">La localisation est gérée par l’application dans la séquence d’événements suivante :</span><span class="sxs-lookup"><span data-stu-id="27de4-156">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="27de4-157">Le navigateur envoie une requête HTTP initiale à l’application.</span><span class="sxs-lookup"><span data-stu-id="27de4-157">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="27de4-158">La culture est affectée par l’intergiciel (middleware) de localisation.</span><span class="sxs-lookup"><span data-stu-id="27de4-158">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="27de4-159">La `OnGet` méthode dans *_Host. cshtml. cs* rend persistante la culture dans un cookie dans le cadre de la réponse.</span><span class="sxs-lookup"><span data-stu-id="27de4-159">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="27de4-160">Le navigateur ouvre une connexion WebSocket pour créer une Blazor session de serveur interactive.</span><span class="sxs-lookup"><span data-stu-id="27de4-160">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="27de4-161">L’intergiciel de localisation lit le cookie et assigne la culture.</span><span class="sxs-lookup"><span data-stu-id="27de4-161">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="27de4-162">La Blazor session serveur commence par la culture correcte.</span><span class="sxs-lookup"><span data-stu-id="27de4-162">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="27de4-163">Fournir l’interface utilisateur pour choisir la culture</span><span class="sxs-lookup"><span data-stu-id="27de4-163">Provide UI to choose the culture</span></span>

<span data-ttu-id="27de4-164">Pour fournir une interface utilisateur permettant à un utilisateur de sélectionner une culture, il est recommandé d’effectuer une *approche basée sur la redirection* .</span><span class="sxs-lookup"><span data-stu-id="27de4-164">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="27de4-165">Le processus est similaire à ce qui se produit dans une application Web lorsqu’un utilisateur tente d’accéder à une ressource sécurisée.</span><span class="sxs-lookup"><span data-stu-id="27de4-165">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="27de4-166">L’utilisateur est redirigé vers une page de connexion, puis redirigé vers la ressource d’origine.</span><span class="sxs-lookup"><span data-stu-id="27de4-166">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="27de4-167">L’application conserve la culture sélectionnée de l’utilisateur via une redirection vers un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="27de4-167">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="27de4-168">Le contrôleur définit la culture sélectionnée de l’utilisateur dans un cookie et redirige l’utilisateur vers l’URI d’origine.</span><span class="sxs-lookup"><span data-stu-id="27de4-168">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="27de4-169">Établissez un point de terminaison HTTP sur le serveur pour définir la culture sélectionnée de l’utilisateur dans un cookie et effectuer la redirection vers l’URI d’origine :</span><span class="sxs-lookup"><span data-stu-id="27de4-169">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="27de4-170">Utilisez le <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> résultat de l’action pour empêcher les attaques de redirection ouvertes.</span><span class="sxs-lookup"><span data-stu-id="27de4-170">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="27de4-171">Pour plus d'informations, consultez <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="27de4-171">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="27de4-172">Le composant suivant montre un exemple d’exécution de la redirection initiale lorsque l’utilisateur sélectionne une culture :</span><span class="sxs-lookup"><span data-stu-id="27de4-172">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="27de4-173">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="27de4-173">Additional resources</span></span>

* <xref:fundamentals/localization>
