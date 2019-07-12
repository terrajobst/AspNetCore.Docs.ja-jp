---
title: ASP.NET Core でのオープンリダイレクト攻撃の防止
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
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>ASP.NET Core でのオープンリダイレクト攻撃の防止

クエリ文字列やフォームデータのように要求を介して指定された URL にリダイレクトをする Web アプリでは、改ざんされてユーザーを外部の悪意ある URL へリダイレクトする可能性が潜在的にあります。この改ざんは、オープンリダイレクト攻撃と呼ばれます。

アプリケーションロジックが指定した URL にリダイレクトする度に、リダイレクト URL が改ざんされていないことを確認する必要があります。ASP.NET Core には、オープンリダイレクト（Open Redirection とも呼ばれます）攻撃からアプリを保護するための機能が組み込まれています。

## <a name="what-is-an-open-redirect-attack"></a>オープンリダイレクト攻撃とは何か

Web アプリは認証が必要なリソースにアクセスするときに、ユーザーをログインページへリダイレクトすることが多いです。リダイレクトは、一般的にクエリ文字列のパラメーター `returnUrl` が含まれており、 ログイン成功後にもともと要求した URL に遷移します。ユーザーは認証の成功後、もともと要求された URL にリダイレクトされます。

送信先の URL はクエリ文字列で指定されているので、悪意のあるユーザーはクエリ文字列を改ざんする可能性があります。改ざんされたクエリ文字列によって、サイトはユーザーを外部の悪意あるサイトにリダイレクトできるようになります。この手法は、オープンリダイレクト（または open redirection）攻撃と呼ばれます。

### <a name="an-example-attack"></a>攻撃の例

悪意のあるユーザーは、攻撃を受けるユーザーの資格情報や機密情報にアクセスできるような攻撃を開発する可能性があります。攻撃するために、 `returnUrl` クエリ文字列の値に URL を追加したままサイトのログインページのリンクをクリックするよう誘導します。例として、`http://contoso.com/Account/LogOn?returnUrl=/Home/About` というログインページを持つ `contoso.com` のアプリを考えてみましょう。攻撃は次のステップです:

1. ユーザーは、悪意のある `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` へのリンクをクリックします（2つ目の URL は "contoso**1**.com" であり、"contoso.com" ではありません）。
2. ユーザーは、正常にログインします。
3. ユーザーは、サイトによって `http://contoso1.com/Account/LogOn`（実際のサイトと全く同じように見える悪意のあるサイト）へリダイレクトされます。
4. ユーザーは、再度ログインし（悪意のあるサイトに資格情報を与えて）、実際のサイトへリダイレクトされます。

ほとんどのユーザーは、最初のログインに失敗し2度目で成功したと思うでしょう。多くのユーザーが、自身の資格情報が侵害されたことに気付かないままでしょう。

![オープン リダイレクト攻撃のプロセス](preventing-open-redirects/_static/open-redirection-attack-process.png)

ログインページに加え、リダイレクトのページやエンドポイントを提供しているサイトはいくつもあります。アプリが、`/Home/Redirect` というオープンリダイレクトを持つページがあるとします。例えば、攻撃者は Eメールの中で `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` にアクセスするリンクを作成する可能性があります。多くのユーザーは URL を見て、それがアクセスしたいサイト名で始まっているのをみます。それを信頼してリンクをクリックするでしょう。そして、オープンリダイレクトによってユーザーはフィッシングサイトへ送られ、ユーザーは実際のサイトだと信じてログインするでしょう。

## <a name="protecting-against-open-redirect-attacks"></a>オープンリダイレクト攻撃からの保護

Web アプリを開発するときは、ユーザーが入力したデータは信頼できないものとして扱います。もし アプリが URL の内容に基づいてユーザーをリダイレクトする機能を持っている場合、そのようなリダイレクトは、アプリのローカルのみ（または、クエリ文字列で提供される URL ではなく、既知の ULR ）で行われるようにします。

### <a name="localredirect"></a>LocalRedirect

ベースの `Controller` クラスから `LocalRedirect` ヘルパーメソッドを使用します:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` は、ローカルではない URL を指定した場合、例外をスローします。それ以外の場合、`Redirect` メソッドと同様の振る舞いをします。

### <a name="islocalurl"></a>IsLocalUrl

[IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) メソッドを使って、リダイレクト前に URL をテストします:

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

`IsLocalUrl`メソッドは、ユーザーが不意に悪意のあるサイトへリダイレクトされることを防ぎます。ローカルの URL が見込まれる状況でローカルではない URL が指定されたときはログに記録できます。リダイレクトの URL をログに記録することでリダイレクト攻撃の診断に役立ちます。

