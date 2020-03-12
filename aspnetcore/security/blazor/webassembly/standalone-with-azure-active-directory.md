---
title: Azure Active Directory を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 76bcac29d86a236e56c0eaea24a694c4845ecbcf
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083585"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Azure Active Directory を使用して ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

認証に[Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/)を使用する、Blazor WebAssembly スタンドアロンアプリを作成するには、次のようにします。

[AAD テナントと web アプリケーションを作成し](/azure/active-directory/develop/v2-overview)ます。

Azure portal の**Azure Active Directory** > **アプリの登録**領域に AAD アプリを登録します。

1. アプリの**名前**を指定します ( **Blazor クライアント AAD**など)。
1. **サポートされているアカウントの種類**を選択します。 この組織の**ディレクトリにあるアカウント**は、このエクスペリエンスのためにのみ選択できます。
1. **[リダイレクト uri]** ドロップダウンを **[Web]** に設定し、`https://localhost:5001/authentication/login-callback`のリダイレクト uri を指定します。
1. アクセス**許可 > ** **openid and offline_access permissions に管理者求めるプロンプトを付与する** チェックボックスをオフにします。
1. **[登録]** を選択します。

**認証** > **プラットフォームの構成** > **Web**:

1. `https://localhost:5001/authentication/login-callback` の**リダイレクト URI**が存在することを確認します。
1. **暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。
1. アプリの残りの既定値は、このエクスペリエンスで許容されます。
1. **[保存]** を選択します。

次の情報を記録します。

* アプリケーション ID (クライアント ID) (たとえば、`11111111-1111-1111-1111-111111111111`)
* ディレクトリ ID (テナント ID) (`22222222-2222-2222-2222-222222222222`など)

次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。 フォルダー名もプロジェクトの名前の一部になります。

## <a name="authentication-package"></a>認証パッケージ

職場または学校のアカウント (`SingleOrg`) を使用するようにアプリを作成すると、アプリは[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)(`Microsoft.Authentication.WebAssembly.Msal`) のパッケージ参照を自動的に受け取ります。 このパッケージには、アプリがユーザーを認証し、保護された Api を呼び出すためのトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクトファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

前のパッケージ参照の `{VERSION}` を、<xref:blazor/get-started> に記載されている `Microsoft.AspNetCore.Blazor.Templates` パッケージのバージョンに置き換えます。

`Microsoft.Authentication.WebAssembly.Msal` パッケージは、`Microsoft.AspNetCore.Components.WebAssembly.Authentication` パッケージを推移的にアプリに追加します。

## <a name="authentication-service-support"></a>認証サービスのサポート

ユーザー認証のサポートは、`Microsoft.Authentication.WebAssembly.Msal` パッケージによって提供される `AddMsalAuthentication` 拡張メソッドを使用して、サービスコンテナーに登録されます。 このメソッドは、アプリが Id プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。

*Program.cs*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。 アプリを構成するために必要な値は、アプリを登録するときに Azure Portal AAD 構成から取得できます。

Blazor WebAssembly テンプレートでは、セキュリティで保護された API のアクセストークンを要求するようにアプリが自動的に構成されるわけではありません。 サインインフローの一部としてトークンをプロビジョニングするには、`MsalProviderOptions`の既定のアクセストークンスコープにスコープを追加します。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> 既定のアクセストークンのスコープは、`{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` (`11111111-1111-1111-1111-111111111111/API.Access`など) の形式である必要があります。 スキームまたはスキームとホストがスコープ設定に提供されている場合 (Azure Portal に示されているように)、*サーバー API アプリ*から承認されて*いない 401*の応答を受信すると、*クライアントアプリ*は未処理の例外をスローします。

## <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a>アプリコンポーネント

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin コンポーネント

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay コンポーネント

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>認証コンポーネント

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authentication/azure-active-directory/index>
