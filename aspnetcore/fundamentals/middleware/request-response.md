---
title: ASP.NET Core での要求と応答の操作
author: jkotalik
description: ASP.NET Core で要求本文の読み取りと応答本文の書き込みを行う方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: e992401da2d194b178afbe51a293d103def0f940
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238153"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>ASP.NET Core での要求と応答の操作

作成者: [Justin Kotalik](https://github.com/jkotalik)

この記事では、要求本文からの読み取りと、応答本文への書き込みを行う方法について説明します。 ミドルウェアを作成するときは、これらの操作のコードが必要になることがあります。 操作は MVC と Razor Pages によって処理されるため、ミドルウェアの作成以外では、通常、カスタムコードは必要ありません。

要求と応答の本文には 2 つの抽象化があります: <xref:System.IO.Stream> と <xref:System.IO.Pipelines.Pipe> です。 要求の読み取りでは、[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) は <xref:System.IO.Stream> に、`HttpRequest.BodyReader` は <xref:System.IO.Pipelines.PipeReader> になります。 応答の書き込みでは、[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) は `HttpResponse.BodyWriter` は <xref:System.IO.Pipelines.PipeWriter> になります。

パイプラインは、ストリームよりも推奨されます。 一部の単純な操作ではストリームの方が使いやすい場合がありますが、パイプラインの方がパフォーマンスに優れていて、ほとんどのシナリオでより簡単に使えます。 ASP.NET Core では、内部的にストリームではなくパイプラインが使われ始めています。 その例は次のとおりです。

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

ストリームがフレームワークから削除されていません。 ストリームは .NET 全体で使われ続けます。また、ストリームの種類の多くにはパイプラインに相当するものがありません (`FileStreams` や `ResponseCompression` など)。

## <a name="stream-examples"></a>ストリームの例

要求本文全体を、改行で分割した文字列のリストとして読み取るミドルウェアを作成したいとします。 単純なストリームの実装は、次の例のようになります。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

このコードで動作しますが、いくつか問題があります。

* 例では、`StringBuilder` に追加する前に、すぐに破棄される別の文字列 (`encodedString`) を作成しています。 このプロセスはストリーム内のすべてのバイトに対して実行されるため、要求本文全体のサイズよりも余分にメモリの割り当てが発生します。
* 例では、改行で分割する前に文字列全体を読み取っています。 バイト配列内で改行をチェックした方がより効率的です。

これらの問題をいくつか修正した例を次に示します。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

前の例の場合:

* 要求本文全体を `StringBuilder` にバッファーすることがありません (改行文字がない場合を除く)。
* 文字列の `Split` を呼び出しません。

ただし、まだいくつかの問題が残っています。

* 改行文字がスパースだった場合、要求本文の多くが文字列にバッファーされます。
* コードで依然として文字列 (`remainingString`) が作成され、文字列バッファーに追加されているため、余分な割り当てが発生します。

これらの問題は修正可能ですが、わずかな改善でコードがますます複雑になっていきます。 パイプラインには、これらの問題を、コードの複雑さを最小限に抑えて解決する方法が用意されています。

## <a name="pipelines"></a>パイプライン

同じシナリオを、`PipeReader` を使って処理する方法を次の例に示します。

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

この例では、ストリームの実装で発生していた多くの問題が修正されています。

* 使われていないバイトを `PipeReader` で処理するため、文字列バッファーが不要です。
* エンコードされた文字列は、返される文字列のリストに直接追加されます。
* 文字列で使うメモリを除いて、文字列の作成に関する割り当てが発生しません (`ToArray()` の呼び出しを除く)。

## <a name="adapters"></a>アダプター

`Body` と `BodyReader/BodyWriter` の両方のプロパティが、`HttpRequest` と `HttpResponse` で使用できます。 `Body` を別のストリームに設定すると、新しいアダプターのセットにより、各種類が別のものに自動的に適応します。 `HttpRequest.Body` を新しいストリームに設定した場合、`HttpRequest.BodyReader` は自動的に、`HttpRequest.Body` をラップする新しい `PipeReader` に設定されます。

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` は、ヘッダーが変更不可能であり、また `OnStarting` コールバックを実行することを示すために使います。 サーバーとして Kestrel を使う場合、`PipeReader` を使う前に `StartAsync` を呼び出すことで、`GetMemory` によって返されるメモリが、外部バッファーではなく Kestrel の内部 <xref:System.IO.Pipelines.Pipe> に属するよう保証できます。

## <a name="additional-resources"></a>その他の技術情報

* [System.IO.Pipelines の概要](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
