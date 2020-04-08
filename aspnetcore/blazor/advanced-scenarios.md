---
title: ASP.NET Core Blazor の高度なシナリオ
author: guardrex
description: Blazor の高度なシナリオについて説明します。これには、手動の RenderTreeBuilder ロジックをアプリに組み込む方法などが含まれます。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5edbbe36e8389bac0335594b1e4331aee1c02867
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647414"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a>ASP.NET Core Blazor の高度なシナリオ

作成者: [Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)

## <a name="blazor-server-circuit-handler"></a>Blazor サーバー回線ハンドラー

Blazor サーバーを使用すると、コードで "*回線ハンドラー*" を定義できます。これにより、ユーザーの回線の状態の変更時にコードを実行できます。 回線ハンドラーは、`CircuitHandler` から派生させ、そのクラスをアプリのサービス コンテナーに登録することで実装します。 次の回線ハンドラーの例では、開いている SignalR 接続を追跡します。

```csharp
using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Server.Circuits;

public class TrackingCircuitHandler : CircuitHandler
{
    private HashSet<Circuit> _circuits = new HashSet<Circuit>();

    public override Task OnConnectionUpAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Add(circuit);

        return Task.CompletedTask;
    }

    public override Task OnConnectionDownAsync(Circuit circuit, 
        CancellationToken cancellationToken)
    {
        _circuits.Remove(circuit);

        return Task.CompletedTask;
    }

    public int ConnectedCircuits => _circuits.Count;
}
```

回線ハンドラーは DI を使用して登録されます。 スコープを持つインスタンスは、回線のインスタンスごとに作成されます。 前の例の `TrackingCircuitHandler` を使用すると、すべての回線の状態を追跡する必要があるため、シングルトン サービスが作成されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddSingleton<CircuitHandler, TrackingCircuitHandler>();
}
```

カスタム回線ハンドラーのメソッドでハンドルされない例外がスローされる場合は、その例外は Blazor サーバー回線にとって致命的です。 ハンドラーのコードまたはメソッドで例外が許容されるようにするには、エラー処理とログを含む 1 つ以上の [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントでコードをラップします。

ユーザーが切断し、フレームワークで回線の状態がクリーンアップされていることが原因で回線が終了すると、フレームワークによって回線の DI スコープが破棄されます。 スコープが破棄されると、<xref:System.IDisposable?displayProperty=fullName> を実装するサーキットスコープの DI サービスはすべて破棄されます。 破棄中にいずれかの DI サービスでハンドルされない例外がスローされると、フレームワークによって例外がログに記録されます。

## <a name="manual-rendertreebuilder-logic"></a>手動の RenderTreeBuilder ロジック

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` には、コンポーネントと要素を操作するためのメソッドが用意されています。これには、C# コードでコンポーネントを手動で作成することも含まれます。

> [!NOTE]
> コンポーネントの作成に `RenderTreeBuilder` を使用することは、高度なシナリオです。 形式が正しくないコンポーネント (閉じられていないマークアップ タグなど) により、定義されていない動作が発生する可能性があります。

次の `PetDetails` コンポーネントを考えてみましょう。これは手動で別のコンポーネントに組み込むことができます。

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

次の例では、`CreateComponent` メソッド内のループによって、3 つの `PetDetails` コンポーネントが生成されます。 `RenderTreeBuilder` メソッドを呼び出してコンポーネント (`OpenComponent` と `AddAttribute`) を作成する場合、シーケンス番号はソース コードの行番号になります。 Blazor の差分アルゴリズムは、個別の呼び出しではなく、個別のコード行に対応するシーケンス番号に依存しています。 `RenderTreeBuilder` メソッドを使用してコンポーネントを作成する場合は、シーケンス番号の引数をハードコードします。 **計算またはカウンターを使用してシーケンス番号を生成すると、パフォーマンスが低下する可能性があります。** 詳細については、「[シーケンス番号は実行順序ではなくコード行番号に関係する](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)」セクションを参照してください。

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
> `Microsoft.AspNetCore.Components.RenderTree` の型により、レンダリング操作の "*結果*" の処理が許可されます。 これらは、Blazor フレームワーク実装の内部的な詳細です。 これらの型は "*不安定*" と考えるべきで、今後のリリースで変更される可能性があります。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>シーケンス番号は実行順序ではなくコード行番号に関係する

Razor コンポーネント ファイル (" *.razor*") は常にコンパイルされます。 コンパイル ステップは、実行時にアプリのパフォーマンスを向上させる情報を挿入するために使用できるため、コンパイルの方がコードを解釈するよりも潜在的な利点があります。

これらの機能強化の主な例として、"*シーケンス番号*" があります。 シーケンス番号は、出力がコードの個別の順序付けられたどの行からのものかをランタイムに示します。 ランタイムでは、この情報を使用して、効率的なツリーの差分を線形時間で生成します。これは、一般的なツリーの差分アルゴリズムで通常にできるよりもかなり高速です。

次の Razor コンポーネント (" *.razor*") ファイルについて考えてみましょう。

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

コードを初めて実行するときに、`someFlag` が `true` の場合、ビルダーは以下を受け取ります。

| シーケンス | 種類      | データ   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First (先頭へ)  |
| 1        | テキスト ノード | Second |

`someFlag` が `false` になり、マークアップが再びレンダリングされるとします。 今度は、ビルダーは以下を受け取ります。

| シーケンス | 種類       | データ   |
| :------: | ---------- | :----: |
| 1        | テキスト ノード  | Second |

ランタイムで差分を実行すると、シーケンス `0` の項目が削除されたことが認識されるため、次のような単純な "*編集スクリプト*" が生成されます。

* Remove the first text node. (最初のテキスト ノードを削除します。)

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a>プログラムによってシーケンス番号を生成する場合の問題

代わりに、次のレンダリング ツリー ビルダー ロジックを記述したとします。

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

最初の出力は次のようになります。

| シーケンス | 種類      | データ   |
| :------: | --------- | :----: |
| 0        | テキスト ノード | First (先頭へ)  |
| 1        | テキスト ノード | Second |

この結果は前のケースと同じであるため、否定的な問題は存在しません。 `someFlag` は 2 番目のレンダリングでは `false` で、出力は次のようになります。

| シーケンス | 種類      | データ   |
| :------: | --------- | ------ |
| 0        | テキスト ノード | Second |

今度は、差分アルゴリズムによって、"*2 つ*" の変更が発生したことが認識され、アルゴリズムによって次の編集スクリプトが生成されます。

* 最初のテキスト ノードの値を `Second` に変更します。
* 2 番目のテキスト ノードを削除します。

シーケンス番号を生成すると、`if/else` 分岐とループが元のコードのどこにあったかに関する有用な情報がすべて失われます。 これにより、差分が以前の **2 倍の長さ**なります。

これは簡単な例です。 複雑で深く入れ子になった構造体で、特にループがある、より現実的なケースでは、通常、パフォーマンス コストが高くなります。 どのループ ブロックまたは分岐が挿入または削除されたかを即座に特定する代わりに、差分アルゴリズムではレンダリング ツリー内を深く再帰処理する必要があります。 これにより、古い構造と新しい構造体が相互にどのように関連しているかについて差分アルゴリズムが誤って通知されるため、通常、より長い編集スクリプトを作成することが必要になります。

### <a name="guidance-and-conclusions"></a>ガイダンスと結論

* シーケンス番号が動的に生成される場合、アプリのパフォーマンスが低下します。
* コンパイル時にキャプチャされない限り、必要な情報が存在しないため、実行時にフレームワークで独自のシーケンス番号を自動的に作成することはできません。
* 手動で実装された `RenderTreeBuilder` ロジックの長いブロックは記述しないでください。 " *.razor*" ファイルを優先し、コンパイラがシーケンス番号を処理できるようにします。 手動の `RenderTreeBuilder` ロジックを回避できない場合は、長いブロックのコードを `OpenRegion`/`CloseRegion` 呼び出しでラップされたより小さな部分に分割します。 各リージョンには独自のシーケンス番号の個別のスペースがあるため、各リージョン内でゼロ (または他の任意の数) から再開できます。
* シーケンス番号がハードコードされている場合、差分アルゴリズムでは、シーケンス番号の値が増えることだけが要求されます。 初期値とギャップは関係ありません。 合理的な選択肢の 1 つは、コード行番号をシーケンス番号として使用するか、ゼロから開始し、1 つずつまたは 100 ずつ (または任意の間隔で) 増やすことです。 
* Blazor ではシーケンス番号が使用されていますが、他のツリー差分 UI フレームワークでは使用されていません。 シーケンス番号を使用すると、差分がはるかに高速になります。また、Blazor には、" *.razor*" ファイルを作成する開発者に対して、シーケンス番号を自動的に処理するコンパイル ステップの利点があります。

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a>Blazor サーバー アプリで大規模なデータ転送を実行する

シナリオによっては、JavaScript と Blazor 間で大量のデータを転送する必要があります。 通常、大規模なデータ転送は次の場合に発生します。

* ブラウザー ファイル システム API が、ファイルをアップロードまたはダウンロードするために使用されている場合。
* サードパーティのライブラリとの相互運用が必要な場合。

Blazor サーバーでは、パフォーマンスの問題を引き起こす可能性がある 1 つの大きなメッセージを渡すことができないように制限されています。

JavaScript と Blazor 間でデータを転送するコードを開発するときは、次のガイダンスを考慮してください。

* データをより小さな部分にスライスし、すべてのデータがサーバーによって受信されるまでデータ セグメントを順番に送信します。
* JavaScript および C# コードで大きなオブジェクトを割り当てないでください。
* データを送受信するときに、メイン UI スレッドを長時間ブロックしないでください。
* プロセスの完了時またはキャンセル時に、消費していたメモリを解放します。
* セキュリティ上の理由から、次の追加要件を適用します。
  * 渡すことのできるファイルまたはデータの最大サイズを宣言します。
  * クライアントからサーバーへの最小アップロード レートを宣言します。
* データがサーバーによって受信されたら、データは:
  * すべてのセグメントが収集されるまで、一時的にメモリ バッファーに格納できます。
  * 直ちに消費できます。 たとえば、データは、データベースに直ちに格納することも、セグメントを受信するたびにディスクに書き込むこともできます。

次のファイル アップローダー クラスは、クライアントとの JS 相互運用を処理します。 アップローダー クラスは、JS 相互運用を使用して次のことを行います。

* データ セグメントを送信するクライアントをポーリングします。
* ポーリングがタイムアウトした場合は、トランザクションを中止します。

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

前の例の場合:

* `_maxBase64SegmentSize` は `8192` に設定されます。これは `_maxBase64SegmentSize = _segmentSize * 4 / 3` から計算されます。
* 低レベルの .NET Core メモリ管理 API は、`_uploadedSegments` のサーバーにメモリ セグメントを格納するために使用されます。
* `ReceiveFile` メソッドは、JS 相互運用によるアップロードを処理するために使用されます。
  * ファイル サイズは、`_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)` を使用した JS 相互運用を通じて、バイト単位で決定されます。
  * 受信するセグメントの数が計算され、`numberOfSegments` に格納されます。
  * セグメントは、`for` を使用した JS 相互運用を通じて `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)` ループで要求されます。 デコードする前のセグメントは、最後を除いてすべてが 8192 バイトである必要があります。 クライアントは、効率的な方法でデータを送信することを強制されます。
  * 受信した各セグメントに対して、<xref:System.Convert.TryFromBase64String*> でデコードする前にチェックが実行されます。
  * アップロードが完了すると、データを含むストリームが新しい <xref:System.IO.Stream> (`SegmentedStream`) として返されます。

セグメント化されたストリーム クラスは、セグメントの一覧を読み取り専用のシーク可能ではない <xref:System.IO.Stream> として公開します。

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

次のコードでは、データを受け取る JavaScript 関数を実装します。

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```
