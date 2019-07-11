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
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="e274a-103">ASP.NET Core でのオープン リダイレクト攻撃を防止します。</span><span class="sxs-lookup"><span data-stu-id="e274a-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="e274a-104">悪意のある、外部 URL にユーザーをリダイレクトする、クエリ文字列またはフォーム データなどの要求で指定されている URL にリダイレクトする web アプリできます改ざんされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e274a-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="e274a-105">この改ざんは、オープン リダイレクト攻撃と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e274a-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="e274a-106">アプリケーション ロジックは、指定された URL にリダイレクトされるたびに、リダイレクト URL 改ざんされていないことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e274a-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="e274a-107">ASP.NET Core では、オープン リダイレクト (とも呼ばれるオープン リダイレクト) 攻撃からアプリを保護するための組み込みの機能を持ちます。</span><span class="sxs-lookup"><span data-stu-id="e274a-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="e274a-108">オープン リダイレクト攻撃とは何ですか。</span><span class="sxs-lookup"><span data-stu-id="e274a-108">What is an open redirect attack?</span></span>

<span data-ttu-id="e274a-109">Web アプリケーションは、認証を必要とするリソースにアクセスするときに頻繁にユーザーとログイン ページにリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="e274a-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="e274a-110">通常は、リダイレクト、 `returnUrl` querystring パラメーターのユーザーが正常にログインした後、ユーザーが最初に要求された URL に返されるようにします。</span><span class="sxs-lookup"><span data-stu-id="e274a-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="e274a-111">ユーザーが認証された後は、最初に要求した URL にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e274a-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="e274a-112">リンク先の URL が指定されて、要求のクエリ文字列で、クエリ文字列を悪意のあるユーザーが改ざんする可能性です。</span><span class="sxs-lookup"><span data-stu-id="e274a-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="e274a-113">改ざんされたクエリ文字列には、外部の悪意のあるサイトにユーザーをリダイレクトするサイトを許可できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="e274a-114">この手法は、オープン リダイレクト (またはリダイレクト) 攻撃と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e274a-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="e274a-115">攻撃の例</span><span class="sxs-lookup"><span data-stu-id="e274a-115">An example attack</span></span>

<span data-ttu-id="e274a-116">悪意のあるユーザーは、攻撃を受けるユーザーの資格情報または機密情報に悪意のあるユーザーのアクセスを許可するためのものを開発できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="e274a-117">悪意のあるユーザーがサイトのログイン ページへのリンクをクリックするユーザーを仕向け、攻撃を開始する、`returnUrl`クエリ文字列値を URL に追加します。</span><span class="sxs-lookup"><span data-stu-id="e274a-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="e274a-118">たとえば、アプリで`contoso.com`でログイン ページを含む`http://contoso.com/Account/LogOn?returnUrl=/Home/About`します。</span><span class="sxs-lookup"><span data-stu-id="e274a-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="e274a-119">攻撃では、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="e274a-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="e274a-120">ユーザーへの悪意のあるリンクをクリックした`http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn`(2 番目の URL は"contoso**1**.com"、"contoso.com")。</span><span class="sxs-lookup"><span data-stu-id="e274a-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="e274a-121">ユーザーが正常にログインします。</span><span class="sxs-lookup"><span data-stu-id="e274a-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="e274a-122">ユーザーが (サイト) をリダイレクトする`http://contoso1.com/Account/LogOn`(次の実際のサイトとまったく同じような悪意のあるサイト)。</span><span class="sxs-lookup"><span data-stu-id="e274a-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="e274a-123">ユーザーがもう一度ログイン (自分の資格情報付与悪意のあるサイト) とは、実際のサイトにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="e274a-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="e274a-124">可能性の高いユーザーは、ログインに、最初の試行が失敗したことと、2 回目の試行が成功したことと考えています。</span><span class="sxs-lookup"><span data-stu-id="e274a-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="e274a-125">ほとんどの場合、ユーザーが自分の資格情報の侵害に関係なくままです。</span><span class="sxs-lookup"><span data-stu-id="e274a-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![オープン リダイレクト攻撃のプロセス](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="e274a-127">一部のサイトは、ログイン ページだけでなく、リダイレクト ページまたはエンドポイントを提供します。</span><span class="sxs-lookup"><span data-stu-id="e274a-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="e274a-128">アプリのオープン リダイレクトでは、ページが imagine`/Home/Redirect`します。</span><span class="sxs-lookup"><span data-stu-id="e274a-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="e274a-129">攻撃者を作成して、たとえばに移動する電子メールのリンク`[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`します。</span><span class="sxs-lookup"><span data-stu-id="e274a-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="e274a-130">一般的なユーザーは URL を確認し、サイト名で始まるを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e274a-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="e274a-131">信頼する、リンクがクリックしてされます。</span><span class="sxs-lookup"><span data-stu-id="e274a-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="e274a-132">オープン リダイレクトは、ユーザーは、自分と同じ、フィッシング詐欺サイトに送信し、ユーザーがそうなログインが信じられませんが、サイト。</span><span class="sxs-lookup"><span data-stu-id="e274a-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="e274a-133">オープン リダイレクト攻撃から保護します。</span><span class="sxs-lookup"><span data-stu-id="e274a-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="e274a-134">Web アプリケーションを開発する場合は、信頼できないとしてすべてのユーザーが入力したデータを処理します。</span><span class="sxs-lookup"><span data-stu-id="e274a-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="e274a-135">アプリケーションに、URL の内容に基づくユーザーをリダイレクトする機能がある場合は、このようなリダイレクトがのみ行われることローカルで、アプリ内で (または、既知の url、クエリ文字列で指定することがありますいない任意の URL) を確認します。</span><span class="sxs-lookup"><span data-stu-id="e274a-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="e274a-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="e274a-136">LocalRedirect</span></span>

<span data-ttu-id="e274a-137">使用して、`LocalRedirect`ベースからヘルパー メソッド`Controller`クラス。</span><span class="sxs-lookup"><span data-stu-id="e274a-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="e274a-138">`LocalRedirect` 非ローカル URL が指定されている場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e274a-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="e274a-139">それ以外の場合、動作と同じように、`Redirect`メソッド。</span><span class="sxs-lookup"><span data-stu-id="e274a-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="e274a-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="e274a-140">IsLocalUrl</span></span>

<span data-ttu-id="e274a-141">使用して、 [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_)をリダイレクトする前に Url をテストするメソッド。</span><span class="sxs-lookup"><span data-stu-id="e274a-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="e274a-142">次の例では、リダイレクトする前に、URL がローカルかどうかを確認する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e274a-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="e274a-143">`IsLocalUrl`メソッドは、悪意のあるサイトにリダイレクトされる誤ってからユーザーを保護します。</span><span class="sxs-lookup"><span data-stu-id="e274a-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="e274a-144">ローカルの URL を意図したような状況で非ローカル URL が指定されている場合に提供された URL の詳細を記録できます。</span><span class="sxs-lookup"><span data-stu-id="e274a-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="e274a-145">ログイン リダイレクト Url がリダイレクト攻撃の診断に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="e274a-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
