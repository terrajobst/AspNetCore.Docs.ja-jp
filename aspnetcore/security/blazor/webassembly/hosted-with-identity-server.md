---
title: アイデンティティサーバーを使用BlazorしてASP.NETコア WebAssembly ホストアプリケーションを保護する
author: guardrex
description: '[Id サーバー](https://identityserver.io/) Blazorバックエンドを使用する Visual Studio 内から認証を使用して新しいホストされたアプリを作成するには'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501279"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>アイデンティティサーバーを使用BlazorしてASP.NETコア WebAssembly ホストアプリケーションを保護する

[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

ユーザーと APIBlazor呼び出しを認証するために[IdentityServer を](https://identityserver.io/)使用する Visual Studio で新しいホストされたアプリを作成するには、次の手順を実行します。

1. 新しい**BlazorWeb アセンブリ**アプリを作成するのにには、Visual Studio を使用します。 詳細については、「<xref:blazor/get-started>」を参照してください。
1. [**新しいBlazorアプリの作成**] ダイアログで、[**認証**] セクションの **[変更**] を選択します。
1. [**個々のユーザー アカウントと**それに続く**OK] を**選択します。
1. [**詳細**] セクションの **[core ホストASP.NET]** チェックボックスをオンにします。
1. **[作成]** ボタンを選択します。

コマンド シェルでアプリを作成するには、次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。 フォルダー名もプロジェクト名の一部になります。

## <a name="server-app-configuration"></a>サーバー アプリの構成

次のセクションでは、認証サポートが含まれている場合のプロジェクトへの追加について説明します。

### <a name="startup-class"></a>スタートアップ クラス

この`Startup`クラスには、次の追加機能があります。

* `Startup.ConfigureServices`: 

  * 既定の UI を持つ ID:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdServer の上<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>にいくつかの既定のASP.NETコア規約を設定する追加のヘルパー メソッドを持つ IdServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * IdentityServer によって<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>生成された JWT トークンを検証するようにアプリを構成する追加のヘルパー メソッドを使用した認証:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure`: 

  * 要求の資格情報を検証し、要求コンテキストでユーザーを設定する認証ミドルウェア:

    ```csharp
    app.UseAuthentication();
    ```

  * オープン ID 接続 (OIDC) エンドポイントを公開する Id サーバーミドルウェア:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>認証の追加

ヘルパー<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>メソッドは、ASP.NETコア シナリオ用[の IdentityServer](https://identityserver.io/)を構成します。 IdentityServer は、アプリのセキュリティに関する懸念を処理するための強力で拡張可能なフレームワークです。 IdentityServer は、最も一般的なシナリオで不要な複雑さを公開します。 したがって、一連の規則と構成オプションが提供され、開始点として適しています。 認証の変更が必要になると、IdentityServerの全機能を使用して、アプリの要件に合わせて認証をカスタマイズできます。

### <a name="addidentityserverjwt"></a>を追加します。

ヘルパー<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>メソッドは、既定の認証ハンドラーとしてアプリのポリシー スキームを構成します。 ポリシーは、Identity URL スペース`/Identity`内のサブパスにルーティングされるすべてのリクエストを Identity が処理できるように構成されています。 他<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>のすべての要求を処理します。 さらに、このメソッドは次のとおりです。

* `{APPLICATION NAME}API`既定のスコープが`{APPLICATION NAME}API`.
* アプリに対して IdentityServer によって発行されたトークンを検証するように JWT ベアラー トークン ミドルウェアを構成します。

### <a name="weatherforecastcontroller"></a>天気予報コントローラ

(*コントローラ /天気予報コントローラ.cs*)では、[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)属性がクラスに適用されます。 `WeatherForecastController` この属性は、リソースにアクセスするために、デフォルト・ポリシーに基づいてユーザーが許可される必要があることを示します。 デフォルトの許可ポリシーは、前述のポリシー スキーム<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>に設定されたデフォルト認証方式を使用するように構成されます。 ヘルパー メソッドは、<xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>アプリへの要求の既定のハンドラーとして構成されます。

### <a name="applicationdbcontext"></a>コンテキスト

(*データ/アプリケーションDbContext.cs*) では<xref:Microsoft.EntityFrameworkCore.DbContext>、同じことが IdentityServer のスキーマを<xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>含むように拡張される例外を除いて、IDENTITY でも使用されます。 `ApplicationDbContext` <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> から派生しています。

データベース スキーマの完全な制御を取得するには、使用可能な Id<xref:Microsoft.EntityFrameworkCore.DbContext>クラスのいずれかを継承し、メソッドを呼び出`builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)`すことによって、Identity`OnModelCreating`スキーマを含めるコンテキストを構成します。

### <a name="oidcconfigurationcontroller"></a>オイドコンセプコンコントローラ

`OidcConfigurationController` (*コントローラ/OidcConfigurationController.cs)では、* クライアント エンドポイントが OIDC パラメータを提供するようにプロビジョニングされます。

### <a name="app-settings-files"></a>アプリ設定ファイル

プロジェクト ルートのアプリ設定ファイル (*appsettings.json*)`IdentityServer`では、構成済みクライアントの一覧を説明します。 次の例では、1 つのクライアントがあります。 クライアント名はアプリ名に対応し、規則によって OAuth`ClientId`パラメーターにマップされます。 プロファイルは、構成されているアプリの種類を示します。 プロファイルは、サーバーの構成プロセスを簡素化する規則を駆動するために内部的に使用されます。 <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

開発環境アプリ設定ファイル (*アプリ設定) でDevelopment.json*) は、プロジェクトルート`IdentityServer`で、トークンの署名に使用されるキーについて説明します。 <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>クライアント アプリの構成

### <a name="authentication-package"></a>認証パッケージ

個々のユーザー アカウント (`Individual`) を使用するアプリが作成されると、アプリは自動的に`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリのプロジェクト ファイル内のパッケージのパッケージ参照を受け取ります。 パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。

### <a name="api-authorization-support"></a>API 承認のサポート

ユーザー認証のサポートは、パッケージ内に用意されている拡張メソッドによってサービス コンテナーに接続されます`Microsoft.AspNetCore.Components.WebAssembly.Authentication`。 このメソッドは、アプリが既存の承認システムと対話するために必要なすべてのサービスを設定します。

```csharp
builder.Services.AddApiAuthorization();
```

既定では、アプリの構成を規則に従って`_configuration/{client-id}`から読み込みます。 慣例により、クライアント ID はアプリのアセンブリ名に設定されます。 この URL は、オプションを指定してオーバーロードを呼び出すことによって、別のエンドポイントを指すように変更できます。

### <a name="imports-file"></a>ファイルをインポートする

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>アプリ コンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>コンポーネントをリダイレクトします。

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>ログインディスプレイコンポーネント

コンポーネント (*共有 /ログインディスプレイ.razor*)`MainLayout`はコンポーネント (*共有/メインレイアウト.カミソリ*) でレンダリングされ、次の動作を管理します。 `LoginDisplay`

* 認証されたユーザーの場合:
  * 現在のユーザー名を表示します。
  * コア ID のユーザー プロファイル ページへのリンクASP.NET提供します。
  * アプリからログアウトするためのボタンを提供します。
* 匿名ユーザーの場合:
  * 登録するオプションを提供します。
  * ログインオプションを提供します。

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>コンポーネントをフェッチします。

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>アプリを実行する

サーバー プロジェクトからアプリを実行します。 Visual Studio を使用する場合は、**ソリューション エクスプローラー**でサーバー プロジェクトを選択し、ツール バーの [**実行**] ボタンを選択するか、[**デバッグ**] メニューからアプリを起動します。

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他のリソース

* [追加のアクセス トークンを要求する](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
