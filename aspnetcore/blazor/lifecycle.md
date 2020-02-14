---
title: ASP.NET Core Blazor ライフサイクル
author: guardrex
description: ASP.NET Core Blazor アプリで Razor コンポーネントライフサイクルメソッドを使用する方法について説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213390"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor ライフサイクル

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

Blazor framework には、同期および非同期のライフサイクルメソッドが含まれています。 コンポーネントの初期化およびレンダリング中にコンポーネントに対して追加の操作を実行するには、ライフサイクルメソッドをオーバーライドします。

## <a name="lifecycle-methods"></a>ライフサイクル メソッド

### <a name="component-initialization-methods"></a>コンポーネントの初期化メソッド

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> は、その親コンポーネントから初期パラメーターを受け取った後に、コンポーネントが初期化されるときに呼び出されます。 コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、`OnInitializedAsync` を使用します。

同期操作の場合は、`OnInitialized`をオーバーライドします。

```csharp
protected override void OnInitialized()
{
    ...
}
```

非同期操作を実行するには、`OnInitializedAsync` をオーバーライドし、操作で `await` キーワードを使用します。

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

コンテンツの呼び出しを[プリレンダリング](xref:blazor/hosting-model-configuration#render-mode)する Blazor サーバーアプリは、 **_2 回_** `OnInitializedAsync` ます。

* コンポーネントが最初にページの一部として静的にレンダリングされたとき。
* ブラウザーがサーバーへの接続を確立する2回目。

`OnInitializedAsync` の開発者コードが2回実行されないようにするには、「[プリコーディング後のステートフル再接続](#stateful-reconnection-after-prerendering)」セクションを参照してください。

Blazor Server アプリをプリレンダリングしている間、ブラウザーとの接続が確立されていないため、JavaScript への呼び出しなどの特定のアクションは実行できません。 Prerendered の場合、コンポーネントのレンダリングが異なる場合があります。 詳細については、「[アプリのプリレンダリングを検出する](#detect-when-the-app-is-prerendering)」セクションを参照してください。

### <a name="before-parameters-are-set"></a>パラメーターを設定する前

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> は、レンダリングツリーのコンポーネントの親によって提供されるパラメーターを設定します。

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> には、`SetParametersAsync` が呼び出されるたびに、パラメーター値のセット全体が含まれます。

`SetParametersAsync` の既定の実装では、`ParameterView`に対応する値を持つ `[Parameter]` または `[CascadingParameter]` 属性を使用して、各プロパティの値を設定します。 `ParameterView` に対応する値を持たないパラメーターは、変更されずに残ります。

`base.SetParametersAync` が呼び出されない場合、カスタムコードでは、必要に応じて受信パラメーター値を解釈できます。 たとえば、受信したパラメーターをクラスのプロパティに割り当てる必要はありません。

### <a name="after-parameters-are-set"></a>パラメーターが設定された後

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> は次のように呼び出されます。

* コンポーネントが初期化され、その親コンポーネントからパラメーターの最初のセットを受け取ったとき。
* 親コンポーネントが再レンダリングし、次のものを提供する場合:
  * 少なくとも1つのパラメーターが変更された既知のプリミティブ不変型のみです。
  * 任意の複合型のパラメーター。 フレームワークは、複合型のパラメーターの値が内部で変更されているかどうかを認識できないため、パラメーターセットは変更済みとして扱われます。

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> パラメーターとプロパティ値を適用するときの非同期処理は、`OnParametersSetAsync` ライフサイクルイベント中に発生する必要があります。

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>コンポーネントのレンダリング後

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> は、コンポーネントのレンダリングが完了した後に呼び出されます。 この時点で、要素参照とコンポーネント参照が設定されます。 レンダリングされた DOM 要素を操作するサードパーティ製の JavaScript ライブラリをアクティブ化するなど、レンダリングされたコンテンツを使用して追加の初期化手順を実行するには、この段階を使用します。

`OnAfterRenderAsync` と `OnAfterRender`の `firstRender` パラメーター:

* は、コンポーネントインスタンスを初めて表示するときに `true` に設定されます。
* を使用すると、初期化作業が1回だけ実行されるようにすることができます。

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> `OnAfterRenderAsync` のライフサイクルイベント中に、レンダリング直後の非同期作業が発生する必要があります。
>
> `OnAfterRenderAsync`から <xref:System.Threading.Tasks.Task> を返した場合でも、フレームワークは、そのタスクが完了すると、コンポーネントに対してさらにレンダリングサイクルをスケジュールしません。 これは、無限のレンダーループを回避するためです。 これは、返されたタスクが完了すると、さらにレンダリングサイクルをスケジュールする他のライフサイクルメソッドとは異なります。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` と `OnAfterRenderAsync`*は、サーバーでのプリレンダリング時に呼び出されません。*

### <a name="suppress-ui-refreshing"></a>UI 更新の抑制

<xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> をオーバーライドして、UI の更新を抑制します。 実装によって `true`が返された場合は、UI が更新されます。

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender` は、コンポーネントがレンダリングされるたびに呼び出されます。

`ShouldRender` がオーバーライドされた場合でも、コンポーネントは常に最初に表示されます。

## <a name="state-changes"></a>状態の変化

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> は、状態が変更されたことをコンポーネントに通知します。 必要に応じて、`StateHasChanged` を呼び出すと、コンポーネントが再スローされます。

## <a name="handle-incomplete-async-actions-at-render"></a>レンダー時の不完全な非同期アクションを処理する

ライフサイクルイベントで実行される非同期アクションは、コンポーネントがレンダリングされる前に完了していない可能性があります。 ライフサイクルメソッドの実行中に、オブジェクトが `null` されているか、データが不完全に設定されている可能性があります。 オブジェクトが初期化されていることを確認するためのレンダリングロジックを提供します。 オブジェクトの `null`中に、プレースホルダー UI 要素 (読み込みメッセージなど) をレンダリングします。

Blazor テンプレートの `FetchData` コンポーネントでは、`OnInitializedAsync` がユーザー receive 予測データ (`forecasts`) に上書きされます。 `forecasts` が `null`と、読み込みメッセージがユーザーに表示されます。 `OnInitializedAsync` によって返された `Task` が完了すると、コンポーネントは更新された状態とピアリングされます。

Blazor サーバーテンプレートでの*Pages/FetchData. razor* :

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>IDisposable によるコンポーネントの破棄

コンポーネントが <xref:System.IDisposable>を実装している場合は、コンポーネントが UI から削除されるときに[Dispose メソッド](/dotnet/standard/garbage-collection/implementing-dispose)が呼び出されます。 次のコンポーネントでは、`@implements IDisposable` と `Dispose` メソッドが使用されます。

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> `Dispose` での <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> の呼び出しはサポートされていません。 `StateHasChanged` は、レンダラーの破棄の一部として呼び出されることがあるため、その時点での UI 更新の要求はサポートされていません。

## <a name="handle-errors"></a>エラーを処理する

ライフサイクルメソッドの実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。

## <a name="stateful-reconnection-after-prerendering"></a>プリレンダリング後のステートフル再接続

Blazor サーバーアプリで `RenderMode` が `ServerPrerendered`場合、コンポーネントは最初にページの一部として静的にレンダリングされます。 ブラウザーがサーバーへの接続を確立すると、コンポーネントが*再び*表示され、コンポーネントが対話型になります。 コンポーネントを初期化するための[Oninitialized 化された {Async}](xref:blazor/lifecycle#component-initialization-methods)ライフサイクルメソッドが存在する場合、メソッドは*2 回*実行されます。

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
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

`RenderMode`の詳細については、「<xref:blazor/hosting-model-configuration#render-mode>」を参照してください。

## <a name="detect-when-the-app-is-prerendering"></a>アプリがプリレンダリングされるタイミングを検出する

[!INCLUDE[](~/includes/blazor-prerendering.md)]
