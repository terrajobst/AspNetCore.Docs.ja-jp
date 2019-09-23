---
title: ASP.NET Core での要求と応答の操作
author: jkotalik
description: ASP.NET Core で要求本文の読み取りと応答本文の書き込みを行う方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 5e531c0ce0ed48097054fd81ddc3655a66cc7c5f
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081680"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="5ced7-103">ASP.NET Core での要求と応答の操作</span><span class="sxs-lookup"><span data-stu-id="5ced7-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="5ced7-104">作成者: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="5ced7-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="5ced7-105">この記事では、要求本文からの読み取りと、応答本文への書き込みを行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="5ced7-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="5ced7-106">ミドルウェアを作成するときは、これらの操作のコードが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="5ced7-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="5ced7-107">操作は MVC と Razor Pages によって処理されるため、ミドルウェアの作成以外では、通常、カスタムコードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5ced7-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="5ced7-108">要求と応答の本文には 2 つの抽象化があります: <xref:System.IO.Stream> と <xref:System.IO.Pipelines.Pipe> です。</span><span class="sxs-lookup"><span data-stu-id="5ced7-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="5ced7-109">要求の読み取りでは、[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) は <xref:System.IO.Stream> に、`HttpRequest.BodyReader` は <xref:System.IO.Pipelines.PipeReader> になります。</span><span class="sxs-lookup"><span data-stu-id="5ced7-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="5ced7-110">応答の書き込みでは、[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) は <xref:System.IO.Stream>、`HttpResponse.BodyWriter` は <xref:System.IO.Pipelines.PipeWriter> になります。</span><span class="sxs-lookup"><span data-stu-id="5ced7-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="5ced7-111">パイプラインは、ストリームよりも推奨されます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-111">Pipelines are recommended over streams.</span></span> <span data-ttu-id="5ced7-112">一部の単純な操作ではストリームの方が使いやすい場合がありますが、パイプラインの方がパフォーマンスに優れていて、ほとんどのシナリオでより簡単に使えます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="5ced7-113">ASP.NET Core では、内部的にストリームではなくパイプラインが使われ始めています。</span><span class="sxs-lookup"><span data-stu-id="5ced7-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="5ced7-114">その例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5ced7-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="5ced7-115">ストリームがフレームワークから削除されていません。</span><span class="sxs-lookup"><span data-stu-id="5ced7-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="5ced7-116">ストリームは .NET 全体で使われ続けます。また、ストリームの種類の多くにはパイプラインに相当するものがありません (`FileStreams` や `ResponseCompression` など)。</span><span class="sxs-lookup"><span data-stu-id="5ced7-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="5ced7-117">ストリームの例</span><span class="sxs-lookup"><span data-stu-id="5ced7-117">Stream examples</span></span>

<span data-ttu-id="5ced7-118">要求本文全体を、改行で分割した文字列のリストとして読み取るミドルウェアを作成したいとします。</span><span class="sxs-lookup"><span data-stu-id="5ced7-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="5ced7-119">単純なストリームの実装は、次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="5ced7-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="5ced7-120">このコードで動作しますが、いくつか問題があります。</span><span class="sxs-lookup"><span data-stu-id="5ced7-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="5ced7-121">例では、`StringBuilder` に追加する前に、すぐに破棄される別の文字列 (`encodedString`) を作成しています。</span><span class="sxs-lookup"><span data-stu-id="5ced7-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="5ced7-122">このプロセスはストリーム内のすべてのバイトに対して実行されるため、要求本文全体のサイズよりも余分にメモリの割り当てが発生します。</span><span class="sxs-lookup"><span data-stu-id="5ced7-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="5ced7-123">例では、改行で分割する前に文字列全体を読み取っています。</span><span class="sxs-lookup"><span data-stu-id="5ced7-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="5ced7-124">バイト配列内で改行をチェックした方がより効率的です。</span><span class="sxs-lookup"><span data-stu-id="5ced7-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="5ced7-125">これらの問題をいくつか修正した例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5ced7-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="5ced7-126">前の例の場合:</span><span class="sxs-lookup"><span data-stu-id="5ced7-126">This preceding example:</span></span>

* <span data-ttu-id="5ced7-127">要求本文全体を `StringBuilder` にバッファーすることがありません (改行文字がない場合を除く)。</span><span class="sxs-lookup"><span data-stu-id="5ced7-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="5ced7-128">文字列の `Split` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="5ced7-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="5ced7-129">ただし、まだいくつかの問題が残っています。</span><span class="sxs-lookup"><span data-stu-id="5ced7-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="5ced7-130">改行文字がスパースだった場合、要求本文の多くが文字列にバッファーされます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="5ced7-131">コードで依然として文字列 (`remainingString`) が作成され、文字列バッファーに追加されているため、余分な割り当てが発生します。</span><span class="sxs-lookup"><span data-stu-id="5ced7-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="5ced7-132">これらの問題は修正可能ですが、わずかな改善でコードがますます複雑になっていきます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="5ced7-133">パイプラインには、これらの問題を、コードの複雑さを最小限に抑えて解決する方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="5ced7-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="5ced7-134">パイプライン</span><span class="sxs-lookup"><span data-stu-id="5ced7-134">Pipelines</span></span>

<span data-ttu-id="5ced7-135">同じシナリオを、`PipeReader` を使って処理する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="5ced7-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="5ced7-136">この例では、ストリームの実装で発生していた多くの問題が修正されています。</span><span class="sxs-lookup"><span data-stu-id="5ced7-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="5ced7-137">使われていないバイトを `PipeReader` で処理するため、文字列バッファーが不要です。</span><span class="sxs-lookup"><span data-stu-id="5ced7-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="5ced7-138">エンコードされた文字列は、返される文字列のリストに直接追加されます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="5ced7-139">文字列で使うメモリを除いて、文字列の作成に関する割り当てが発生しません (`ToArray()` の呼び出しを除く)。</span><span class="sxs-lookup"><span data-stu-id="5ced7-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="5ced7-140">アダプター</span><span class="sxs-lookup"><span data-stu-id="5ced7-140">Adapters</span></span>

<span data-ttu-id="5ced7-141">`Body` と `BodyReader/BodyWriter` の両方のプロパティが、`HttpRequest` と `HttpResponse` で使用できます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="5ced7-142">`Body` を別のストリームに設定すると、新しいアダプターのセットにより、各種類が別のものに自動的に適応します。</span><span class="sxs-lookup"><span data-stu-id="5ced7-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="5ced7-143">`HttpRequest.Body` を新しいストリームに設定した場合、`HttpRequest.BodyReader` は自動的に、`HttpRequest.Body` をラップする新しい `PipeReader` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="5ced7-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="5ced7-144">StartAsync</span></span>

<span data-ttu-id="5ced7-145">`HttpResponse.StartAsync` は、ヘッダーが変更不可能であり、また `OnStarting` コールバックを実行することを示すために使います。</span><span class="sxs-lookup"><span data-stu-id="5ced7-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="5ced7-146">サーバーとして Kestrel を使う場合、`PipeReader` を使う前に `StartAsync` を呼び出すことで、`GetMemory` によって返されるメモリが、外部バッファーではなく Kestrel の内部 <xref:System.IO.Pipelines.Pipe> に属するよう保証できます。</span><span class="sxs-lookup"><span data-stu-id="5ced7-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ced7-147">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5ced7-147">Additional resources</span></span>

* [<span data-ttu-id="5ced7-148">System.IO.Pipelines の概要</span><span class="sxs-lookup"><span data-stu-id="5ced7-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
