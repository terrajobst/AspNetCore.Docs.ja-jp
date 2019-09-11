---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor クライアント側とサーバー側のホストモデルについて説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: blazor/hosting-models
ms.openlocfilehash: 7880affa59af1fa4fc47aee3dc98ae9aa53729af
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878347"
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

### <a name="comparison-to-server-rendered-ui"></a>サーバーレンダリングの UI との比較

Blazor Server アプリを理解する方法の1つは、Razor ビューまたは Razor Pages を使用して ASP.NET Core アプリで UI をレンダリングするための従来のモデルとの違いを理解することです。 どちらのモデルでも、Razor 言語を使用して HTML コンテンツを記述しますが、マークアップのレンダリング方法が大きく異なります。

Razor ページまたはビューがレンダリングされると、Razor コードのすべての行で HTML がテキスト形式で出力されます。 レンダリング後、サーバーは、生成されたすべての状態を含むページまたはビューインスタンスを破棄します。 サーバーの検証に失敗し、検証の概要が表示される場合など、ページに対する別の要求が発生したとき。

* ページ全体が HTML テキストに再び表示されます。
* ページがクライアントに送信されます。

Blazor アプリは、*コンポーネント*と呼ばれる UI の再利用可能な要素で構成されています。 コンポーネントにはC# 、コード、マークアップ、およびその他のコンポーネントが含まれています。 コンポーネントがレンダリングされると、Blazor は HTML または XML ドキュメントオブジェクトモデル (DOM) のような、含まれているコンポーネントのグラフを生成します。 このグラフには、プロパティとフィールドに保持されているコンポーネントの状態が含まれます。 Blazor は、コンポーネントグラフを評価して、マークアップのバイナリ表現を生成します。 バイナリ形式は次のようになります。

* (プリレンダリング中に) HTML テキストに変換されます。
* 通常のレンダリング中にマークアップを効率的に更新するために使用されます。

Blazor の UI 更新は、次の方法でトリガーされます。

* ユーザー操作 (ボタンの選択など)。
* タイマーなどのアプリトリガー。

グラフが再ピアリングされ、UI *diff* (差分) が計算されます。 この diff は、クライアントで UI を更新するために必要な DOM 編集の最小セットです。 Diff はバイナリ形式でクライアントに送信され、ブラウザーによって適用されます。

コンポーネントは、ユーザーがクライアント上で移動した後に破棄されます。 ユーザーがコンポーネントを操作している間、コンポーネントの状態 (サービス、リソース) は、サーバーのメモリに保持されている必要があります。 多くのコンポーネントの状態は同時にサーバーによって維持される可能性があるため、メモリ不足に対処する必要があります。 Blazor Server アプリを作成してサーバーのメモリを最大限に活用する方法については<xref:security/blazor/server-side>、「」を参照してください。

### <a name="circuits"></a>接続

Blazor Server アプリは、 [ASP.NET Core SignalR](xref:signalr/introduction)の上に構築されています。 各クライアントは、*回線*と呼ばれる1つ以上の SignalR 接続を介してサーバーと通信します。 回線は、一時的なネットワーク中断を許容できる SignalR 接続に対する Blazor の抽象化です。 Blazor クライアントは、SignalR 接続が切断されていることを確認すると、新しい SignalR 接続を使用してサーバーへの再接続を試みます。

Blazor Server アプリに接続されている各ブラウザー画面 (ブラウザータブまたは iframe) は、SignalR 接続を使用します。 これは、サーバーでレンダリングされる一般的なアプリと比較して、もう1つ重要な違いです。 サーバー側でレンダリングされるアプリでは、複数のブラウザー画面で同じアプリを開くのは、通常、サーバーに対する追加のリソース要求には変換されません。 Blazor Server アプリでは、各ブラウザー画面に個別の回線が必要で、コンポーネント状態の個別のインスタンスがサーバーによって管理されます。

Blazor は、ブラウザータブを閉じるか、外部 URL に移動して*正常*に終了することを検討します。 正常な終了が発生した場合、回線と関連するリソースが直ちに解放されます。 クライアントは、ネットワークの中断などによって、正常に切断されることもあります。 Blazor Server は、クライアントが再接続できるように、接続されていない回線を構成可能な間隔で格納します。 詳細については、「[同じサーバーへ](#reconnection-to-the-same-server)の再接続」セクションを参照してください。

### <a name="ui-latency"></a>UI の待機時間

UI 待機時間とは、開始されたアクションから UI が更新されるまでにかかる時間のことです。 アプリがユーザーに応答できるようにするには、UI の待機時間の値を小さくすることが不可欠です。 Blazor Server アプリでは、各アクションがサーバーに送信され、処理されて、UI diff が返されます。 その結果、UI の待機時間は、ネットワーク待機時間の合計と、アクションを処理するサーバーの待機時間です。

企業のプライベートネットワークに限定された基幹業務アプリの場合、ネットワーク待機時間による待ち時間のユーザーへの影響は、通常はなるべくです。 インターネット経由で展開されたアプリの場合、ユーザーにとって待機時間が顕著になる可能性があります。ユーザーが地理的に広く分散している場合は特にそうです。

メモリ使用量は、アプリの待機時間に寄与する場合もあります。 メモリ使用量が増加すると、ガベージコレクションまたはメモリのページングが頻繁に発生します。どちらの場合も、アプリのパフォーマンスが低下し、その結果、UI の遅延が増加します。 詳細については、「 <xref:security/blazor/server-side> 」を参照してください。

Blazor サーバーアプリは、ネットワーク待機時間とメモリ使用量を削減することで、UI の待機時間を最小限に抑えるように最適化する必要があります。 ネットワーク待機時間を測定する方法につい<xref:host-and-deploy/blazor/server-side#measure-network-latency>ては、「」を参照してください。 SignalR と Blazor の詳細については、次を参照してください。

* <xref:host-and-deploy/blazor/server-side>
* <xref:security/blazor/server-side>

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
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

`RenderMode`コンポーネントを構成するかどうかを構成します。

* ページに prerendered ます。
* は、ページに静的 HTML として表示されるか、ユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバー側アプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 パラメーターはサポートされていません。 |
| `Server`            | Blazor サーバー側アプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 パラメーターはサポートされていません。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 パラメーターがサポートされています。 |

静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。

クライアントは、アプリの再レンダリングに使用されたのと同じ状態でサーバーに再接続します。 アプリの状態がまだメモリ内にある場合は、SignalR 接続が確立された後に、コンポーネントの状態が再び実行されることはありません。

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor ページとビューからのステートフル対話型コンポーネントのレンダリング

ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。

ページまたはビューが表示される場合:

* コンポーネントは、ページまたはビューと prerendered ます。
* プリレンダリングに使用される初期コンポーネントの状態は失われます。
* SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。

次の Razor ページでは`Counter` 、コンポーネントがレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor ページとビューからの非対話型コンポーネントのレンダリング

次の Razor ページ`MyComponent`では、フォームを使用して指定された初期値を使用して、コンポーネントが静的にレンダリングされます。

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

は`MyComponent`静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。

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
