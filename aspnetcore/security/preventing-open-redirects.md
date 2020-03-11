---
title: ASP.NET Core でのオープンリダイレクト攻撃の防止
author: ardalis
description: ASP.NET Core アプリに対するオープンリダイレクト攻撃を防ぐ方法を示します。
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652220"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>ASP.NET Core でのオープンリダイレクト攻撃の防止

クエリ文字列やフォームデータのように要求を介して指定された URL にリダイレクトをする Web アプリでは、改ざんされてユーザーを外部の悪意ある URL へリダイレクトする可能性が潜在的にあります。 この改ざんは、オープンリダイレクト攻撃と呼ばれます。

アプリケーションロジックが指定した URL にリダイレクトする度に、リダイレクト URL が改ざんされていないことを確認する必要があります。 ASP.NET Core には、オープンリダイレクト（Open Redirection とも呼ばれます）攻撃からアプリを保護するための機能が組み込まれています。

## <a name="what-is-an-open-redirect-attack"></a>オープンリダイレクト攻撃とは何か

Web アプリは認証が必要なリソースにアクセスするときに、ユーザーをログインページへリダイレクトすることが多いです。 通常、リダイレクトには、ユーザーが正常にログインした後に最初に要求された URL にユーザーを返すことができるように、`returnUrl` querystring パラメーターが含まれています。 ユーザーは認証の成功後、もともと要求された URL にリダイレクトされます。

送信先の URL はクエリ文字列で指定されているので、悪意のあるユーザーはクエリ文字列を改ざんする可能性があります。 改ざんされたクエリ文字列によって、サイトはユーザーを外部の悪意あるサイトにリダイレクトできるようになります。 この手法は、オープンリダイレクト（または open redirection）攻撃と呼ばれます。

### <a name="an-example-attack"></a>攻撃の例

悪意のあるユーザーは、攻撃を受けるユーザーの資格情報や機密情報にアクセスできるような攻撃を開発する可能性があります。 攻撃を開始するために、悪意のあるユーザーは、`returnUrl` querystring 値が URL に追加されたサイトのログインページへのリンクをクリックすることをユーザーに仕向けるます。 たとえば、`http://contoso.com/Account/LogOn?returnUrl=/Home/About`にログインページを含む `contoso.com` のアプリを考えてみます。 攻撃は次のステップです:

1. ユーザーが悪意のあるリンクをクリックして `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` します (2 番目の URL は "contoso.com" ではなく "contoso**1**.com" です)。
2. ユーザーは、正常にログインします。
3. ユーザーは (サイトによって) `http://contoso1.com/Account/LogOn` にリダイレクトされます (実際のサイトとまったく同じように見える悪意のあるサイト)。
4. ユーザーは、再度ログインし（悪意のあるサイトに資格情報を与えて）、実際のサイトへリダイレクトされます。

ほとんどのユーザーは、最初のログインに失敗し2度目で成功したと思うでしょう。 多くのユーザーが、自身の資格情報が侵害されたことに気付かないままでしょう。

![オープンリダイレクト攻撃プロセス](preventing-open-redirects/_static/open-redirection-attack-process.png)

ログインページに加え、リダイレクトのページやエンドポイントを提供しているサイトはいくつもあります。 たとえば、開いているリダイレクトがあるページがアプリにあるとします。 `/Home/Redirect`します。 攻撃者は、たとえば、電子メールのリンクを作成して `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`にすることができます。 多くのユーザーは URL を見て、それがアクセスしたいサイト名で始まっていることを確認します。 それを信頼してリンクをクリックするでしょう。 そして、オープンリダイレクトによってユーザーはフィッシングサイトへ送られ、ユーザーは実際のサイトだと信じてログインするでしょう。

## <a name="protecting-against-open-redirect-attacks"></a>オープンリダイレクト攻撃からの保護

Web アプリを開発するときは、ユーザーが入力したデータは信頼できないものとして扱います。 もし アプリが URL の内容に基づいてユーザーをリダイレクトする機能を持っている場合、そのようなリダイレクトは、アプリのローカルのみ（または、クエリ文字列で提供される URL ではなく、既知の ULR ）で行われるようにします。

### <a name="localredirect"></a>LocalRedirect

基本 `Controller` クラスの `LocalRedirect` ヘルパーメソッドを使用します。

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

ローカル以外の URL が指定されている場合、`LocalRedirect` は例外をスローします。 それ以外の場合は、`Redirect` メソッドと同じように動作します。

### <a name="islocalurl"></a>IsLocalUrl

リダイレクトする前に Url をテストするには、 [Islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)メソッドを使用します。

次の例は、リダイレクトする前に URL がローカルかどうかを確認する方法です:

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

`IsLocalUrl` 方法では、ユーザーが誤って悪意のあるサイトにリダイレクトされるのを防ぐことができます。 ローカルの URL が見込まれる状況でローカルではない URL が指定されたときはログに記録できます。 リダイレクトの URL をログに記録することでリダイレクト攻撃の診断に役立ちます。
