---
title: Microsoft アカウントを使用して、ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219260"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Microsoft アカウントを使用して、ASP.NET Core Blazor のスタンドアロンアプリをセキュリティで保護する

[Javier Calvarro jeannine](https://github.com/javiercn)と[Luke latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

認証に[Microsoft アカウントと Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する Blazor WebAssembly スタンドアロンアプリを作成するには、次のようにします。

1. [AAD テナントと web アプリケーションを作成する](/azure/active-directory/develop/v2-overview)

   Azure portal の**Azure Active Directory** > **アプリの登録**領域に AAD アプリを登録します。

   1\. アプリの**名前**を指定します ( **Blazor クライアント AAD**など)。<br>
   2\. **[サポートされているアカウントの種類]** で、**任意の組織ディレクトリのアカウント**を選択します。<br>
   3\. **[リダイレクト uri]** ドロップダウンを **[Web]** に設定し、`https://localhost:5001/authentication/login-callback`のリダイレクト uri を指定します。<br>
   4\. アクセス**許可 > ** **openid and offline_access permissions に管理者求めるプロンプトを付与する** チェックボックスをオフにします。<br>
   5\. **[登録]** を選択します。

   **認証** > **プラットフォームの構成** > **Web**:

   1\. `https://localhost:5001/authentication/login-callback` の**リダイレクト URI**が存在することを確認します。<br>
   2\. **暗黙の許可**では、**アクセストークン**と**ID トークン**のチェックボックスをオンにします。<br>
   3\. アプリの残りの既定値は、このエクスペリエンスで許容されます。<br>
   4\. **[保存]** を選択します。

   アプリケーション ID (クライアント ID) を記録します (たとえば、`11111111-1111-1111-1111-111111111111`)。

1. 次のコマンドのプレースホルダーを、前に記録した情報に置き換え、コマンドシェルでコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   プロジェクトフォルダーが存在しない場合に作成する出力場所を指定するには、コマンドにパスを含む出力オプション (`-o BlazorSample`など) を含めます。 フォルダー名もプロジェクトの名前の一部になります。

アプリを作成すると、次のことができるようになります。

* Microsoft アカウントを使用してアプリにログインします。
* アプリが正しく構成されている場合は、スタンドアロンの Blazor アプリの場合と同じ方法を使用して、Microsoft Api のアクセストークンを要求します。 詳細については、「[クイックスタート: Web api を公開するようにアプリケーションを構成](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)する」を参照してください。

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication` メソッドは、コールバックを受け入れて、アプリの認証に必要なパラメーターを構成します。 アプリの構成に必要な値は、アプリを登録するときに Microsoft アカウントの構成から取得できます。

## <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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

* [クイックスタート: Microsoft id プラットフォームにアプリケーションを登録する](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [クイックスタート: web Api を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
