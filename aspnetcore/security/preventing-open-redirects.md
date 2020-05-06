---
title: Empêcher les attaques par redirection ouverte dans ASP.NET Core
author: ardalis
description: Montre comment empêcher les attaques de redirection ouvertes contre une application ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: ad4c9806146567b6ef1f5e78eaeca96cb649c1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774390"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="8505b-103">Empêcher les attaques par redirection ouverte dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8505b-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="8505b-104">Une application Web qui effectue une redirection vers une URL spécifiée via la requête telle que la chaîne de requête ou les données de formulaire peut potentiellement être falsifiée pour rediriger les utilisateurs vers une URL externe et malveillante.</span><span class="sxs-lookup"><span data-stu-id="8505b-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="8505b-105">Cette falsification s’appelle une attaque de redirection ouverte.</span><span class="sxs-lookup"><span data-stu-id="8505b-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="8505b-106">Chaque fois que votre logique d’application redirige vers une URL spécifiée, vous devez vérifier que l’URL de redirection n’a pas été falsifiée.</span><span class="sxs-lookup"><span data-stu-id="8505b-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="8505b-107">ASP.NET Core offre des fonctionnalités intégrées permettant de protéger les applications contre les attaques par redirection ouverte (également appelées « redirections ouvertes »).</span><span class="sxs-lookup"><span data-stu-id="8505b-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="8505b-108">Qu’est-ce qu’une attaque de redirection ouverte ?</span><span class="sxs-lookup"><span data-stu-id="8505b-108">What is an open redirect attack?</span></span>

<span data-ttu-id="8505b-109">Les applications Web redirigent fréquemment les utilisateurs vers une page de connexion quand ils accèdent aux ressources qui requièrent une authentification.</span><span class="sxs-lookup"><span data-stu-id="8505b-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="8505b-110">La redirection comprend généralement un `returnUrl` paramètre QueryString afin que l’utilisateur puisse être renvoyé à l’URL demandée à l’origine une fois qu’il a réussi à se connecter.</span><span class="sxs-lookup"><span data-stu-id="8505b-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="8505b-111">Une fois que l’utilisateur s’est authentifié, il est redirigé vers l’URL demandée à l’origine.</span><span class="sxs-lookup"><span data-stu-id="8505b-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="8505b-112">Étant donné que l’URL de destination est spécifiée dans la chaîne de requête de la demande, un utilisateur malveillant peut falsifier la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="8505b-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="8505b-113">Une QueryString falsifiée peut permettre au site de rediriger l’utilisateur vers un site externe malveillant.</span><span class="sxs-lookup"><span data-stu-id="8505b-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="8505b-114">Cette technique s’appelle une attaque de redirection (ou de redirection) ouverte.</span><span class="sxs-lookup"><span data-stu-id="8505b-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="8505b-115">Exemple d’attaque</span><span class="sxs-lookup"><span data-stu-id="8505b-115">An example attack</span></span>

<span data-ttu-id="8505b-116">Un utilisateur malveillant peut développer une attaque destinée à autoriser l’utilisateur malveillant à accéder aux informations d’identification d’un utilisateur ou à des informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="8505b-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="8505b-117">Pour lancer l’attaque, l’utilisateur malveillant persuade l’utilisateur de cliquer sur un lien vers la page de connexion de votre `returnUrl` site avec une valeur QueryString ajoutée à l’URL.</span><span class="sxs-lookup"><span data-stu-id="8505b-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="8505b-118">Par exemple, considérez une application `contoso.com` sur qui comprend une page de `http://contoso.com/Account/LogOn?returnUrl=/Home/About`connexion à l’adresse.</span><span class="sxs-lookup"><span data-stu-id="8505b-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="8505b-119">L’attaque suit les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="8505b-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="8505b-120">L’utilisateur clique sur un lien `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` malveillant vers (la deuxième URL est « contoso**1**. com », et non pas « contoso.com »).</span><span class="sxs-lookup"><span data-stu-id="8505b-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="8505b-121">L’utilisateur se connecte avec succès.</span><span class="sxs-lookup"><span data-stu-id="8505b-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="8505b-122">L’utilisateur est redirigé (par le site) vers ( `http://contoso1.com/Account/LogOn` un site malveillant qui ressemble exactement à un site réel).</span><span class="sxs-lookup"><span data-stu-id="8505b-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="8505b-123">L’utilisateur se reconnecte (ce qui donne des informations d’identification au site malveillant) et est redirigé vers le site réel.</span><span class="sxs-lookup"><span data-stu-id="8505b-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="8505b-124">L’utilisateur pense probablement que sa première tentative de connexion a échoué et que la deuxième tentative est réussie.</span><span class="sxs-lookup"><span data-stu-id="8505b-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="8505b-125">L’utilisateur n’est probablement pas conscient que leurs informations d’identification sont compromises.</span><span class="sxs-lookup"><span data-stu-id="8505b-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Processus d’attaque de redirection ouvert](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="8505b-127">En plus des pages de connexion, certains sites fournissent des pages de redirection ou des points de terminaison.</span><span class="sxs-lookup"><span data-stu-id="8505b-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="8505b-128">Imaginez que votre application comporte une page avec une redirection ouverte `/Home/Redirect`,.</span><span class="sxs-lookup"><span data-stu-id="8505b-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="8505b-129">Un attaquant pourrait créer, par exemple, un lien dans un message électronique qui accède à `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span><span class="sxs-lookup"><span data-stu-id="8505b-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="8505b-130">Un utilisateur classique regardera l’URL pour voir qu’elle commence par le nom de votre site.</span><span class="sxs-lookup"><span data-stu-id="8505b-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="8505b-131">Pour ce faire, ils cliquent sur le lien.</span><span class="sxs-lookup"><span data-stu-id="8505b-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="8505b-132">La redirection ouverte envoie ensuite l’utilisateur au site de hameçonnage, qui ressemble à la vôtre, et l’utilisateur se connecte probablement à ce que votre site estime être.</span><span class="sxs-lookup"><span data-stu-id="8505b-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="8505b-133">Protection contre les attaques par redirection ouverte</span><span class="sxs-lookup"><span data-stu-id="8505b-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="8505b-134">Lors du développement d’applications Web, traitez toutes les données fournies par l’utilisateur comme non fiables.</span><span class="sxs-lookup"><span data-stu-id="8505b-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="8505b-135">Si votre application a une fonctionnalité qui redirige l’utilisateur en fonction du contenu de l’URL, assurez-vous que ces redirections sont effectuées uniquement localement dans votre application (ou sur une URL connue, et non dans une URL qui peut être fournie dans la chaîne de chaîne).</span><span class="sxs-lookup"><span data-stu-id="8505b-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="8505b-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="8505b-136">LocalRedirect</span></span>

<span data-ttu-id="8505b-137">Utilisez la `LocalRedirect` méthode d’assistance de la classe `Controller` de base :</span><span class="sxs-lookup"><span data-stu-id="8505b-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="8505b-138">`LocalRedirect`lèvera une exception si une URL non locale est spécifiée.</span><span class="sxs-lookup"><span data-stu-id="8505b-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="8505b-139">Dans le cas contraire, il se comporte comme `Redirect` la méthode.</span><span class="sxs-lookup"><span data-stu-id="8505b-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="8505b-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="8505b-140">IsLocalUrl</span></span>

<span data-ttu-id="8505b-141">Utilisez la méthode [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) pour tester des URL avant de rediriger :</span><span class="sxs-lookup"><span data-stu-id="8505b-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="8505b-142">L’exemple suivant montre comment vérifier si une URL est locale avant la redirection.</span><span class="sxs-lookup"><span data-stu-id="8505b-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="8505b-143">La `IsLocalUrl` méthode empêche les utilisateurs d’être redirigés par inadvertance vers un site malveillant.</span><span class="sxs-lookup"><span data-stu-id="8505b-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="8505b-144">Vous pouvez consigner les détails de l’URL qui a été fournie quand une URL non locale est fournie dans une situation où vous attendiez une URL locale.</span><span class="sxs-lookup"><span data-stu-id="8505b-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="8505b-145">Les URL de redirection de la journalisation peuvent aider à diagnostiquer les attaques de redirection.</span><span class="sxs-lookup"><span data-stu-id="8505b-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
