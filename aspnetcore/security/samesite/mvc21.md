---
title: Exemple de cookie SameSite ASP.NET Core MVC 2,1
author: rick-anderson
description: Exemple de cookie SameSite ASP.NET Core MVC 2,1
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: 4239321531f3a7696a15b1dea164450ea0860c2b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409057"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="bff26-103">Exemple de cookie SameSite ASP.NET Core MVC 2,1</span><span class="sxs-lookup"><span data-stu-id="bff26-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="bff26-104">ASP.NET Core 2,1 dispose d’une prise en charge intégrée de l’attribut [SameSite](https://www.owasp.org/index.php/SameSite) , mais il a été écrit dans la norme d’origine.</span><span class="sxs-lookup"><span data-stu-id="bff26-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="bff26-105">Le [comportement corrigé](https://github.com/dotnet/aspnetcore/issues/8212) a modifié la signification de `SameSite.None` pour émettre l’attribut sameSite avec une valeur de `None` , au lieu de ne pas émettre la valeur du tout.</span><span class="sxs-lookup"><span data-stu-id="bff26-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="bff26-106">Si vous ne souhaitez pas émettre la valeur, vous pouvez définir la `SameSite` propriété sur un cookie sur-1.</span><span class="sxs-lookup"><span data-stu-id="bff26-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="bff26-107">Écriture de l’attribut SameSite</span><span class="sxs-lookup"><span data-stu-id="bff26-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="bff26-108">Voici un exemple d’écriture d’un attribut SameSite sur un cookie :</span><span class="sxs-lookup"><span data-stu-id="bff26-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="bff26-109">Définition des cookies d’authentification de cookie et d’état de session</span><span class="sxs-lookup"><span data-stu-id="bff26-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="bff26-110">L’authentification par cookie, l’état de session et [divers autres composants](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) définissent leurs options sameSite via des options de cookie, par exemple</span><span class="sxs-lookup"><span data-stu-id="bff26-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="bff26-111">Dans le code précédent, l’authentification de cookie et l’état de session attribuent à leur attribut sameSite `None` la valeur, en émettant l’attribut avec une `None` valeur, et attribuent également à l’attribut Secure la valeur true.</span><span class="sxs-lookup"><span data-stu-id="bff26-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="bff26-112">Exécution de l'exemple</span><span class="sxs-lookup"><span data-stu-id="bff26-112">Run the sample</span></span>

<span data-ttu-id="bff26-113">Si vous exécutez l' [exemple de projet](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), chargez votre débogueur de navigateur sur la page initiale et utilisez-le pour afficher la collection de cookies pour le site.</span><span class="sxs-lookup"><span data-stu-id="bff26-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="bff26-114">Pour ce faire, dans Edge et chrome, appuyez sur l' `F12` `Application` onglet, puis cliquez sur l’URL du site sous l' `Cookies` option dans la `Storage` section.</span><span class="sxs-lookup"><span data-stu-id="bff26-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Liste des cookies du débogueur de navigateur](BrowserDebugger.png)

<span data-ttu-id="bff26-116">Vous pouvez voir à partir de l’image ci-dessus que le cookie créé par l’exemple lorsque vous cliquez sur le bouton « créer un cookie SameSite » a une valeur d’attribut SameSite égale `Lax` à, qui correspond à la valeur définie dans l' [exemple de code](#sampleCode).</span><span class="sxs-lookup"><span data-stu-id="bff26-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="bff26-117">Interception des cookies</span><span class="sxs-lookup"><span data-stu-id="bff26-117">Intercepting cookies</span></span>

<span data-ttu-id="bff26-118">Afin d’intercepter les cookies, pour ajuster la valeur None en fonction de sa prise en charge dans l’agent Browser de l’utilisateur, vous devez utiliser l' `CookiePolicy` intergiciel (middleware).</span><span class="sxs-lookup"><span data-stu-id="bff26-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="bff26-119">Celui-ci doit être placé dans le pipeline de requête HTTP **avant** les composants qui écrivent des cookies et configurés dans `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="bff26-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="bff26-120">Pour l’insérer dans le pipeline `app.UseCookiePolicy()` , utilisez la `Configure(IApplicationBuilder, IHostingEnvironment)` méthode dans [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="bff26-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="bff26-121">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="bff26-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="bff26-122">Ensuite, dans la, `ConfigureServices(IServiceCollection services)` configurez la stratégie de cookie pour appeler une classe d’assistance lorsque des cookies sont ajoutés ou supprimés.</span><span class="sxs-lookup"><span data-stu-id="bff26-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="bff26-123">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="bff26-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="bff26-124">Fonction d’assistance `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="bff26-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="bff26-125">Est appelé lorsque les cookies sont ajoutés à la demande ou supprimés de la requête.</span><span class="sxs-lookup"><span data-stu-id="bff26-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="bff26-126">Vérifie si la `SameSite` propriété a la valeur `None` .</span><span class="sxs-lookup"><span data-stu-id="bff26-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="bff26-127">Si `SameSite` a la valeur `None` et que l’agent utilisateur actuel est connu pour ne pas prendre en charge la valeur d’attribut None.</span><span class="sxs-lookup"><span data-stu-id="bff26-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="bff26-128">La vérification s’effectue à l’aide de la classe [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) :</span><span class="sxs-lookup"><span data-stu-id="bff26-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="bff26-129">Définit `SameSite` pour ne pas émettre la valeur en affectant à la propriété la valeur`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="bff26-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="bff26-130">Ciblage .NET Framework</span><span class="sxs-lookup"><span data-stu-id="bff26-130">Targeting .NET Framework</span></span>

<span data-ttu-id="bff26-131">ASP.NET Core et System. Web (ASP.NET Classic) ont des implémentations indépendantes de SameSite.</span><span class="sxs-lookup"><span data-stu-id="bff26-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="bff26-132">Les correctifs de SameSite KB pour les .NET Framework ne sont pas requis si vous utilisez ASP.NET Core et que la version minimale requise de l’infrastructure System. Web SameSite (.NET 4.7.2) s’appliquent à ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bff26-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="bff26-133">ASP.NET Core sur .NET requiert la mise à jour des dépendances de package NuGet pour récupérer les correctifs appropriés.</span><span class="sxs-lookup"><span data-stu-id="bff26-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="bff26-134">Pour obtenir les modifications de ASP.NET Core pour .NET Framework Vérifiez que vous disposez d’une référence directe aux packages et versions corrigés (2.1.14 ou versions ultérieures 2,1).</span><span class="sxs-lookup"><span data-stu-id="bff26-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="bff26-135">Informations complémentaires</span><span class="sxs-lookup"><span data-stu-id="bff26-135">More Information</span></span>
 
<span data-ttu-id="bff26-136">[Mises à jour chrome](https://www.chromium.org/updates/same-site) 
 [Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 
 ASP.net Core SameSite [Annonce de modification de ASP.NET Core 2,1 SameSite](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="bff26-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>