---
title: Appliquer une stratégie de sécurité de contenu pour ASP.NET CoreBlazor
author: guardrex
description: Découvrez comment utiliser une stratégie de sécurité de contenu (CSP) avec des Blazor applications de ASP.net Core pour vous protéger contre les attaques de script entre sites (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 5c53ac64d3ae1b365b40c519eb119f913d58cad1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402440"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="9dde8-103">Appliquer une stratégie de sécurité de contenu pour ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="9dde8-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="9dde8-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9dde8-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9dde8-105">L’exécution de scripts [entre sites (XSS)](xref:security/cross-site-scripting) est une faille de sécurité dans laquelle un attaquant place un ou plusieurs scripts malveillants côté client dans le contenu rendu d’une application.</span><span class="sxs-lookup"><span data-stu-id="9dde8-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="9dde8-106">Une stratégie de sécurité de contenu (CSP) vous aide à vous protéger contre les attaques XSS en informant le navigateur de la validité :</span><span class="sxs-lookup"><span data-stu-id="9dde8-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="9dde8-107">Sources pour le contenu chargé, y compris les scripts, les feuilles de style et les images.</span><span class="sxs-lookup"><span data-stu-id="9dde8-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="9dde8-108">Actions effectuées par une page, en spécifiant des cibles d’URL autorisées de formulaires.</span><span class="sxs-lookup"><span data-stu-id="9dde8-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="9dde8-109">Plug-ins qui peuvent être chargés.</span><span class="sxs-lookup"><span data-stu-id="9dde8-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="9dde8-110">Pour appliquer un CSP à une application, le développeur spécifie plusieurs *directives* de sécurité de contenu CSP dans un ou plusieurs `Content-Security-Policy` en-têtes ou `<meta>` balises.</span><span class="sxs-lookup"><span data-stu-id="9dde8-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="9dde8-111">Les stratégies sont évaluées par le navigateur pendant le chargement d’une page.</span><span class="sxs-lookup"><span data-stu-id="9dde8-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="9dde8-112">Le navigateur inspecte les sources de la page et détermine si elles répondent aux exigences des directives de sécurité du contenu.</span><span class="sxs-lookup"><span data-stu-id="9dde8-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="9dde8-113">Lorsque les directives de stratégie ne sont pas respectées pour une ressource, le navigateur ne charge pas la ressource.</span><span class="sxs-lookup"><span data-stu-id="9dde8-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="9dde8-114">Par exemple, considérez une stratégie qui n’autorise pas les scripts tiers.</span><span class="sxs-lookup"><span data-stu-id="9dde8-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="9dde8-115">Quand une page contient une `<script>` balise avec une origine tierce dans l' `src` attribut, le navigateur empêche le chargement du script.</span><span class="sxs-lookup"><span data-stu-id="9dde8-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="9dde8-116">CSP est pris en charge dans la plupart des navigateurs mobiles et de bureau modernes, notamment chrome, Edge, Firefox, Opera et Safari.</span><span class="sxs-lookup"><span data-stu-id="9dde8-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="9dde8-117">CSP est recommandé pour les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="9dde8-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="9dde8-118">Directives de stratégie</span><span class="sxs-lookup"><span data-stu-id="9dde8-118">Policy directives</span></span>

<span data-ttu-id="9dde8-119">Au minimum, spécifiez les directives et les sources suivantes pour les Blazor applications.</span><span class="sxs-lookup"><span data-stu-id="9dde8-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="9dde8-120">Ajoutez des directives et des sources supplémentaires selon vos besoins.</span><span class="sxs-lookup"><span data-stu-id="9dde8-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="9dde8-121">Les directives suivantes sont utilisées dans la section [appliquer la stratégie](#apply-the-policy) de cet article, où des exemples de stratégies de sécurité pour Blazor WebAssembly et Blazor Server sont fournis :</span><span class="sxs-lookup"><span data-stu-id="9dde8-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="9dde8-122">[base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): restreint les URL pour la balise d’une page `<base>` .</span><span class="sxs-lookup"><span data-stu-id="9dde8-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="9dde8-123">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="9dde8-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="9dde8-124">[bloc-tout-contenu mixte](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): empêche le chargement de contenu HTTP et HTTPS mixte.</span><span class="sxs-lookup"><span data-stu-id="9dde8-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="9dde8-125">[default-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): indique une solution de secours pour les directives source qui ne sont pas explicitement spécifiées par la stratégie.</span><span class="sxs-lookup"><span data-stu-id="9dde8-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="9dde8-126">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="9dde8-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="9dde8-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): indique des sources valides pour les images.</span><span class="sxs-lookup"><span data-stu-id="9dde8-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="9dde8-128">Spécifiez `data:` pour autoriser le chargement d’images à partir d' `data:` URL.</span><span class="sxs-lookup"><span data-stu-id="9dde8-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="9dde8-129">Spécifiez `https:` pour autoriser le chargement d’images à partir de points de terminaison HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9dde8-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="9dde8-130">[Object-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): indique des sources valides pour les `<object>` `<embed>` `<applet>` balises, et.</span><span class="sxs-lookup"><span data-stu-id="9dde8-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="9dde8-131">Spécifiez `none` pour empêcher toutes les sources d’URL.</span><span class="sxs-lookup"><span data-stu-id="9dde8-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="9dde8-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): indique des sources valides pour les scripts.</span><span class="sxs-lookup"><span data-stu-id="9dde8-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="9dde8-133">Spécifiez la `https://stackpath.bootstrapcdn.com/` source de l’hôte pour les scripts de démarrage.</span><span class="sxs-lookup"><span data-stu-id="9dde8-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="9dde8-134">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="9dde8-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="9dde8-135">Dans une Blazor WebAssembly application :</span><span class="sxs-lookup"><span data-stu-id="9dde8-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="9dde8-136">Spécifiez les hachages suivants pour permettre le Blazor WebAssembly chargement des scripts Inline requis :</span><span class="sxs-lookup"><span data-stu-id="9dde8-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="9dde8-137">Spécifiez `unsafe-eval` pour utiliser `eval()` les méthodes et afin de créer du code à partir de chaînes.</span><span class="sxs-lookup"><span data-stu-id="9dde8-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="9dde8-138">Dans une Blazor Server application, spécifiez le `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hachage du script inline qui effectue une détection de secours pour les feuilles de style.</span><span class="sxs-lookup"><span data-stu-id="9dde8-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="9dde8-139">[style-SRC](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): indique des sources valides pour les feuilles de style.</span><span class="sxs-lookup"><span data-stu-id="9dde8-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="9dde8-140">Spécifiez la `https://stackpath.bootstrapcdn.com/` source de l’hôte pour les feuilles de style de démarrage.</span><span class="sxs-lookup"><span data-stu-id="9dde8-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="9dde8-141">Spécifiez `self` pour indiquer que l’origine de l’application, y compris le schéma et le numéro de port, est une source valide.</span><span class="sxs-lookup"><span data-stu-id="9dde8-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="9dde8-142">Spécifiez `unsafe-inline` pour autoriser l’utilisation de styles intralignes.</span><span class="sxs-lookup"><span data-stu-id="9dde8-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="9dde8-143">La déclaration inline est requise pour l’interface utilisateur dans les Blazor Server applications pour la reconnexion du client et du serveur après la demande initiale.</span><span class="sxs-lookup"><span data-stu-id="9dde8-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="9dde8-144">Dans une version ultérieure, le style intraligne peut être supprimé afin qu' `unsafe-inline` il ne soit plus nécessaire.</span><span class="sxs-lookup"><span data-stu-id="9dde8-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="9dde8-145">[Upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): indique que les URL de contenu provenant de sources non sécurisées (http) doivent être acquises en toute sécurité via HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9dde8-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="9dde8-146">Les directives précédentes sont prises en charge par tous les navigateurs, à l’exception de Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="9dde8-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="9dde8-147">Pour obtenir des hachages SHA pour d’autres scripts inline :</span><span class="sxs-lookup"><span data-stu-id="9dde8-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="9dde8-148">Appliquez le CSP présenté dans la section [appliquer la stratégie](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="9dde8-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="9dde8-149">Accédez à la console outils de développement du navigateur tout en exécutant l’application localement.</span><span class="sxs-lookup"><span data-stu-id="9dde8-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="9dde8-150">Le navigateur calcule et affiche les hachages pour les scripts bloqués lorsqu’un en-tête ou une `meta` balise CSP est présent.</span><span class="sxs-lookup"><span data-stu-id="9dde8-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="9dde8-151">Copiez les hachages fournis par le navigateur dans les `script-src` sources.</span><span class="sxs-lookup"><span data-stu-id="9dde8-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="9dde8-152">Utilisez des guillemets simples autour de chaque hachage.</span><span class="sxs-lookup"><span data-stu-id="9dde8-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="9dde8-153">Pour obtenir une matrice de prise en charge des navigateurs de niveau 2 de stratégie de sécurité de contenu, consultez la page [comment utiliser le niveau de stratégie de sécurité de contenu 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="9dde8-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="9dde8-154">Application de la stratégie</span><span class="sxs-lookup"><span data-stu-id="9dde8-154">Apply the policy</span></span>

<span data-ttu-id="9dde8-155">Utilisez une `<meta>` balise pour appliquer la stratégie :</span><span class="sxs-lookup"><span data-stu-id="9dde8-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="9dde8-156">Affectez `http-equiv` à l’attribut la valeur `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="9dde8-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="9dde8-157">Placez les directives dans la `content` valeur de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="9dde8-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="9dde8-158">Séparez les directives par un point-virgule ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="9dde8-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="9dde8-159">Placez toujours la `meta` balise dans le `<head>` contenu.</span><span class="sxs-lookup"><span data-stu-id="9dde8-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="9dde8-160">Les sections suivantes présentent des exemples de stratégies pour Blazor WebAssembly et Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="9dde8-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="9dde8-161">Ces exemples sont associés à des versions de cet article pour chaque version de Blazor .</span><span class="sxs-lookup"><span data-stu-id="9dde8-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="9dde8-162">Pour utiliser une version appropriée pour votre version, sélectionnez la version du document avec le sélecteur de **version** déroulante sur cette page Web.</span><span class="sxs-lookup"><span data-stu-id="9dde8-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="9dde8-163">Dans la `<head>` page contenu de l' `wwwroot/index.html` hôte, appliquez les directives décrites dans la section [directives de stratégie](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="9dde8-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### Blazor Server

<span data-ttu-id="9dde8-164">Dans la `<head>` page contenu de l' `Pages/_Host.cshtml` hôte, appliquez les directives décrites dans la section [directives de stratégie](#policy-directives) :</span><span class="sxs-lookup"><span data-stu-id="9dde8-164">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="9dde8-165">Limitations des balises meta</span><span class="sxs-lookup"><span data-stu-id="9dde8-165">Meta tag limitations</span></span>

<span data-ttu-id="9dde8-166">Une `<meta>` stratégie de balise ne prend pas en charge les directives suivantes :</span><span class="sxs-lookup"><span data-stu-id="9dde8-166">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="9dde8-167">ancêtres de cadre</span><span class="sxs-lookup"><span data-stu-id="9dde8-167">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="9dde8-168">rapport-à</span><span class="sxs-lookup"><span data-stu-id="9dde8-168">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="9dde8-169">rapport-URI</span><span class="sxs-lookup"><span data-stu-id="9dde8-169">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="9dde8-170">immédiatement</span><span class="sxs-lookup"><span data-stu-id="9dde8-170">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="9dde8-171">Pour prendre en charge les directives précédentes, utilisez un en-tête nommé `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="9dde8-171">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="9dde8-172">La chaîne de directive est la valeur de l’en-tête.</span><span class="sxs-lookup"><span data-stu-id="9dde8-172">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="9dde8-173">Tester une stratégie et recevoir des rapports de violation</span><span class="sxs-lookup"><span data-stu-id="9dde8-173">Test a policy and receive violation reports</span></span>

<span data-ttu-id="9dde8-174">Le test permet de vérifier que les scripts tiers ne sont pas bloqués par inadvertance lors de la création d’une stratégie initiale.</span><span class="sxs-lookup"><span data-stu-id="9dde8-174">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="9dde8-175">Pour tester une stratégie sur une période sans appliquer les directives de stratégie, définissez le `<meta>` `http-equiv` nom d’en-tête ou d’attribut de la balise d’une stratégie basée sur un en-tête sur `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="9dde8-175">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="9dde8-176">Les rapports d’échec sont envoyés en tant que documents JSON à une URL spécifiée.</span><span class="sxs-lookup"><span data-stu-id="9dde8-176">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="9dde8-177">Pour plus d’informations, consultez la [page documentation Web MDN : contenu-sécurité-stratégie-rapport uniquement](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="9dde8-177">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="9dde8-178">Pour la création de rapports sur les violations pendant qu’une stratégie est active, consultez les articles suivants :</span><span class="sxs-lookup"><span data-stu-id="9dde8-178">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="9dde8-179">rapport-à</span><span class="sxs-lookup"><span data-stu-id="9dde8-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="9dde8-180">rapport-URI</span><span class="sxs-lookup"><span data-stu-id="9dde8-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="9dde8-181">Bien que ne `report-uri` soit plus recommandé pour une utilisation, les deux directives doivent être utilisées jusqu’à ce que `report-to` soit pris en charge par tous les principaux navigateurs.</span><span class="sxs-lookup"><span data-stu-id="9dde8-181">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="9dde8-182">N’utilisez pas exclusivement `report-uri` , car la prise en charge de `report-uri` est sujette à être supprimée à *tout moment* à partir des navigateurs.</span><span class="sxs-lookup"><span data-stu-id="9dde8-182">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="9dde8-183">Supprimez la prise en charge de `report-uri` dans vos stratégies lorsque `report-to` est entièrement pris en charge.</span><span class="sxs-lookup"><span data-stu-id="9dde8-183">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="9dde8-184">Pour suivre l’adoption de `report-to` , consultez [puis-je utiliser : Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="9dde8-184">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="9dde8-185">Testez et mettez à jour la stratégie d’une application chaque version.</span><span class="sxs-lookup"><span data-stu-id="9dde8-185">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="9dde8-186">Dépanner</span><span class="sxs-lookup"><span data-stu-id="9dde8-186">Troubleshoot</span></span>

* <span data-ttu-id="9dde8-187">Les erreurs s’affichent dans la console outils de développement du navigateur.</span><span class="sxs-lookup"><span data-stu-id="9dde8-187">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="9dde8-188">Les navigateurs fournissent des informations sur les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="9dde8-188">Browsers provide information about:</span></span>
  * <span data-ttu-id="9dde8-189">Éléments qui ne sont pas conformes à la stratégie.</span><span class="sxs-lookup"><span data-stu-id="9dde8-189">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="9dde8-190">Comment modifier la stratégie pour autoriser un élément bloqué.</span><span class="sxs-lookup"><span data-stu-id="9dde8-190">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="9dde8-191">Une stratégie est entièrement efficace lorsque le navigateur du client prend en charge toutes les directives incluses.</span><span class="sxs-lookup"><span data-stu-id="9dde8-191">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="9dde8-192">Pour obtenir une matrice de prise en charge actuelle du navigateur, consultez Comment [utiliser : Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="9dde8-192">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9dde8-193">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9dde8-193">Additional resources</span></span>

* [<span data-ttu-id="9dde8-194">MDN Web docs : Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="9dde8-194">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="9dde8-195">Niveau de stratégie de sécurité du contenu 2</span><span class="sxs-lookup"><span data-stu-id="9dde8-195">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="9dde8-196">Évaluateur Google CSP</span><span class="sxs-lookup"><span data-stu-id="9dde8-196">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
