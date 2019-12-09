---
title: ASP.NET Core の認証の概要
author: mjrousos
description: ASP.NET Core での認証について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 12/04/2019
uid: security/authentication/index
ms.openlocfilehash: 40b2fb59b96486435a2ec0a7d69bee5ab4a814d2
ms.sourcegitcommit: 76d7fff62014c3db02564191ab768acea00f1b26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852715"
---
# <a name="overview-of-aspnet-core-authentication"></a><span data-ttu-id="071ad-103">ASP.NET Core の認証の概要</span><span class="sxs-lookup"><span data-stu-id="071ad-103">Overview of ASP.NET Core authentication</span></span>

<span data-ttu-id="071ad-104">作成者: [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="071ad-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="071ad-105">認証は、ユーザーの ID を突き止めるプロセスです。</span><span class="sxs-lookup"><span data-stu-id="071ad-105">Authentication is the process of determining a user's identity.</span></span> <span data-ttu-id="071ad-106">[認可](xref:security/authorization/introduction)は、ユーザーがリソースにアクセスできるかどうかを判断するプロセスです。</span><span class="sxs-lookup"><span data-stu-id="071ad-106">[Authorization](xref:security/authorization/introduction) is the process of determining whether a user has access to a resource.</span></span> <span data-ttu-id="071ad-107">ASP.NET Core では、認証は `IAuthenticationService` によって処理されます。これは、認証[ミドルウェア](xref:fundamentals/middleware/index)によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-107">In ASP.NET Core, authentication is handled by the `IAuthenticationService`, which is used by authentication [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="071ad-108">認証サービスは、登録済みの認証ハンドラーを使用して、認証関連のアクションを完了します。</span><span class="sxs-lookup"><span data-stu-id="071ad-108">The authentication service uses registered authentication handlers to complete authentication-related actions.</span></span> <span data-ttu-id="071ad-109">認証関連のアクションの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="071ad-109">Examples of authentication-related actions include:</span></span>

* <span data-ttu-id="071ad-110">ユーザーを認証する。</span><span class="sxs-lookup"><span data-stu-id="071ad-110">Authenticating a user.</span></span>
* <span data-ttu-id="071ad-111">認証されていないユーザーが制限されたリソースにアクセスしようとしたときに応答する。</span><span class="sxs-lookup"><span data-stu-id="071ad-111">Responding when an unauthenticated user tries to access a restricted resource.</span></span>

<span data-ttu-id="071ad-112">登録済みの認証ハンドラーとその構成オプションは、"スキーム" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="071ad-112">The registered authentication handlers and their configuration options are called "schemes".</span></span>

<span data-ttu-id="071ad-113">認証方式は `Startup.ConfigureServices` に認証サービスを登録することによって指定されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-113">Authentication schemes are specified by registering authentication services in `Startup.ConfigureServices`:</span></span>

* <span data-ttu-id="071ad-114">`services.AddAuthentication` の呼び出しの後にスキーム固有の拡張メソッド (`AddJwtBearer` や `AddCookie` など) を呼び出すことによって行います。</span><span class="sxs-lookup"><span data-stu-id="071ad-114">By calling a scheme-specific extension method after a call to `services.AddAuthentication` (such as `AddJwtBearer` or `AddCookie`, for example).</span></span> <span data-ttu-id="071ad-115">これらの拡張メソッドは [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) を使用してスキームを適切な設定で登録します。</span><span class="sxs-lookup"><span data-stu-id="071ad-115">These extension methods use [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) to register schemes with appropriate settings.</span></span>
* <span data-ttu-id="071ad-116">あまり一般的ではありませんが、[AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) を直接呼び出す方法もあります。</span><span class="sxs-lookup"><span data-stu-id="071ad-116">Less commonly, by calling [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) directly.</span></span>

<span data-ttu-id="071ad-117">たとえば、次のコードでは、Cookie と JWT ベアラー認証スキームの認証サービスとハンドラーが登録されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-117">For example, the following code registers authentication services and handlers for cookie and JWT bearer authentication schemes:</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

<span data-ttu-id="071ad-118">`AddAuthentication` パラメーター `JwtBearerDefaults.AuthenticationScheme` は、特定のスキームが要求されていない場合に既定で使用されるスキームの名前です。</span><span class="sxs-lookup"><span data-stu-id="071ad-118">The `AddAuthentication` parameter `JwtBearerDefaults.AuthenticationScheme` is the name of the scheme to use by default when a specific scheme isn't requested.</span></span>

<span data-ttu-id="071ad-119">複数のスキームが使用される場合、認可ポリシー (または認可属性) で、ユーザーの認証時に使用する[認証スキーム (複数可) を指定する](xref:security/authorization/limitingidentitybyscheme)ことができます。</span><span class="sxs-lookup"><span data-stu-id="071ad-119">If multiple schemes are used, authorization policies (or authorization attributes) can [specify the authentication scheme (or schemes)](xref:security/authorization/limitingidentitybyscheme) they depend on to authenticate the user.</span></span> <span data-ttu-id="071ad-120">上記の例では、Cookie 認証スキームを使用する場合に名前を指定しています (既定では `CookieAuthenticationDefaults.AuthenticationScheme` ですが、`AddCookie` を呼び出すときに別の名前を指定することもできます)。</span><span class="sxs-lookup"><span data-stu-id="071ad-120">In the example above, the cookie authentication scheme could be used by specifying its name (`CookieAuthenticationDefaults.AuthenticationScheme` by default, though a different name could be provided when calling `AddCookie`).</span></span>

<span data-ttu-id="071ad-121">場合によっては、`AddAuthentication` の呼び出しが、他の拡張メソッドによって自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="071ad-121">In some cases, the call to `AddAuthentication` is automatically made by other extension methods.</span></span> <span data-ttu-id="071ad-122">たとえば、[ASP.NET Core の ID](xref:security/authentication/identity) を使用する場合、`AddAuthentication` が内部的に呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-122">For example, when using [ASP.NET Core Identity](xref:security/authentication/identity), `AddAuthentication` is called internally.</span></span>

<span data-ttu-id="071ad-123">認証ミドルウェアは、アプリの `IApplicationBuilder` の <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 拡張メソッドを呼び出すことによって `Startup.Configure` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-123">The Authentication middleware is added in `Startup.Configure` by calling the <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> extension method on the app's `IApplicationBuilder`.</span></span> <span data-ttu-id="071ad-124">`UseAuthentication` を呼び出すと、以前に登録された認証スキームを使用するミドルウェアが登録されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-124">Calling `UseAuthentication` registers the middleware which uses the previously registered authentication schemes.</span></span> <span data-ttu-id="071ad-125">認証されているユーザーに依存するすべてのミドルウェアの前に `UseAuthentication` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="071ad-125">Call `UseAuthentication` before any middleware that depends on users being authenticated.</span></span> <span data-ttu-id="071ad-126">エンドポイント ルーティングを使用する場合は、次のタイミングで `UseAuthentication` の呼び出しを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="071ad-126">When using endpoint routing, the call to `UseAuthentication` must go:</span></span>

* <span data-ttu-id="071ad-127">ルート情報が認証の決定に使用できるように、`UseRouting` の後。</span><span class="sxs-lookup"><span data-stu-id="071ad-127">After `UseRouting`, so that route information is available for authentication decisions.</span></span>
* <span data-ttu-id="071ad-128">エンドポイントにアクセスする前にユーザーが認証されるように、`UseEndpoints` の前。</span><span class="sxs-lookup"><span data-stu-id="071ad-128">Before `UseEndpoints`, so that users are authenticated before accessing the endpoints.</span></span>

## <a name="authentication-concepts"></a><span data-ttu-id="071ad-129">認証の概念</span><span class="sxs-lookup"><span data-stu-id="071ad-129">Authentication Concepts</span></span>

### <a name="authentication-scheme"></a><span data-ttu-id="071ad-130">認証スキーム</span><span class="sxs-lookup"><span data-stu-id="071ad-130">Authentication scheme</span></span>

<span data-ttu-id="071ad-131">認証スキームは、次のものに対応する名前です。</span><span class="sxs-lookup"><span data-stu-id="071ad-131">An authentication scheme is a name which corresponds to:</span></span>

* <span data-ttu-id="071ad-132">認証ハンドラー。</span><span class="sxs-lookup"><span data-stu-id="071ad-132">An authentication handler.</span></span>
* <span data-ttu-id="071ad-133">ハンドラーの特定のインスタンスを構成するためのオプション。</span><span class="sxs-lookup"><span data-stu-id="071ad-133">Options for configuring that specific instance of the handler.</span></span>

<span data-ttu-id="071ad-134">スキームは、関連付けられたハンドラーの認証、チャレンジ、禁止動作を参照するためのメカニズムとして役立ちます。</span><span class="sxs-lookup"><span data-stu-id="071ad-134">Schemes are useful as a mechanism for referring to the authentication, challenge, and forbid behaviors of the associated handler.</span></span> <span data-ttu-id="071ad-135">たとえば、認証ポリシーでは、スキーム名を使用して、ユーザーの認証に使用する認証スキーム (またはスキーム) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="071ad-135">For example, an authorization policy can use scheme names to specify which authentication scheme (or schemes) should be used to authenticate the user.</span></span> <span data-ttu-id="071ad-136">認証を構成する場合は、既定の認証スキームを指定するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="071ad-136">When configuring authentication, it's common to specify the default authentication scheme.</span></span> <span data-ttu-id="071ad-137">リソースが特定のスキームを要求しない限り、既定のスキームが使用されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-137">The default scheme is used unless a resource requests a specific scheme.</span></span> <span data-ttu-id="071ad-138">また、次のことも可能です。</span><span class="sxs-lookup"><span data-stu-id="071ad-138">It's also possible to:</span></span>

* <span data-ttu-id="071ad-139">認証、チャレンジ、禁止アクションに使用する別の既定のスキームを指定する。</span><span class="sxs-lookup"><span data-stu-id="071ad-139">Specify different default schemes to use for authenticate, challenge, and forbid actions.</span></span>
* <span data-ttu-id="071ad-140">[ポリシー スキーム](xref:security/authentication/policyschemes)を使用して、複数のスキームを 1 つに結合する。</span><span class="sxs-lookup"><span data-stu-id="071ad-140">Combine multiple schemes into one using [policy schemes](xref:security/authentication/policyschemes).</span></span>

### <a name="authentication-handler"></a><span data-ttu-id="071ad-141">認証ハンドラー</span><span class="sxs-lookup"><span data-stu-id="071ad-141">Authentication handler</span></span>

<span data-ttu-id="071ad-142">認証ハンドラーは:</span><span class="sxs-lookup"><span data-stu-id="071ad-142">An authentication handler:</span></span>

* <span data-ttu-id="071ad-143">スキームの動作を実装する型です。</span><span class="sxs-lookup"><span data-stu-id="071ad-143">Is a type that implements the behavior of a scheme.</span></span>
* <span data-ttu-id="071ad-144"><xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> または <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> から派生しています。</span><span class="sxs-lookup"><span data-stu-id="071ad-144">Is derived from <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> or <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span>
* <span data-ttu-id="071ad-145">ユーザーを認証するための主要な役割を担います。</span><span class="sxs-lookup"><span data-stu-id="071ad-145">Has the primary responsibility to authenticate users.</span></span>

<span data-ttu-id="071ad-146">認証スキームの構成と受信要求コンテキストに基づき、認証ハンドラーは:</span><span class="sxs-lookup"><span data-stu-id="071ad-146">Based on the authentication scheme's configuration and the incoming request context, authentication handlers:</span></span>

* <span data-ttu-id="071ad-147">認証が成功した場合に、ユーザーの ID を表す <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> オブジェクトを構築します。</span><span class="sxs-lookup"><span data-stu-id="071ad-147">Construct <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket> objects representing the user's identity if authentication is successful.</span></span>
* <span data-ttu-id="071ad-148">認証に失敗した場合は、'no result' または 'failure' を返します。</span><span class="sxs-lookup"><span data-stu-id="071ad-148">Return 'no result' or 'failure' if authentication is unsuccessful.</span></span>
* <span data-ttu-id="071ad-149">ユーザーがリソースにアクセスしようとするときのチャレンジおよび禁止アクションの方法を用意しています。</span><span class="sxs-lookup"><span data-stu-id="071ad-149">Have methods for challenge and forbid actions for when users attempt to access resources:</span></span>
  * <span data-ttu-id="071ad-150">アクセスが許可されていない (禁止)。</span><span class="sxs-lookup"><span data-stu-id="071ad-150">They are unauthorized to access (forbid).</span></span>
  * <span data-ttu-id="071ad-151">認証されていない (チャレンジ)。</span><span class="sxs-lookup"><span data-stu-id="071ad-151">When they are unauthenticated (challenge).</span></span>

### <a name="authenticate"></a><span data-ttu-id="071ad-152">認証</span><span class="sxs-lookup"><span data-stu-id="071ad-152">Authenticate</span></span>

<span data-ttu-id="071ad-153">認証スキームの認証アクションは、要求コンテキストに基づいてユーザーの ID を構築する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="071ad-153">An authentication scheme's authenticate action is responsible for constructing the user's identity based on request context.</span></span> <span data-ttu-id="071ad-154">認証が成功したかどうかを示す <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> を返します。成功した場合は、認証チケットに含まれるユーザーの ID です。</span><span class="sxs-lookup"><span data-stu-id="071ad-154">It returns an <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult> indicating whether authentication was successful and, if so, the user's identity in an authentication ticket.</span></span> <span data-ttu-id="071ad-155">以下を参照してください。`HttpContext.AuthenticateAsync`</span><span class="sxs-lookup"><span data-stu-id="071ad-155">See `HttpContext.AuthenticateAsync`.</span></span> <span data-ttu-id="071ad-156">認証の例を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="071ad-156">Authenticate examples include:</span></span>

* <span data-ttu-id="071ad-157">Cookie からユーザーの ID を構築する Cookie 認証スキーム。</span><span class="sxs-lookup"><span data-stu-id="071ad-157">A cookie authentication scheme constructing the user's identity from cookies.</span></span>
* <span data-ttu-id="071ad-158">ユーザーの ID を構築するための JWT ベアラー トークンを逆シリアル化し、検証する JWT ベアラー スキーム。</span><span class="sxs-lookup"><span data-stu-id="071ad-158">A JWT bearer scheme deserializing and validating a JWT bearer token to construct the user's identity.</span></span>

### <a name="challenge"></a><span data-ttu-id="071ad-159">課題</span><span class="sxs-lookup"><span data-stu-id="071ad-159">Challenge</span></span>

<span data-ttu-id="071ad-160">認証チャレンジは、認証されていないユーザーが認証を必要とするエンドポイントを要求したときに、認可によって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-160">An authentication challenge is invoked by Authorization when an unauthenticated user requests an endpoint that requires authentication.</span></span> <span data-ttu-id="071ad-161">認証チャレンジが発行されるのは、匿名ユーザーが制限されたリソースを要求した場合や、ログイン リンクをクリックした場合などです。</span><span class="sxs-lookup"><span data-stu-id="071ad-161">An authentication challenge is issued, for example, when an anonymous user requests a restricted resource or clicks on a login link.</span></span> <span data-ttu-id="071ad-162">認可では、指定された認証スキームを使用してチャレンジが呼び出されます。何も指定されていない場合は、既定値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-162">Authorization invokes a challenge using the specified authentication scheme(s), or the default if none is specified.</span></span> <span data-ttu-id="071ad-163">以下を参照してください。`HttpContext.ChallengeAsync`</span><span class="sxs-lookup"><span data-stu-id="071ad-163">See `HttpContext.ChallengeAsync`.</span></span> <span data-ttu-id="071ad-164">認証チャレンジの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="071ad-164">Authentication challenge examples include:</span></span>

* <span data-ttu-id="071ad-165">ログイン ページにユーザーをリダイレクトする Cookie 認証スキーム。</span><span class="sxs-lookup"><span data-stu-id="071ad-165">A cookie authentication scheme redirecting the user to a login page.</span></span>
* <span data-ttu-id="071ad-166">`www-authenticate: bearer` ヘッダーを持つ 401 結果を返す JWT ベアラースキーム。</span><span class="sxs-lookup"><span data-stu-id="071ad-166">A JWT bearer scheme returning a 401 result with a `www-authenticate: bearer` header.</span></span>

<span data-ttu-id="071ad-167">チャレンジ アクションでは、要求されたリソースへのアクセスに使用する認証メカニズムがユーザーに通知されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-167">A challenge action should let the user know what authentication mechanism to use to access the requested resource.</span></span>

### <a name="forbid"></a><span data-ttu-id="071ad-168">禁止</span><span class="sxs-lookup"><span data-stu-id="071ad-168">Forbid</span></span>

<span data-ttu-id="071ad-169">認証されたユーザーがアクセスを許可されていないリソースにアクセスしようとすると、認可によって認証スキームの禁止アクションが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="071ad-169">An authentication scheme's forbid action is called by Authorization when an authenticated user attempts to access a resource they are not permitted to access.</span></span> <span data-ttu-id="071ad-170">以下を参照してください。`HttpContext.ForbidAsync`</span><span class="sxs-lookup"><span data-stu-id="071ad-170">See `HttpContext.ForbidAsync`.</span></span> <span data-ttu-id="071ad-171">認証の禁止の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="071ad-171">Authentication forbid examples include:</span></span>
* <span data-ttu-id="071ad-172">アクセスが禁止されていることを示すページにユーザーをリダイレクトする Cookie 認証スキーム。</span><span class="sxs-lookup"><span data-stu-id="071ad-172">A cookie authentication scheme redirecting the user to a page indicating access was forbidden.</span></span>
* <span data-ttu-id="071ad-173">403 結果を返す JWT ベアラースキーム。</span><span class="sxs-lookup"><span data-stu-id="071ad-173">A JWT bearer scheme returning a 403 result.</span></span>
* <span data-ttu-id="071ad-174">ユーザーがリソースへのアクセスを要求できるページにリダイレクトするカスタム認証スキーム。</span><span class="sxs-lookup"><span data-stu-id="071ad-174">A custom authentication scheme redirecting to a page where the user can request access to the resource.</span></span>

<span data-ttu-id="071ad-175">禁止アクションを使用すると、ユーザーは次のことを知ることができます。</span><span class="sxs-lookup"><span data-stu-id="071ad-175">A forbid action can let the user know:</span></span>

* <span data-ttu-id="071ad-176">認証されている。</span><span class="sxs-lookup"><span data-stu-id="071ad-176">They are authenticated.</span></span>
* <span data-ttu-id="071ad-177">要求されたリソースにアクセスすることは許可されていない。</span><span class="sxs-lookup"><span data-stu-id="071ad-177">They aren't permitted to access the requested resource.</span></span>

<span data-ttu-id="071ad-178">チャレンジと禁止の違いについては、次のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="071ad-178">See the following links for differences between challenge and forbid:</span></span>

* <span data-ttu-id="071ad-179">[運用リソースハンドラーを使用したチャレンジと禁止](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)。</span><span class="sxs-lookup"><span data-stu-id="071ad-179">[Challenge and forbid with an operational resource handler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler).</span></span>
* <span data-ttu-id="071ad-180">[チャレンジと禁止の違い](xref:security/authorization/secure-data#challenge)。</span><span class="sxs-lookup"><span data-stu-id="071ad-180">[Differences between challenge and forbid](xref:security/authorization/secure-data#challenge).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="071ad-181">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="071ad-181">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
