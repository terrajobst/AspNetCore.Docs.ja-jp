---
title: ASP.NET Core で特定のスキームを使用して承認する
author: rick-anderson
description: この記事では、複数の認証方法を使用する場合に、特定のスキームに id を制限する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 38da80519b9d5d097c24d38b5a37503174629fc4
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896963"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="8a5d2-103">ASP.NET Core で特定のスキームを使用して承認する</span><span class="sxs-lookup"><span data-stu-id="8a5d2-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="8a5d2-104">シングルページアプリケーション (spa) などの一部のシナリオでは、複数の認証方法を使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="8a5d2-105">たとえば、アプリでは、cookie ベースの認証を使用して、JavaScript 要求のログインと JWT ベアラー認証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="8a5d2-106">場合によっては、アプリに認証ハンドラーのインスタンスが複数存在することがあります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="8a5d2-107">たとえば、1つのに基本 id が格納されている2つの cookie ハンドラーと、multi-factor authentication (MFA) がトリガーされたときに作成されるクッキーハンドラーがあります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="8a5d2-108">ユーザーが追加のセキュリティを必要とする操作を要求したため、MFA がトリガーされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

<span data-ttu-id="8a5d2-109">認証時に認証サービスが構成されると、認証スキームに名前が付けられます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-109">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="8a5d2-110">(例:</span><span class="sxs-lookup"><span data-stu-id="8a5d2-110">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="8a5d2-111">上記のコードでは、2つの認証ハンドラーが追加されています。1つは cookie 用で、もう1つはベアラー用です。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-111">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="8a5d2-112">既定のスキームを指定すると、`HttpContext.User` プロパティがその id に設定されます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-112">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="8a5d2-113">この動作が望ましくない場合は、`AddAuthentication`のパラメーターなしの形式を呼び出して無効にします。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-113">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="8a5d2-114">認証属性を使用したスキームの選択</span><span class="sxs-lookup"><span data-stu-id="8a5d2-114">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="8a5d2-115">承認の時点で、アプリは使用するハンドラーを示します。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-115">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="8a5d2-116">認証スキームのコンマ区切りリストを `[Authorize]`に渡すことによって、アプリが承認するハンドラーを選択します。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-116">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="8a5d2-117">`[Authorize]` 属性は、既定値が構成されているかどうかに関係なく、使用する認証スキームを指定します。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-117">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="8a5d2-118">(例:</span><span class="sxs-lookup"><span data-stu-id="8a5d2-118">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="8a5d2-119">前の例では、cookie ハンドラーとベアラーハンドラーの両方が実行され、現在のユーザーの id を作成して追加する機会があります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-119">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="8a5d2-120">1つのスキームのみを指定すると、対応するハンドラーが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-120">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="8a5d2-121">前のコードでは、"ベアラー" スキームを持つハンドラーだけが実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-121">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="8a5d2-122">Cookie ベースの id は無視されます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-122">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="8a5d2-123">ポリシーを使用したスキームの選択</span><span class="sxs-lookup"><span data-stu-id="8a5d2-123">Selecting the scheme with policies</span></span>

<span data-ttu-id="8a5d2-124">[ポリシー](xref:security/authorization/policies)で目的のスキームを指定する場合は、ポリシーを追加するときに `AuthenticationSchemes` コレクションを設定できます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-124">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="8a5d2-125">前の例では、"Over18" ポリシーは "ベアラー" ハンドラーによって作成された id に対してのみ実行されます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-125">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="8a5d2-126">ポリシーを使用するには、`[Authorize]` 属性の `Policy` プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-126">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="8a5d2-127">複数の認証方式を使用する</span><span class="sxs-lookup"><span data-stu-id="8a5d2-127">Use multiple authentication schemes</span></span>

<span data-ttu-id="8a5d2-128">アプリによっては、複数の種類の認証をサポートする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-128">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="8a5d2-129">たとえば、アプリは Azure Active Directory とユーザーデータベースからユーザーを認証する場合があります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-129">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="8a5d2-130">もう1つの例として、Active Directory フェデレーションサービス (AD FS) と Azure Active Directory B2C の両方からユーザーを認証するアプリがあります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-130">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="8a5d2-131">この場合、アプリはいくつかの発行者から JWT ベアラートークンを受け入れる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-131">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="8a5d2-132">同意するすべての認証スキームを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-132">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="8a5d2-133">たとえば、`Startup.ConfigureServices` の次のコードは、異なる発行者を持つ2つの JWT ベアラー認証スキームを追加します。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-133">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="8a5d2-134">既定の認証スキーム `JwtBearerDefaults.AuthenticationScheme`に登録されている JWT ベアラー認証は1つだけです。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-134">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="8a5d2-135">追加の認証は、一意の認証スキームを使用して登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-135">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="8a5d2-136">次の手順では、両方の認証方式を受け入れるように既定の承認ポリシーを更新します。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-136">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="8a5d2-137">(例:</span><span class="sxs-lookup"><span data-stu-id="8a5d2-137">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="8a5d2-138">既定の承認ポリシーがオーバーライドされると、コントローラーで `[Authorize]` 属性を使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-138">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="8a5d2-139">コントローラーは、最初または2番目の発行者によって発行された JWT の要求を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="8a5d2-139">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
