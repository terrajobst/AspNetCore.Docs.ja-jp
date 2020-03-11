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
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="1ad9f-103">ASP.NET Core でのオープンリダイレクト攻撃の防止</span><span class="sxs-lookup"><span data-stu-id="1ad9f-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="1ad9f-104">クエリ文字列やフォームデータのように要求を介して指定された URL にリダイレクトをする Web アプリでは、改ざんされてユーザーを外部の悪意ある URL へリダイレクトする可能性が潜在的にあります。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="1ad9f-105">この改ざんは、オープンリダイレクト攻撃と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="1ad9f-106">アプリケーションロジックが指定した URL にリダイレクトする度に、リダイレクト URL が改ざんされていないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="1ad9f-107">ASP.NET Core には、オープンリダイレクト（Open Redirection とも呼ばれます）攻撃からアプリを保護するための機能が組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="1ad9f-108">オープンリダイレクト攻撃とは何か</span><span class="sxs-lookup"><span data-stu-id="1ad9f-108">What is an open redirect attack?</span></span>

<span data-ttu-id="1ad9f-109">Web アプリは認証が必要なリソースにアクセスするときに、ユーザーをログインページへリダイレクトすることが多いです。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="1ad9f-110">通常、リダイレクトには、ユーザーが正常にログインした後に最初に要求された URL にユーザーを返すことができるように、`returnUrl` querystring パラメーターが含まれています。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="1ad9f-111">ユーザーは認証の成功後、もともと要求された URL にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="1ad9f-112">送信先の URL はクエリ文字列で指定されているので、悪意のあるユーザーはクエリ文字列を改ざんする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="1ad9f-113">改ざんされたクエリ文字列によって、サイトはユーザーを外部の悪意あるサイトにリダイレクトできるようになります。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="1ad9f-114">この手法は、オープンリダイレクト（または open redirection）攻撃と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="1ad9f-115">攻撃の例</span><span class="sxs-lookup"><span data-stu-id="1ad9f-115">An example attack</span></span>

<span data-ttu-id="1ad9f-116">悪意のあるユーザーは、攻撃を受けるユーザーの資格情報や機密情報にアクセスできるような攻撃を開発する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="1ad9f-117">攻撃を開始するために、悪意のあるユーザーは、`returnUrl` querystring 値が URL に追加されたサイトのログインページへのリンクをクリックすることをユーザーに仕向けるます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="1ad9f-118">たとえば、`http://contoso.com/Account/LogOn?returnUrl=/Home/About`にログインページを含む `contoso.com` のアプリを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="1ad9f-119">攻撃は次のステップです:</span><span class="sxs-lookup"><span data-stu-id="1ad9f-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="1ad9f-120">ユーザーが悪意のあるリンクをクリックして `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` します (2 番目の URL は "contoso.com" ではなく "contoso**1**.com" です)。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="1ad9f-121">ユーザーは、正常にログインします。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="1ad9f-122">ユーザーは (サイトによって) `http://contoso1.com/Account/LogOn` にリダイレクトされます (実際のサイトとまったく同じように見える悪意のあるサイト)。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="1ad9f-123">ユーザーは、再度ログインし（悪意のあるサイトに資格情報を与えて）、実際のサイトへリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="1ad9f-124">ほとんどのユーザーは、最初のログインに失敗し2度目で成功したと思うでしょう。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="1ad9f-125">多くのユーザーが、自身の資格情報が侵害されたことに気付かないままでしょう。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![オープンリダイレクト攻撃プロセス](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="1ad9f-127">ログインページに加え、リダイレクトのページやエンドポイントを提供しているサイトはいくつもあります。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="1ad9f-128">たとえば、開いているリダイレクトがあるページがアプリにあるとします。 `/Home/Redirect`します。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="1ad9f-129">攻撃者は、たとえば、電子メールのリンクを作成して `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`にすることができます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="1ad9f-130">多くのユーザーは URL を見て、それがアクセスしたいサイト名で始まっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="1ad9f-131">それを信頼してリンクをクリックするでしょう。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="1ad9f-132">そして、オープンリダイレクトによってユーザーはフィッシングサイトへ送られ、ユーザーは実際のサイトだと信じてログインするでしょう。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="1ad9f-133">オープンリダイレクト攻撃からの保護</span><span class="sxs-lookup"><span data-stu-id="1ad9f-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="1ad9f-134">Web アプリを開発するときは、ユーザーが入力したデータは信頼できないものとして扱います。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="1ad9f-135">もし アプリが URL の内容に基づいてユーザーをリダイレクトする機能を持っている場合、そのようなリダイレクトは、アプリのローカルのみ（または、クエリ文字列で提供される URL ではなく、既知の ULR ）で行われるようにします。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="1ad9f-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="1ad9f-136">LocalRedirect</span></span>

<span data-ttu-id="1ad9f-137">基本 `Controller` クラスの `LocalRedirect` ヘルパーメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="1ad9f-138">ローカル以外の URL が指定されている場合、`LocalRedirect` は例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="1ad9f-139">それ以外の場合は、`Redirect` メソッドと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="1ad9f-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="1ad9f-140">IsLocalUrl</span></span>

<span data-ttu-id="1ad9f-141">リダイレクトする前に Url をテストするには、 [Islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="1ad9f-142">次の例は、リダイレクトする前に URL がローカルかどうかを確認する方法です:</span><span class="sxs-lookup"><span data-stu-id="1ad9f-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="1ad9f-143">`IsLocalUrl` 方法では、ユーザーが誤って悪意のあるサイトにリダイレクトされるのを防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="1ad9f-144">ローカルの URL が見込まれる状況でローカルではない URL が指定されたときはログに記録できます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="1ad9f-145">リダイレクトの URL をログに記録することでリダイレクト攻撃の診断に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1ad9f-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
