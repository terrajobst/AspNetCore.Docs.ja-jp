---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor Webasと Blazor サーバーホスティングモデルについて理解します。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7676d16bddf146ea38619ed35c5e32c5bce731de
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943767"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>Blazor ホスティングモデルの ASP.NET Core

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor は、ブラウザーでブラウザーでクライアント側を実行するために設計された web フレームワークであり、 [webasに](https://webassembly.org/)基づく .net ランタイム ( *Blazor webassembly*、または ASP.NET Core ( *Blazor サーバー*) のサーバー側で実行されます。 ホスティングモデルに関係なく、アプリモデルとコンポーネントモデル*は同じ*です。

この記事で説明されているホスティングモデルのプロジェクトを作成するには、「<xref:blazor/get-started>」を参照してください。

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Blazor のプリンシパルホスティングモデルは、ブラウザーでクライアント側で実行されます。 Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。 アプリがブラウザー UI スレッド上で直接実行されます。 UI の更新とイベントの処理は、同じプロセス内で行われます。 アプリの資産は静的ファイルとして、静的コンテンツをクライアントに提供できる web サーバーまたはサービスに展開されます。

![[!ファンド.NO LOC (Blazor)] WebAssembly [!ファンド.NO LOC (Blazor)] アプリは、ブラウザー内の UI スレッドで実行されます。](hosting-models/_static/blazor-webassembly.png)

クライアント側のホスティングモデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ**テンプレート ([new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。

**Blazor WebAssembly**テンプレートを選択した後、[ホストされている**ASP.NET Core** ] チェックボックス ([dotnet new blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) を選択して、ASP.NET Core バックエンドを使用するようにアプリを構成することができます。 ASP.NET Core アプリは、クライアントに対して Blazor アプリを提供します。 Blazor WebAssembly は、web API 呼び出しまたは[SignalR](xref:signalr/introduction)を使用して、ネットワーク経由でサーバーと通信できます。

テンプレートには、を処理する*blazor*スクリプトが含まれています。

* .NET ランタイム、アプリ、およびアプリの依存関係をダウンロードしています。
* アプリを実行するランタイムの初期化。

Blazor WebAssembly ホスティングモデルには、いくつかの利点があります。

* .NET サーバー側の依存関係はありません。 クライアントにダウンロードされた後、アプリは完全に機能しています。
* クライアントのリソースと機能は完全に活用されています。
* 作業はサーバーからクライアントにオフロードされます。
* アプリケーションをホストするために ASP.NET Core web サーバーは必要ありません。 サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。

Blazor Webasホストには欠点があります。

* アプリは、ブラウザーの機能に制限されています。
* サポートされているクライアントハードウェアとソフトウェア (たとえば、WebAssembly サポート) が必要です。
* ダウンロードサイズが大きくなり、アプリの読み込みに時間がかかります。
* .NET ランタイムとツールのサポートの成熟度は低くなります。 たとえば、 [.NET Standard](/dotnet/standard/net-standard)のサポートとデバッグには制限があります。

## <a name="opno-locblazor-server"></a>Blazor サーバー

Blazor サーバーホスティングモデルでは、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。

![ブラウザーは、サーバー上の (ASP.NET Core アプリ内でホストされている) アプリと対話します。ファンド.NO LOC (SignalR)] 接続。](hosting-models/_static/blazor-server.png)

Blazor Server ホスティングモデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor サーバーアプリ**テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。 ASP.NET Core アプリは、Blazor Server アプリをホストし、クライアントが接続する SignalR エンドポイントを作成します。

ASP.NET Core アプリは、追加するアプリの `Startup` クラスを参照します。

* サーバー側サービス。
* 要求処理パイプラインに対するアプリ。

*Blazor*スクリプト&dagger; によって、クライアント接続が確立されます。 アプリケーションの状態は、必要に応じて永続化および復元する必要があります (ネットワーク接続が切断された場合など)。

Blazor サーバーホスティングモデルには、いくつかの利点があります。

* ダウンロードサイズは Blazor Webasapp よりも大幅に小さく、アプリの読み込みにかかる時間が大幅に短縮されます。
* このアプリでは、.NET Core と互換性のある Api の使用を含め、サーバーの機能を最大限に活用できます。
* サーバー上の .NET Core はアプリを実行するために使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。
* シンクライアントがサポートされています。 たとえば、Blazor サーバーアプリは、WebAssembly サポートされていないブラウザーや、リソースが制限されたデバイスで動作します。
* アプリのコンポーネントコードをC#含む、アプリの .net/コードベースはクライアントに提供されません。

Blazor サーバーのホストには欠点があります。

* 通常、待機時間が長くなります。 すべてのユーザーの操作には、ネットワークホップが関係します。
* オフラインサポートはありません。 クライアント接続が失敗した場合、アプリは動作を停止します。
* 多くのユーザーがいるアプリでは、スケーラビリティが困難です。 サーバーは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。
* アプリを提供するには、ASP.NET Core サーバーが必要です。 サーバーレス展開シナリオは使用できません (たとえば、CDN からアプリを提供するなど)。

&dagger;、 *blazor*スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。

### <a name="comparison-to-server-rendered-ui"></a>サーバーレンダリングの UI との比較

Blazor サーバーアプリを理解する方法の1つは、Razor ビューまたは Razor Pages を使用する ASP.NET Core アプリで UI をレンダリングするための従来のモデルとの違いを理解することです。 どちらのモデルでも、Razor 言語を使用して HTML コンテンツを記述しますが、マークアップのレンダリング方法が大きく異なります。

Razor ページまたはビューがレンダリングされると、Razor コードのすべての行で HTML がテキスト形式で出力されます。 レンダリング後、サーバーは、生成されたすべての状態を含むページまたはビューインスタンスを破棄します。 サーバーの検証に失敗し、検証の概要が表示される場合など、ページに対する別の要求が発生したとき。

* ページ全体が HTML テキストに再び表示されます。
* ページがクライアントに送信されます。

Blazor アプリは、*コンポーネント*と呼ばれる UI の再利用可能な要素で構成されます。 コンポーネントにはC# 、コード、マークアップ、およびその他のコンポーネントが含まれています。 コンポーネントがレンダリングされると、Blazor は HTML または XML ドキュメントオブジェクトモデル (DOM) のような、含まれているコンポーネントのグラフを生成します。 このグラフには、プロパティとフィールドに保持されているコンポーネントの状態が含まれます。 Blazor は、コンポーネントグラフを評価して、マークアップのバイナリ表現を生成します。 バイナリ形式は次のようになります。

* (プリレンダリング中に) HTML テキストに変換されます。
* 通常のレンダリング中にマークアップを効率的に更新するために使用されます。

Blazor の UI の更新は、次の方法でトリガーされます。

* ユーザー操作 (ボタンの選択など)。
* タイマーなどのアプリトリガー。

グラフが再ピアリングされ、UI *diff* (差分) が計算されます。 この diff は、クライアントで UI を更新するために必要な DOM 編集の最小セットです。 Diff はバイナリ形式でクライアントに送信され、ブラウザーによって適用されます。

コンポーネントは、ユーザーがクライアント上で移動した後に破棄されます。 ユーザーがコンポーネントを操作している間、コンポーネントの状態 (サービス、リソース) は、サーバーのメモリに保持されている必要があります。 多くのコンポーネントの状態は同時にサーバーによって維持される可能性があるため、メモリ不足に対処する必要があります。 サーバーメモリを最大限に活用するために Blazor Server アプリを作成する方法については、「<xref:security/blazor/server>」を参照してください。

### <a name="circuits"></a>接続

Blazor サーバーアプリは[ASP.NET Core SignalR](xref:signalr/introduction)の上に構築されています。 各クライアントは、*回線*と呼ばれる1つ以上の SignalR 接続を介してサーバーと通信します。 回線は、一時的なネットワーク中断が許容される SignalR 接続に対して Blazor抽象化されています。 Blazor クライアントは、SignalR 接続が切断されていることを確認すると、新しい SignalR 接続を使用してサーバーへの再接続を試みます。

Blazor Server アプリに接続されている各ブラウザー画面 (ブラウザータブまたは iframe) は、SignalR 接続を使用します。 これは、サーバーでレンダリングされる一般的なアプリと比較して、もう1つ重要な違いです。 サーバー側でレンダリングされるアプリでは、複数のブラウザー画面で同じアプリを開くのは、通常、サーバーに対する追加のリソース要求には変換されません。 Blazor サーバーアプリでは、各ブラウザー画面に個別の回線が必要で、コンポーネント状態の個別のインスタンスがサーバーによって管理されます。

Blazor は、ブラウザータブを閉じるか、外部 URL に移動して*正常*に終了すると見なされます。 正常な終了が発生した場合、回線と関連するリソースが直ちに解放されます。 クライアントは、ネットワークの中断などによって、正常に切断されることもあります。 Blazor サーバーは、クライアントが再接続できるように、接続されていない回線を構成可能な間隔で格納します。 詳細については、「[同じサーバーへ](#reconnection-to-the-same-server)の再接続」セクションを参照してください。

### <a name="ui-latency"></a>UI の待機時間

UI 待機時間とは、開始されたアクションから UI が更新されるまでにかかる時間のことです。 アプリがユーザーに応答できるようにするには、UI の待機時間の値を小さくすることが不可欠です。 Blazor Server アプリでは、各アクションがサーバーに送信され、処理されて、UI diff が返されます。 その結果、UI の待機時間は、ネットワーク待機時間の合計と、アクションを処理するサーバーの待機時間です。

企業のプライベートネットワークに限定された基幹業務アプリの場合、ネットワーク待機時間による待ち時間のユーザーへの影響は、通常はなるべくです。 インターネット経由で展開されたアプリの場合、ユーザーにとって待機時間が顕著になる可能性があります。ユーザーが地理的に広く分散している場合は特にそうです。

メモリ使用量は、アプリの待機時間に寄与する場合もあります。 メモリ使用量が増加すると、ガベージコレクションまたはメモリのページングが頻繁に発生します。どちらの場合も、アプリのパフォーマンスが低下し、その結果、UI の遅延が増加します。 詳細については、「<xref:security/blazor/server>」を参照してください。

Blazor サーバーアプリは、ネットワーク待機時間とメモリ使用量を削減することで、UI の待機時間を最小限に抑えるように最適化する必要があります。 ネットワーク待機時間を測定する方法については、「<xref:host-and-deploy/blazor/server#measure-network-latency>」を参照してください。 SignalR と Blazorの詳細については、以下を参照してください。

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>サーバーへの接続

Blazor サーバーアプリには、サーバーへのアクティブな SignalR 接続が必要です。 接続が失われた場合、アプリはサーバーへの再接続を試みます。 クライアントの状態がまだメモリ内にある限り、クライアントセッションは状態を失うことなく再開されます。

#### <a name="reconnection-to-the-same-server"></a>同じサーバーへの再接続

最初のクライアント要求への応答としての Blazor Server アプリ prerenders。これにより、サーバー上で UI の状態が設定されます。 クライアントが SignalR 接続を作成しようとすると、クライアントは同じサーバーに再接続する必要があります。 複数のバックエンドサーバーを使用する Blazor サーバーアプリは、SignalR 接続用の*固定セッション*を実装する必要があります。

Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。 このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。 Azure SignalR サービスでは、サービスの `ServerStickyMode` オプションまたは構成値を `Required`に設定することにより、固定セッションが有効になります。 詳細については、「<xref:host-and-deploy/blazor/server#signalr-configuration>」を参照してください。

IIS を使用すると、スティッキー セッションはアプリケーション要求ルーティングによって有効になります。 詳しくは、「[アプリケーション要求ルーティングを使用した HTTP 負荷分散](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」をご覧ください。

#### <a name="reflect-the-connection-state-in-the-ui"></a>UI の接続状態を反映します。

クライアントが接続が失われたことを検出すると、クライアントが再接続しようとしているときに、既定の UI がユーザーに表示されます。 再接続に失敗した場合、ユーザーには再試行のオプションが表示されます。

UI をカスタマイズするには、 *_Host*の `<body>` の `components-reconnect-modal` の `id` を持つ要素を定義します。

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

次の表では、`components-reconnect-modal` 要素に適用される CSS クラスについて説明します。

| CSS クラス                       | &hellip; を示します。 |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | 失われた接続。 クライアントが再接続しようとしています。 モーダルを表示します。 |
| `components-reconnect-hide`     | サーバーへのアクティブな接続が再確立されます。 モーダルを非表示にします。 |
| `components-reconnect-failed`   | 再接続に失敗しました。ネットワーク障害が原因である可能性があります。 再接続を試みるには、`window.Blazor.reconnect()`を呼び出します。 |
| `components-reconnect-rejected` | 再接続は拒否されました。 サーバーに到達したが接続を拒否したため、サーバー上のユーザーの状態が失われています。 アプリを再度読み込むには、`location.reload()`を呼び出します。 この接続状態は、次の場合に発生する可能性があります。<ul><li>サーバー側回線でクラッシュが発生した場合。</li><li>サーバーがユーザーの状態を削除するのに十分な時間、クライアントが接続されていません。 ユーザーが対話しているコンポーネントのインスタンスは破棄されます。</li><li>サーバーが再起動されたか、アプリのワーカープロセスがリサイクルされています。</li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a>プリレンダリング後のステートフル再接続

サーバーへのクライアント接続が確立される前に、サーバー上の UI を事前に作成するために、Blazor サーバーアプリが既定で設定されます。 これは *_Host. cshtml* Razor ページで設定されます。

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

コンポーネントの `RenderMode` を構成します。

* ページに prerendered ます。
* は、ページに静的な HTML として表示されるか、またはユーザーエージェントから Blazor アプリをブートストラップするために必要な情報が含まれている場合に表示されます。

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Server`            | Blazor サーバーアプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | 説明 |
| ------------------- | ----------- |
| `ServerPrerendered` | コンポーネントを静的 HTML にレンダリングし、Blazor サーバーアプリのマーカーを含めます。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 パラメーターはサポートされていません。 |
| `Server`            | Blazor サーバーアプリのマーカーをレンダリングします。 コンポーネントからの出力は含まれていません。 ユーザーエージェントが起動すると、このマーカーは Blazor アプリをブートストラップするために使用されます。 パラメーターはサポートされていません。 |
| `Static`            | コンポーネントを静的 HTML にレンダリングします。 パラメーターがサポートされています。 |

::: moniker-end

静的な HTML ページからのサーバーコンポーネントのレンダリングはサポートされていません。

`RenderMode` が `ServerPrerendered`場合、コンポーネントは最初にページの一部として静的にレンダリングされます。 ブラウザーがサーバーへの接続を確立すると、コンポーネントが*再び*表示され、コンポーネントが対話型になります。 コンポーネントを初期化するための[Oninitialized 化された {Async}](xref:blazor/lifecycle#component-initialization-methods)ライフサイクルメソッドが存在する場合、メソッドは*2 回*実行されます。

* コンポーネントが静的に prerendered された場合。
* サーバー接続が確立された後。

これにより、コンポーネントが最終的にレンダリングされるときに、UI に表示されるデータが大幅に変化する可能性があります。

Blazor サーバーアプリでダブルレンダリングのシナリオを回避するには、次の手順を実行します。

* プリレンダリング中に状態をキャッシュし、アプリの再起動後に状態を取得するために使用できる識別子を渡します。
* コンポーネントの状態を保存するには、プリレンダリング時に識別子を使用します。
* プリレンダリング後に識別子を使用して、キャッシュされた状態を取得します。

次のコードは、テンプレートベースの Blazor サーバーアプリで、ダブルレンダリングを回避する更新された `WeatherForecastService` を示しています。

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor ページとビューからのステートフル対話型コンポーネントのレンダリング

ステートフル対話型コンポーネントは、Razor ページまたはビューに追加できます。

ページまたはビューが表示される場合:

* コンポーネントは、ページまたはビューと prerendered ます。
* プリレンダリングに使用される初期コンポーネントの状態は失われます。
* SignalR 接続が確立されると、新しいコンポーネントの状態が作成されます。

次の Razor ページでは、`Counter` コンポーネントがレンダリングされます。

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor ページとビューからの非対話型コンポーネントのレンダリング

次の Razor ページでは、フォームを使用して指定された初期値を使用して、`Counter` コンポーネントが静的にレンダリングされます。

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<Counter>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

`MyComponent` は静的にレンダリングされるため、コンポーネントを対話形式にすることはできません。

### <a name="detect-when-the-app-is-prerendering"></a>アプリがプリレンダリングされるタイミングを検出する

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor Server アプリ用に SignalR クライアントを構成する

場合によっては、Blazor サーバーアプリによって使用される SignalR クライアントを構成する必要があります。 たとえば、接続の問題を診断するために SignalR クライアントのログ記録を構成することができます。

*Pages/_Host cshtml*ファイルで SignalR クライアントを構成するには、次のようにします。

* *Blazor*スクリプトの `<script>` タグに `autostart="false"` 属性を追加します。
* `Blazor.start` を呼び出し、SignalR ビルダーを指定する構成オブジェクトを渡します。

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/get-started>
* <xref:signalr/introduction>
