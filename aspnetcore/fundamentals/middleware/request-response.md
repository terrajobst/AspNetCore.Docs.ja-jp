---
title: ASP.NET Core での要求と応答の操作
author: jkotalik
description: ASP.NET Core で要求本文の読み取りと応答本文の書き込みを行う方法について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 0c321dad256e239b61907980c09d2c088c1407ff
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538574"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="d33f5-103">ASP.NET Core での要求と応答の操作</span><span class="sxs-lookup"><span data-stu-id="d33f5-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="d33f5-104">作成者: [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="d33f5-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="d33f5-105">この記事では、要求本文からの読み取りと、応答本文への書き込みを行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d33f5-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="d33f5-106">ミドルウェアを記述している場合、これらの操作に関するコードを記述する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="d33f5-106">You might need to write code for these operations when you're writing middleware.</span></span> <span data-ttu-id="d33f5-107">それ以外の場合は、MVC と Razor Pages によって操作が処理されるため、通常はこのコードを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d33f5-107">Otherwise, you typically don't have to write this code because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="d33f5-108">ASP.NET Core 3.0 には、要求と応答の本文に関する 2 つの抽象化があります: <xref:System.IO.Stream> と <xref:System.IO.Pipelines.Pipe> です。</span><span class="sxs-lookup"><span data-stu-id="d33f5-108">In ASP.NET Core 3.0, there are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="d33f5-109">要求の読み取りでは、[HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) は <xref:System.IO.Stream> に、`HttpRequest.BodyPipe` は <xref:System.IO.Pipelines.PipeReader> になります。</span><span class="sxs-lookup"><span data-stu-id="d33f5-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyPipe` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="d33f5-110">応答の書き込みでは、[HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) は `HttpResponse.BodyPipe` は <xref:System.IO.Pipelines.PipeWriter> になります。</span><span class="sxs-lookup"><span data-stu-id="d33f5-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyPipe` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="d33f5-111">ストリームよりもパイプラインをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d33f5-111">We recommend pipelines over streams.</span></span> <span data-ttu-id="d33f5-112">一部の単純な操作ではストリームの方が使いやすい場合がありますが、パイプラインの方がパフォーマンスに優れていて、ほとんどのシナリオでより簡単に使えます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="d33f5-113">ASP.NET Core では、3.0 から、内部的にストリームではなくパイプラインが使われ始めています。</span><span class="sxs-lookup"><span data-stu-id="d33f5-113">In 3.0, ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="d33f5-114">その例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d33f5-114">Examples include:</span></span>

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

<span data-ttu-id="d33f5-115">ストリームはなくなりません。</span><span class="sxs-lookup"><span data-stu-id="d33f5-115">Streams aren't going away.</span></span> <span data-ttu-id="d33f5-116">それらは .NET 全体で使われ続けます。また、ストリームの種類の多くには対応するパイプラインがありません (`FileStreams` や `ResponseCompression` など)。</span><span class="sxs-lookup"><span data-stu-id="d33f5-116">They continue to be used throughout .NET, and many stream types don't have pipe equivalents, like `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="d33f5-117">ストリームの例</span><span class="sxs-lookup"><span data-stu-id="d33f5-117">Stream examples</span></span>

<span data-ttu-id="d33f5-118">要求本文全体を、改行で分割した文字列のリストとして読み取るミドルウェアを作成したいとします。</span><span class="sxs-lookup"><span data-stu-id="d33f5-118">Suppose you want to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="d33f5-119">単純なストリームの実装は、次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="d33f5-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="d33f5-120">このコードで動作しますが、いくつか問題があります。</span><span class="sxs-lookup"><span data-stu-id="d33f5-120">This code works, but there are some issues:</span></span>

- <span data-ttu-id="d33f5-121">例では、`StringBuilder` に追加する前に、すぐに破棄される別の文字列 (`encodedString`) を作成しています。</span><span class="sxs-lookup"><span data-stu-id="d33f5-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="d33f5-122">このプロセスはストリーム内のすべてのバイトに対して実行されるため、要求本文全体のサイズよりも余分にメモリの割り当てが発生します。</span><span class="sxs-lookup"><span data-stu-id="d33f5-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
- <span data-ttu-id="d33f5-123">例では、改行で分割する前に文字列全体を読み取っています。</span><span class="sxs-lookup"><span data-stu-id="d33f5-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="d33f5-124">バイト配列内で改行をチェックした方がより効率的です。</span><span class="sxs-lookup"><span data-stu-id="d33f5-124">It would be more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="d33f5-125">これらの問題をいくつか修正した例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d33f5-125">Here's an example that fixes some of these issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="d33f5-126">この例の場合:</span><span class="sxs-lookup"><span data-stu-id="d33f5-126">This example:</span></span>

- <span data-ttu-id="d33f5-127">要求本文全体を `StringBuilder` にバッファーすることがありません (改行文字がない場合を除く)。</span><span class="sxs-lookup"><span data-stu-id="d33f5-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
- <span data-ttu-id="d33f5-128">文字列の `Split` を呼び出しません。</span><span class="sxs-lookup"><span data-stu-id="d33f5-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="d33f5-129">ただし、まだいくつかの問題が残っています。</span><span class="sxs-lookup"><span data-stu-id="d33f5-129">However, there are still are a few issues:</span></span>

- <span data-ttu-id="d33f5-130">改行文字がスパースだった場合、要求本文の多くが文字列にバッファーされます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-130">If newline characters are sparse, much of the request body is buffered in the string .</span></span>
- <span data-ttu-id="d33f5-131">依然として文字列 (`remainingString`) を作成し、それらを文字列バッファーに追加しているため、余分な割り当てが発生します。</span><span class="sxs-lookup"><span data-stu-id="d33f5-131">It still creates strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="d33f5-132">これらの問題は修正可能ですが、わずかな改善でコードがますます複雑になっていきます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-132">These issues are fixable, but the code is becoming more and more complicated with little improvement.</span></span> <span data-ttu-id="d33f5-133">パイプラインには、これらの問題を、コードの複雑さを最小限に抑えて解決する方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="d33f5-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="d33f5-134">パイプライン</span><span class="sxs-lookup"><span data-stu-id="d33f5-134">Pipelines</span></span>

<span data-ttu-id="d33f5-135">同じシナリオを、`PipeReader` を使って処理する方法を次の例に示します。</span><span class="sxs-lookup"><span data-stu-id="d33f5-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="d33f5-136">この例では、ストリームの実装で発生していた多くの問題が修正されています。</span><span class="sxs-lookup"><span data-stu-id="d33f5-136">This example fixes many issues that the streams implementations had:</span></span>

- <span data-ttu-id="d33f5-137">使われていないバイトを `PipeReader` で処理するため、文字列バッファーが不要です。</span><span class="sxs-lookup"><span data-stu-id="d33f5-137">There is no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
- <span data-ttu-id="d33f5-138">エンコードされた文字列は、返される文字列のリストに直接追加されます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-138">Encoded strings are directly added to the list of returned strings.</span></span>
- <span data-ttu-id="d33f5-139">文字列で使うメモリを除いて、文字列の作成に関する割り当てが発生しません (`ToArray()` の呼び出しを除く)。</span><span class="sxs-lookup"><span data-stu-id="d33f5-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="d33f5-140">アダプター</span><span class="sxs-lookup"><span data-stu-id="d33f5-140">Adapters</span></span>

<span data-ttu-id="d33f5-141">これで、`Body` と `BodyPipe` プロパティの両方を `HttpRequest` と `HttpResponse` に対して使用できるようになりました。`Body` を別のストリームに設定した場合はどうなるでしょうか。</span><span class="sxs-lookup"><span data-stu-id="d33f5-141">Now that both `Body` and `BodyPipe` properties are available for `HttpRequest` and `HttpResponse`, what happens when you set `Body` to a different stream?</span></span> <span data-ttu-id="d33f5-142">3\.0 では、新しいアダプターのセットにより、各種類を別のものに自動的に適応させます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-142">In 3.0, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="d33f5-143">たとえば、`HttpRequest.Body` を新しいストリームに設定した場合、`HttpRequest.BodyPipe` は自動的に、`HttpRequest.Body` をラップする新しい `PipeReader` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-143">For example, if you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyPipe` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span> <span data-ttu-id="d33f5-144">`BodyPipe` プロパティを設定する場合にも同じ動作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-144">The same behavior applies to setting the `BodyPipe` property.</span></span> <span data-ttu-id="d33f5-145">`HttpResponse.BodyPipe` を新しい `PipeWriter` に設定した場合、`HttpResponse.Body` は自動的に、`HttpResponse.BodyPipe` をラップする新しいストリームに設定されます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-145">If `HttpResponse.BodyPipe` is set to a new `PipeWriter`, the `HttpResponse.Body` is automatically set to a new stream that wraps `HttpResponse.BodyPipe`.</span></span>

## <a name="startasync"></a><span data-ttu-id="d33f5-146">StartAsync</span><span class="sxs-lookup"><span data-stu-id="d33f5-146">StartAsync</span></span>

<span data-ttu-id="d33f5-147">`HttpResponse.StartAsync` は 3.0 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="d33f5-147">`HttpResponse.StartAsync` is new in 3.0.</span></span> <span data-ttu-id="d33f5-148">これは、ヘッダーが変更不可能であり、また `OnStarting` コールバックを実行することを示すために使います。</span><span class="sxs-lookup"><span data-stu-id="d33f5-148">It is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="d33f5-149">3\.0-preview3 では、`HttpRequest.BodyPipe` を使う前に `StartAsync` を呼び出す必要がありました。今後のリリースでは、これは推奨事項になる予定です。</span><span class="sxs-lookup"><span data-stu-id="d33f5-149">In 3.0-preview3, you have to call `StartAsync` before using `HttpRequest.BodyPipe`, and in future releases, it will be a recommendation.</span></span> <span data-ttu-id="d33f5-150">サーバーとして Kestrel を使う場合、`PipeReader` を使う前に StartAsync を呼び出すことで、`GetMemory` によって返されるメモリが、外部バッファーではなく Kestrel の内部 <xref:System.IO.Pipelines.Pipe> に属するよう保証できます。</span><span class="sxs-lookup"><span data-stu-id="d33f5-150">When using Kestrel as a server, calling StartAsync before using the `PipeReader` guarantees that memory returned by `GetMemory` will belong to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d33f5-151">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="d33f5-151">Additional resources</span></span>

- [<span data-ttu-id="d33f5-152">System.IO.Pipelines の概要</span><span class="sxs-lookup"><span data-stu-id="d33f5-152">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>