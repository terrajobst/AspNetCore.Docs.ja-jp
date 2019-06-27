---
title: ASP.NET Core での単純な承認
author: rick-anderson
description: Authorize 属性を使用して ASP.NET Core コント ローラーおよびアクションへのアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897679"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="fb1e4-103">ASP.NET Core での単純な承認</span><span class="sxs-lookup"><span data-stu-id="fb1e4-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="fb1e4-104">MVC の承認は、`AuthorizeAttribute` 属性とそのさまざまなパラメーターによって制御されます。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="fb1e4-105">簡単にいうと、`AuthorizeAttribute` をコントローラーまたはアクションに適用することで、コントローラーまたはアクションへのアクセスを認証済みのユーザーのみに制限できます。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="fb1e4-106">たとえば、次のコードは `AccountController` へのアクセスを認証済みのユーザーのみに制限します。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="fb1e4-107">承認を、コントローラーではなくアクションに適用したい場合は、アクション自体に `AuthorizeAttribute` 属性を適用します。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="fb1e4-108">これで `Logout` 関数には、認証済みのユーザーのみがアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="fb1e4-109">`AllowAnonymous` 属性を使うことで、個別のアクションに対して認証されていないユーザーのアクセスを許可することもできます。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="fb1e4-110">例:</span><span class="sxs-lookup"><span data-stu-id="fb1e4-110">For example:</span></span>

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

<span data-ttu-id="fb1e4-111">これで、認証済みのユーザーのみ `AccountController` にアクセスできますが、`Login` アクションだけは、認証済みまたは非認証/匿名に関わらずアクセス可能となります。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="fb1e4-112">`[AllowAnonymous]` は全ての承認ステートメントをバイパスします。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="fb1e4-113">`[AllowAnonymous]` と `[Authorize]` 属性を組み合わせた場合、`[Authorize]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="fb1e4-114">例として、`[AllowAnonymous]` をコントローラーに適用すると、そのコントローラー (またはすべてのアクション) にあるすべての `[Authorize]` は無視されます。</span><span class="sxs-lookup"><span data-stu-id="fb1e4-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
