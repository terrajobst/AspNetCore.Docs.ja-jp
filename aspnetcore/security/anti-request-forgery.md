---
title: ASP.NET Core で防ぐクロスサイト リクエスト フォージェリ (XSRF または CSRF) 攻撃
author: steve-smith
description: 悪意のある web サイトがクライアント ブラウザーとアプリ間のやり取りに影響する web アプリに対する攻撃を防ぐ方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: a32e0e2dbd7fab95562a562cb88767d4c1e8049d
ms.sourcegitcommit: c5339594101d30b189f61761275b7d310e80d18a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2019
ms.locfileid: "66458486"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="98158-103">ASP.NET Core で防ぐクロスサイト リクエスト フォージェリ (XSRF または CSRF) 攻撃</span><span class="sxs-lookup"><span data-stu-id="98158-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="98158-104">著者: [Steve Smith](https://ardalis.com/)、[Fiyaz Hasan](https://twitter.com/FiyazBinHasan)、および [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="98158-104">By [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="98158-105">クロスサイトリクエストフォージェリ（XSRF または CSRF とも呼ばれます）は、Web でホストされているアプリへの攻撃であり、それによって悪意のある Web アプリが、クライアントのブラウザーとそのブラウザーを信頼している Web アプリとのやり取りに影響を及ぼします。</span><span class="sxs-lookup"><span data-stu-id="98158-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="98158-106">Web ブラウザーは、Web サイトへのリクエストの度にある種の認証トークンを送信しているため、攻撃が可能です。</span><span class="sxs-lookup"><span data-stu-id="98158-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="98158-107">この形式の攻撃は、*ワンクリック攻撃*や*セッションライディング*としても知られています。これは、 攻撃者がユーザーの認証済みのセッションを利用するためです。</span><span class="sxs-lookup"><span data-stu-id="98158-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="98158-108">CSRF 攻撃の例:</span><span class="sxs-lookup"><span data-stu-id="98158-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="98158-109">ユーザーがフォーム認証を使用して `www.good-banking-site.com` にサインインします。</span><span class="sxs-lookup"><span data-stu-id="98158-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="98158-110">サーバーはユーザーを認証し、認証 Cookie を含む応答を返します。</span><span class="sxs-lookup"><span data-stu-id="98158-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="98158-111">このサイトは有効な認証 Cookie を持った要求を全て信頼しているため、攻撃に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="98158-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="98158-112">ユーザーは、悪意のあるサイトである `www.bad-crook-site.com` にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="98158-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="98158-113">悪意のあるサイトである `www.bad-crook-site.com` では、次のような HTML フォームが含まれているとします:</span><span class="sxs-lookup"><span data-stu-id="98158-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="98158-114">注意として、フォームの `action` は脆弱性のあるサイトにポストしており、悪意のあるサイトへではありません。</span><span class="sxs-lookup"><span data-stu-id="98158-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="98158-115">これは、CSRF の "cross-site" の一部です。</span><span class="sxs-lookup"><span data-stu-id="98158-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="98158-116">ユーザーは、[送信] ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="98158-116">The user selects the submit button.</span></span> <span data-ttu-id="98158-117">ブラウザーが要求を行うと、自動的に、要求先のドメインである `www.good-banking-site.com` の認証 Cookie が含まれます。</span><span class="sxs-lookup"><span data-stu-id="98158-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="98158-118">要求は、ユーザーの認証コンテキストを持った状態で `www.good-banking-site.com` サーバー上で実行されるので、認証されたユーザーに許可されている任意のアクションを実行できます。</span><span class="sxs-lookup"><span data-stu-id="98158-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="98158-119">ユーザーがフォームの送信ボタンを選択するシナリオに加えて、悪意のあるサイトは以下のことを行う可能性があります。</span><span class="sxs-lookup"><span data-stu-id="98158-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="98158-120">自動的にフォームの送信をするスクリプトの実行</span><span class="sxs-lookup"><span data-stu-id="98158-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="98158-121">フォームの送信を AJAX 要求として送信</span><span class="sxs-lookup"><span data-stu-id="98158-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="98158-122">CSS を使用してフォームを非表示</span><span class="sxs-lookup"><span data-stu-id="98158-122">Hide the form using CSS.</span></span>

<span data-ttu-id="98158-123">これらのシナリオでは、最初に悪意のあるサイトにアクセスする以外に、ユーザーの操作や入力は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="98158-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="98158-124">HTTPS を使用しても CSRF 攻撃を防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="98158-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="98158-125">悪意のあるサイトは、セキュアではないリクエストを送信するの同じくらい容易に `https://www.good-banking-site.com/` へリクエストを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="98158-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="98158-126">GET 要求の応答のエンドポイントを標的とした攻撃もあります。その場合、イメージタグがアクションの実行によく使われます。</span><span class="sxs-lookup"><span data-stu-id="98158-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="98158-127">この形式の攻撃は、画像は許可するが JavaScript はブロックするフォーラムのサイトでよくあります。</span><span class="sxs-lookup"><span data-stu-id="98158-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="98158-128">GETメソッドの変数やリソースの変更によって状態を変更するアプリは、悪意のある攻撃に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="98158-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="98158-129">**状態を変更する GET 要求はセキュアではありません。ベストプラクティスは、GET 要求で状態を変更しないことです。**</span><span class="sxs-lookup"><span data-stu-id="98158-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="98158-130">認証に Cookie を使用する Web アプリに対して、CSRF 攻撃が可能です。理由は以下です。</span><span class="sxs-lookup"><span data-stu-id="98158-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="98158-131">ブラウザーは Web アプリが発行した Cookie を保存します。</span><span class="sxs-lookup"><span data-stu-id="98158-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="98158-132">保存された Cookie には、認証されたユーザーのセッション Cookie が含まれています。</span><span class="sxs-lookup"><span data-stu-id="98158-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="98158-133">ブラウザーは、ブラウザー内でアプリへのリクエストがどのように生成されたかにかかわらず、Web アプリの全ての要求に対しドメインに関連する全ての Cookie を送信します。</span><span class="sxs-lookup"><span data-stu-id="98158-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="98158-134">しかし、CSRF 攻撃は Cookie の悪用だけではありません。</span><span class="sxs-lookup"><span data-stu-id="98158-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="98158-135">例えば、ベーシック認証やダイジェスト認証も脆弱です。</span><span class="sxs-lookup"><span data-stu-id="98158-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="98158-136">ベーシック認証やダイジェスト認証でサインインした後、ブラウザーは動的にセッション&dagger;が終了するまで自動的に資格情報を送信します。</span><span class="sxs-lookup"><span data-stu-id="98158-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="98158-137">&dagger;ここでの *セッション* は、ユーザーが認証されている間のクライアントサイドのセッションを指します。</span><span class="sxs-lookup"><span data-stu-id="98158-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="98158-138">サーバーサイドのセッションや[ASP.NET Core のセッションミドルウェア](xref:fundamentals/app-state)とは無関係です。</span><span class="sxs-lookup"><span data-stu-id="98158-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="98158-139">ユーザーは、次の対策をとることで CSRF の脆弱性を防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="98158-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="98158-140">使用が終わったら、Web アプリからサインオフする</span><span class="sxs-lookup"><span data-stu-id="98158-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="98158-141">定期的にブラウザーの Cookie をクリアする</span><span class="sxs-lookup"><span data-stu-id="98158-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="98158-142">ただし、CSRF の脆弱性は基本的に Web アプリの問題であり、エンドユーザーの問題ではありません。</span><span class="sxs-lookup"><span data-stu-id="98158-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="98158-143">認証の基礎</span><span class="sxs-lookup"><span data-stu-id="98158-143">Authentication fundamentals</span></span>

<span data-ttu-id="98158-144">Cookie ベースの認証は一般的な認証方式です。</span><span class="sxs-lookup"><span data-stu-id="98158-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="98158-145">トークンベースの認証システムは、特にシングルページアプリケーション（SPA）で人気が高まっています。</span><span class="sxs-lookup"><span data-stu-id="98158-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="98158-146">Cookie ベースの認証</span><span class="sxs-lookup"><span data-stu-id="98158-146">Cookie-based authentication</span></span>

<span data-ttu-id="98158-147">ユーザーがユーザー名とパスワードを使って認証すると、ユーザーが認証と承認ができる認証チケットを含むトークンが発行されます。</span><span class="sxs-lookup"><span data-stu-id="98158-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="98158-148">トークンは、クライアントが生成する全てのリクエストに含まれる Cookie として保存されます。</span><span class="sxs-lookup"><span data-stu-id="98158-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="98158-149">この Cookie の生成と検証は Cookie 認証ミドルウェアによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="98158-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="98158-150">[ミドルウェア](xref:fundamentals/middleware/index)は、ユーザープリンシパルを暗号化した Cookie にシリアル化します。</span><span class="sxs-lookup"><span data-stu-id="98158-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="98158-151">以降の要求では、ミドルウェアが Cookie を検証し、プリンシパルを再作成したりそのプリンシパルを [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext) の [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="98158-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="98158-152">トークンベースの認証</span><span class="sxs-lookup"><span data-stu-id="98158-152">Token-based authentication</span></span>

<span data-ttu-id="98158-153">ユーザーが認証されると、（偽造防止トークンとは別の）トークンが発行されます。</span><span class="sxs-lookup"><span data-stu-id="98158-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="98158-154">トークンには、[Claim](/dotnet/framework/security/claims-based-identity-model) 形式のユーザー情報や、アプリ内で保持されているユーザーの状態をアプリに紐づける参照トークンが含まれています。</span><span class="sxs-lookup"><span data-stu-id="98158-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="98158-155">ユーザーが認証の必要なリソースにアクセスしようとすると、ベアラートークンの形式で追加の認証ヘッダーを付与して、アプリにトークンが送信されます。</span><span class="sxs-lookup"><span data-stu-id="98158-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="98158-156">これによってアプリはステートレスになります。</span><span class="sxs-lookup"><span data-stu-id="98158-156">This makes the app stateless.</span></span> <span data-ttu-id="98158-157">以降の要求では、サーバーサイドでの検証のためにトークンが渡されます。</span><span class="sxs-lookup"><span data-stu-id="98158-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="98158-158">このトークンは、*暗号化* されていません。 *エンコード* されています。</span><span class="sxs-lookup"><span data-stu-id="98158-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="98158-159">サーバーでは、この情報にアクセスするためにトークンがデコードされます。</span><span class="sxs-lookup"><span data-stu-id="98158-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="98158-160">以降のリクエストでトークンを送信する際は、ブラウザーのローカルストレージに保存します。</span><span class="sxs-lookup"><span data-stu-id="98158-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="98158-161">トークンがブラウザーのローカルストレージに保存された場合、CSRF の脆弱性の心配はありません。</span><span class="sxs-lookup"><span data-stu-id="98158-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="98158-162">トークンが Cookie に保存されている場合は、CSRF の懸念があります。</span><span class="sxs-lookup"><span data-stu-id="98158-162">CSRF is a concern when the token is stored in a cookie.</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="98158-163">1 つのドメインでホストされている複数のアプリ</span><span class="sxs-lookup"><span data-stu-id="98158-163">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="98158-164">共有のホスティング環境では、セッションハイジャック、ログインの CSRFやその他の攻撃に対して脆弱です。</span><span class="sxs-lookup"><span data-stu-id="98158-164">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="98158-165">`example1.contoso.net` と `example2.contoso.net` は別のホストですが、 `*.contoso.net` ドメイン下のホストにあるため、暗黙の信頼関係があります。</span><span class="sxs-lookup"><span data-stu-id="98158-165">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="98158-166">この暗黙の信頼関係により、潜在的に信頼できないホストに対して互いの Cookie が影響を及ぼす可能性があります（AJAX 要求を管理する同一オリジンポリシーは、HTTP の Cookie には必ずしも適用されるわけではありません）。</span><span class="sxs-lookup"><span data-stu-id="98158-166">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="98158-167">同じドメインでホストされているアプリ感の信頼された Cookie を悪用した攻撃は、ドメインを共有しないことで防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="98158-167">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="98158-168">アプリがそれぞれ各自のドメインでホストされていれば、悪用できる信頼関係をもつ暗黙的な Cookie はありません。</span><span class="sxs-lookup"><span data-stu-id="98158-168">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="98158-169">ASP.NET Core の偽造防止の構成</span><span class="sxs-lookup"><span data-stu-id="98158-169">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="98158-170">ASP.NET Core では、[ASP.NET Core データ保護](xref:security/data-protection/introduction)を使用して偽造防止の実装をします。</span><span class="sxs-lookup"><span data-stu-id="98158-170">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="98158-171">データ保護のスタックは、サーバー ファームで動作するように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="98158-171">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="98158-172">詳しくは、[データ保護の構成](xref:security/data-protection/configuration/overview)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="98158-172">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

<span data-ttu-id="98158-173">ASP.NET Core 2.0 以降では、 [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper)が HTML フォームの要素に偽造防止トークンを挿入します。</span><span class="sxs-lookup"><span data-stu-id="98158-173">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="98158-174">Razor ファイルの次のマークアップには、偽造防止トークンが自動的に生成されます:</span><span class="sxs-lookup"><span data-stu-id="98158-174">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="98158-175">同様に、 [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) も GET 以外のメソッドの場合、デフォルトで偽造防止トークンを生成します。</span><span class="sxs-lookup"><span data-stu-id="98158-175">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="98158-176">HTML フォーム要素の偽造防止トークンの自動生成は、`<form>` タグに `method="post"` 属性が含まれていて、次のいずれかが当てはまる場合に生成されます:</span><span class="sxs-lookup"><span data-stu-id="98158-176">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="98158-177">Action 属性が空（`action=""`）</span><span class="sxs-lookup"><span data-stu-id="98158-177">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="98158-178">Action 属性が指定されていない（`<form method="post">`）</span><span class="sxs-lookup"><span data-stu-id="98158-178">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="98158-179">HTML フォーム要素で偽造防止トークンの自動生成を無効にすることができます:</span><span class="sxs-lookup"><span data-stu-id="98158-179">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="98158-180">`asp-antiforgery`属性によって偽造防止トークンを明示的に無効化する:</span><span class="sxs-lookup"><span data-stu-id="98158-180">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="98158-181">フォーム要素で、タグヘルパーの [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out) によって、タグヘルパーがオプトアウトされます:</span><span class="sxs-lookup"><span data-stu-id="98158-181">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="98158-182">ビューから `FormTagHelper` を削除します。</span><span class="sxs-lookup"><span data-stu-id="98158-182">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="98158-183">`FormTagHelper` は、Razor ビューに次のディレクティブを追加することで、ビューから削除できます:</span><span class="sxs-lookup"><span data-stu-id="98158-183">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="98158-184">[Razor ページ](xref:razor-pages/index)は、自動的に XSRF/CSRF から保護されます。</span><span class="sxs-lookup"><span data-stu-id="98158-184">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="98158-185">詳細については、[XSRF/CSRF と Razor ページ](xref:razor-pages/index#xsrf) 参照してください。</span><span class="sxs-lookup"><span data-stu-id="98158-185">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="98158-186">CSRF 攻撃に対する防御の最も一般的なアプローチは、*Synchronizer Token Pattern*（STP）を使用することです。</span><span class="sxs-lookup"><span data-stu-id="98158-186">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="98158-187">STPは、ユーザーがフォームデータを含むページを要求したときに使用されます:</span><span class="sxs-lookup"><span data-stu-id="98158-187">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="98158-188">サーバーは、現在のユーザーの ID に関連したトークンをクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="98158-188">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="98158-189">クライアントは、検証のためにトークンをサーバーに送り返します。</span><span class="sxs-lookup"><span data-stu-id="98158-189">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="98158-190">サーバーが認証されたユーザーの ID と一致しないトークンを受け取った場合、要求は拒否されます。</span><span class="sxs-lookup"><span data-stu-id="98158-190">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="98158-191">トークンは一意で予測不可能です。</span><span class="sxs-lookup"><span data-stu-id="98158-191">The token is unique and unpredictable.</span></span> <span data-ttu-id="98158-192">トークンは、一連の要求の適切な順序付け（例えば、ページ1 &ndash; ページ2 &ndash; ページ3といった要求順の保証）を保証するためにも使用できます。</span><span class="sxs-lookup"><span data-stu-id="98158-192">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="98158-193">ASP.NET Core MVC と Razor Pages のテンプレートの全てのフォームで偽造防止トークンは生成できます。</span><span class="sxs-lookup"><span data-stu-id="98158-193">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="98158-194">次の2つのビューの例で、偽造防止トークンを生成します:</span><span class="sxs-lookup"><span data-stu-id="98158-194">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="98158-195">タグヘルパーを使わず、HTML ヘルパーの[@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken)を使って明示的に偽造防止トークンを追加します:</span><span class="sxs-lookup"><span data-stu-id="98158-195">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="98158-196">上の 2 つのケースでは、ASP.NET Core は次の隠しフォームフィールドを追加します:</span><span class="sxs-lookup"><span data-stu-id="98158-196">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="98158-197">ASP.NET Core では、偽造防止トークンを動作するために 3 つの[フィルター](xref:mvc/controllers/filters)が含まれています:</span><span class="sxs-lookup"><span data-stu-id="98158-197">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="98158-198">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="98158-198">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="98158-199">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="98158-199">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="98158-200">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="98158-200">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="98158-201">偽造防止のオプション</span><span class="sxs-lookup"><span data-stu-id="98158-201">Antiforgery options</span></span>

<span data-ttu-id="98158-202">`Startup.ConfigureServices` で、[偽造防止のオプション](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions)をカスタマイズできます:</span><span class="sxs-lookup"><span data-stu-id="98158-202">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="98158-203">&dagger; [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) クラスのプロパティを使って、偽造防止の `Cookie` プロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="98158-203">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="98158-204">OPTION</span><span class="sxs-lookup"><span data-stu-id="98158-204">Option</span></span> | <span data-ttu-id="98158-205">説明</span><span class="sxs-lookup"><span data-stu-id="98158-205">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="98158-206">Cookie</span><span class="sxs-lookup"><span data-stu-id="98158-206">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="98158-207">偽造防止の Cookie の作成に使用する設定を決定します。</span><span class="sxs-lookup"><span data-stu-id="98158-207">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="98158-208">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="98158-208">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="98158-209">偽造防止トークンをビューに表示するために偽造防止システムで使われる、隠しフォームフィールドの名前。</span><span class="sxs-lookup"><span data-stu-id="98158-209">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="98158-210">HeaderName</span><span class="sxs-lookup"><span data-stu-id="98158-210">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="98158-211">偽造防止システムで使用されるヘッダーの名前。</span><span class="sxs-lookup"><span data-stu-id="98158-211">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="98158-212">`null` の場合、システムはフォームデータのみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="98158-212">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="98158-213">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="98158-213">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="98158-214">`X-Frame-Options` ヘッダーの生成を抑制するかを指定します。</span><span class="sxs-lookup"><span data-stu-id="98158-214">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="98158-215">デフォルトでは、ヘッダーは "SAMEORIGIN"の値で生成されます。</span><span class="sxs-lookup"><span data-stu-id="98158-215">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="98158-216">デフォルトは `false` です。</span><span class="sxs-lookup"><span data-stu-id="98158-216">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="98158-217">OPTION</span><span class="sxs-lookup"><span data-stu-id="98158-217">Option</span></span> | <span data-ttu-id="98158-218">説明</span><span class="sxs-lookup"><span data-stu-id="98158-218">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="98158-219">Cookie</span><span class="sxs-lookup"><span data-stu-id="98158-219">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="98158-220">偽造防止の Cookie の作成に使用する設定を決定します。</span><span class="sxs-lookup"><span data-stu-id="98158-220">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="98158-221">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="98158-221">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="98158-222">Cookie のドメイン。</span><span class="sxs-lookup"><span data-stu-id="98158-222">The domain of the cookie.</span></span> <span data-ttu-id="98158-223">既定値は `null` です。</span><span class="sxs-lookup"><span data-stu-id="98158-223">Defaults to `null`.</span></span> <span data-ttu-id="98158-224">このプロパティは廃止され、今後のバージョンで削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="98158-224">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="98158-225">Cookie.Domain お勧めします。</span><span class="sxs-lookup"><span data-stu-id="98158-225">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="98158-226">CookieName</span><span class="sxs-lookup"><span data-stu-id="98158-226">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="98158-227">クッキーの名前。</span><span class="sxs-lookup"><span data-stu-id="98158-227">The name of the cookie.</span></span> <span data-ttu-id="98158-228">設定されていないシステム生成一意の名前の先頭に、 [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) ("。AspNetCore.Antiforgery。")。</span><span class="sxs-lookup"><span data-stu-id="98158-228">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="98158-229">このプロパティは廃止され、今後のバージョンで削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="98158-229">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="98158-230">Cookie.Name お勧めします。</span><span class="sxs-lookup"><span data-stu-id="98158-230">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="98158-231">CookiePath</span><span class="sxs-lookup"><span data-stu-id="98158-231">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="98158-232">Cookie の設定のパス。</span><span class="sxs-lookup"><span data-stu-id="98158-232">The path set on the cookie.</span></span> <span data-ttu-id="98158-233">このプロパティは廃止され、今後のバージョンで削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="98158-233">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="98158-234">Cookie.Path お勧めします。</span><span class="sxs-lookup"><span data-stu-id="98158-234">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="98158-235">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="98158-235">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="98158-236">偽造防止トークンをビューに表示するために偽造防止システムで使われる、隠しフォームフィールドの名前。</span><span class="sxs-lookup"><span data-stu-id="98158-236">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="98158-237">HeaderName</span><span class="sxs-lookup"><span data-stu-id="98158-237">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="98158-238">偽造防止システムで使用されるヘッダーの名前。</span><span class="sxs-lookup"><span data-stu-id="98158-238">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="98158-239">`null` の場合、システムはフォームデータのみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="98158-239">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="98158-240">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="98158-240">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="98158-241">偽造防止システムで HTTPS が必要かどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="98158-241">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="98158-242">場合`true`、HTTPS 以外の要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="98158-242">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="98158-243">既定値は `false` です。</span><span class="sxs-lookup"><span data-stu-id="98158-243">Defaults to `false`.</span></span> <span data-ttu-id="98158-244">このプロパティは廃止され、今後のバージョンで削除される予定です。</span><span class="sxs-lookup"><span data-stu-id="98158-244">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="98158-245">Cookie.SecurePolicy を設定することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="98158-245">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="98158-246">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="98158-246">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="98158-247">`X-Frame-Options` ヘッダーの生成を抑制するかを指定します。</span><span class="sxs-lookup"><span data-stu-id="98158-247">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="98158-248">デフォルトでは、ヘッダーは "SAMEORIGIN"の値で生成されます。</span><span class="sxs-lookup"><span data-stu-id="98158-248">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="98158-249">デフォルトは `false` です。</span><span class="sxs-lookup"><span data-stu-id="98158-249">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="98158-250">詳細については、[CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="98158-250">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="98158-251">IAntiforgery 偽造防止の機能を構成する</span><span class="sxs-lookup"><span data-stu-id="98158-251">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="98158-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) は偽造防止の機能を構成する API を提供します。</span><span class="sxs-lookup"><span data-stu-id="98158-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="98158-253">`IAntiforgery` は `Startup` クラスの `Configure` メソッドで要求されます。</span><span class="sxs-lookup"><span data-stu-id="98158-253">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="98158-254">次の例では、ミドルウェアを使用して、アプリのホームページから偽造防止トークンを生成し、それを Cookie として応答を送信しています(このトピックで後述する Angular のデフォルトの命名規則を使っています):</span><span class="sxs-lookup"><span data-stu-id="98158-254">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="98158-255">偽造防止の検証の要求</span><span class="sxs-lookup"><span data-stu-id="98158-255">Require antiforgery validation</span></span>

<span data-ttu-id="98158-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) は、個々のアクション、コントローラー、またはグローバルに適用できるアクションフィルターです。</span><span class="sxs-lookup"><span data-stu-id="98158-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="98158-257">このフィルターが適用したアクションへの要求は、有効な偽造防止トークンが含まていなければブロックされます。</span><span class="sxs-lookup"><span data-stu-id="98158-257">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="98158-258">`ValidateAntiForgeryToken` 属性は、HTTP GET の要求も含め、属性の付いたアクションメソッドへの要求にはトークンが必要になります。</span><span class="sxs-lookup"><span data-stu-id="98158-258">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it decorates, including HTTP GET requests.</span></span> <span data-ttu-id="98158-259">`ValidateAntiForgeryToken` 属性がアプリのコントローラーで適用されている場合は、`IgnoreAntiforgeryToken` 属性で上書きできます。</span><span class="sxs-lookup"><span data-stu-id="98158-259">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="98158-260">ASP.NET Core では、GET 要求に対して偽造防止トークンの自動的な追加はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="98158-260">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="98158-261">安全でない HTTP メソッドのみ偽造防止トークンを自動的に検証</span><span class="sxs-lookup"><span data-stu-id="98158-261">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="98158-262">ASP.NET Core アプリケーションでは、安全な HTTP メソッド（GET、HEAD、OPTIONS、および TRACE）に対して偽造防止トークンを生成しません。</span><span class="sxs-lookup"><span data-stu-id="98158-262">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="98158-263">明示的に `ValidateAntiForgeryToken` 属性を適用してから `IgnoreAntiforgeryToken` で上書きする代わりとして、[AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) 属性を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="98158-263">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="98158-264">この属性は、`ValidateAntiForgeryToken` 属性と同様に機能しますが、次の HTTP メソッドを使った要求にトークンを必要としません:</span><span class="sxs-lookup"><span data-stu-id="98158-264">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="98158-265">GET</span><span class="sxs-lookup"><span data-stu-id="98158-265">GET</span></span>
* <span data-ttu-id="98158-266">HEAD</span><span class="sxs-lookup"><span data-stu-id="98158-266">HEAD</span></span>
* <span data-ttu-id="98158-267">OPTION</span><span class="sxs-lookup"><span data-stu-id="98158-267">OPTIONS</span></span>
* <span data-ttu-id="98158-268">TRACE</span><span class="sxs-lookup"><span data-stu-id="98158-268">TRACE</span></span>

<span data-ttu-id="98158-269">API ではないシナリオでは、`AutoValidateAntiforgeryToken` の使用をお薦めします。</span><span class="sxs-lookup"><span data-stu-id="98158-269">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="98158-270">これで POST のアクションはデフォルトで保護されます。</span><span class="sxs-lookup"><span data-stu-id="98158-270">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="98158-271">別の方法として、`ValidateAntiForgeryToken` が個別にアクションメソッドに適用されていない限り、デフォルトで偽造防止トークンを無効にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="98158-271">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="98158-272">このシナリオでは、POST のアクションメソッドが誤って保護されずにアプリが CSRF 攻撃に対して脆弱のままになる可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="98158-272">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="98158-273">すべての POST メソッドが偽造防止トークンを送信すべきです。</span><span class="sxs-lookup"><span data-stu-id="98158-273">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="98158-274">API は、トークンの Cookie 以外の部分を送信する自動的なメカニズムはありません。</span><span class="sxs-lookup"><span data-stu-id="98158-274">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="98158-275">その実装はクライアントのコードの実装に依存するでしょう。</span><span class="sxs-lookup"><span data-stu-id="98158-275">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="98158-276">以下でいくつかの例を挙げます。</span><span class="sxs-lookup"><span data-stu-id="98158-276">Some examples are shown below:</span></span>

<span data-ttu-id="98158-277">クラスレベルの例:</span><span class="sxs-lookup"><span data-stu-id="98158-277">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="98158-278">グローバルの例:</span><span class="sxs-lookup"><span data-stu-id="98158-278">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="98158-279">グローバルまたはコントローラーの偽造防止属性の上書き</span><span class="sxs-lookup"><span data-stu-id="98158-279">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="98158-280">[IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) フィルターを使用して、特定のアクション（またはコントローラー）の偽造防止トークンの必要性を無くすことができます。</span><span class="sxs-lookup"><span data-stu-id="98158-280">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="98158-281">適用すると、このフィルターはハイレベル（グローバルまたはコントローラー上）で指定された `ValidateAntiForgeryToken` と `AutoValidateAntiforgeryToken` フィルターを上書きします。</span><span class="sxs-lookup"><span data-stu-id="98158-281">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="98158-282">認証後のリフレッシュトークン</span><span class="sxs-lookup"><span data-stu-id="98158-282">Refresh tokens after authentication</span></span>

<span data-ttu-id="98158-283">ユーザーの認証後、ビューまたは Razor Pages のページにリダイレクトするときにトークンを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="98158-283">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="98158-284">JavaScript、AJAX、および SPA</span><span class="sxs-lookup"><span data-stu-id="98158-284">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="98158-285">従来の HTML ベースのアプリでは、偽造防止トークンは隠しフォーム フィールドを使ってサーバーに渡されていました。</span><span class="sxs-lookup"><span data-stu-id="98158-285">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="98158-286">最新の JavaScript ベースや SPA では多くの要求がプログラムによって行われています。</span><span class="sxs-lookup"><span data-stu-id="98158-286">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="98158-287">これらの AJAX 要求は、トークンを送信するための他の技術（要求ヘッダーや Cookie）を使うことがあります。</span><span class="sxs-lookup"><span data-stu-id="98158-287">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="98158-288">Cookie に認証トークンを保存しサーバーの API の要求で認証するために使われるなら、CSRF は潜在的な問題です。</span><span class="sxs-lookup"><span data-stu-id="98158-288">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="98158-289">トークンの保存にローカルストレージを使用すると CSRF の脆弱性が軽減される可能性があります。なぜならローカルストレージの値は、全ての要求は自動でサーバーに送信されないからです。</span><span class="sxs-lookup"><span data-stu-id="98158-289">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="98158-290">したがって、クライアントの偽造防止トークンの保存にローカルストレージを使い、要求ヘッダーとしてトークンを送信することがお薦めのアプローチです。</span><span class="sxs-lookup"><span data-stu-id="98158-290">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="98158-291">JavaScript</span><span class="sxs-lookup"><span data-stu-id="98158-291">JavaScript</span></span>

<span data-ttu-id="98158-292">ビューで JavaScript を使うとビュー内でサービスを使ってトークンを作ることができます。</span><span class="sxs-lookup"><span data-stu-id="98158-292">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="98158-293">[Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) サービスをビューに挿入して [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens) を呼び出します:</span><span class="sxs-lookup"><span data-stu-id="98158-293">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="98158-294">このアプローチで、サーバーから Cookie を設定したりクライアントから Cookie を読み取る必要が無くなります。</span><span class="sxs-lookup"><span data-stu-id="98158-294">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="98158-295">上の例では、JavaScript を使用して AJAX の POST のヘッダーの非表示フィールドの値を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="98158-295">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="98158-296">JavaScriptは Cookie 内のトークンにアクセスし、その Cookie の内容を使ってトークンの値を持つヘッダーを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="98158-296">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="98158-297">スクリプトが `X-CSRF-TOKEN` というヘッダーでトークンの送信を要求すると想定して、`X-CSRF-TOKEN` ヘッダーを探すように偽造防止サービスを構成します:</span><span class="sxs-lookup"><span data-stu-id="98158-297">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="98158-298">次の例は、JavaScript を使用して適切なヘッダーで AJAX の要求をします:</span><span class="sxs-lookup"><span data-stu-id="98158-298">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="98158-299">AngularJS</span><span class="sxs-lookup"><span data-stu-id="98158-299">AngularJS</span></span>

<span data-ttu-id="98158-300">AngularJS では CSRF に対処する規約があります。</span><span class="sxs-lookup"><span data-stu-id="98158-300">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="98158-301">サーバーが `XSRF-TOKEN` という名前の Cookie を送信すると、AngularJS の `$http` サービスはサーバーに要求を送信するとき、ヘッダーに Cookie の値を追加します。</span><span class="sxs-lookup"><span data-stu-id="98158-301">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="98158-302">このプロセスは自動です。</span><span class="sxs-lookup"><span data-stu-id="98158-302">This process is automatic.</span></span> <span data-ttu-id="98158-303">クライアントで明示的にヘッダーを設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="98158-303">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="98158-304">ヘッダーの名前は `X-XSRF-TOKEN` です。</span><span class="sxs-lookup"><span data-stu-id="98158-304">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="98158-305">サーバーはこのヘッダー検出して検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="98158-305">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="98158-306">ASP.NET Core の API では、アプリケーションの起動時にこの規約が動作するようにします:</span><span class="sxs-lookup"><span data-stu-id="98158-306">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="98158-307">`XSRF-TOKEN` という Cookie にトークンを提供するようにアプリを構成する</span><span class="sxs-lookup"><span data-stu-id="98158-307">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="98158-308">`X-XSRF-TOKEN` という名前のヘッダーを探すように偽造防止サービスを構成する</span><span class="sxs-lookup"><span data-stu-id="98158-308">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="98158-309">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="98158-309">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="98158-310">偽造防止の拡張</span><span class="sxs-lookup"><span data-stu-id="98158-310">Extend antiforgery</span></span>

<span data-ttu-id="98158-311">開発者は、[IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) 型を使用して各トークンの追加のデータをラウンドトリップすることで、CSRF 対策の動作を拡張できます。</span><span class="sxs-lookup"><span data-stu-id="98158-311">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="98158-312">[GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) メソッドは、フィールドトークンが生成される度に呼ばれ、戻り値は生成されたトークン内に埋め込まれます。</span><span class="sxs-lookup"><span data-stu-id="98158-312">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="98158-313">実装する人は、タイムスタンプ、nonce、その他の値を返しトークンが検証されたときにデータを検証するために [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) を呼ぶことができます。</span><span class="sxs-lookup"><span data-stu-id="98158-313">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="98158-314">クライアントのユーザー名は既に生成されたトークンに埋め込まれているので、この情報を含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="98158-314">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="98158-315">トークンに補足のデータが含まれていても `IAntiForgeryAdditionalDataProvider` が構成されていなければ、補足のデータは検証されません。</span><span class="sxs-lookup"><span data-stu-id="98158-315">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98158-316">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="98158-316">Additional resources</span></span>

* <span data-ttu-id="98158-317">[Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page)(OWASP) の [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))  。</span><span class="sxs-lookup"><span data-stu-id="98158-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
