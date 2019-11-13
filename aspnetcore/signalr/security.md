---
title: ASP.NET Core SignalR でのセキュリティに関する考慮事項
author: bradygaster
description: ASP.NET Core SignalRで認証と承認を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: c5a34ae67bdfb8f7fd92c00f18973b66b685a99c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963902"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="70c9a-103">ASP.NET Core SignalR でのセキュリティに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="70c9a-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="70c9a-104">By [Andrew Stanton-看護師](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="70c9a-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="70c9a-105">この記事では、SignalRのセキュリティ保護について説明します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="70c9a-106">クロスオリジンリソース共有</span><span class="sxs-lookup"><span data-stu-id="70c9a-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="70c9a-107">[クロスオリジンリソース共有 (CORS)](https://www.w3.org/TR/cors/)を使用して、ブラウザーでのクロスオリジン SignalR 接続を許可することができます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="70c9a-108">JavaScript コードが SignalR アプリとは別のドメインでホストされている場合、JavaScript が SignalR アプリに接続できるようにするには、 [CORS ミドルウェア](xref:security/cors)を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="70c9a-109">信頼または制御するドメインからのクロスオリジン要求を許可します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="70c9a-110">(例:</span><span class="sxs-lookup"><span data-stu-id="70c9a-110">For example:</span></span>

* <span data-ttu-id="70c9a-111">サイトは `http://www.example.com` でホストされています</span><span class="sxs-lookup"><span data-stu-id="70c9a-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="70c9a-112">SignalR アプリは `http://signalr.example.com` でホストされています</span><span class="sxs-lookup"><span data-stu-id="70c9a-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="70c9a-113">SignalR アプリで CORS を構成して、オリジン `www.example.com`のみを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="70c9a-114">CORS の構成の詳細については、「[クロスオリジン要求 (cors) を有効にする](xref:security/cors)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="70c9a-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="70c9a-115"> には、次の CORS ポリシー**が必要**です。</span><span class="sxs-lookup"><span data-stu-id="70c9a-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="70c9a-116">想定される特定のオリジンを許可します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-116">Allow the specific expected origins.</span></span> <span data-ttu-id="70c9a-117">配信元を許可することは可能ですが、安全でも推奨され**ません**。</span><span class="sxs-lookup"><span data-stu-id="70c9a-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="70c9a-118">HTTP メソッド `GET` と `POST` は許可されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="70c9a-119">認証が使用されていない場合でも、資格情報を有効にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="70c9a-120">たとえば、次の CORS ポリシーを使用すると、`https://example.com` でホストされている SignalR ブラウザークライアントは、`https://signalr.example.com`でホストされている SignalR アプリにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR<span data-ttu-id="70c9a-121"> は、Azure App Service の組み込みの CORS 機能と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="70c9a-121"> is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="70c9a-122">WebSocket 配信元の制限</span><span class="sxs-lookup"><span data-stu-id="70c9a-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="70c9a-123">CORS で提供される保護は、WebSocket には適用されません。</span><span class="sxs-lookup"><span data-stu-id="70c9a-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="70c9a-124">Websocket の配信元の制限については、「 [websocket の配信元の制限](xref:fundamentals/websockets#websocket-origin-restriction)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="70c9a-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="70c9a-125">CORS で提供される保護は、WebSocket には適用されません。</span><span class="sxs-lookup"><span data-stu-id="70c9a-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="70c9a-126">ブラウザーでは以下を実行**しません**。</span><span class="sxs-lookup"><span data-stu-id="70c9a-126">Browsers do **not**:</span></span>

* <span data-ttu-id="70c9a-127">CORS の事前要求を実行する。</span><span class="sxs-lookup"><span data-stu-id="70c9a-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="70c9a-128">WebSocket 要求を行うときに `Access-Control` ヘッダーに指定された制限を考慮する。</span><span class="sxs-lookup"><span data-stu-id="70c9a-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="70c9a-129">ただし、WebSocket 要求を発行するときにはブラウザーから `Origin` ヘッダーが送信されます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="70c9a-130">予期した配信元からの WebSocket のみが許可されるように、アプリケーションでこれらのヘッダーが検証されるように構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="70c9a-131">ASP.NET Core 2.1 以降では、 **`UseSignalR`前に**配置されたカスタムミドルウェアと `Configure`の認証ミドルウェアを使用して、ヘッダーの検証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="70c9a-132">`Origin` ヘッダーは、クライアントによって制御され、`Referer` のように偽装することができます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="70c9a-133">これらのヘッダーは、認証メカニズムとして使用**しない**でください。</span><span class="sxs-lookup"><span data-stu-id="70c9a-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="70c9a-134">アクセストークンのログ記録</span><span class="sxs-lookup"><span data-stu-id="70c9a-134">Access token logging</span></span>

<span data-ttu-id="70c9a-135">Websocket またはサーバー送信イベントを使用する場合、ブラウザークライアントはクエリ文字列にアクセストークンを送信します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="70c9a-136">一般に、クエリ文字列を使用してアクセストークンを受け取ることは、標準の `Authorization` ヘッダーを使用する場合と同様に安全です。</span><span class="sxs-lookup"><span data-stu-id="70c9a-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="70c9a-137">クライアントとサーバー間のセキュリティで保護されたエンドツーエンド接続を確保するには、常に HTTPS を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="70c9a-138">多くの web サーバーでは、クエリ文字列を含め、各要求の URL がログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="70c9a-139">Url をログに記録すると、アクセストークンがログに記録される場合があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="70c9a-140">では、各要求の URL が既定でログに記録されます。これには、クエリ文字列が含まれます。 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70c9a-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="70c9a-141">(例:</span><span class="sxs-lookup"><span data-stu-id="70c9a-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="70c9a-142">このデータをサーバーログに記録することに懸念がある場合は、`Microsoft.AspNetCore.Hosting` logger を `Warning` レベル以上に構成することで、このログ記録を完全に無効にすることができます (これらのメッセージは `Info` レベルで書き込まれます)。</span><span class="sxs-lookup"><span data-stu-id="70c9a-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="70c9a-143">詳細については、[ログのフィルター処理](xref:fundamentals/logging/index#log-filtering)に関するドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="70c9a-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="70c9a-144">それでも特定の要求情報をログに記録する場合は、必要なデータをログに記録する[ミドルウェアを記述](xref:fundamentals/middleware/write)し、`access_token` クエリ文字列値 (存在する場合) を除外することができます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="70c9a-145">例外</span><span class="sxs-lookup"><span data-stu-id="70c9a-145">Exceptions</span></span>

<span data-ttu-id="70c9a-146">例外メッセージは、通常、クライアントに公開されない機密データと見なされます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="70c9a-147">既定では、SignalR は、ハブメソッドによってスローされた例外の詳細をクライアントに送信しません。</span><span class="sxs-lookup"><span data-stu-id="70c9a-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="70c9a-148">代わりに、エラーが発生したことを示す一般的なメッセージをクライアントが受信します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="70c9a-149">クライアントへの例外メッセージの配信は、 [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options)を使用して (開発やテストなどで) オーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-149">Exception message delivery to the client can be overridden (for example in development or test) with [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="70c9a-150">例外メッセージは、実稼働アプリでクライアントに公開しないでください。</span><span class="sxs-lookup"><span data-stu-id="70c9a-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="70c9a-151">バッファー管理</span><span class="sxs-lookup"><span data-stu-id="70c9a-151">Buffer management</span></span>

SignalR<span data-ttu-id="70c9a-152"> は、接続ごとのバッファーを使用して、受信メッセージと送信メッセージを管理します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-152"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="70c9a-153">既定では、SignalR によってこれらのバッファーは 32 KB に制限されます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="70c9a-154">クライアントまたはサーバーが送信できる最大メッセージサイズは 32 KB です。</span><span class="sxs-lookup"><span data-stu-id="70c9a-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="70c9a-155">メッセージの接続で消費される最大メモリは 32 KB です。</span><span class="sxs-lookup"><span data-stu-id="70c9a-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="70c9a-156">メッセージが常に 32 KB より小さい場合は、次の制限を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="70c9a-157">クライアントが大きなメッセージを送信できないようにします。</span><span class="sxs-lookup"><span data-stu-id="70c9a-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="70c9a-158">サーバーは、メッセージを受け入れるために大きなバッファーを割り当てる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="70c9a-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="70c9a-159">メッセージが 32 KB を超える場合は、制限を増やすことができます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="70c9a-160">この制限を引き上げると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-160">Increasing this limit means:</span></span>

* <span data-ttu-id="70c9a-161">クライアントは、サーバーが大きなメモリバッファーを割り当てる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="70c9a-162">大きなバッファーをサーバーに割り当てると、同時接続の数が減少する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="70c9a-163">受信メッセージと送信メッセージには制限があり、`MapHub`で構成された[`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options)オブジェクトで両方を構成できます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-163">There are limits for incoming and outgoing messages, both can be configured on the [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="70c9a-164">`ApplicationMaxBufferSize` は、サーバーがバッファーするクライアントからの最大バイト数を表します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="70c9a-165">クライアントがこの制限を超えるメッセージを送信しようとすると、接続が閉じられる場合があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="70c9a-166">`TransportMaxBufferSize` は、サーバーが送信できる最大バイト数を表します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="70c9a-167">サーバーがこの制限を超えるメッセージ (ハブメソッドからの戻り値を含む) を送信しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="70c9a-168">制限を `0` に設定すると、制限が無効になります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="70c9a-169">制限を削除すると、クライアントは任意のサイズのメッセージを送信できます。</span><span class="sxs-lookup"><span data-stu-id="70c9a-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="70c9a-170">サイズの大きいメッセージを送信する悪意のあるクライアントは、余分なメモリが割り当てられる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="70c9a-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="70c9a-171">メモリ使用量が多すぎると、同時接続の数が大幅に減少します。</span><span class="sxs-lookup"><span data-stu-id="70c9a-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
