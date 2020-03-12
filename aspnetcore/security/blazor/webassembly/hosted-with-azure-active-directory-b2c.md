---
title: Azure Active Directory B2C を使用して、ASP.NET Core Blazor Webashosted アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 232a4247f8bea23eec3dc35cba4659c88887124d
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083687"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して、ASP.NET Core Blazor Webashosted アプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

この記事では、認証に[Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用する Blazor WebAssembly スタンドアロンアプリを作成する方法について説明します。

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>AAD B2C でのアプリの登録とソリューションの作成

### <a name="create-a-tenant"></a>テナントの作成

[「チュートリアル: Azure Active Directory B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)する」のガイダンスに従って AAD B2C テナントを作成し、次の情報を記録します。

* AAD B2C インスタンス (たとえば、末尾のスラッシュを含む `https://contoso.b2clogin.com/`)
* テナントドメインの AAD B2C (例 `contoso.onmicrosoft.com`)

### <a name="register-a-server-api-app"></a>サーバー API アプリを登録する

[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*サーバー API アプリ*の AAD アプリを登録します。

1. **[新規登録]** を選択します。
1. アプリの**名前**を指定します (たとえば、 **Blazor Server AAD B2C**)。
1. **サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。** (マルチテナント)。
1. このシナリオでは、*サーバー API アプリ*に**リダイレクト uri**は必要ないため、ドロップダウンは **[Web]** に設定し、リダイレクト uri は入力しないでください。
1. **Offline_access openid に管理者求めるプロンプトを付与** > **アクセス**許可が有効になっていることを確認します。
1. **[登録]** を選択します。

で**API を公開**します。

1. **[Scope の追加]** を選択します。
1. **[Save and continue]\(保存して続行\)** を選択します。
1. **スコープ名**を指定します (たとえば、`API.Access`)。
1. **管理者の同意の表示名**を指定します (たとえば、`Access API`)。
1. **管理者の同意の説明**(`Allows the app to access server app API endpoints.`など) を指定します。
1. **状態**が**有効**に設定されていることを確認します。
1. **[スコープの追加]** を選択します。

次の情報を記録します。

* *サーバー API アプリ*アプリケーション ID (クライアント ID) (たとえば、`11111111-1111-1111-1111-111111111111`)
* ディレクトリ ID (テナント ID) (`222222222-2222-2222-2222-222222222222`など)
* *サーバー API アプリ*アプリ ID URI (たとえば、`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`の場合、Azure portal によって既定値がクライアント ID に設定される可能性があります)
* 既定のスコープ (たとえば、`API.Access`)

### <a name="register-a-client-app"></a>クライアント アプリを登録する

[「チュートリアル: Azure Active Directory B2C にアプリケーションを登録](/azure/active-directory-b2c/tutorial-register-applications)する」のガイダンスに従って、Azure portal の**Azure Active Directory** > **アプリの登録**領域に*クライアントアプリ*の AAD アプリを登録します。

1. **[新規登録]** を選択します。
1. アプリの**名前**を指定します (たとえば、 **Blazor Client AAD B2C**)。
1. **サポートされているアカウントの種類**について**は、任意の組織ディレクトリまたは任意の Id プロバイダーでアカウントを選択します。Azure AD B2C を使用してユーザーを認証します。** (マルチテナント)。
1. **[リダイレクト uri]** ドロップダウンを **[Web]** に設定し、`https://localhost:5001/authentication/login-callback`のリダイレクト uri を指定します。
1. **Offline_access openid に管理者求めるプロンプトを付与** > **アクセス**許可が有効になっていることを確認します。
1. **[登録]** を選択します。

**認証** > **プラットフォームの構成** > **Web**:

1. `https://localhost:5001/authentication/login-callback` の**リダイレクト URI**が存在することを確認します。
1. **暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。
1. アプリの残りの既定値は、このエクスペリエンスで許容されます。
1. **[保存]** を選択します。

**API のアクセス許可**:

1. アプリに > ユーザー **Microsoft Graph**があることを確認**します。読み取り**アクセス許可。
1. [**アクセス許可の追加]、** **[api**の追加] の順に選択します。
1. **[名前]** 列から*サーバー API アプリ*を選択します (たとえば、 **Blazor server AAD B2C**)。
1. **API**の一覧を開きます。
1. API へのアクセスを有効にします (たとえば、`API.Access`)。
1. **[アクセス許可の追加]** を選択します.
1. **[{テナント名} の管理者コンテンツを付与]** ボタンを選択します。 **[はい]** を選択して確定します。

**ホーム** > の**Azure AD B2C** > **ユーザーフロー**:

[サインアップとサインインのユーザーフローを作成する](/azure/active-directory-b2c/tutorial-create-user-flows)

次の情報を記録します。

* *クライアントアプリ*アプリケーション Id (クライアント id) を記録します (たとえば、`33333333-3333-3333-3333-333333333333`)。
* アプリ用に作成されたサインアップおよびサインインユーザーフロー名を記録します (たとえば、`B2C_1_signupsignin`)。

### <a name="create-the-app"></a>アプリを作成する

次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。 フォルダー名もプロジェクトの名前の一部になります。

## <a name="server-app-configuration"></a>サーバーアプリの構成

*このセクションは、ソリューションの**サーバー**アプリに関連しています。*

### <a name="authentication-package"></a>認証パッケージ

ASP.NET Core Web Api の呼び出しを認証および承認するためのサポートは、`Microsoft.AspNetCore.Authentication.AzureAD.UI`によって提供されます。

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>認証サービスのサポート

`AddAuthentication` メソッドは、アプリ内で認証サービスを設定し、JWT ベアラーハンドラーを既定の認証方法として構成します。 `AddAzureADBearer` メソッドは、Azure Active Directory によって出力されるトークンを検証するために必要な JWT ベアラーハンドラー内の特定のパラメーターを設定します。

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` と `UseAuthorization` 次のことを確認します。

* アプリは、受信要求のトークンの解析と検証を試みます。
* 適切な資格情報を使用せずに保護されたリソースにアクセスしようとした要求は失敗します。

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>アプリケーション設定

*Appsettings*ファイルには、アクセストークンの検証に使用される JWT ベアラーハンドラーを構成するためのオプションが含まれています。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast コントローラー

WeatherForecast コントローラー (*Controllers/WeatherForecastController*) は、コントローラーに `[Authorize]` 属性が適用された保護された API を公開します。 次の**点**を理解しておくことが重要です。

* この api コントローラーの `[Authorize]` 属性は、この API を承認されていないアクセスから保護する唯一の方法です。
* Blazor WebAssembly で使用される `[Authorize]` 属性は、アプリに対するヒントとしてのみ機能し、アプリが正しく動作することをユーザーに承認する必要があります。

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>クライアントアプリの構成

*このセクションは、ソリューションの**クライアント**アプリに関連しています。*

### <a name="authentication-package"></a>認証パッケージ

個々の B2C アカウント (`IndividualB2C`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。

`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。

### <a name="authentication-service-support"></a>認証サービスのサポート

ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。 このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。

*Program.cs*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{DEFAULT SCOPE}");
});
```

`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。 アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。

Blazor WebAssembly テンプレートは、`dotnet new` コマンド (`{APP ID URI}/{DEFAULT SCOPE}`) に指定された既定のスコープに対して、セキュリティで保護された API のアクセストークンを要求するようにアプリを自動的に構成します。

既定のアクセストークンスコープは、次のようなアクセストークンスコープの一覧を表します。

* サインイン要求に既定で含まれています。
* 認証直後にアクセストークンをプロビジョニングするために使用されます。

すべてのスコープは、Azure Active Directory ルールごとに同じアプリに属している必要があります。 必要に応じて追加の API アプリ用に追加のスコープを追加できます。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{APP ID URI}/{SCOPE}");
});
```

### <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

### <a name="app-component"></a>アプリコンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay コンポーネント

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData コンポーネント

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authentication/azure-ad-b2c>
* [チュートリアル: Azure Active Directory B2C テナントを作成する](/azure/active-directory-b2c/tutorial-create-tenant)
