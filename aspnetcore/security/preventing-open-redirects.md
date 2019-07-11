---
title: ASP.NET Core でのオープン リダイレクト攻撃を防止します。
author: ardalis
description: ASP.NET Core アプリに対するオープン リダイレクト攻撃を防ぐ方法を示しています。
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815497"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>ASP.NET Core でのオープン リダイレクト攻撃を防止します。

悪意のある、外部 URL にユーザーをリダイレクトする、クエリ文字列またはフォーム データなどの要求で指定されている URL にリダイレクトする web アプリできます改ざんされる可能性があります。 この改ざんは、オープン リダイレクト攻撃と呼ばれます。

アプリケーション ロジックは、指定された URL にリダイレクトされるたびに、リダイレクト URL 改ざんされていないことを確認する必要があります。 ASP.NET Core では、オープン リダイレクト (とも呼ばれるオープン リダイレクト) 攻撃からアプリを保護するための組み込みの機能を持ちます。

## <a name="what-is-an-open-redirect-attack"></a>オープン リダイレクト攻撃とは何ですか。

Web アプリケーションは、認証を必要とするリソースにアクセスするときに頻繁にユーザーとログイン ページにリダイレクトします。 通常は、リダイレクト、 `returnUrl` querystring パラメーターのユーザーが正常にログインした後、ユーザーが最初に要求された URL に返されるようにします。 ユーザーが認証された後は、最初に要求した URL にリダイレクトされます。

リンク先の URL が指定されて、要求のクエリ文字列で、クエリ文字列を悪意のあるユーザーが改ざんする可能性です。 改ざんされたクエリ文字列には、外部の悪意のあるサイトにユーザーをリダイレクトするサイトを許可できます。 この手法は、オープン リダイレクト (またはリダイレクト) 攻撃と呼ばれます。

### <a name="an-example-attack"></a>攻撃の例

悪意のあるユーザーは、攻撃を受けるユーザーの資格情報または機密情報に悪意のあるユーザーのアクセスを許可するためのものを開発できます。 悪意のあるユーザーがサイトのログイン ページへのリンクをクリックするユーザーを仕向け、攻撃を開始する、`returnUrl`クエリ文字列値を URL に追加します。 たとえば、アプリで`contoso.com`でログイン ページを含む`http://contoso.com/Account/LogOn?returnUrl=/Home/About`します。 攻撃では、次の手順に従います。

1. ユーザーへの悪意のあるリンクをクリックした`http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn`(2 番目の URL は"contoso**1**.com"、"contoso.com")。
2. ユーザーが正常にログインします。
3. ユーザーが (サイト) をリダイレクトする`http://contoso1.com/Account/LogOn`(次の実際のサイトとまったく同じような悪意のあるサイト)。
4. ユーザーがもう一度ログイン (自分の資格情報付与悪意のあるサイト) とは、実際のサイトにリダイレクトされます。

可能性の高いユーザーは、ログインに、最初の試行が失敗したことと、2 回目の試行が成功したことと考えています。 ほとんどの場合、ユーザーが自分の資格情報の侵害に関係なくままです。

![オープン リダイレクト攻撃のプロセス](preventing-open-redirects/_static/open-redirection-attack-process.png)

一部のサイトは、ログイン ページだけでなく、リダイレクト ページまたはエンドポイントを提供します。 アプリのオープン リダイレクトでは、ページが imagine`/Home/Redirect`します。 攻撃者を作成して、たとえばに移動する電子メールのリンク`[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`します。 一般的なユーザーは URL を確認し、サイト名で始まるを参照してください。 信頼する、リンクがクリックしてされます。 オープン リダイレクトは、ユーザーは、自分と同じ、フィッシング詐欺サイトに送信し、ユーザーがそうなログインが信じられませんが、サイト。

## <a name="protecting-against-open-redirect-attacks"></a>オープン リダイレクト攻撃から保護します。

Web アプリケーションを開発する場合は、信頼できないとしてすべてのユーザーが入力したデータを処理します。 アプリケーションに、URL の内容に基づくユーザーをリダイレクトする機能がある場合は、このようなリダイレクトがのみ行われることローカルで、アプリ内で (または、既知の url、クエリ文字列で指定することがありますいない任意の URL) を確認します。

### <a name="localredirect"></a>LocalRedirect

使用して、`LocalRedirect`ベースからヘルパー メソッド`Controller`クラス。

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` 非ローカル URL が指定されている場合、例外がスローされます。 それ以外の場合、動作と同じように、`Redirect`メソッド。

### <a name="islocalurl"></a>IsLocalUrl

使用して、 [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)をリダイレクトする前に Url をテストするメソッド。

次の例では、リダイレクトする前に、URL がローカルかどうかを確認する方法を示します。

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

`IsLocalUrl`メソッドは、悪意のあるサイトにリダイレクトされる誤ってからユーザーを保護します。 ローカルの URL を意図したような状況で非ローカル URL が指定されている場合に提供された URL の詳細を記録できます。 ログイン リダイレクト Url がリダイレクト攻撃の診断に役立ちます。
