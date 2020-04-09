---
title: Azure アクティブBlazorディレクトリ B2C を使用して、ASP.NETコア Web アセンブリホスト アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 4c79f7530e18b9f70262812a64abb55122701d15
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977159"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Azure アクティブBlazorディレクトリ B2C を使用して、ASP.NETコア Web アセンブリホスト アプリをセキュリティで保護する

[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

この記事では、認証に Azure Blazor [Active Directory (AAD) B2C](/azure/active-directory-b2c/overview)を使用する WebAssembly スタンドアロン アプリを作成する方法について説明します。

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>AAD B2C でアプリを登録してソリューションを作成する

### <a name="create-a-tenant"></a>テナントの作成

チュートリアル: AAD [B2C テナントを作成](/azure/active-directory-b2c/tutorial-create-tenant)し、次の情報を記録する Azure Active Directory B2C テナントを作成するのガイダンスに従ってください。

* AAD B2C インスタンス (`https://contoso.b2clogin.com/`末尾のスラッシュを含むなど)
* AAD B2C テナント ドメイン`contoso.onmicrosoft.com`(たとえば)

### <a name="register-a-server-api-app"></a>サーバー API アプリの登録

[チュートリアル: Azure の Azure のポータル](/azure/active-directory-b2c/tutorial-register-applications)の Azure アクティブ ディレクトリ**アプリ登録**領域でサーバー API アプリの AAD*アプリ*を登録する Azure Active **Directory** > B2C でアプリケーションを登録するのガイダンスに従ってください。

1. **[新規登録]** を選択します。
1. アプリの**名前**を指定します (たとえば、**Blazorサーバー AAD B2C)。**
1. [**サポートされているアカウントの種類]** で **、[組織ディレクトリ内のアカウント] または任意の ID プロバイダを選択します。Azure AD B2C を使用してユーザーを認証する場合。** (マルチテナント) このエクスペリエンスの場合。
1. このシナリオでは *、Server API アプリ*は**リダイレクト URI**を必要としないので、ドロップ ダウンセットを**Web**に設定したままにして、リダイレクト URI を入力しないでください。
1. **[権限** > **を付与する管理者に、オープンに対する権限の追加とoffline_accessのアクセス許可**が有効になっていることを確認します。
1. **[登録]** を選択します。

公開**API :**

1. **[Scope の追加]** を選択します。
1. **[Save and continue]\(保存して続行\)** を選択します。
1. **スコープ名**(たとえば)`API.Access`を指定します。
1. **管理者の同意**表示名を指定します (たとえば`Access API`)。
1. **管理者の同意の説明**(たとえば)`Allows the app to access server app API endpoints.`を入力します。
1. **[状態**] が **[有効]** に設定されていることを確認します。
1. **[スコープの追加]** を選択します。

以下の情報を記録します。

* *サーバー API アプリ*アプリケーション ID (クライアント ID) `11111111-1111-1111-1111-111111111111`(例: )
* アプリ ID URI (、、、または指定したカスタム値など`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111``api://11111111-1111-1111-1111-111111111111`)
* ディレクトリ ID (テナント ID) `222222222-2222-2222-2222-222222222222`(たとえば)
* *サーバー API アプリ*アプリ ID URI (`https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`たとえば、Azure ポータルの値がクライアント ID に既定値になる場合など)
* 既定のスコープ (たとえば`API.Access`)

### <a name="register-a-client-app"></a>クライアント アプリを登録する

[チュートリアル: Azure の Azure のポータル](/azure/active-directory-b2c/tutorial-register-applications)の Azure アクティブ ディレクトリ**アプリ登録**領域でクライアント*アプリ*の AAD アプリを登録するもう一度 Azure Active **Directory** > B2C でアプリケーションを登録するのガイダンスに従います。

1. **[新規登録]** を選択します。
1. アプリの**名前**を指定します (たとえば、**Blazorクライアント AAD B2C)。**
1. [**サポートされているアカウントの種類]** で **、[組織ディレクトリ内のアカウント] または任意の ID プロバイダを選択します。Azure AD B2C を使用してユーザーを認証する場合。** (マルチテナント) このエクスペリエンスの場合。
1. [**リダイレクト URI]** ドロップダウン リストを **[Web]** に`https://localhost:5001/authentication/login-callback`設定したままにし、 のリダイレクト URI を指定します。
1. **[権限** > **を付与する管理者に、オープンに対する権限の追加とoffline_accessのアクセス許可**が有効になっていることを確認します。
1. **[登録]** を選択します。

**認証** > **プラットフォーム構成** > **Web**で:

1. リダイレクト**URI**が`https://localhost:5001/authentication/login-callback`存在することを確認します。
1. [**暗黙的な許可**] で、[**アクセス トークン**] および [ID トークン] のチェック ボックスをオン**にします**。
1. アプリの残りの既定値は、このエクスペリエンスに対して許容されます。
1. **[保存]** を選択します。

**API 権限**で:

1. アプリに**グラフ** > **ユーザーの読み取り**アクセス許可があることを確認します。
1. [**アクセス許可の追加**とそれに続く**マイ API] の**選択を行います。
1. **[名前**] 列から *[サーバー API] アプリ*を選択します (**Blazorたとえば、サーバー AAD B2C)。**
1. **API**リストを開きます。
1. API へのアクセスを有効にします`API.Access`(たとえば、 )。
1. **[アクセス許可の追加]** を選択します.
1. [{**テナント名} の管理者コンテンツを付与**] ボタンを選択します。 **[はい]** を選択して確定します。

**ホーム** > **Azure AD B2C** > **ユーザー フロー**:

[サインアップとサインイン ユーザー フローを作成する](/azure/active-directory-b2c/tutorial-create-user-flows)

少なくとも、**アプリケーション クレーム** > **の表示名**ユーザー属性を選択して`context.User.Identity.Name``LoginDisplay`、コンポーネント (*Shared/LoginDisplay.razor*) に設定します。

以下の情報を記録します。

* クライアント*アプリケーション*アプリケーション ID (クライアント ID) `33333333-3333-3333-3333-333333333333`(たとえば) を記録します。
* アプリ用に作成されたサインアップユーザーおよびサインイン ユーザー のフロー名 (など`B2C_1_signupsignin`) を記録します。

### <a name="create-the-app"></a>アプリを作成する

次のコマンドのプレースホルダーを、前に記録された情報に置き換えて、コマンド シェルでコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。 フォルダー名もプロジェクト名の一部になります。

> [!NOTE]
> アプリ ID URI を`app-id-uri`オプションに渡しますが、クライアント アプリで構成の変更が必要になる場合があります([アクセス トークンのスコープ](#access-token-scopes)セクションで説明します)。

## <a name="server-app-configuration"></a>サーバー アプリの構成

*このセクションは、ソリューションの**サーバー**アプリに関連します。*

### <a name="authentication-package"></a>認証パッケージ

ASP.NETコア Web API への呼び出しの認証と承認の`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`サポートは、 によって提供されます。

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>認証サービスのサポート

この`AddAuthentication`メソッドは、アプリ内で認証サービスを設定し、JWT Bearer ハンドラーを既定の認証方法として構成します。 この`AddAzureADB2CBearer`メソッドは、Azure Active Directory B2C によって生成されたトークンを検証するために必要な JWT ベアラー ハンドラーの特定のパラメーターを設定します。

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`を`UseAuthorization`確認し、次のことを確認します。

* アプリは、受信要求でトークンを解析し、検証しようとします。
* 適切な資格情報を使用せずに保護されたリソースにアクセスしようとする要求は失敗します。

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

デフォルトでは、`User.Identity.Name`は設定されません。

要求の種類から値を受け取るように`name`アプリを構成するには、in の[トークン検証パラメーター.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType)を<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions>構成`Startup.ConfigureServices`します。

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>アプリケーション設定

*appsettings.json*ファイルには、アクセス トークンの検証に使用される JWT ベアラー ハンドラーを構成するためのオプションが含まれています。

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>天気予報コントローラ

天気予報コントローラ (*コントローラ/天気予報コントローラ.cs)* は、コントローラに適用された`[Authorize]`属性を持つ保護された API を公開します。 次の点を理解することが**重要**です。

* この`[Authorize]`API コントローラの属性は、この API を不正アクセスから保護する唯一のものです。
* WebAssembly アプリでBlazor使用される属性は`[Authorize]`、アプリが正しく動作するためにユーザーに承認される必要があることを示すヒントとしてのみ機能します。

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

## <a name="client-app-configuration"></a>クライアント アプリの構成

*このセクションは、ソリューションの**クライアント**アプリに関連します。*

### <a name="authentication-package"></a>認証パッケージ

個々の B2C アカウント (`IndividualB2C`) を使用するアプリが作成されると、アプリは自動的に Microsoft[認証ライブラリ](/azure/active-directory/develop/msal-overview)( )`Microsoft.Authentication.WebAssembly.Msal`のパッケージ参照を受け取ります。 パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。

パッケージ`Microsoft.Authentication.WebAssembly.Msal`は推移的にパッケージを`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリに追加します。

### <a name="authentication-service-support"></a>認証サービスのサポート

ユーザー認証のサポートは、パッケージによって提供される`AddMsalAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.Authentication.WebAssembly.Msal`登録されます。 このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。

*Program.cs:*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

この`AddMsalAuthentication`メソッドは、アプリの認証に必要なパラメーターを構成するコールバックを受け入れます。 アプリを登録するときに、Azure ポータル AAD 構成からアプリを構成するために必要な値を取得できます。

### <a name="access-token-scopes"></a>アクセス トークン のスコープ

既定のアクセス トークン スコープは、次のアクセス トークン スコープのリストを表します。

* 既定では、サインイン要求に含まれます。
* 認証後すぐにアクセス トークンをプロビジョニングするために使用されます。

すべてのスコープは、Azure アクティブ ディレクトリ ルールごとに同じアプリに属している必要があります。 必要に応じて、追加の API アプリにスコープを追加できます。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure Portal がスコープ URI を提供し、API から*401 未承認*の応答を受け取ったときに**アプリが未処理の例外をスロー**する場合は、スキームとホストを含まないスコープ URI を使用してみてください。 たとえば、Azure ポータルでは、次のいずれかのスコープ URI 形式を提供できます。
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> スキームとホストを使用せずにスコープ URI を指定します。
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。

### <a name="imports-file"></a>ファイルをインポートする

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>アプリ コンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>コンポーネントをリダイレクトします。

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>ログインディスプレイコンポーネント

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>コンポーネントをフェッチします。

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>アプリを実行する

サーバー プロジェクトからアプリを実行します。 Visual Studio を使用する場合は、**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->
[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他のリソース

* [追加のアクセス トークンを要求する](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [チュートリアル:Azure Active Directory B2C テナントの作成](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft ID プラットフォームのドキュメント](/azure/active-directory/develop/)
