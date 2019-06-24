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
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core での単純な承認

<a name="security-authorization-simple"></a>

MVC の承認は、`AuthorizeAttribute` 属性とそのさまざまなパラメーターによって制御されます。簡単にいうと、`AuthorizeAttribute` をコントローラーまたはアクションに適用することで、コントローラーまたはアクションへアクセスを認証済みのユーザーのみに制限できます。

例えば、次のコードは `AccountController` へのアクセスを認証済みのユーザーのみに制限します。

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

承認を、コントローラーではなくアクションに適用したい場合は、アクション自体に `AuthorizeAttribute` 属性を適用します。

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

これで `Logout` 関数には、認証済みのユーザーのみがアクセスできます。

`AllowAnonymous` 属性を使うことで、個別のアクションに対して未認証のユーザーのアクセスを許可することもできます。例:

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

これで、認証済みのユーザーのみが `AccountController` にアクセスできますが、`Login` アクションだけは、認証済みまたは未認証/匿名に関わらずアクセスが可能となります。

> [!WARNING]
> `[AllowAnonymous]` 全ての承認ステートメントをバイパスします。 `[AllowAnonymous]` と `[Authorize]` 属性を組み合わせた場合、`[Authorize]` 属性は無視されます。例として、`[AllowAnonymous]` をコントローラーに適用すると、そのコントローラー（またはすべてのアクション）にあるすべての `[Authorize]` は無視されます。
