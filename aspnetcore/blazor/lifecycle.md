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
ms.openlocfilehash: df5bb676df59b538179a69978040521c4ee78ed1
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146369"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>ASP.NET Core Blazor ライフサイクル

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

Blazor framework には、同期および非同期のライフサイクルメソッドが含まれています。 コンポーネントの初期化およびレンダリング中にコンポーネントに対して追加の操作を実行するには、ライフサイクルメソッドをオーバーライドします。

## <a name="lifecycle-methods"></a>ライフサイクル メソッド

### <a name="component-initialization-methods"></a>コンポーネントの初期化メソッド

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> と <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> は、その親コンポーネントから初期パラメーターを受け取った後に、コンポーネントが初期化されるときに呼び出されます。 コンポーネントが非同期操作を実行し、操作の完了時に更新する必要がある場合は、`OnInitializedAsync` を使用します。 これらのメソッドは、コンポーネントが最初にインスタンス化されるときに1回だけ呼び出されます。

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

## <a name="state-changes"></a>状態変更

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

## <a name="handle-errors"></a>エラーの処理

ライフサイクルメソッドの実行中のエラー処理の詳細については、「<xref:blazor/handle-errors#lifecycle-methods>」を参照してください。
