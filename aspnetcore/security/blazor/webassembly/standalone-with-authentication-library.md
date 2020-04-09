---
title: 認証ライブラリを使用BlazorしてASP.NET Core WebAssembly スタンドアロン アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977042"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>認証ライブラリを使用BlazorしてASP.NET Core WebAssembly スタンドアロン アプリをセキュリティで保護する

[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Azure アクティブ ディレクトリ (AAD) および Azure アクティブ ディレクトリ B2C (AAD B2C) については、このトピックのガイダンスに従わないでください。この目次ノードの AAD および AAD B2C のトピックを参照してください。*

ライブラリを使用Blazor`Microsoft.AspNetCore.Components.WebAssembly.Authentication`する WebAssembly スタンドアロン アプリを作成するには、コマンド シェルで次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual
```

プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。 フォルダー名もプロジェクト名の一部になります。

Visual Studio で[、Web アセンブリBlazorアプリを作成](xref:blazor/get-started)します。 [アプリ内ユーザー アカウントを保存する] オプションを使用して **、[認証**] を **[個別****のユーザー アカウント**] に設定します。

## <a name="authentication-package"></a>認証パッケージ

個々のユーザー アカウントを使用するアプリを作成すると、アプリは自動的にアプリのプロジェクト`Microsoft.AspNetCore.Components.WebAssembly.Authentication`ファイル内のパッケージのパッケージ参照を受け取ります。 パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。

## <a name="authentication-service-support"></a>認証サービスのサポート

ユーザー認証のサポートは、パッケージによって提供される`AddOidcAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.AspNetCore.Components.WebAssembly.Authentication`登録されます。 このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。

*Program.cs:*

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

スタンドアロン アプリの認証サポートは、オープン ID 接続 (OIDC) を使用して提供されます。 この`AddOidcAuthentication`メソッドは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するコールバックを受け入れます。 アプリの構成に必要な値は、OIDC 準拠の IP から取得できます。 通常、オンライン ポータルで発生するアプリを登録するときに値を取得します。

## <a name="access-token-scopes"></a>アクセス トークン のスコープ

Blazor WebAssembly テンプレートは、セキュリティで保護された API のアクセス トークンを要求するようにアプリを自動的に構成しません。 トークンをサインイン フローの一部としてプロビジョニングするには、スコープを の既定のトークン スコープに追加します`OidcProviderOptions`。

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

詳細については、「<xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>」を参照してください。

## <a name="imports-file"></a>ファイルをインポートする

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>アプリ コンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>コンポーネントをリダイレクトします。

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>ログインディスプレイコンポーネント

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他のリソース

* [追加のアクセス トークンを要求する](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
