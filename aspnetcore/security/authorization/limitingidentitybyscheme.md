---
title: ASP.NET Core で特定のスキームを使用して承認する
author: rick-anderson
description: この記事では、複数の認証方法を使用する場合に、特定のスキームに id を制限する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 9c173a4589279b03bc12b4b7dea594fae88cf471
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928382"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>ASP.NET Core で特定のスキームを使用して承認する

シングルページアプリケーション (spa) などの一部のシナリオでは、複数の認証方法を使用するのが一般的です。 たとえば、アプリでは、cookie ベースの認証を使用して、JavaScript 要求のログインと JWT ベアラー認証を行うことができます。 場合によっては、アプリに認証ハンドラーのインスタンスが複数存在することがあります。 たとえば、1つのに基本 id が格納されている2つの cookie ハンドラーと、multi-factor authentication (MFA) がトリガーされたときに作成されるクッキーハンドラーがあります。 ユーザーが追加のセキュリティを必要とする操作を要求したため、MFA がトリガーされる可能性があります。 ユーザーが MFA を必要とするリソースを要求したときに MFA を適用する方法の詳細については、「MFA を使用した GitHub の問題の[保護」セクション](https://github.com/aspnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)を参照してください。

認証時に認証サービスが構成されると、認証スキームに名前が付けられます。 例:

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

上記のコードでは、2つの認証ハンドラーが追加されています。1つは cookie 用で、もう1つはベアラー用です。

>[!NOTE]
>既定のスキームを指定すると、`HttpContext.User` プロパティがその id に設定されます。 この動作が望ましくない場合は、`AddAuthentication`のパラメーターなしの形式を呼び出して無効にします。

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>認証属性を使用したスキームの選択

承認の時点で、アプリは使用するハンドラーを示します。 認証スキームのコンマ区切りリストを `[Authorize]`に渡すことによって、アプリが承認するハンドラーを選択します。 `[Authorize]` 属性は、既定値が構成されているかどうかに関係なく、使用する認証スキームを指定します。 例:

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

前の例では、cookie ハンドラーとベアラーハンドラーの両方が実行され、現在のユーザーの id を作成して追加する機会があります。 1つのスキームのみを指定すると、対応するハンドラーが実行されます。

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

前のコードでは、"ベアラー" スキームを持つハンドラーだけが実行されます。 Cookie ベースの id は無視されます。

## <a name="selecting-the-scheme-with-policies"></a>ポリシーを使用したスキームの選択

[ポリシー](xref:security/authorization/policies)で目的のスキームを指定する場合は、ポリシーを追加するときに `AuthenticationSchemes` コレクションを設定できます。

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

前の例では、"Over18" ポリシーは "ベアラー" ハンドラーによって作成された id に対してのみ実行されます。 ポリシーを使用するには、`[Authorize]` 属性の `Policy` プロパティを設定します。

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>複数の認証方式を使用する

アプリによっては、複数の種類の認証をサポートする必要がある場合があります。 たとえば、アプリは Azure Active Directory とユーザーデータベースからユーザーを認証する場合があります。 もう1つの例として、Active Directory フェデレーションサービス (AD FS) と Azure Active Directory B2C の両方からユーザーを認証するアプリがあります。 この場合、アプリはいくつかの発行者から JWT ベアラートークンを受け入れる必要があります。

同意するすべての認証スキームを追加します。 たとえば、`Startup.ConfigureServices` の次のコードは、異なる発行者を持つ2つの JWT ベアラー認証スキームを追加します。

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
> 既定の認証スキーム `JwtBearerDefaults.AuthenticationScheme`に登録されている JWT ベアラー認証は1つだけです。 追加の認証は、一意の認証スキームを使用して登録する必要があります。

次の手順では、両方の認証方式を受け入れるように既定の承認ポリシーを更新します。 例:

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

既定の承認ポリシーがオーバーライドされると、コントローラーで `[Authorize]` 属性を使用できるようになります。 コントローラーは、最初または2番目の発行者によって発行された JWT の要求を受け入れます。

::: moniker-end
