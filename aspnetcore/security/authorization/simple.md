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
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core での単純な承認

<a name="security-authorization-simple"></a>

MVC での承認は、`AuthorizeAttribute` 属性とそのさまざまなパラメーターによって制御されます。 単純に、`AuthorizeAttribute` 属性をコントローラーまたはアクションに適用すると、認証されたユーザーに対するコントローラーまたはアクションへのアクセスが制限されます。

たとえば、次のコードは、認証されたユーザーへの `AccountController` へのアクセスを制限します。

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

コントローラーではなくアクションに承認を適用する場合は、`AuthorizeAttribute` 属性をアクション自体に適用します。

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

これで、認証されたユーザーのみが `Logout` 関数にアクセスできるようになりました。

また、`AllowAnonymous` 属性を使用して、認証されていないユーザーによる個々のアクションへのアクセスを許可することもできます。 例 :

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

これにより、認証されたユーザーまたは認証されていない/匿名の状態に関係なく、すべてのユーザーがアクセスできる `Login` アクションを除き、`AccountController`に対して認証されたユーザーのみが許可されます。

> [!WARNING]
> `[AllowAnonymous]` は、すべての承認ステートメントをバイパスします。 `[AllowAnonymous]` と任意の `[Authorize]` 属性を組み合わせると、`[Authorize]` 属性は無視されます。 たとえば、コントローラーレベルで `[AllowAnonymous]` を適用した場合、同じコントローラー (またはその中のアクション) のすべての `[Authorize]` 属性は無視されます。
