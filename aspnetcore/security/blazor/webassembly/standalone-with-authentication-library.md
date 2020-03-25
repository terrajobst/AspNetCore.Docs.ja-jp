---
title: 認証ライブラリを使用して、ASP.NET Core Blazor Webasスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: ea50d94835b044f9c3d6a0561868f081d32cb62a
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219007"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>認証ライブラリを使用して、ASP.NET Core Blazor Webasスタンドアロンアプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Azure Active Directory (AAD) と Azure Active Directory B2C (AAD B2C) については、このトピックのガイダンスに従ってください。この目次ノードの AAD と AAD B2C のトピックを参照してください。*

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` ライブラリを使用する Blazor WebAssembly スタンドアロンアプリを作成するには、コマンドシェルで次のコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au Individual
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。 フォルダー名もプロジェクトの名前の一部になります。

Visual Studio で[Blazor WebAssembly を作成](xref:blazor/get-started)します。 [**ユーザーアカウントをアプリ内に格納**する] オプションを使用して、**認証**を**個々のユーザーアカウント**に設定します。

## <a name="authentication-package"></a>認証パッケージ

個々のユーザーアカウントを使用するようにアプリを作成すると、アプリは、アプリのプロジェクトファイル内の `Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージのパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。

## <a name="authentication-service-support"></a>認証サービスのサポート

ユーザー認証のサポートは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージによって提供される `AddOidcAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。 このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。

*Program.cs*

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

スタンドアロンアプリの認証サポートは、Open ID Connect (OIDC) を使用して提供されます。 `AddOidcAuthentication` メソッドは、OIDC を使用してアプリを認証するために必要なパラメーターを構成するためのコールバックを受け入れます。 アプリの構成に必要な値は、OIDC に準拠している IP から取得できます。 アプリを登録するときに値を取得します。これは通常、オンラインポータルで実行されます。

## <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>アプリコンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay コンポーネント

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
