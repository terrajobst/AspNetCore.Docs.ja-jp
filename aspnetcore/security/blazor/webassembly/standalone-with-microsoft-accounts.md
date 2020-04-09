---
title: マイクロソフト アカウントをBlazor使用してASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977081"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>マイクロソフト アカウントをBlazor使用してASP.NETコア Web アセンブリスタンドアロン アプリをセキュリティで保護する

[ハビエル・カルバロ・ネルソン](https://github.com/javiercn)と[ルーク・レイサム](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

認証にBlazor [Azure アクティブ ディレクトリ (AAD) を持つ Microsoft アカウント](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)を使用する WebAssembly スタンドアロン アプリを作成するには、次の手順を実行します。

1. [AAD テナントと Web アプリケーションを作成する](/azure/active-directory/develop/v2-overview)

   Azure ポータルの Azure**アクティブ ディレクトリ** > **アプリ登録**領域に AAD アプリを登録します。

   1\. アプリの**名前**を指定します (たとえば、**Blazorクライアント AAD)。**<br>
   2\. [**サポートされるアカウントの種類**] で、[**組織ディレクトリのアカウント ]** を選択します。<br>
   3\. [**リダイレクト URI]** ドロップダウン リストを **[Web]** に`https://localhost:5001/authentication/login-callback`設定したままにし、 のリダイレクト URI を指定します。<br>
   4\. [**アクセス許可** > **の付与管理者へのアクセス許可のオープンとoffline_accessのアクセス許可]** チェック ボックスを無効にします。<br>
   5\. **[登録]** を選択します。

   **認証** > **プラットフォーム構成** > **Web**で:

   1\. リダイレクト**URI**が`https://localhost:5001/authentication/login-callback`存在することを確認します。<br>
   2\. [**暗黙的な許可**] で、[**アクセス トークン**] および [ID トークン] のチェック ボックスをオン**にします**。<br>
   3\. アプリの残りの既定値は、このエクスペリエンスに対して許容されます。<br>
   4\. **[保存]** を選択します。

   アプリケーション ID (クライアント ID) (たとえば`11111111-1111-1111-1111-111111111111`) を記録します。

1. 次のコマンドのプレースホルダーを、前に記録された情報に置き換えて、コマンド シェルでコマンドを実行します。

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   プロジェクト フォルダーが存在しない場合にプロジェクト フォルダーを作成する出力場所を指定するには、コマンドに出力オプションをパス (など`-o BlazorSample`) で含めます。 フォルダー名もプロジェクト名の一部になります。

アプリを作成したら、次の作業を行う必要があります。

* Microsoft アカウントを使用してアプリにログインします。
* アプリを正しく構成した場合は、スタンドアロンBlazorアプリと同じ方法で Microsoft API のアクセス トークンを要求します。 詳細については、「[クイック スタート : Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)」を参照してください。

## <a name="authentication-package"></a>認証パッケージ

職場または学校のアカウントを使用するアプリが作成されると`SingleOrg`、アプリは自動的に[Microsoft 認証ライブラリ](/azure/active-directory/develop/msal-overview)( )`Microsoft.Authentication.WebAssembly.Msal`のパッケージ参照を受け取ります。 パッケージには、アプリがユーザーを認証し、保護された API を呼び出すトークンを取得するのに役立つ一連のプリミティブが用意されています。

アプリに認証を追加する場合は、アプリのプロジェクト ファイルにパッケージを手動で追加します。

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

上記`{VERSION}`のパッケージリファレンスを<xref:blazor/get-started>、この記事に示されているパッケージ`Microsoft.AspNetCore.Blazor.Templates`のバージョンに置き換えます。

パッケージ`Microsoft.Authentication.WebAssembly.Msal`は推移的にパッケージを`Microsoft.AspNetCore.Components.WebAssembly.Authentication`アプリに追加します。

## <a name="authentication-service-support"></a>認証サービスのサポート

ユーザー認証のサポートは、パッケージによって提供される`AddMsalAuthentication`拡張メソッドを使用してサービス コンテナーに`Microsoft.Authentication.WebAssembly.Msal`登録されます。 このメソッドは、アプリが ID プロバイダー (IP) と対話するために必要なすべてのサービスを設定します。

*Program.cs:*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

この`AddMsalAuthentication`メソッドは、アプリの認証に必要なパラメーターを構成するコールバックを受け入れます。 アプリを登録するときに、Microsoft アカウント構成からアプリを構成するために必要な値を取得できます。

## <a name="access-token-scopes"></a>アクセス トークン のスコープ

Blazor WebAssembly テンプレートは、セキュリティで保護された API のアクセス トークンを要求するようにアプリを自動的に構成しません。 トークンをサインイン フローの一部としてプロビジョニングするには、スコープを の既定のアクセス トークン スコープに追加`MsalProviderOptions`します。

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

## <a name="imports-file"></a>ファイルをインポートする

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Index ページ

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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
* [クイック スタート: アプリケーションを Microsoft ID プラットフォームに登録する](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [クイック スタート: Web API を公開するようにアプリケーションを構成する](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
