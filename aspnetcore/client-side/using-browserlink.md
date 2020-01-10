---
title: ASP.NET Core のブラウザーリンク
author: ncarandini
description: ブラウザーリンクが Visual Studio の機能であり、開発環境を1つ以上の web ブラウザーにリンクする方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828271"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core のブラウザーリンク

[Nicolò Carandini](https://github.com/ncarandini)、 [Mike Wasson](https://github.com/MikeWasson)、 [Tom Dykstra](https://github.com/tdykstra)

ブラウザーリンクは Visual Studio の機能です。 開発環境と1つ以上の web ブラウザーの間に通信チャネルを作成します。 ブラウザーリンクを使用して、複数のブラウザーで web アプリを一度に更新することができます。これは、ブラウザー間のテストに役立ちます。

## <a name="browser-link-setup"></a>ブラウザーリンクの設定

::: moniker range=">= aspnetcore-3.0"

プロジェクトに、 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージを追加します。 ASP.NET Core Razor Pages または MVC プロジェクトの場合は、「<xref:mvc/views/view-compilation>」で説明されているように、Razor (*cshtml*) ファイルのランタイムコンパイルも有効にします。 Razor 構文の変更は、ランタイムコンパイルが有効になっている場合にのみ適用されます。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

ASP.NET Core 2.0 プロジェクトを ASP.NET Core 2.1 に変換し、 [AspNetCore メタパッケージ](xref:fundamentals/metapackage-app)に移行する場合は、ブラウザーリンク機能用の[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージをインストールしてください。 ASP.NET Core 2.1 プロジェクトテンプレートでは、既定で `Microsoft.AspNetCore.App` メタパッケージが使用されます。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **Web アプリケーション**、**空**、および **web API** プロジェクトテンプレートでは、[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) へのパッケージ参照を含む [Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage) が使用されています。 そのため、`Microsoft.AspNetCore.All` メタパッケージを使用する場合は、ブラウザーリンクを使用できるようにするための追加の操作は必要ありません。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **Web アプリケーション**プロジェクトテンプレートには、 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージのパッケージ参照が含まれています。 その他のプロジェクトの種類では、`Microsoft.VisualStudio.Web.BrowserLink`にパッケージ参照を追加する必要があります。

::: moniker-end

### <a name="configuration"></a>の構成

`Startup.Configure` メソッドで `UseBrowserLink` を呼び出します。

```csharp
app.UseBrowserLink();
```

`UseBrowserLink` の呼び出しは、通常、開発環境でブラウザーリンクのみを有効にする `if` ブロック内に配置されます。 例:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

詳細については、「<xref:fundamentals/environments>」を参照してください。

## <a name="how-to-use-browser-link"></a>ブラウザーリンクを使用する方法

ASP.NET Core のプロジェクトを開いている場合、Visual Studio では、**デバッグターゲット** ツールバーコントロールの横に ブラウザーリンク ツールバーコントロールが表示されます。

![ブラウザーリンクのドロップダウンメニュー](using-browserlink/_static/browserLink-dropdown-menu.png)

[ブラウザーリンク] ツールバーコントロールからは、次の操作を実行できます。

* 複数のブラウザーで web アプリを一度に更新します。
* **ブラウザーリンクダッシュボード**を開きます。
* **ブラウザーリンク**を有効または無効にします。 注: Visual Studio では、ブラウザーリンクは既定で無効になっています。
* [CSS 自動同期](#enable-or-disable-css-auto-sync)を有効または無効にします。

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>複数のブラウザーで web アプリを一度に更新する

プロジェクトを開始するときに起動する1つの web ブラウザーを選択するには、 **[デバッグターゲット]** ツールバーコントロールのドロップダウンメニューを使用します。

![F5 ドロップダウンメニュー](using-browserlink/_static/debug-target-dropdown-menu.png)

一度に複数のブラウザーを開く、次のように選択します**を参照しています...** 同じドロップダウン リストから。 <kbd>Ctrl</kbd>キーを押しながら目的のブラウザーを選択し、 **[参照]** をクリックします。

![多数のブラウザーを一度に開く](using-browserlink/_static/open-many-browsers-at-once.png)

次のスクリーンショットは、インデックスビューが開いている Visual Studio と、2つの開いているブラウザーを示しています。

![2つのブラウザーを使用した同期の例](using-browserlink/_static/sync-with-two-browsers-example.png)

プロジェクトに接続されているブラウザーを表示するには、[ブラウザーリンク] ツールバーコントロールの上にマウスポインターを移動します。

![ホバーヒント](using-browserlink/_static/hoover-tip.png)

インデックスビューを変更すると、ブラウザーのリンクの [更新] ボタンをクリックすると、接続されているすべてのブラウザーが更新されます。

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

ブラウザーリンクは、Visual Studio の外部から起動するブラウザーでも動作し、アプリの URL に移動します。

### <a name="the-browser-link-dashboard"></a>ブラウザーリンクダッシュボード

ブラウザーリンクのドロップダウンメニューから**ブラウザーリンクダッシュボード**ウィンドウを開いて、開いているブラウザーとの接続を管理します。

![browserslink-ダッシュボード](using-browserlink/_static/open-browserlink-dashboard.png)

ブラウザーが接続されていない場合は、 **[ブラウザーで表示]** リンクを選択して、非デバッグセッションを開始できます。

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

それ以外の場合は、接続されているブラウザーが、各ブラウザーに表示されているページへのパスと共に表示されます。

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

個々のブラウザー名をクリックして、そのブラウザーのみを更新することもできます。

### <a name="enable-or-disable-browser-link"></a>ブラウザーリンクを有効または無効にする

ブラウザーリンクを無効にした後で再度有効にする場合は、ブラウザーを更新して再接続する必要があります。

### <a name="enable-or-disable-css-auto-sync"></a>CSS 自動同期を有効または無効にする

CSS 自動同期が有効になっていると、CSS ファイルに変更を加えたときに、接続されているブラウザーが自動的に更新されます。

## <a name="how-it-works"></a>機能のしくみ

ブラウザーリンクは[SignalR](xref:signalr/introduction)を使用して、Visual Studio とブラウザーの間の通信チャネルを作成します。 ブラウザーリンクが有効になっている場合、Visual Studio は複数のクライアント (ブラウザー) が接続できる SignalR サーバーとして機能します。 ブラウザーリンクは、ASP.NET Core 要求パイプラインにミドルウェアコンポーネントも登録します。 このコンポーネントは、サーバーからのすべてのページ要求に特別な `<script>` 参照を挿入します。 スクリプト参照を表示するには、ブラウザーで **[ソースの表示]** を選択し、`<body>` タグコンテンツの最後までスクロールします。

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

ソースファイルは変更されていません。 ミドルウェアコンポーネントは、スクリプト参照を動的に挿入します。

ブラウザー側のコードはすべて JavaScript であるため、ブラウザープラグインを必要とせずにサポート SignalR すべてのブラウザーで動作します。
