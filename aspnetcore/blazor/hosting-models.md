---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor クライアント側とサーバー側のホストモデルについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/01/2019
uid: blazor/hosting-models
ms.openlocfilehash: 64393e826cb17550085f468f5916fca55973908f
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993391"
---
# <a name="aspnet-core-blazor-hosting-models"></a>Blazor ホスティングモデルの ASP.NET Core

[Daniel Roth](https://github.com/danroth27)

Blazor は、ブラウザーでブラウザーでクライアント側を実行するために設計された web フレームワークであり、ASP.NET Core (*Blazor サーバー側*) の [WebAssembly](https://webassembly.org/) ベースの .net ランタイム (*Blazor client 側*) またはサーバー側で作成されています。 ホスティングモデルに関係なく、アプリモデルとコンポーネントモデル*は同じ*です。

この記事で説明されているホスティングモデルのプロジェクトを作成<xref:blazor/get-started>するには、「」を参照してください。

## <a name="client-side"></a>クライアント側

Blazor のプリンシパルホスティングモデルは、ブラウザーでクライアント側で実行されます。 Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。 アプリがブラウザー UI スレッド上で直接実行されます。 UI の更新とイベントの処理は、同じプロセス内で行われます。 アプリの資産は静的ファイルとして、静的コンテンツをクライアントに提供できる web サーバーまたはサービスに展開されます。

![Blazor クライアント側:Blazor アプリは、ブラウザー内の UI スレッドで実行されます。](hosting-models/_static/client-side.png)

クライアント側のホスティングモデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ**テンプレート ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。

**Blazor WebAssembly アプリ**テンプレートを選択した後、[ホストされている**ASP.NET Core** ] チェックボックス ([new blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) を選択して、ASP.NET Core バックエンドを使用するようにアプリを構成することができます。 ASP.NET Core アプリは、Blazor アプリをクライアントに提供します。 Blazor クライアント側アプリは、web API 呼び出しまたは[SignalR](xref:signalr/introduction)を使用して、ネットワーク経由でサーバーと通信できます。

テンプレートには、を処理する*blazor*スクリプトが含まれています。

* .NET ランタイム、アプリ、およびアプリの依存関係をダウンロードしています。
* アプリを実行するランタイムの初期化。

クライアント側ホスティングモデルには、いくつかの利点があります。

* .NET サーバー側の依存関係はありません。 クライアントにダウンロードされた後、アプリは完全に機能しています。
* クライアントのリソースと機能は完全に活用されています。
* 作業はサーバーからクライアントにオフロードされます。
* アプリケーションをホストするために ASP.NET Core web サーバーは必要ありません。 サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。

クライアント側のホストには欠点があります。

* アプリは、ブラウザーの機能に制限されています。
* サポートされているクライアントハードウェアとソフトウェア (たとえば、WebAssembly サポート) が必要です。
* ダウンロードサイズが大きくなり、アプリの読み込みに時間がかかります。
* .NET ランタイムとツールのサポートの成熟度は低くなります。 たとえば、 [.NET Standard](/dotnet/standard/net-standard)のサポートとデバッグには制限があります。

## <a name="server-side"></a>サーバー側

サーバー側のホスティングモデルでは、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。

![ブラウザーは、SignalR 接続を介して、サーバー上の (ASP.NET Core アプリ内でホストされている) アプリと対話します。](hosting-models/_static/server-side.png)

サーバー側のホスティングモデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor Server アプリ**テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。 ASP.NET Core アプリはサーバー側アプリをホストし、クライアントが接続する SignalR エンドポイントを作成します。

ASP.NET Core アプリは、追加するアプリ`Startup`のクラスを参照します。

* サーバー側サービス。
* 要求処理パイプラインに対するアプリ。

*Blazor*スクリプト&dagger;は、クライアント接続を確立します。 アプリケーションの状態は、必要に応じて永続化および復元する必要があります (ネットワーク接続が切断された場合など)。

サーバー側ホスティングモデルには、いくつかの利点があります。

* ダウンロードサイズがクライアント側のアプリよりも大幅に小さく、アプリの読み込みにかかる時間が大幅に短縮されます。
* このアプリでは、.NET Core と互換性のある Api の使用を含め、サーバーの機能を最大限に活用できます。
* サーバー上の .NET Core はアプリを実行するために使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。
* シンクライアントがサポートされています。 たとえば、サーバー側のアプリは、WebAssembly サポートされていないブラウザーや、リソースが制限されたデバイスで動作します。
* アプリのコンポーネントコードをC#含む、アプリの .net/コードベースはクライアントに提供されません。

サーバー側のホストには欠点があります。

* 通常、待機時間が長くなります。 すべてのユーザーの操作には、ネットワークホップが関係します。
* オフラインサポートはありません。 クライアント接続が失敗した場合、アプリは動作を停止します。
* 多くのユーザーがいるアプリでは、スケーラビリティが困難です。 サーバーは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。
* アプリを提供するには、ASP.NET Core サーバーが必要です。 サーバーレス展開シナリオは使用できません (たとえば、CDN からアプリを提供するなど)。

&dagger;*Blazor*スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。

### <a name="reconnection-to-the-same-server"></a>同じサーバーへの再接続

Blazor サーバー側アプリには、サーバーへのアクティブな SignalR 接続が必要です。 接続が失われた場合、アプリはサーバーへの再接続を試みます。 クライアントの状態がまだメモリ内にある限り、クライアントセッションは状態を失うことなく再開されます。
 
クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。 再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。 UI をカスタマイズするには、 *_Host*ページ`components-reconnect-modal` `id`でとしてを使用して要素を定義します。 クライアントは、接続の状態に基づいて、次のいずれかの CSS クラスを使用して、この要素を更新します。
 
* `components-reconnect-show`&ndash;接続が失われたことを示す UI を表示し、クライアントが再接続を試みていることを示します。
* `components-reconnect-hide`&ndash;クライアントにアクティブな接続があり、UI が非表示になっています。
* `components-reconnect-failed`&ndash;再接続に失敗しました。 再度再接続を試みるに`window.Blazor.reconnect()`は、を呼び出します。

### <a name="stateful-reconnection-after-prerendering"></a>プリレンダリング後のステートフル再接続
 
Blazor サーバー側アプリは、サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に設定するように既定で設定されます。 これは、 *_Host*ページで設定します。
 
```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>())</app>
 
    <script src="_framework/blazor.server.js"></script>
</body>
```
 
クライアントは、アプリの再レンダリングに使用されたのと同じ状態でサーバーに再接続します。 アプリの状態がまだメモリ内にある場合は、SignalR 接続が確立された後に、コンポーネントの状態が再び実行されることはありません。

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor ページとビューからのステートフル対話型コンポーネントのレンダリング
 
ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。 ページまたはビューがレンダリングされると、コンポーネントは prerendered に表示されます。 その後、状態がまだメモリ内にある限り、クライアント接続が確立されると、アプリはコンポーネントの状態に再接続します。
 
たとえば、次の Razor ページでは、 `Counter`フォームを使用して指定された初期カウントを持つコンポーネントがレンダリングされます。
 
```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialCount" />
    <button type="submit">Set initial count</button>
</form>
 
@(await Html.RenderComponentAsync<Counter>(new { InitialCount = InitialCount }))
 
@code {
    [BindProperty(SupportsGet=true)]
    public int InitialCount { get; set; }
}
```

### <a name="detect-when-the-app-is-prerendering"></a>アプリがプリレンダリングされるタイミングを検出する
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-side-apps"></a>Blazor サーバー側アプリ用に SignalR クライアントを構成する
 
場合によっては、Blazor サーバー側アプリによって使用される SignalR クライアントを構成する必要があります。 たとえば、接続の問題を診断するために、SignalR クライアントのログ記録を構成することができます。
 
*Pages/_Host*ファイルで SignalR クライアントを構成するには、次のようにします。

* *Blazor スクリプト*の`<script>`タグに属性を追加します。`autostart="false"`
* を`Blazor.start`呼び出し、SignalR builder を指定する構成オブジェクトを渡します。
 
```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>その他の資料

* <xref:blazor/get-started>
* <xref:signalr/introduction>
