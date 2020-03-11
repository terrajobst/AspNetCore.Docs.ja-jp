---
title: ASP.NET Core での単純な承認
author: rick-anderson
description: 承認属性を使用して、ASP.NET Core コントローラーとアクションへのアクセスを制限する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653558"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="eeda6-103">ASP.NET Core での単純な承認</span><span class="sxs-lookup"><span data-stu-id="eeda6-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="eeda6-104">MVC での承認は、`AuthorizeAttribute` 属性とそのさまざまなパラメーターによって制御されます。</span><span class="sxs-lookup"><span data-stu-id="eeda6-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="eeda6-105">単純に、`AuthorizeAttribute` 属性をコントローラーまたはアクションに適用すると、認証されたユーザーに対するコントローラーまたはアクションへのアクセスが制限されます。</span><span class="sxs-lookup"><span data-stu-id="eeda6-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="eeda6-106">たとえば、次のコードは、認証されたユーザーへの `AccountController` へのアクセスを制限します。</span><span class="sxs-lookup"><span data-stu-id="eeda6-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="eeda6-107">コントローラーではなくアクションに承認を適用する場合は、`AuthorizeAttribute` 属性をアクション自体に適用します。</span><span class="sxs-lookup"><span data-stu-id="eeda6-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="eeda6-108">これで、認証されたユーザーのみが `Logout` 関数にアクセスできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eeda6-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="eeda6-109">また、`AllowAnonymous` 属性を使用して、認証されていないユーザーによる個々のアクションへのアクセスを許可することもできます。</span><span class="sxs-lookup"><span data-stu-id="eeda6-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="eeda6-110">例 :</span><span class="sxs-lookup"><span data-stu-id="eeda6-110">For example:</span></span>

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

<span data-ttu-id="eeda6-111">これにより、認証されたユーザーまたは認証されていない/匿名の状態に関係なく、すべてのユーザーがアクセスできる `Login` アクションを除き、`AccountController`に対して認証されたユーザーのみが許可されます。</span><span class="sxs-lookup"><span data-stu-id="eeda6-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="eeda6-112">`[AllowAnonymous]` は、すべての承認ステートメントをバイパスします。</span><span class="sxs-lookup"><span data-stu-id="eeda6-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="eeda6-113">`[AllowAnonymous]` と任意の `[Authorize]` 属性を組み合わせると、`[Authorize]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="eeda6-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="eeda6-114">たとえば、コントローラーレベルで `[AllowAnonymous]` を適用した場合、同じコントローラー (またはその中のアクション) のすべての `[Authorize]` 属性は無視されます。</span><span class="sxs-lookup"><span data-stu-id="eeda6-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
