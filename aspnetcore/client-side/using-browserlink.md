---
title: ASP.NET Core の Browser Link
author: ncarandini
description: Browser Link が Visual Studio の機能であり、これにより開発環境を 1 つまたは複数の Web ブラウザーにリンクする方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: 19cc3c2ed91bd9e05df3c036123c78ecbf81fcc0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647102"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core の Browser Link

作成者: [Nicolò Carandini](https://github.com/ncarandini)、[Mike Wasson](https://github.com/MikeWasson)、[Tom Dykstra](https://github.com/tdykstra)

Browser Link は Visual Studio の機能です。 これにより、開発環境と 1 つまたは複数の Web ブラウザー間に通信チャネルが作成されます。 Browser Link を使用して、複数のブラウザーで Web アプリを一度に更新することができます。これは、ブラウザー間のテストに役立ちます。

## <a name="browser-link-setup"></a>Browser Link のセットアップ

::: moniker range=">= aspnetcore-3.0"

[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) パッケージを自分のプロジェクトに追加します。 ASP.NET Core Razor Pages または MVC プロジェクトでは、「<xref:mvc/views/view-compilation>」で説明されているように、Razor ( *.cshtml*) ファイルのランタイム コンパイルを有効にすることもできます。 Razor 構文の変更は、ランタイム コンパイルが有効になっている場合にのみ適用されます。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

ASP.NET Core 2.0 プロジェクトを ASP.NET Core 2.1 に変換し、[Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に切り替える場合は、Browser Link 機能のために [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) パッケージをインストールします。 ASP.NET Core 2.1 プロジェクト テンプレートでは、既定で `Microsoft.AspNetCore.App` メタパッケージが使用されます。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 の **Web アプリケーション**、**空**、**Web API** プロジェクト テンプレートでは、[Microsoft.AspNetCore.All メタパッケージ](xref:fundamentals/metapackage)が使用されます。これには [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 用のパッケージ参照が含まれています。 そのため、`Microsoft.AspNetCore.All` メタパッケージを使用する場合、Browser Link を使用できるようにするために、追加の操作を行う必要ありません。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x の **Web アプリケーション** プロジェクト テンプレートには、[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) パッケージ用のパッケージ参照が含まれています。 その他のプロジェクトの種類では、`Microsoft.VisualStudio.Web.BrowserLink` にパッケージ参照を追加する必要があります。

::: moniker-end

### <a name="configuration"></a>構成

`Startup.Configure` メソッドで `UseBrowserLink` を呼び出します。

```csharp
app.UseBrowserLink();
```

`UseBrowserLink` の呼び出しは、通常、開発環境で Browser Link のみを有効にする `if` ブロック内に配置されます。 次に例を示します。

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

詳細については、「<xref:fundamentals/environments>」を参照してください。

## <a name="how-to-use-browser-link"></a>Browser Link の使用方法

ASP.NET Core プロジェクトが開いている場合、Visual Studio では、 **[デバッグ ターゲット]** ツール バー コントロールの横に Browser Link ツール バー コントロールが表示されます。

![Browser Link のドロップダウン メニュー](using-browserlink/_static/browserLink-dropdown-menu.png)

Browser Link のツール バー コントロールからは、次の操作を実行できます。

* 複数のブラウザーで Web アプリを一度に更新する。
* **[Browser link ダッシュボード]** を開く。
* **Browser Link** を有効/無効にする。 メモ:Visual Studio では、Browser Link は既定で無効になっています。
* [[CSS 自動同期]](#enable-or-disable-css-auto-sync) を有効/無効にする。

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>複数のブラウザーで Web アプリを一度に更新する

プロジェクトを開始するときに起動する 1 つの Web ブラウザーを選択するには、 **[デバッグ ターゲット]** ツール バー コントロールのドロップダウン メニューを使用します。

![F5 ドロップダウン メニュー](using-browserlink/_static/debug-target-dropdown-menu.png)

複数のブラウザーを一度に開くには、同じドロップダウンから **[ブラウザーの選択]** を選択します。 <kbd>Ctrl</kbd> キーを押しながら目的のブラウザーを選択し、 **[参照]** をクリックします。

![多数のブラウザーを一度に開く](using-browserlink/_static/open-many-browsers-at-once.png)

次のスクリーンショットでは、インデックス ビューが開いている Visual Studio と、2 つの開いているブラウザーが示されています。

![2 つのブラウザーとの同期の例](using-browserlink/_static/sync-with-two-browsers-example.png)

プロジェクトに接続されているブラウザーを表示するには、Browser Link ツール バー コントロールの上にマウス ポインターを置きます。

![ポイント時のヒント](using-browserlink/_static/hoover-tip.png)

インデックス ビューを変更して、Browser Link の [更新] ボタンをクリックすると、接続されているすべてのブラウザーが更新されます。

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Browser Link は、Visual Studio 外から起動するブラウザーでも動作し、アプリの URL に移動します。

### <a name="the-browser-link-dashboard"></a>Browser link ダッシュボード

Browser Link のドロップダウン メニューから **[Browser link ダッシュボード]** ウィンドウを開いて、開いているブラウザーとの接続を管理します。

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

ブラウザーが接続されていない場合は、 **[ブラウザーで表示]** リンクを選択することで、デバッグ以外のセッションを開始することができます。

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

それ以外の場合は、接続されているブラウザーが、各ブラウザーに表示されているページへのパスと共に表示されます。

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

個々のブラウザー名をクリックして、そのブラウザーのみを更新することもできます。

### <a name="enable-or-disable-browser-link"></a>Browser Link を有効/無効にする

Browser Link を無効にした後で再度有効にする場合は、ブラウザーを更新して再接続する必要があります。

### <a name="enable-or-disable-css-auto-sync"></a>CSS 自動同期を有効/無効にする

CSS 自動同期が有効になっている場合、CSS ファイルに変更を加えたときに、接続されているブラウザーが自動的に更新されます。

## <a name="how-it-works"></a>しくみ

Browser Link では、[SignalR](xref:signalr/introduction) を使用して、Visual Studio とブラウザー間の通信チャネルを作成します。 Browser Link が有効になっている場合、Visual Studio は複数のクライアント (ブラウザー) が接続できる SignalR サーバーとして機能します。 Browser Link では、ASP.NET Core 要求パイプラインにミドルウェア コンポーネントも登録されます。 このコンポーネントにより、サーバーからすべてのページ要求に特別な `<script>` 参照が挿入されます。 スクリプト参照を表示するには、ブラウザーで **[ソースの表示]** を選択し、`<body>` タグ コンテンツの最後までスクロールします。

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

お使いのソース ファイルは変更されません。 ミドルウェア コンポーネントによって、スクリプト参照が動的に挿入されます。

ブラウザー側のコードはすべて JavaScript であるため、ブラウザー プラグインを必要とせずに、SignalR でサポートされるすべてのブラウザーで動作します。
