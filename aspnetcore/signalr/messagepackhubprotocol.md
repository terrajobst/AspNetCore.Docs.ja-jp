---
title: ASP.NET Core SignalR で MessagePack ハブプロトコルを使用する
author: bradygaster
description: MessagePack ハブプロトコルを ASP.NET Core SignalR に追加します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/08/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: fe09b646eba5ae15cbd9e568b276aaf7763e4b1b
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165405"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="1d22a-103">ASP.NET Core SignalR で MessagePack ハブプロトコルを使用する</span><span class="sxs-lookup"><span data-stu-id="1d22a-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="1d22a-104">作成者: [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="1d22a-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="1d22a-105">この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="1d22a-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="1d22a-106">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="1d22a-106">What is MessagePack?</span></span>

<span data-ttu-id="1d22a-107">[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="1d22a-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="1d22a-108">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="1d22a-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="1d22a-109">バイナリ形式であるため、メッセージが MessagePack パーサーによって渡されない限り、ネットワークトレースとログを確認するときにメッセージを読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="1d22a-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="1d22a-110">SignalR には MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する API が用意されています。</span><span class="sxs-lookup"><span data-stu-id="1d22a-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="1d22a-111">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="1d22a-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="1d22a-112">サーバーで MessagePack ハブプロトコルを有効にするには、アプリに @no__t 0 パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="1d22a-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="1d22a-113">Startup.cs ファイルで `AddMessagePackProtocol` を @no__t の呼び出しに追加して、サーバーで MessagePack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="1d22a-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1d22a-114">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="1d22a-114">JSON is enabled by default.</span></span> <span data-ttu-id="1d22a-115">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="1d22a-116">MessagePack によるデータの書式設定方法をカスタマイズするために、`AddMessagePackProtocol` はオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="1d22a-117">このデリゲートでは、`FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="1d22a-118">リゾルバーの動作の詳細については、 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の MessagePack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d22a-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="1d22a-119">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="1d22a-120">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="1d22a-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="1d22a-121">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="1d22a-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="1d22a-122">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="1d22a-123">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="1d22a-124">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="1d22a-124">.NET client</span></span>

<span data-ttu-id="1d22a-125">.NET クライアントで MessagePack を有効にするには、@no__t 0 のパッケージをインストールし、`HubConnectionBuilder` で `AddMessagePackProtocol` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="1d22a-126">この `AddMessagePackProtocol` 呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="1d22a-127">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="1d22a-127">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d22a-128">JavaScript クライアントの MessagePack のサポートは、`@microsoft/signalr-protocol-msgpack` npm パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-128">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="1d22a-129">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="1d22a-129">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d22a-130">JavaScript クライアントの MessagePack のサポートは、`@aspnet/signalr-protocol-msgpack` npm パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-130">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="1d22a-131">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="1d22a-131">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="1d22a-132">npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-132">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1d22a-133">*node_modules @ no__t-1 @ no__t*</span><span class="sxs-lookup"><span data-stu-id="1d22a-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1d22a-134">*node_modules @ no__t-1 @ no__t*</span><span class="sxs-lookup"><span data-stu-id="1d22a-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="1d22a-135">ブラウザーでは、`msgpack5` ライブラリも参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-135">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="1d22a-136">参照を作成するには、@no__t 0 のタグを使用します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-136">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="1d22a-137">ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-137">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="1d22a-138">@No__t-0 要素を使用する場合、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="1d22a-138">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="1d22a-139">*Msgpack5*の前に*signalr-protocol-msgpack*が参照されている場合、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-139">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="1d22a-140">*signalr*は、 *signalr-protocol-msgpack*の前にも必要です。</span><span class="sxs-lookup"><span data-stu-id="1d22a-140">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="1d22a-141">@No__t-1 に `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` を追加すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-141">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="1d22a-142">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="1d22a-142">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="1d22a-143">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="1d22a-143">MessagePack quirks</span></span>

<span data-ttu-id="1d22a-144">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-144">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="1d22a-145">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="1d22a-145">MessagePack is case-sensitive</span></span>

<span data-ttu-id="1d22a-146">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-146">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="1d22a-147">たとえば、次C#のクラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-147">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="1d22a-148">JavaScript クライアントから送信する場合は、大文字と小文字が正確に C# のクラスと一致する必要があるため、`PascalCased` のプロパティ名を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-148">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="1d22a-149">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-149">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="1d22a-150">@No__t-0 の名前を使用してもC# 、クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="1d22a-150">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="1d22a-151">この問題を回避するには、`Key` 属性を使用して、MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-151">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="1d22a-152">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d22a-152">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="1d22a-153">シリアル化/逆シリアル化時には DateTime. Kind は保持されません</span><span class="sxs-lookup"><span data-stu-id="1d22a-153">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="1d22a-154">MessagePack プロトコルは、`DateTime` の @no__t 0 値をエンコードする手段を提供していません。</span><span class="sxs-lookup"><span data-stu-id="1d22a-154">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="1d22a-155">その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-155">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="1d22a-156">現地時刻で @no__t 0 の値を使用している場合は、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1d22a-156">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="1d22a-157">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-157">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="1d22a-158">この制限の詳細については、「GitHub issue [aspnet/SignalR # 2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d22a-158">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="1d22a-159">DateTime.MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="1d22a-159">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="1d22a-160">SignalR JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、MessagePack の `timestamp96` 型をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="1d22a-160">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="1d22a-161">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="1d22a-161">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="1d22a-162">@No__t-0 の値は `January 1, 0001` で、`timestamp96` の値でエンコードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d22a-162">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="1d22a-163">このため、JavaScript クライアントへの `DateTime.MinValue` の送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1d22a-163">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="1d22a-164">@No__t-0 が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-164">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="1d22a-165">通常、`DateTime.MinValue` は、"missing" 値または `null` 値をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-165">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="1d22a-166">MessagePack でその値をエンコードする必要がある場合は、null 許容の @no__t 0 の値 (`DateTime?`) を使用するか、日付が存在するかどうかを示す別個の `bool` の値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="1d22a-166">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="1d22a-167">この制限の詳細については、「GitHub issue [aspnet/SignalR # 2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d22a-167">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="1d22a-168">"事前に" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="1d22a-168">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="1d22a-169">.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-169">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="1d22a-170">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="1d22a-170">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="1d22a-171">これらの環境では、シリアライザー/デシリアライザーコードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-171">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="1d22a-172">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d22a-172">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="1d22a-173">シリアライザーを事前に生成したら、`AddMessagePackProtocol` に渡された構成デリゲートを使用してそれらを登録できます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-173">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="1d22a-174">MessagePack での型チェックの方が厳しい</span><span class="sxs-lookup"><span data-stu-id="1d22a-174">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="1d22a-175">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="1d22a-175">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="1d22a-176">たとえば、受信したオブジェクトのプロパティ値が数値 (`{ foo: 42 }`) であっても、.NET クラスのプロパティの型が `string` の場合、その値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-176">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="1d22a-177">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="1d22a-177">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="1d22a-178">この制限の詳細については、「GitHub issue [aspnet/SignalR # 2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d22a-178">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="1d22a-179">関連資料</span><span class="sxs-lookup"><span data-stu-id="1d22a-179">Related resources</span></span>

* [<span data-ttu-id="1d22a-180">開始するには</span><span class="sxs-lookup"><span data-stu-id="1d22a-180">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="1d22a-181">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="1d22a-181">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="1d22a-182">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="1d22a-182">JavaScript client</span></span>](xref:signalr/javascript-client)
