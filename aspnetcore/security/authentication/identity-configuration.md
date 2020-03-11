---
title: ASP.NET Core Id の構成
author: AdrienTorris
description: ASP.NET Core Id の既定値について理解し、カスタム値を使用するように Id プロパティを構成する方法について説明します。
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 823182bed2cb953e07f9374d135868aeb2be9c60
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652694"
---
# <a name="configure-aspnet-core-identity"></a>ASP.NET Core Id の構成

ASP.NET Core Id では、パスワードポリシー、ロックアウト、cookie の構成などの設定に既定値を使用します。 これらの設定は、`Startup` クラスでオーバーライドできます。

## <a name="identity-options"></a>Id オプション

Id システムを構成するために使用できるオプションを[指定します](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)。 `IdentityOptions` は `AddIdentity` または `AddDefaultIdentity`を呼び出し**た後**に設定する必要があります。

### <a name="claims-identity"></a>要求 Id

[ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)は、次の表に示すプロパティを使用して[ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions)を指定します。

| プロパティ | 説明 | 既定値 |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | ロール要求に使用されるクレームの種類を取得または設定します。 | [ClaimTypes。 Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | セキュリティスタンプ要求に使用される要求の種類を取得または設定します。 | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | ユーザー識別子要求に使用されるクレームの種類を取得または設定します。 | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | ユーザー名要求に使用される要求の種類を取得または設定します。 | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>アウト

ロックアウトは、 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_)メソッドで設定されます。

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

上記のコードは、`Login` Id テンプレートに基づいています。 

ロックアウトオプションは `StartUp.ConfigureServices`で設定されます。

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

上記のコードでは、 [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) [オプション](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)を既定値に設定しています。

成功した認証は失敗したアクセス試行数がリセットされ、時計をリセットします。

[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)は、テーブルに示されているプロパティを使用して、そのプロパティを指定し[ます。](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)

| プロパティ | 説明 | 既定値 |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | 新しいユーザーをロックアウトできるかどうかを決定します。 | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | ロックアウトが発生したときにユーザーがロックアウトされる時間。 | 5 分 |
| [Max失敗した Accessattempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | ロックアウトが有効になっている場合に、ユーザーがロックアウトされるまでのアクセス試行の失敗回数。 | 5 |

### <a name="password"></a>Password

既定では、Id を使用するには、パスワードに大文字と小文字、数字、英数字以外の文字を含める必要があります。 パスワードの長さは6文字以上である必要があります。 [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)は `Startup.ConfigureServices`で設定できます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)は、テーブルに表示されるプロパティを使用して指定し[ます。](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)

::: moniker range=">= aspnetcore-2.0"

| プロパティ | 説明 | 既定値 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | では、パスワードに0-9 を指定する必要があります。 | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | パスワードの最小長。 | 6 |
| [RequireLowercase 文字](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | パスワードに小文字が必要です。 | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | パスワードには英数字以外の文字が必要です。 | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | ASP.NET Core 2.0 以降にのみ適用されます。<br><br> パスワードに含まれる個別の文字数が必要です。 | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | パスワードには大文字が必要です。 | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| プロパティ | 説明 | 既定値 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | では、パスワードに0-9 を指定する必要があります。 | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | パスワードの最小長。 | 6 |
| [RequireLowercase 文字](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | パスワードに小文字が必要です。 | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | パスワードには英数字以外の文字が必要です。 | `true` |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | パスワードには大文字が必要です。 | `true` |

::: moniker-end

### <a name="sign-in"></a>サインイン

次のコードは、`SignIn` 設定 (既定値) を設定します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions)は、表に示されているプロパティを使用して指定し[ます。](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)

| プロパティ | 説明 | 既定値 |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | サインインするには、確認済みの電子メールが必要です。 | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | サインインするには、確認済みの電話番号が必要です。 | `false` |

### <a name="tokens"></a>トークン

[トークンオプション。トークン](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)は、テーブルに示されているプロパティを使用して[tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions)を指定します。

|                                                        プロパティ                                                         |                                                                                      説明                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       認証子を使用して2要素サインインを検証するために使用される `AuthenticatorTokenProvider` を取得または設定します。                                       |
|       [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     電子メール変更の確認メールで使用されるトークンを生成するために使用される `ChangeEmailTokenProvider` を取得または設定します。                                     |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      電話番号を変更するときに使用するトークンを生成するために使用される `ChangePhoneNumberTokenProvider` を取得または設定します。                                      |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             アカウントの確認メールで使用されるトークンを生成するために使用されるトークンプロバイダーを取得または設定します。                                              |
|     [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | パスワードリセット電子メールで使用されるトークンを生成するために使用される[IUserTwoFactorTokenProvider\<tuser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1)を取得または設定します。 |
|                    [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                プロバイダーの名前として使用されるキーを使用して[ユーザートークンプロバイダー](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor)を構築するために使用されます。                 |

### <a name="user"></a>User

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ユーザー設定。ユーザー](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)は、テーブルに示されているプロパティを使用して[useroptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions)を指定します。

| プロパティ | 説明 | 既定値 |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | ユーザー名に使用できる文字。 | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | 各ユーザーが一意の電子メールを持っている必要があります。 | `false` |

### <a name="cookie-settings"></a>Cookie の設定

`Startup.ConfigureServices`でアプリの cookie を構成します。 `AddIdentity` または `AddDefaultIdentity`を呼び出し**た後**に[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__)を呼び出す必要があります。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

詳細については、「 [Cookieauthenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)」を参照してください。

## <a name="password-hasher-options"></a>パスワードの Hasher オプション

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> は、パスワードハッシュのオプションを取得して設定します。

| オプション | 説明 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | 新しいパスワードをハッシュするときに使用する互換性モード。 既定値は <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> です。 *形式マーカー*と呼ばれるハッシュされたパスワードの最初のバイトは、パスワードのハッシュに使用されるハッシュアルゴリズムのバージョンを指定します。 ハッシュに対してパスワードを確認する場合、<xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> 方法では、最初のバイトに基づいて正しいアルゴリズムが選択されます。 クライアントは、パスワードのハッシュに使用されたアルゴリズムのバージョンに関係なく認証を行うことができます。 互換性モードを設定すると、*新しいパスワード*のハッシュに影響します。 |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | PBKDF2 を使用してパスワードをハッシュするときに使用されるイテレーションの数。 この値は、<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> が <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>に設定されている場合にのみ使用されます。 値は正の整数である必要があり、既定値は `10000`です。 |

次の例では、<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> が `Startup.ConfigureServices`で `12000` に設定されています。

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
