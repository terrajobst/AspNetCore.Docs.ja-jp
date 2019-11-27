---
title: ASP.NET Core のブラウザーリンク
author: ncarandini
description: ブラウザーリンクが Visual Studio の機能であり、開発環境を1つ以上の web ブラウザーにリンクする方法について説明します。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/using-browserlink
ms.openlocfilehash: b21b698d49e72b559cd9cd3753c48a38c99db24d
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962781"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core のブラウザーリンク

[Nicolò Carandini](https://github.com/ncarandini)、 [Mike Wasson](https://github.com/MikeWasson)、 [Tom Dykstra](https://github.com/tdykstra)

Browser Link は、開発環境と1つ以上の web ブラウザーの間の通信チャネルを作成する Visual Studio の機能です。 ブラウザーリンクを使用すると、複数のブラウザーで一度に web アプリケーションを更新できます。これは、ブラウザー間のテストに役立ちます。

## <a name="browser-link-setup"></a>ブラウザーリンクの設定

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2.0 プロジェクトを ASP.NET Core 2.1 に変換し、 [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に移行する場合は、browserlink 機能用の[Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージをインストールします。 ASP.NET Core 2.1 プロジェクトテンプレートでは、既定で `Microsoft.AspNetCore.App` メタパッケージが使用されます。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **Web アプリケーション**、**空**、および**web API**プロジェクトテンプレートでは、メタパッケージのパッケージ参照を含む[AspNetCore](xref:fundamentals/metapackage)が使用されて[いますが](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)、 そのため、`Microsoft.AspNetCore.All` メタパッケージを使用するには、ブラウザーリンクを使用できるようにするための追加の操作は必要ありません。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **Web アプリケーション**プロジェクトテンプレートには、 [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)パッケージのパッケージ参照が含まれています。 **空**のプロジェクトまたは**Web API**テンプレートプロジェクトでは、`Microsoft.VisualStudio.Web.BrowserLink`にパッケージ参照を追加する必要があります。

これは Visual Studio の機能であるため、**空**のまたは**Web API**テンプレートプロジェクトにパッケージを追加する最も簡単な方法は、**パッケージマネージャーコンソール**(**他の Windows** >**パッケージマネージャーコンソール**を**表示**>) を開き、次のコマンドを実行することです。

```console
install-package Microsoft.VisualStudio.Web.BrowserLink
```

または、 **NuGet パッケージマネージャー**を使用することもできます。 **ソリューションエクスプローラー**でプロジェクト名を右クリックし、 **[NuGet パッケージの管理]** を選択します。

![NuGet パッケージマネージャーを開く](using-browserlink/_static/open-nuget-package-manager.png)

パッケージを検索してインストールします。

![NuGet パッケージマネージャーを使用したパッケージの追加](using-browserlink/_static/add-package-with-nuget-package-manager.png)

::: moniker-end

### <a name="configuration"></a>構成

`Startup.Configure` メソッドの場合:

```csharp
app.UseBrowserLink();
```

通常、コードは、次に示すように、開発環境で Browser リンクを有効にする `if` ブロック内にあります。

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

詳細については、「[Use multiple environments](xref:fundamentals/environments)」(複数の環境の使用) を参照してください。

## <a name="how-to-use-browser-link"></a>ブラウザーリンクを使用する方法

ASP.NET Core のプロジェクトを開いている場合、Visual Studio では、**デバッグターゲット** ツールバーコントロールの横に ブラウザーリンク ツールバーコントロールが表示されます。

![ブラウザーリンクのドロップダウンメニュー](using-browserlink/_static/browserLink-dropdown-menu.png)

[ブラウザーリンク] ツールバーコントロールからは、次の操作を実行できます。

* 複数のブラウザーで web アプリケーションを一度に更新します。
* **ブラウザーリンクダッシュボード**を開きます。
* **ブラウザーリンク**を有効または無効にします。 注: Visual Studio 2017 (15.3) では、ブラウザーリンクは既定で無効になっています。
* [CSS 自動同期](#enable-or-disable-css-auto-sync)を有効または無効にします。

> [!NOTE]
> 一部の Visual Studio プラグイン (特に*Web Extension pack 2015*および*web extension pack 2017*) は、ブラウザーリンク用の拡張機能を提供しますが、ASP.NET Core プロジェクトでは機能しません。

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>複数のブラウザーで web アプリを一度に更新する

プロジェクトを開始するときに起動する1つの web ブラウザーを選択するには、 **[デバッグターゲット]** ツールバーコントロールのドロップダウンメニューを使用します。

![F5 ドロップダウンメニュー](using-browserlink/_static/debug-target-dropdown-menu.png)

複数のブラウザーを一度に開くには、同じドロップダウンから **[参照]** を選択します。 CTRL キーを押しながら目的のブラウザーを選択し、 **[参照]** をクリックします。

![多数のブラウザーを一度に開く](using-browserlink/_static/open-many-browsers-at-once.png)

インデックスビューが開いている Visual Studio と2つの開いているブラウザーを示すスクリーンショットを次に示します。

![2つのブラウザーを使用した同期の例](using-browserlink/_static/sync-with-two-browsers-example.png)

プロジェクトに接続されているブラウザーを表示するには、[ブラウザーリンク] ツールバーコントロールの上にマウスポインターを移動します。

![ホバーヒント](using-browserlink/_static/hoover-tip.png)

インデックスビューを変更すると、ブラウザーのリンクの [更新] ボタンをクリックすると、接続されているすべてのブラウザーが更新されます。

![ブラウザー-変更の同期](using-browserlink/_static/browsers-sync-to-changes.png)

ブラウザーリンクは、Visual Studio の外部から起動するブラウザーでも動作し、アプリケーションの URL に移動します。

### <a name="the-browser-link-dashboard"></a>ブラウザーリンクダッシュボード

ブラウザーリンクのドロップダウンメニューからブラウザーリンクダッシュボードを開いて、開いているブラウザーとの接続を管理します。

![browserslink-ダッシュボード](using-browserlink/_static/open-browserlink-dashboard.png)

ブラウザーが接続されていない場合は、[*ブラウザーで表示*] リンクを選択して、非デバッグセッションを開始できます。

![browserlink-ダッシュボード-接続なし](using-browserlink/_static/browserlink-dashboard-no-connections.png)

それ以外の場合は、接続されているブラウザーが、各ブラウザーに表示されているページへのパスと共に表示されます。

![browserlink-dashboard-2-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

必要に応じて、表示されているブラウザー名をクリックして、その1つのブラウザーを更新できます。

### <a name="enable-or-disable-browser-link"></a>ブラウザーリンクを有効または無効にする

ブラウザーリンクを無効にした後で再度有効にする場合は、ブラウザーを更新して再接続する必要があります。

### <a name="enable-or-disable-css-auto-sync"></a>CSS 自動同期を有効または無効にする

CSS 自動同期が有効になっていると、CSS ファイルに変更を加えたときに、接続されているブラウザーが自動的に更新されます。

## <a name="how-it-works"></a>しくみ

ブラウザーリンクは SignalR を使用して、Visual Studio とブラウザーの間の通信チャネルを作成します。 ブラウザーリンクが有効になっている場合、Visual Studio は複数のクライアント (ブラウザー) が接続できる SignalR サーバーとして機能します。 ブラウザーリンクは、ASP.NET Core 要求パイプラインにミドルウェアコンポーネントも登録します。 このコンポーネントは、サーバーからのすべてのページ要求に特別な `<script>` 参照を挿入します。 スクリプト参照を表示するには、ブラウザーで **[ソースの表示]** を選択し、`<body>` タグコンテンツの最後までスクロールします。

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
