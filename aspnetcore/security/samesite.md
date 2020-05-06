---
title: Utiliser des cookies SameSite dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser pour SameSite des cookies dans ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 43d5a3dbc5e202688e006355e0b105a86d721460
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775100"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="49948-103">Utiliser des cookies SameSite dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49948-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="49948-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="49948-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="49948-105">SameSite est un projet standard de l' [IETF](https://ietf.org/about/) conçu pour offrir une protection contre les attaques de falsification de requête intersites (CSRF).</span><span class="sxs-lookup"><span data-stu-id="49948-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="49948-106">Initialement rédigée dans [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), le brouillon standard a été mis à jour dans [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="49948-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="49948-107">La norme mise à jour n’est pas à compatibilité descendante avec la norme précédente, avec comme suit les différences les plus perceptibles :</span><span class="sxs-lookup"><span data-stu-id="49948-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="49948-108">Les cookies sans en-tête SameSite `SameSite=Lax` sont traités par défaut.</span><span class="sxs-lookup"><span data-stu-id="49948-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="49948-109">`SameSite=None`doit être utilisé pour autoriser l’utilisation de cookies inter-sites.</span><span class="sxs-lookup"><span data-stu-id="49948-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="49948-110">Les cookies qui `SameSite=None` affirment doivent également être `Secure`marqués comme.</span><span class="sxs-lookup"><span data-stu-id="49948-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="49948-111">Les applications qui [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) utilisent peuvent rencontrer des `sameSite=Lax` problèmes `sameSite=Strict` avec ou `<iframe>` , car est traité comme des scénarios intersites.</span><span class="sxs-lookup"><span data-stu-id="49948-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="49948-112">La valeur `SameSite=None` n’est pas autorisée par la [norme 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) et oblige certaines implémentations à traiter ces cookies `SameSite=Strict`comme.</span><span class="sxs-lookup"><span data-stu-id="49948-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="49948-113">Consultez [prise en charge des navigateurs plus anciens](#sob) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="49948-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="49948-114">Le `SameSite=Lax` paramètre fonctionne pour la plupart des cookies d’application.</span><span class="sxs-lookup"><span data-stu-id="49948-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="49948-115">Certaines formes d’authentification comme [OpenID Connect](https://openid.net/connect/) (OIDC) et [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default pour la publication de redirections basées sur.</span><span class="sxs-lookup"><span data-stu-id="49948-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="49948-116">Les redirections basées sur la publication déclenchent les protections du navigateur SameSite, donc SameSite est désactivé pour ces composants.</span><span class="sxs-lookup"><span data-stu-id="49948-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="49948-117">La plupart des connexions [OAuth](https://oauth.net/) ne sont pas affectées en raison de différences de flux de demande.</span><span class="sxs-lookup"><span data-stu-id="49948-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="49948-118">Chaque composant ASP.NET Core qui émet des cookies doit décider si SameSite est approprié.</span><span class="sxs-lookup"><span data-stu-id="49948-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="49948-119">Exemple de code de test SameSite</span><span class="sxs-lookup"><span data-stu-id="49948-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="49948-120">Les exemples suivants peuvent être téléchargés et testés :</span><span class="sxs-lookup"><span data-stu-id="49948-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="49948-121">Exemple</span><span class="sxs-lookup"><span data-stu-id="49948-121">Sample</span></span>               | <span data-ttu-id="49948-122">Document</span><span class="sxs-lookup"><span data-stu-id="49948-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="49948-123">MVC .NET Core</span><span class="sxs-lookup"><span data-stu-id="49948-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="49948-124">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="49948-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49948-125">L’exemple suivant peut être téléchargé et testé :</span><span class="sxs-lookup"><span data-stu-id="49948-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="49948-126">Exemple</span><span class="sxs-lookup"><span data-stu-id="49948-126">Sample</span></span>               | <span data-ttu-id="49948-127">Document</span><span class="sxs-lookup"><span data-stu-id="49948-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="49948-128">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="49948-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="49948-129">Prise en charge de .NET Core pour l’attribut sameSite</span><span class="sxs-lookup"><span data-stu-id="49948-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="49948-130">.NET Core 2,2 prend en charge 2019 Draft Standard pour SameSite depuis la publication des mises à jour en décembre 2019.</span><span class="sxs-lookup"><span data-stu-id="49948-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="49948-131">Les développeurs sont en mesure de contrôler par programmation la valeur de l’attribut sameSite à `HttpCookie.SameSite` l’aide de la propriété.</span><span class="sxs-lookup"><span data-stu-id="49948-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="49948-132">Si vous `SameSite` affectez à la propriété la valeur strict, LAX ou None, ces valeurs sont écrites sur le réseau avec le cookie.</span><span class="sxs-lookup"><span data-stu-id="49948-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="49948-133">Si la valeur est égale à (SameSiteMode) (-1), aucun attribut sameSite ne doit être inclus sur le réseau avec le cookie</span><span class="sxs-lookup"><span data-stu-id="49948-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="49948-134">.NET Core 3,0 prend en charge les valeurs SameSite mises à jour et ajoute une `SameSiteMode.Unspecified` valeur enum `SameSiteMode` supplémentaire à l’enum.</span><span class="sxs-lookup"><span data-stu-id="49948-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="49948-135">Cette nouvelle valeur indique qu’aucun sameSite ne doit être envoyé avec le cookie.</span><span class="sxs-lookup"><span data-stu-id="49948-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="49948-136">Modifications du comportement du correctif décembre</span><span class="sxs-lookup"><span data-stu-id="49948-136">December patch behavior changes</span></span>

<span data-ttu-id="49948-137">Le changement de comportement spécifique pour .NET Framework et .NET Core 2,1 est la `SameSite` manière dont la propriété `None` interprète la valeur.</span><span class="sxs-lookup"><span data-stu-id="49948-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="49948-138">Avant que le correctif ait une `None` valeur « ne pas émettre l’attribut », après le correctif, cela signifie « émettre l’attribut avec la valeur `None`« ».</span><span class="sxs-lookup"><span data-stu-id="49948-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="49948-139">Une fois le correctif `SameSite` ayant la `(SameSiteMode)(-1)` valeur, l’attribut n’est pas émis.</span><span class="sxs-lookup"><span data-stu-id="49948-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="49948-140">La valeur SameSite par défaut pour les cookies d’authentification par formulaire et d' `None` état `Lax`de session a été remplacée par.</span><span class="sxs-lookup"><span data-stu-id="49948-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="49948-141">Utilisation des API avec SameSite</span><span class="sxs-lookup"><span data-stu-id="49948-141">API usage with SameSite</span></span>

<span data-ttu-id="49948-142">[HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) a pour valeur `Unspecified`par défaut, ce qui signifie qu’aucun attribut SameSite n’a été ajouté au cookie et que le client utilise son comportement par défaut (LAX pour les nouveaux navigateurs, None pour les anciens navigateurs).</span><span class="sxs-lookup"><span data-stu-id="49948-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="49948-143">Le code suivant montre comment modifier la valeur du cookie SameSite en `SameSiteMode.Lax`:</span><span class="sxs-lookup"><span data-stu-id="49948-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="49948-144">Tous les composants ASP.NET Core qui émettent des cookies remplacent les valeurs par défaut précédentes par les paramètres appropriés pour leurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="49948-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="49948-145">Les valeurs par défaut substituées ne sont pas modifiées.</span><span class="sxs-lookup"><span data-stu-id="49948-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="49948-146">Composant</span><span class="sxs-lookup"><span data-stu-id="49948-146">Component</span></span> | <span data-ttu-id="49948-147">cookie</span><span class="sxs-lookup"><span data-stu-id="49948-147">cookie</span></span> | <span data-ttu-id="49948-148">Default</span><span class="sxs-lookup"><span data-stu-id="49948-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="49948-149">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="49948-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="49948-150">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="49948-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="49948-151">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="49948-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="49948-152">Authentification par cookie</span><span class="sxs-lookup"><span data-stu-id="49948-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="49948-153">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="49948-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="49948-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="49948-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="49948-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="49948-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="49948-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="49948-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="49948-157">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="49948-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="49948-158">ASP.NET Core 3,1 et versions ultérieures fournissent la prise en charge SameSite suivante :</span><span class="sxs-lookup"><span data-stu-id="49948-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="49948-159">Redéfinit le comportement de `SameSiteMode.None` à émettre`SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="49948-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="49948-160">Ajoute une nouvelle valeur `SameSiteMode.Unspecified` pour omettre l’attribut SameSite.</span><span class="sxs-lookup"><span data-stu-id="49948-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="49948-161">Toutes les API de `Unspecified`cookies ont par défaut la valeur.</span><span class="sxs-lookup"><span data-stu-id="49948-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="49948-162">Certains composants qui utilisent des cookies définissent des valeurs plus spécifiques à leurs scénarios.</span><span class="sxs-lookup"><span data-stu-id="49948-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="49948-163">Consultez le tableau ci-dessus pour obtenir des exemples.</span><span class="sxs-lookup"><span data-stu-id="49948-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49948-164">Dans ASP.NET Core 3,0 et versions ultérieures, les valeurs par défaut SameSite ont été modifiées pour éviter les conflits avec les paramètres par défaut incohérents du client.</span><span class="sxs-lookup"><span data-stu-id="49948-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="49948-165">Les API suivantes ont modifié la valeur par `SameSiteMode.Lax ` défaut `-1` de à afin d’éviter d’émettre un attribut SameSite pour ces cookies :</span><span class="sxs-lookup"><span data-stu-id="49948-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="49948-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>utilisé avec [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="49948-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="49948-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>utilisé comme fabrique pour`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="49948-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="49948-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="49948-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="49948-169">Historique et modifications</span><span class="sxs-lookup"><span data-stu-id="49948-169">History and changes</span></span>

<span data-ttu-id="49948-170">La prise en charge de SameSite a été implémentée pour la première fois dans ASP.NET Core dans 2,0 à l’aide de la [norme 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="49948-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="49948-171">La norme 2016 était opt-in.</span><span class="sxs-lookup"><span data-stu-id="49948-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="49948-172">ASP.NET Core choisi en définissant plusieurs cookies `Lax` par défaut.</span><span class="sxs-lookup"><span data-stu-id="49948-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="49948-173">Après avoir rencontré plusieurs [problèmes](https://github.com/aspnet/Announcements/issues/318) avec l’authentification, la plupart des utilisations de SameSite ont été [désactivées](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="49948-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="49948-174">Les [correctifs](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) ont été émis en novembre 2019 pour être mis à jour de la norme 2016 vers la norme 2019.</span><span class="sxs-lookup"><span data-stu-id="49948-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="49948-175">Le [brouillon 2019 de la spécification SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="49948-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="49948-176">N’est **pas** à compatibilité descendante avec le brouillon 2016.</span><span class="sxs-lookup"><span data-stu-id="49948-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="49948-177">Pour plus d’informations, consultez [prise en charge des navigateurs plus anciens](#sob) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="49948-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="49948-178">Spécifie que `SameSite=Lax` les cookies sont traités par défaut.</span><span class="sxs-lookup"><span data-stu-id="49948-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="49948-179">Spécifie les cookies qui `SameSite=None` déclarent explicitement afin d’activer la remise entre sites qui doit `Secure`être marquée comme.</span><span class="sxs-lookup"><span data-stu-id="49948-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="49948-180">`None`nouvelle entrée à refuser.</span><span class="sxs-lookup"><span data-stu-id="49948-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="49948-181">Est pris en charge par les correctifs émis pour ASP.NET Core 2,1, 2,2 et 3,0.</span><span class="sxs-lookup"><span data-stu-id="49948-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="49948-182">ASP.NET Core 3,1 dispose d’une prise en charge supplémentaire de SameSite.</span><span class="sxs-lookup"><span data-stu-id="49948-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="49948-183">Est planifié pour être activé par [chrome](https://chromestatus.com/feature/5088147346030592) par défaut au [2020 février](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="49948-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="49948-184">Les navigateurs ont commencé à passer à cette norme dans 2019.</span><span class="sxs-lookup"><span data-stu-id="49948-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="49948-185">Les API affectées par la modification de la norme préliminaire 2016 SameSite à la norme de projet 2019</span><span class="sxs-lookup"><span data-stu-id="49948-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="49948-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="49948-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="49948-187">CookieOptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="49948-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="49948-188">CookieBuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="49948-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="49948-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="49948-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="49948-190">Prise en charge des navigateurs plus anciens</span><span class="sxs-lookup"><span data-stu-id="49948-190">Supporting older browsers</span></span>

<span data-ttu-id="49948-191">La norme 2016 SameSite stipule que les valeurs inconnues doivent `SameSite=Strict` être traitées en tant que valeurs.</span><span class="sxs-lookup"><span data-stu-id="49948-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="49948-192">Les applications accessibles depuis des navigateurs plus anciens qui prennent en charge la norme 2016 SameSite peuvent s’arrêter lorsqu’ils obtiennent une `None`propriété SameSite avec la valeur.</span><span class="sxs-lookup"><span data-stu-id="49948-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="49948-193">Les applications Web doivent implémenter la détection du navigateur si elles envisagent de prendre en charge des navigateurs plus anciens.</span><span class="sxs-lookup"><span data-stu-id="49948-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="49948-194">ASP.NET Core n’implémente pas la détection du navigateur, car les valeurs des agents utilisateur sont très volatiles et changent fréquemment.</span><span class="sxs-lookup"><span data-stu-id="49948-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="49948-195">Un point d’extension <xref:Microsoft.AspNetCore.CookiePolicy> dans permet de brancher une logique spécifique à l’agent utilisateur.</span><span class="sxs-lookup"><span data-stu-id="49948-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="49948-196">Dans `Startup.Configure`, ajoutez le code qui <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> appelle avant <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> d’appeler ou toute méthode qui écrit *des* cookies :</span><span class="sxs-lookup"><span data-stu-id="49948-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="49948-197">Dans `Startup.ConfigureServices`, ajoutez un code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="49948-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="49948-198">Dans l’exemple précédent, `MyUserAgentDetectionLib.DisallowsSameSiteNone` est une bibliothèque fournie par l’utilisateur qui détecte si l’agent utilisateur ne prend `None`pas en charge SameSite :</span><span class="sxs-lookup"><span data-stu-id="49948-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="49948-199">Le code suivant illustre un exemple `DisallowsSameSiteNone` de méthode :</span><span class="sxs-lookup"><span data-stu-id="49948-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="49948-200">Le code suivant est uniquement à des fins de démonstration :</span><span class="sxs-lookup"><span data-stu-id="49948-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="49948-201">Elle ne doit pas être considérée comme terminée.</span><span class="sxs-lookup"><span data-stu-id="49948-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="49948-202">Elle n’est pas gérée ni prise en charge.</span><span class="sxs-lookup"><span data-stu-id="49948-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="49948-203">Tester des applications pour les problèmes SameSite</span><span class="sxs-lookup"><span data-stu-id="49948-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="49948-204">Les applications qui interagissent avec des sites distants, par exemple via une connexion tierce, doivent :</span><span class="sxs-lookup"><span data-stu-id="49948-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="49948-205">Testez l’interaction sur plusieurs navigateurs.</span><span class="sxs-lookup"><span data-stu-id="49948-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="49948-206">Appliquez la [détection et l’atténuation du navigateur CookiePolicy](#sob) présentées dans ce document.</span><span class="sxs-lookup"><span data-stu-id="49948-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="49948-207">Testez les applications Web à l’aide d’une version du client qui peut s’abonner au nouveau comportement SameSite.</span><span class="sxs-lookup"><span data-stu-id="49948-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="49948-208">Chrome, Firefox et chrome Edge ont tous des indicateurs de fonctionnalités d’abonnement qui peuvent être utilisés à des fins de test.</span><span class="sxs-lookup"><span data-stu-id="49948-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="49948-209">Une fois que votre application applique les correctifs SameSite, testez-la avec des versions client plus anciennes, en particulier Safari.</span><span class="sxs-lookup"><span data-stu-id="49948-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="49948-210">Pour plus d’informations, consultez [prise en charge des navigateurs plus anciens](#sob) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="49948-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="49948-211">Tester avec chrome</span><span class="sxs-lookup"><span data-stu-id="49948-211">Test with Chrome</span></span>

<span data-ttu-id="49948-212">Chrome 78 + donne des résultats trompeurs, car une atténuation temporaire est en place.</span><span class="sxs-lookup"><span data-stu-id="49948-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="49948-213">Le chrome 78 + atténuation temporaire autorise les cookies datant de moins de deux minutes.</span><span class="sxs-lookup"><span data-stu-id="49948-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="49948-214">Le chrome 76 ou 77 avec les indicateurs de test appropriés activés fournit des résultats plus précis.</span><span class="sxs-lookup"><span data-stu-id="49948-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="49948-215">Pour tester le nouveau comportement de SameSite, `chrome://flags/#same-site-by-default-cookies` basculez vers **activé**.</span><span class="sxs-lookup"><span data-stu-id="49948-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="49948-216">Les anciennes versions de chrome (75 et versions antérieures) sont signalées pour échouer avec le nouveau `None` paramètre.</span><span class="sxs-lookup"><span data-stu-id="49948-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="49948-217">Consultez [prise en charge des navigateurs plus anciens](#sob) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="49948-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="49948-218">Google ne rend pas les versions de chrome plus anciennes disponibles.</span><span class="sxs-lookup"><span data-stu-id="49948-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="49948-219">Suivez les instructions de [Télécharger chrome](https://www.chromium.org/getting-involved/download-chromium) pour tester les anciennes versions de chrome.</span><span class="sxs-lookup"><span data-stu-id="49948-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="49948-220">Ne téléchargez **pas** chrome à partir des liens fournis en recherchant les anciennes versions de chrome.</span><span class="sxs-lookup"><span data-stu-id="49948-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="49948-221">Chrome 76 Win64</span><span class="sxs-lookup"><span data-stu-id="49948-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="49948-222">Chrome 74 Win64</span><span class="sxs-lookup"><span data-stu-id="49948-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="49948-223">À partir de la `80.0.3975.0`version de la version de la version des Canaries, vous pouvez désactiver la réduction des risques de `--enable-features=SameSiteDefaultChecksMethodRigorously` type Lax + postal à des fins de test à l’aide du nouvel indicateur pour permettre le test des sites et des services dans l’état final éventuel de la fonctionnalité dans laquelle l’atténuation a été supprimée.</span><span class="sxs-lookup"><span data-stu-id="49948-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="49948-224">Pour plus d’informations, consultez [mises à jour](https://www.chromium.org/updates/same-site) des projets de chrome SameSite</span><span class="sxs-lookup"><span data-stu-id="49948-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="49948-225">Tester avec Safari</span><span class="sxs-lookup"><span data-stu-id="49948-225">Test with Safari</span></span>

<span data-ttu-id="49948-226">Safari 12 implémentait strictement le brouillon précédent et échoue lorsque la `None` nouvelle valeur est dans un cookie.</span><span class="sxs-lookup"><span data-stu-id="49948-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="49948-227">`None`est évité par le biais du code de détection du navigateur [prenant en charge les anciens navigateurs](#sob) dans ce document.</span><span class="sxs-lookup"><span data-stu-id="49948-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="49948-228">Testez les connexions de style du système d’exploitation Safari 12, Safari 13 et WebKit à l’aide de MSAL, ADAL ou toute bibliothèque que vous utilisez.</span><span class="sxs-lookup"><span data-stu-id="49948-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="49948-229">Le problème dépend de la version du système d’exploitation sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="49948-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="49948-230">OSX Mojave (10,14) et iOS 12 sont connus pour avoir des problèmes de compatibilité avec le nouveau comportement de SameSite.</span><span class="sxs-lookup"><span data-stu-id="49948-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="49948-231">La mise à niveau du système d’exploitation vers OSX Catalina (10,15) ou iOS 13 résout le problème.</span><span class="sxs-lookup"><span data-stu-id="49948-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="49948-232">Safari ne dispose pas actuellement d’un indicateur d’abonnement pour tester le nouveau comportement des spécifications.</span><span class="sxs-lookup"><span data-stu-id="49948-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="49948-233">Test avec Firefox</span><span class="sxs-lookup"><span data-stu-id="49948-233">Test with Firefox</span></span>

<span data-ttu-id="49948-234">La prise en charge de Firefox pour la nouvelle norme peut être testée sur la version 68 `about:config` + en acceptant sur `network.cookie.sameSite.laxByDefault`la page avec l’indicateur de fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="49948-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="49948-235">Il n’y a eu aucun rapport sur les problèmes de compatibilité avec les versions antérieures de Firefox.</span><span class="sxs-lookup"><span data-stu-id="49948-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="49948-236">Tester avec le navigateur Edge</span><span class="sxs-lookup"><span data-stu-id="49948-236">Test with Edge browser</span></span>

<span data-ttu-id="49948-237">Edge prend en charge l’ancien standard SameSite.</span><span class="sxs-lookup"><span data-stu-id="49948-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="49948-238">Edge version 44 ne présente aucun problème de compatibilité connu avec la nouvelle norme.</span><span class="sxs-lookup"><span data-stu-id="49948-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="49948-239">Test avec Edge (chrome)</span><span class="sxs-lookup"><span data-stu-id="49948-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="49948-240">Les indicateurs SameSite sont définis sur `edge://flags/#same-site-by-default-cookies` la page.</span><span class="sxs-lookup"><span data-stu-id="49948-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="49948-241">Aucun problème de compatibilité n’a été découvert avec le chrome Edge.</span><span class="sxs-lookup"><span data-stu-id="49948-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="49948-242">Test avec électron</span><span class="sxs-lookup"><span data-stu-id="49948-242">Test with Electron</span></span>

<span data-ttu-id="49948-243">Les versions d’électrons incluent des versions plus anciennes de chrome.</span><span class="sxs-lookup"><span data-stu-id="49948-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="49948-244">Par exemple, la version de l’électron utilisée par les équipes est le chrome 66, qui présente le comportement plus ancien.</span><span class="sxs-lookup"><span data-stu-id="49948-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="49948-245">Vous devez effectuer vos propres tests de compatibilité avec la version d’électron que votre produit utilise.</span><span class="sxs-lookup"><span data-stu-id="49948-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="49948-246">Consultez [prise en charge des navigateurs plus anciens](#sob) dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="49948-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49948-247">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="49948-247">Additional resources</span></span>

* [<span data-ttu-id="49948-248">Blog du chrome : développeurs : Préparez-vous à la nouvelle SameSite = None ; Sécuriser les paramètres de cookie</span><span class="sxs-lookup"><span data-stu-id="49948-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="49948-249">Explication des cookies SameSite</span><span class="sxs-lookup"><span data-stu-id="49948-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="49948-250">Correctifs de novembre 2019</span><span class="sxs-lookup"><span data-stu-id="49948-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="49948-251">Exemple</span><span class="sxs-lookup"><span data-stu-id="49948-251">Sample</span></span>               | <span data-ttu-id="49948-252">Document</span><span class="sxs-lookup"><span data-stu-id="49948-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="49948-253">MVC .NET Core</span><span class="sxs-lookup"><span data-stu-id="49948-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="49948-254">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="49948-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="49948-255">Exemple</span><span class="sxs-lookup"><span data-stu-id="49948-255">Sample</span></span>               | <span data-ttu-id="49948-256">Document</span><span class="sxs-lookup"><span data-stu-id="49948-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="49948-257">[Pages .NET Razor Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="49948-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
