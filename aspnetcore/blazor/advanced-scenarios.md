---
title: ASP.NET Core Blazor 高度なシナリオ
author: guardrex
description: 手動の RenderTreeBuilder ロジックをアプリに組み込む方法など、Blazorの高度なシナリオについて説明します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5e0618faa7b1b5e4cc15e30d9c16afaf7ccabaf0
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453182"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core Blazor の高度なシナリオ

[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)

## <a name="manual-rendertreebuilder-logic"></a>手動の RenderTreeBuilder ロジック

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` には、コンポーネントと要素を操作するためのメソッドがC#用意されています

> [!NOTE]
> コンポーネントの作成に `RenderTreeBuilder` を使用することは、高度なシナリオです。 形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。

次の `PetDetails` コンポーネントを検討してください。これは手動で別のコンポーネントに組み込むことができます。

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

次の例では、`CreateComponent` メソッドのループによって、3つの `PetDetails` コンポーネントが生成されます。 `RenderTreeBuilder` メソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。 Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。 `RenderTreeBuilder` メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。 **計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。** 詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。

`BuiltContent` コンポーネント:

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> [!WARNING]
> `Microsoft.AspNetCore.Components.RenderTree` の型により、レンダリング操作の*結果*を処理できます。 これらは、Blazor framework 実装の内部的な詳細です。 これらの型は*不安定*であると見なされ、今後のリリースで変更される可能性があります。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>シーケンス番号は、実行順序ではなくコード行番号に関連します

Razor コンポーネントファイル (*razor*) は常にコンパイルされます。 コンパイル手順を使用すると、実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、コードを解釈するよりもコンパイルの方が優れています。

これらの機能強化の主要な例には、*シーケンス番号*が含まれます。 シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。 ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。

次の Razor コンポーネント (*razor*) ファイルについて考えてみます。

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

上記のコードは、次のようにコンパイルされます。

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

コードを初めて実行するときに、`someFlag` が `true`場合、ビルダーは次のメッセージを受け取ります。

| Sequence | 種類      | Data   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First (先頭へ)  |
| 1        | テキスト ノード | 秒 |

`someFlag` が `false`になり、マークアップが再び表示されるとします。 この時点で、ビルダーは次のものを受け取ります。

| Sequence | 種類       | Data   |
| :------: | ---------- | :----: |
| 1        | テキスト ノード  | 秒 |

ランタイムが diff を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な*編集スクリプト*が生成されます。

* 最初のテキストノードを削除します。

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>プログラムによってシーケンス番号を生成する場合の問題

代わりに、次のレンダーツリービルダーのロジックを記述したとします。

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

これで、最初の出力は次のようになります。

| Sequence | 種類      | Data   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First (先頭へ)  |
| 1        | テキスト ノード | 秒 |

この結果は前のケースと同じであるため、負の問題は存在しません。 2番目のレンダリングでは `someFlag` が `false`、出力は次のようになります。

| Sequence | 種類      | Data   |
| :------: | --------- | ------ |
| 0        | テキスト ノード | 秒 |

今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。

* 最初のテキストノードの値を `Second`に変更します。
* 2番目のテキストノードを削除します。

シーケンス番号を生成すると、`if/else` 分岐とループが元のコードに存在する場所に関する有用な情報がすべて失われます。 これにより、diff は前の**2 倍**になります。

これは簡単な例です。 複雑で深く入れ子になった構造体と、特にループを使用したより現実的なケースでは、通常、パフォーマンスコストが高くなります。 挿入または削除されたループブロックまたは分岐をすぐに識別するのではなく、diff アルゴリズムがレンダリングツリーに深く依存する必要があります。 これにより、古い構造と新しい構造が相互にどのように関連しているかが diff アルゴリズムによって誤って通知されるため、通常、スクリプトの編集時間が長くなります。

### <a name="guidance-and-conclusions"></a>ガイダンスと結論

* シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。
* コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。
* 手動で実装された `RenderTreeBuilder` ロジックの長いブロックは記述しないでください。 Razor ファイルを優先し、コンパイラがシーケンス番号を処理できるようにし*ます*。 手動の `RenderTreeBuilder` ロジックを回避できない場合は、長いブロックのコードを `OpenRegion`/`CloseRegion` 呼び出しでラップされた小さな部分に分割します。 各リージョンにはシーケンス番号の個別のスペースがあるため、各リージョン内のゼロ (または任意の任意の数) から再起動できます。
* シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。 初期値とギャップは関係ありません。 正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。 
* Blazor はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。 シーケンス番号を使用すると比較ははるかに高速になり*ます。* また、Blazor には、開発者が razor ファイルを作成するときにシーケンス番号を自動的に処理するコンパイル手順の利点があります。
