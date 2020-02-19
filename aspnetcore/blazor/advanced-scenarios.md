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
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="bd7bb-103">ASP.NET Core Blazor の高度なシナリオ</span><span class="sxs-lookup"><span data-stu-id="bd7bb-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="bd7bb-104">[Luke Latham](https://github.com/guardrex)および[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="bd7bb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="bd7bb-105">手動の RenderTreeBuilder ロジック</span><span class="sxs-lookup"><span data-stu-id="bd7bb-105">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="bd7bb-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` には、コンポーネントと要素を操作するためのメソッドがC#用意されています</span><span class="sxs-lookup"><span data-stu-id="bd7bb-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="bd7bb-107">コンポーネントの作成に `RenderTreeBuilder` を使用することは、高度なシナリオです。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-107">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="bd7bb-108">形式が正しくないコンポーネント (たとえば、閉じていないマークアップタグなど) は、未定義の動作になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-108">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="bd7bb-109">次の `PetDetails` コンポーネントを検討してください。これは手動で別のコンポーネントに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-109">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="bd7bb-110">次の例では、`CreateComponent` メソッドのループによって、3つの `PetDetails` コンポーネントが生成されます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-110">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="bd7bb-111">`RenderTreeBuilder` メソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソースコードの行番号です。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-111">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="bd7bb-112">Blazor の相違アルゴリズムは、個別の呼び出し呼び出しではなく、個別のコード行に対応するシーケンス番号に依存します。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-112">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="bd7bb-113">`RenderTreeBuilder` メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコーディングします。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-113">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="bd7bb-114">**計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。**</span><span class="sxs-lookup"><span data-stu-id="bd7bb-114">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="bd7bb-115">詳細については、「[シーケンス番号はコード行番号に関連し、実行順序を](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)使用しない」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-115">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="bd7bb-116">`BuiltContent` コンポーネント:</span><span class="sxs-lookup"><span data-stu-id="bd7bb-116">`BuiltContent` component:</span></span>

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
> <span data-ttu-id="bd7bb-117">`Microsoft.AspNetCore.Components.RenderTree` の型により、レンダリング操作の*結果*を処理できます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-117">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="bd7bb-118">これらは、Blazor framework 実装の内部的な詳細です。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-118">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="bd7bb-119">これらの型は*不安定*であると見なされ、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-119">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="bd7bb-120">シーケンス番号は、実行順序ではなくコード行番号に関連します</span><span class="sxs-lookup"><span data-stu-id="bd7bb-120">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="bd7bb-121">Razor コンポーネントファイル (*razor*) は常にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-121">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="bd7bb-122">コンパイル手順を使用すると、実行時にアプリのパフォーマンスを向上させる情報を挿入できるため、コードを解釈するよりもコンパイルの方が優れています。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-122">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="bd7bb-123">これらの機能強化の主要な例には、*シーケンス番号*が含まれます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-123">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="bd7bb-124">シーケンス番号は、コードの特定の行と順序付けられた行の出力元をランタイムに示します。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-124">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="bd7bb-125">ランタイムは、この情報を使用して、効率的なツリーの相違を線形時間で生成します。これは、一般的なツリーの相違アルゴリズムでは通常可能です。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-125">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="bd7bb-126">次の Razor コンポーネント (*razor*) ファイルについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-126">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="bd7bb-127">上記のコードは、次のようにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-127">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="bd7bb-128">コードを初めて実行するときに、`someFlag` が `true`場合、ビルダーは次のメッセージを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-128">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="bd7bb-129">Sequence</span><span class="sxs-lookup"><span data-stu-id="bd7bb-129">Sequence</span></span> | <span data-ttu-id="bd7bb-130">種類</span><span class="sxs-lookup"><span data-stu-id="bd7bb-130">Type</span></span>      | <span data-ttu-id="bd7bb-131">Data</span><span class="sxs-lookup"><span data-stu-id="bd7bb-131">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="bd7bb-132">0</span><span class="sxs-lookup"><span data-stu-id="bd7bb-132">0</span></span>        | <span data-ttu-id="bd7bb-133">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="bd7bb-133">Text node</span></span> | <span data-ttu-id="bd7bb-134">First (先頭へ)</span><span class="sxs-lookup"><span data-stu-id="bd7bb-134">First</span></span>  |
| <span data-ttu-id="bd7bb-135">1</span><span class="sxs-lookup"><span data-stu-id="bd7bb-135">1</span></span>        | <span data-ttu-id="bd7bb-136">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="bd7bb-136">Text node</span></span> | <span data-ttu-id="bd7bb-137">秒</span><span class="sxs-lookup"><span data-stu-id="bd7bb-137">Second</span></span> |

<span data-ttu-id="bd7bb-138">`someFlag` が `false`になり、マークアップが再び表示されるとします。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-138">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="bd7bb-139">この時点で、ビルダーは次のものを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-139">This time, the builder receives:</span></span>

| <span data-ttu-id="bd7bb-140">Sequence</span><span class="sxs-lookup"><span data-stu-id="bd7bb-140">Sequence</span></span> | <span data-ttu-id="bd7bb-141">種類</span><span class="sxs-lookup"><span data-stu-id="bd7bb-141">Type</span></span>       | <span data-ttu-id="bd7bb-142">Data</span><span class="sxs-lookup"><span data-stu-id="bd7bb-142">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="bd7bb-143">1</span><span class="sxs-lookup"><span data-stu-id="bd7bb-143">1</span></span>        | <span data-ttu-id="bd7bb-144">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="bd7bb-144">Text node</span></span>  | <span data-ttu-id="bd7bb-145">秒</span><span class="sxs-lookup"><span data-stu-id="bd7bb-145">Second</span></span> |

<span data-ttu-id="bd7bb-146">ランタイムが diff を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な*編集スクリプト*が生成されます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-146">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="bd7bb-147">最初のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-147">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="bd7bb-148">プログラムによってシーケンス番号を生成する場合の問題</span><span class="sxs-lookup"><span data-stu-id="bd7bb-148">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="bd7bb-149">代わりに、次のレンダーツリービルダーのロジックを記述したとします。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-149">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="bd7bb-150">これで、最初の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-150">Now, the first output is:</span></span>

| <span data-ttu-id="bd7bb-151">Sequence</span><span class="sxs-lookup"><span data-stu-id="bd7bb-151">Sequence</span></span> | <span data-ttu-id="bd7bb-152">種類</span><span class="sxs-lookup"><span data-stu-id="bd7bb-152">Type</span></span>      | <span data-ttu-id="bd7bb-153">Data</span><span class="sxs-lookup"><span data-stu-id="bd7bb-153">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="bd7bb-154">0</span><span class="sxs-lookup"><span data-stu-id="bd7bb-154">0</span></span>        | <span data-ttu-id="bd7bb-155">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="bd7bb-155">Text node</span></span> | <span data-ttu-id="bd7bb-156">First (先頭へ)</span><span class="sxs-lookup"><span data-stu-id="bd7bb-156">First</span></span>  |
| <span data-ttu-id="bd7bb-157">1</span><span class="sxs-lookup"><span data-stu-id="bd7bb-157">1</span></span>        | <span data-ttu-id="bd7bb-158">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="bd7bb-158">Text node</span></span> | <span data-ttu-id="bd7bb-159">秒</span><span class="sxs-lookup"><span data-stu-id="bd7bb-159">Second</span></span> |

<span data-ttu-id="bd7bb-160">この結果は前のケースと同じであるため、負の問題は存在しません。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-160">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="bd7bb-161">2番目のレンダリングでは `someFlag` が `false`、出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-161">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="bd7bb-162">Sequence</span><span class="sxs-lookup"><span data-stu-id="bd7bb-162">Sequence</span></span> | <span data-ttu-id="bd7bb-163">種類</span><span class="sxs-lookup"><span data-stu-id="bd7bb-163">Type</span></span>      | <span data-ttu-id="bd7bb-164">Data</span><span class="sxs-lookup"><span data-stu-id="bd7bb-164">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="bd7bb-165">0</span><span class="sxs-lookup"><span data-stu-id="bd7bb-165">0</span></span>        | <span data-ttu-id="bd7bb-166">テキスト ノード</span><span class="sxs-lookup"><span data-stu-id="bd7bb-166">Text node</span></span> | <span data-ttu-id="bd7bb-167">秒</span><span class="sxs-lookup"><span data-stu-id="bd7bb-167">Second</span></span> |

<span data-ttu-id="bd7bb-168">今回は、diff アルゴリズムによって*2 つ*の変更が行われたことが確認され、アルゴリズムによって次の編集スクリプトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-168">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="bd7bb-169">最初のテキストノードの値を `Second`に変更します。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-169">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="bd7bb-170">2番目のテキストノードを削除します。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-170">Remove the second text node.</span></span>

<span data-ttu-id="bd7bb-171">シーケンス番号を生成すると、`if/else` 分岐とループが元のコードに存在する場所に関する有用な情報がすべて失われます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-171">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="bd7bb-172">これにより、diff は前の**2 倍**になります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-172">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="bd7bb-173">これは簡単な例です。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-173">This is a trivial example.</span></span> <span data-ttu-id="bd7bb-174">複雑で深く入れ子になった構造体と、特にループを使用したより現実的なケースでは、通常、パフォーマンスコストが高くなります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-174">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="bd7bb-175">挿入または削除されたループブロックまたは分岐をすぐに識別するのではなく、diff アルゴリズムがレンダリングツリーに深く依存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-175">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="bd7bb-176">これにより、古い構造と新しい構造が相互にどのように関連しているかが diff アルゴリズムによって誤って通知されるため、通常、スクリプトの編集時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-176">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="bd7bb-177">ガイダンスと結論</span><span class="sxs-lookup"><span data-stu-id="bd7bb-177">Guidance and conclusions</span></span>

* <span data-ttu-id="bd7bb-178">シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-178">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="bd7bb-179">コンパイル時にキャプチャされない場合、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-179">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="bd7bb-180">手動で実装された `RenderTreeBuilder` ロジックの長いブロックは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-180">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="bd7bb-181">Razor ファイルを優先し、コンパイラがシーケンス番号を処理できるようにし*ます*。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-181">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="bd7bb-182">手動の `RenderTreeBuilder` ロジックを回避できない場合は、長いブロックのコードを `OpenRegion`/`CloseRegion` 呼び出しでラップされた小さな部分に分割します。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-182">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="bd7bb-183">各リージョンにはシーケンス番号の個別のスペースがあるため、各リージョン内のゼロ (または任意の任意の数) から再起動できます。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-183">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="bd7bb-184">シーケンス番号がハードコードされている場合、diff アルゴリズムでは、シーケンス番号の値を大きくする必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-184">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="bd7bb-185">初期値とギャップは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-185">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="bd7bb-186">正当な選択肢の1つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1または数百 (または任意の間隔) で増やすことです。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-186">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="bd7bb-187"> はシーケンス番号を使用しますが、他のツリー比較 UI フレームワークでは使用しません。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-187"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="bd7bb-188">シーケンス番号を使用すると比較ははるかに高速になり*ます。* また、Blazor には、開発者が razor ファイルを作成するときにシーケンス番号を自動的に処理するコンパイル手順の利点があります。</span><span class="sxs-lookup"><span data-stu-id="bd7bb-188">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>
