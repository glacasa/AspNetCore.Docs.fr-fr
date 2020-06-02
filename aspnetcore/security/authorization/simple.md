---
<span data-ttu-id="b96f2-101">title : autorisation simple dans ASP.NET Core auteur : description de Rick-Anderson : Découvrez comment utiliser l’attribut Authorize pour restreindre l’accès aux contrôleurs et aux actions ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b96f2-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="b96f2-102">ms. Author : Riande ms. Date : 10/14/2016 No-Loc :</span><span class="sxs-lookup"><span data-stu-id="b96f2-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="b96f2-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b96f2-103">'Blazor'</span></span>
- <span data-ttu-id="b96f2-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b96f2-104">'Identity'</span></span>
- <span data-ttu-id="b96f2-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b96f2-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b96f2-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b96f2-106">'Razor'</span></span>
- <span data-ttu-id="b96f2-107">' SignalR 'UID : Security/Authorization/simple</span><span class="sxs-lookup"><span data-stu-id="b96f2-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="b96f2-108">Autorisation simple dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b96f2-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="b96f2-109">L’autorisation dans ASP.NET Core est contrôlée par <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> et ses différents paramètres.</span><span class="sxs-lookup"><span data-stu-id="b96f2-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="b96f2-110">Dans sa forme la plus simple, l’application `[Authorize]` de l’attribut à un contrôleur, une action ou une Razor page limite l’accès à ce composant à n’importe quel utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="b96f2-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="b96f2-111">Par exemple, le code suivant limite l’accès à `AccountController` n’importe quel utilisateur authentifié.</span><span class="sxs-lookup"><span data-stu-id="b96f2-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="b96f2-112">Si vous souhaitez appliquer une autorisation à une action plutôt qu’au contrôleur, appliquez l' `AuthorizeAttribute` attribut à l’action elle-même :</span><span class="sxs-lookup"><span data-stu-id="b96f2-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="b96f2-113">Désormais, seuls les utilisateurs authentifiés peuvent accéder à la `Logout` fonction.</span><span class="sxs-lookup"><span data-stu-id="b96f2-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="b96f2-114">Vous pouvez également utiliser l' `AllowAnonymous` attribut pour autoriser l’accès par des utilisateurs non authentifiés à des actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="b96f2-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="b96f2-115">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="b96f2-115">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="b96f2-116">Cela autorise uniquement les utilisateurs authentifiés à, à l' `AccountController` exception de l' `Login` action, qui est accessible par tout le monde, indépendamment de leur état authentifié ou non authentifié/anonyme.</span><span class="sxs-lookup"><span data-stu-id="b96f2-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="b96f2-117">`[AllowAnonymous]`ignore toutes les instructions d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="b96f2-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="b96f2-118">Si vous combinez `[AllowAnonymous]` et n’importe quel `[Authorize]` attribut, les `[Authorize]` attributs sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="b96f2-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="b96f2-119">Par exemple, si vous appliquez `[AllowAnonymous]` au niveau du contrôleur, tous les `[Authorize]` attributs du même contrôleur (ou de toute action qu’il contient) sont ignorés.</span><span class="sxs-lookup"><span data-stu-id="b96f2-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
