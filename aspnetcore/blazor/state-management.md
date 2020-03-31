---
title: ASP.NET Core Blazor 状態管理
author: guardrex
description: Blazor Server アプリで状態を維持する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218870"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a>ASP.NET Core Blazor 状態管理

作成者: [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Server はステートフル アプリ フレームワークです。 ほとんどの場合、アプリではサーバーとの現在進行中の接続が維持されます。 ユーザーの状態は、"*回線*" の中のサーバーのメモリに保持されます。 

ユーザーの回線に保存される状態には次のとおりです。

* レンダリングされた UI &mdash; 階層になっているコンポーネント インスタンスとその最近のレンダリング出力。
* コンポーネント インスタンスに含まれるあらゆるフィールドとプロパティの値。
* 回線に範囲が設定されている[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) サービス インスタンスに保存されているデータ。

> [!NOTE]
> この記事では、Blazor Server アプリの状態維持について取り扱います。 Blazor WebAssembly アプリでは、[ブラウザーのクライアント側の状態維持](#client-side-in-the-browser)が利用されますが、この記事に扱う範囲を超えたカスタム ソリューションやサードパーティ製のパッケージが必要です。

## <a name="opno-locblazor-circuits"></a>Blazor 回線

ユーザーが一時的にネットワークに接続できなくなる場合、Blazor では、ユーザーがアプリを引き続き使用できるよう、そのユーザーを元の回線に再接続が試行されます。 ただし、サーバーのメモリにある元の回線にいつでもユーザーを再接続できるわけではありません。

* サーバーでは、切断された回線を永久に保持することはできません。 サーバーでは、タイムアウト後、あるいはサーバーがメモリ不足になったとき、切断された回線を解放しなければなりません。
* マルチサーバーによる負荷が分散された展開環境では、要求を処理するサーバーが突然利用不可能になることがあります。 個々のサーバーは、それがなくても全体的な要求量を処理できるようになると、作動しなくなったり、自動的に削除されたりすることがあります。 ユーザーが再接続を試みたとき、元のサーバーが利用できないことがあります。
* ユーザーはブラウザーを閉じて再び起動するか、ページを再読み込みできます。それでブラウザーのメモリに保存されている状態が削除されます。 たとえば、JavaScript の相互運用呼び出しによって設定された値は失われます。

ユーザーが元の回線に再接続できないとき、そのユーザーには、状態が空の新しい回線が与えられます。 これはデスクトップ アプリを終了してもう一度起動することと同じです。

## <a name="preserve-state-across-circuits"></a>回線間で状態を維持する

シナリオによっては、回線間で状態を維持することが望ましい場合もあります。 次の場合、アプリでは、ユーザーの重要なデータを保持できます。

* Web サーバーが利用できなくなる。
* ユーザーのブラウザーが、新しい Web サーバーで新しい回線を始めるように強制される。

一般的に、ユーザーが既存のデータを読み取るだけでなく、積極的にデータを作成するとき、回線間の状態の維持が適用されます。

1 つの回線を超えて状態を維持するには、"*データをサーバーのメモリに保存するだけでは不十分です*"。 アプリでは、データを他の保管場所にも保存する必要があります。 状態は自動的に保存されません。ステートフルなデータ保存を実装するアプリを開発するとき、措置を講じる必要があります。

データの永続性は通常、その状態を作り出すためにユーザーが労力を費やす、高価値の状態にのみ必要です。 次の例では、状態を維持することで、時間が節約され、商業活動の支援になります。

* マルチステップ Web フォーム &ndash; 手順が複数になるプロセスでいくつかの手順を完了しているのに、状態が失われるため、データを再入力しなければならないのは時間の浪費です。 このシナリオでは、マルチステップ フォームからユーザーが離れ、後で戻ってきた場合、状態が失われます。
* ショッピング カート &ndash; アプリの中でビジネス上、重要な構成要素が潜在的な収益を表わすとき、その要素を維持できます。 ユーザーが自分の状態を失い、そのため、自分のショッピング カートが消えると、後でサイトに戻ってきたとき、製品やサービスの購入数が減ることがあります。

簡単に再現される状態は通常、保存する必要がありません。サインイン ダイアログに入力されたユーザー名が送信されていない場合などです。

> [!IMPORTANT]
> アプリでは、"*アプリの状態*" のみが維持されます。 コンポーネント インスタンスやそのレンダー ツリーなど、UI は維持されません。 コンポーネントとレンダー ツリーは一般的にシリアル化されません。 展開された TreeView ノードなど、UI の状態に似た何かを維持するには、シリアル化できるアプリ状態として動作をモデル化するためのカスタム コードをアプリに与える必要があります。

## <a name="where-to-persist-state"></a>状態を維持する場所

Blazor Server アプリには、一般に、状態を保存する場所が 3 つあります。 どの手法にもそれに最も適したケースがあり、注意すべきことも異なります。

* [データベースのサーバー側](#server-side-in-a-database)
* [URL](#url)
* [ブラウザーのクライアント側](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>データベースのサーバー側

データを永続的に維持したり、あるデータを複数のユーザーやデバイスで使用したりするときは、独立したサーバー側データセットがおそらく最善の選択肢です。 次のオプションがあります。

* リレーショナル SQL データベース
* キー値ストア
* BLOB ストア
* テーブル ストア

データがデータベースに保存されたら、ユーザーはいつでも新しい回線を開始できます。 ユーザーのデータは保存され、あらゆる新しい回線で利用が可能です。

Azure データ ストレージ オプションに関する詳細については、「[Azure Storage のドキュメント](/azure/storage/)」と[Azure のデータベース](https://azure.microsoft.com/product-categories/databases/)に関するページを参照してください。

### <a name="url"></a>URL

ナビゲーションの状態を表わす一時的なデータについては、URL の一部としてデータをモデル化します。 URL でモデル化された状態の例を次に示します。

* 表示されるエンティティの ID。
* ページ付きグリッドでの現在のページ番号。

次の場合、ブラウザーのアドレス バーのコンテンツが保持されます。

* ユーザーがページを手動で再読み込みした。
* Web サーバーが利用できなくなった &mdash; 別のサーバーに接続する目的で、ページの再読み込みがユーザーに強制される。

`@page` ディレクティブで URL パターンを定義する方法については、「<xref:blazor/routing>」を参照してください。

### <a name="client-side-in-the-browser"></a>ブラウザーのクライアント側

ユーザーが一時的なデータを頻繁に作成する場合、一般的なバッキング ストアはブラウザーの `localStorage` コレクションと `sessionStorage` コレクションになります。 回線が放棄された場合、保存されている状態を管理したり、消去したりすることはアプリに求められません。これはサーバー側ストレージより優れている点です。

> [!NOTE]
> このセクションの "クライアント側" は、[Blazor WebAssembly ホスティング モデル](xref:blazor/hosting-models#blazor-webassembly)ではなく、ブラウザーのクライアント側シナリオを指します。 `localStorage` と `sessionStorage` は Blazor WebAssembly アプリで使用できますが、カスタム コードを記述するか、サードパーティ製のパッケージを使用する必要があります。

`localStorage` と `sessionStorage` は次の点で異なります。

* `localStorage` は範囲がユーザーのブラウザーに設定されています。 ユーザーがページを再読み込みするか、ブラウザーを閉じて再び開くと、状態は維持されます。 ユーザーが複数のブラウザー タブを開くと、状態はすべてのタブで同じになります。 データは直接消去されるまで `localStorage` に残ります。
* `sessionStorage` は範囲がユーザーのブラウザー タブに設定されています。ユーザーがタブを再読み込みすると、状態は維持されます。 ユーザーがタブかブラウザーを閉じると、状態は失われます。 ユーザーが複数のブラウザー タブを開くと、それぞれのタブには、他に依存しないそのタブだけのバージョンのデータが保持されます。

一般に、`sessionStorage` を使用しておけば安全です。 `sessionStorage` の場合、ユーザーが複数のタブを開き、以下に遭遇するリスクが回避されます。

* タブ間の状態保存に含まれるバグ。
* タブで他のタブの状態が上書きされるときの紛らわしい動作。

ブラウザーを閉じて再び開いても状態を維持することがアプリに求められる場合、`localStorage` が最善の選択肢です。

ブラウザー ストレージ使用時の注意事項:

* サーバー側データベースの使用に似ていますが、データの読み込みと保存は非同期です。
* サーバー側データベースとは異なり、プリレンダリング中はストレージを利用できません。プリレンダリング中は、要求されたページがブラウザーに存在しないためです。
* Blazor Server アプリの場合、数キロバイトのデータをストレージに保持するのが妥当です。 数キロバイトを超えると、パフォーマンスに影響が出ることを考慮する必要があります。ネットワーク中でデータが読み込まれ、保存されるためです。
* ユーザーはデータを見たり、改ざんしたりするかもしれません。 ASP.NET Core [データ保護](xref:security/data-protection/introduction)でこのリスクを軽減できます。

## <a name="third-party-browser-storage-solutions"></a>サードパーティ製ブラウザーのストレージ ソリューション

サードパーティ製 NuGet パッケージからは、`localStorage` と `sessionStorage` を使用するための API が与えられます。

ASP.NET Core の[データ保護](xref:security/data-protection/introduction)を透過的に使用するパッケージを選択してみることもお勧めします。 ASP.NET Core データ保護では、保存中のデータが暗号化され、改ざんされるという潜在的リスクが減ります。 JSON でシリアル化されたデータがプレーンテキストで保存されている場合、ユーザーはブラウザー開発者ツールでデータを表示できます。また、保存中のデータを変更できます。 データが性質上、取るに足りないものであれば、データ セキュリティを問題にする必要はないかもしれません。 たとえば、UI 要素に保存されている色を読み取られたり、変更されたりしたところで、それはユーザーや組織にとって大きなセキュリティ リスクではありません。 "*取り扱いに慎重を要するデータ*" を見たり、改ざんしたりすることをユーザーに禁止します。

## <a name="protected-browser-storage-experimental-package"></a>Protected Browser Storage 試験用パッケージ

NuGet パッケージには `localStorage` や `sessionStorage` の[データを保護する](xref:security/data-protection/introduction)ものがありますが、その一例が [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) です。

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` はサポートのない試験用パッケージであり、現時点では運用環境での使用に適していません。

### <a name="installation"></a>インストール

`Microsoft.AspNetCore.ProtectedBrowserStorage` パッケージをインストールするには:

1. Blazor Server アプリ プロジェクトで、[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) にパッケージ参照を追加します。
1. 最上位 HTML (たとえば、デフォルト プロジェクト テンプレートの *Pages/_Host.cshtml* ファイルで) で、次の `<script>` タグを追加します。

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. `Startup.ConfigureServices` メソッドで、`AddProtectedBrowserStorage` を呼び出し、`localStorage` サービスと `sessionStorage` サービスをサービス コレクションに追加します。

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>コンポーネント内でデータを保存し、読み込む

データを読み込むか、ブラウザー ストレージに保存する必要があるあらゆるコンポーネントで、[`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) を使用し、次のいずれかのインスタンスを挿入します。

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

選択肢は、使用するバッキング ストアによって決まります。 次の例では、`sessionStorage` が使用されています。

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

`@using` ステートメントは、コンポーネントの代わりに、 *_Imports.razor* ファイルに入れることができます。 *_Imports.razor* ファイルを使用すると、アプリの中の大きなセグメントで、あるいはアプリ全体で名前空間を利用できます。

プロジェクト テンプレートの `Counter` コンポーネントに `_currentCount` 値を保持するには、`ProtectedSessionStore.SetAsync` を使用するように `IncrementCount` メソッドを変更します。

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

もっと大規模で現実に即したアプリの場合、個々のフィールドを保管するというのはありそうにないシナリオです。 アプリでは多くの場合、状態が複雑なモデル オブジェクト全体を保存します。 `ProtectedSessionStore` では、JSON データが自動的にシリアル化され、逆シリアル化されます。

前のコード例では、`_currentCount` データは、ユーザーのブラウザーに `sessionStorage['count']` として保存されます。 データはプレーンテキストに保存されず、ASP.NET Core の[データ保護](xref:security/data-protection/introduction)で保護されます。 ブラウザーの開発者コンソールで `sessionStorage['count']` が評価されるとき、暗号化されたデータを確認できることがあります。

ユーザーが後で `Counter` コンポーネントに戻ったときに `_currentCount` データを回復するには (まったく新しい回線にいる場合も含め)、`ProtectedSessionStore.GetAsync` を使用します。

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

コンポーネントのパラメーターにナビゲーションの状態が含まれている場合、`ProtectedSessionStore.GetAsync` を呼び出し、`OnInitializedAsync` ではなく、`OnParametersSetAsync` で結果を割り当てます。 `OnInitializedAsync` は、コンポーネントが最初にインスタンス化されたときに 1 回だけ呼び出されます。 後で、その同じページにいるとき、ユーザーが別の URL に移動しても `OnInitializedAsync` が再び呼び出されることはありません。 詳細については、「<xref:blazor/lifecycle>」を参照してください。

> [!WARNING]
> このセクションの例は、サーバーでプリレンダリングが有効になっていない場合に機能します。 プリレンダリングが有効になっている場合、次のようなエラーが発生します。
>
> > JavaScript 相互運用呼び出しは、この時点では発行できません。 コンポーネントが事前レンダリングされているからです。
>
> プリレンダリングを無効にするか、プリレンダリングで使用するコードを追加します。 プリレンダリングと連動するコードを記述する方法の詳細については、「[プリレンダリングを処理する](#handle-prerendering)」を参照してください。

### <a name="handle-the-loading-state"></a>読み込み状態を処理する

ブラウザー ストレージは非同期であるため (ネットワーク接続でアクセスされるため)、データが読み込まれ、コンポーネントで利用できるようになるまで常に一定の時間があります。 最良の結果を得るには、空のデータや既定のデータを表示するのではなく、読み込みが進行中のとき、読み込み状態メッセージをレンダリングします。

1 つの手法は、データが `null` (読み込み中) かどうかを追跡することです。 既定の `Counter` コンポーネントでは、カウントは `int` に保持されます。 型 (`int`) に疑問符 (`?`) を追加し、`_currentCount` を null 許容にします。

```csharp
private int? _currentCount;
```

カウントや **[インクリメント]** ボタンを無条件で表示するのではなく、データが読み込まれる場合にのみこれらの要素を表示するように選択します。

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>プリレンダリングを処理する

プリレンダリング中:

* ユーザーのブラウザーに対話式で接続することはありません。
* ブラウザーには、JavaScript コードを実行できるページがまだありません。

`localStorage` または `sessionStorage` は、プリレンダリング中に使用できません。 コンポーネントでストレージとのやりとりが試みられると、次のようなエラーが発生します。

> JavaScript 相互運用呼び出しは、この時点では発行できません。 コンポーネントが事前レンダリングされているからです。

このエラーを解決する方法の 1 つは、プリレンダリングを無効にすることです。 これは通常、ブラウザーベースのストレージがアプリで頻繁に使用される場合、最良の選択肢となります。 プリレンダリングによってさらに複雑になり、アプリにとっては良いことがありません。アプリでは `localStorage` または `sessionStorage` が利用できなければ、役に立つコンテンツをプリレンダリングできないからです。

プリレンダリングを無効にするには、*Pages/_Host cshtml* ファイルを開き、[コンポーネント タグ ヘルパー](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)の `render-mode` を <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> に変更します。

プリレンダリングは、`localStorage` や `sessionStorage` を使用しない他のページでは役に立つかもしれません。 プリレンダリングを有効にしておくには、ブラウザーが回線に接続されるまで読み込み操作を延期します。 次はカウンター値を格納する例です。

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>状態保存を取り出し、共通の場所に入れる

さまざまなコンポーネントがブラウザーベースのストレージに依存しているとき、状態プロバイダー コードを何回も再実装すると、コードが重複します。 コードの重複を回避する選択肢の 1 つは、状態プロバイダー ロジックをカプセル化する "*状態プロバイダーの親コンポーネント*" を作成することです。 状態保存メカニズムに関係なく、子コンポーネントは永続保存データとやりとりできます。

`CounterStateProvider` コンポーネントの次の例では、カウンター データが永続保存されます。

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider` コンポーネントによって読み込み段階が処理されます。読み込みが完了するまで、その子コンテンツがレンダリングされることはありません。

`CounterStateProvider` コンポーネントを使用するには、カウンター状態にアクセスする必要がある他のコンポーネントをコンポーネントのインスタンスでラップします。 アプリに含まれるすべてのコンポーネントが状態にアクセスできるようにするには、`App` コンポーネント (*App.razor*) で `Router` を `CounterStateProvider` コンポーネントでラップします。

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

ラップされたコンポーネントの元に永続化されたカウンター状態が届くので、それを変更できます。 次の `Counter` コンポーネントはパターンが実装されています。

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

このコンポーネントは `ProtectedBrowserStorage` とやりとりするために必要ありませんし、"読み込み" 段階にも関係ありません。

前に説明したようにプリレンダリングを扱うには、カウンター データを利用するすべてのコンポーネントが自動的にプリレンダリングを使用するよう、`CounterStateProvider` を変更できます。 詳細については、「[プリレンダリングを処理する](#handle-prerendering)」セクションを参照してください

一般的に、次の場合、"*状態プロバイダーの親コンポーネント*" パターンが推奨されます。

* 他にもさまざまなコンポーネントで状態を使用する場合。
* 最上位の状態オブジェクトを 1 つだけ保持する場合。

さまざまな状態オブジェクトを保持し、さまざまな場所でさまざまなオブジェクト サブセットを使用するには、グローバルに状態を読み込み、保存することを回避することをお勧めします。
