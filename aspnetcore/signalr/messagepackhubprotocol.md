---
title: ASP.NET Core には SignalR の MessagePack ハブプロトコルを使用します。
author: bradygaster
description: MessagePack ハブプロトコルを ASP.NET Core SignalRに追加します。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 3c2a4285945d3fdc6bba195e3160da8b9dcbba44
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654566"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="abae0-103">ASP.NET Core には SignalR の MessagePack ハブプロトコルを使用します。</span><span class="sxs-lookup"><span data-stu-id="abae0-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="abae0-104">[Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="abae0-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="abae0-105">この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。</span><span class="sxs-lookup"><span data-stu-id="abae0-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="abae0-106">MessagePack とは</span><span class="sxs-lookup"><span data-stu-id="abae0-106">What is MessagePack?</span></span>

<span data-ttu-id="abae0-107">[Messagepack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。</span><span class="sxs-lookup"><span data-stu-id="abae0-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="abae0-108">これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="abae0-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="abae0-109">バイナリ形式であるため、メッセージが MessagePack パーサーによって渡されない限り、ネットワークトレースとログを確認するときにメッセージを読み取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="abae0-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="abae0-110"> には MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する Api が用意されています。</span><span class="sxs-lookup"><span data-stu-id="abae0-110"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="abae0-111">サーバーでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="abae0-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="abae0-112">サーバーで MessagePack ハブプロトコルを有効にするには、アプリに `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="abae0-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="abae0-113">Startup.cs ファイルで、`AddSignalR` 呼び出しに `AddMessagePackProtocol` を追加して、サーバーで MessagePack のサポートを有効にします。</span><span class="sxs-lookup"><span data-stu-id="abae0-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="abae0-114">JSON は既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="abae0-114">JSON is enabled by default.</span></span> <span data-ttu-id="abae0-115">MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。</span><span class="sxs-lookup"><span data-stu-id="abae0-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="abae0-116">MessagePack によるデータの書式設定方法をカスタマイズするために、`AddMessagePackProtocol` ではオプションを構成するためのデリゲートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="abae0-117">このデリゲートでは、`FormatterResolvers` プロパティを使用して、MessagePack のシリアル化オプションを構成できます。</span><span class="sxs-lookup"><span data-stu-id="abae0-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="abae0-118">リゾルバーの動作の詳細については、 [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の messagepack ライブラリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="abae0-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="abae0-119">属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。</span><span class="sxs-lookup"><span data-stu-id="abae0-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

> [!WARNING]
> <span data-ttu-id="abae0-120">[CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf)を確認し、推奨される修正プログラムを適用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="abae0-120">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="abae0-121">たとえば、`MessagePackSecurity.Active` 静的プロパティを `MessagePackSecurity.UntrustedData`に設定します。</span><span class="sxs-lookup"><span data-stu-id="abae0-121">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="abae0-122">`MessagePackSecurity.Active` を設定するには、[バージョンの MessagePack の 1.9. x](https://www.nuget.org/packages/MessagePack/1.9.3)を手動でインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="abae0-122">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="abae0-123">`MessagePack` 1.9. x をインストールすると SignalR が使用するバージョンがアップグレードされます。</span><span class="sxs-lookup"><span data-stu-id="abae0-123">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="abae0-124">`MessagePackSecurity.Active` が `MessagePackSecurity.UntrustedData`に設定されていない場合、悪意のあるクライアントによってサービス拒否が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="abae0-124">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="abae0-125">次のコードに示すように `Program.Main`で `MessagePackSecurity.Active` を設定します。</span><span class="sxs-lookup"><span data-stu-id="abae0-125">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="abae0-126">クライアントでの MessagePack の構成</span><span class="sxs-lookup"><span data-stu-id="abae0-126">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="abae0-127">JSON は、サポートされているクライアントに対して既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="abae0-127">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="abae0-128">クライアントは、1つのプロトコルのみをサポートできます。</span><span class="sxs-lookup"><span data-stu-id="abae0-128">Clients can only support a single protocol.</span></span> <span data-ttu-id="abae0-129">MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="abae0-129">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="abae0-130">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="abae0-130">.NET client</span></span>

<span data-ttu-id="abae0-131">.NET クライアントで MessagePack を有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack` パッケージをインストールし、`HubConnectionBuilder`で `AddMessagePackProtocol` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="abae0-131">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="abae0-132">この `AddMessagePackProtocol` の呼び出しでは、サーバーと同じようにオプションを構成するためのデリゲートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-132">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="abae0-133">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="abae0-133">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="abae0-134">JavaScript クライアントの MessagePack のサポートは、`@microsoft/signalr-protocol-msgpack` npm パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-134">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="abae0-135">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="abae0-135">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="abae0-136">JavaScript クライアントの MessagePack のサポートは、`@aspnet/signalr-protocol-msgpack` npm パッケージによって提供されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-136">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="abae0-137">コマンドシェルで次のコマンドを実行して、パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="abae0-137">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="abae0-138">npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。</span><span class="sxs-lookup"><span data-stu-id="abae0-138">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="abae0-139">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="abae0-139">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="abae0-140">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="abae0-140">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="abae0-141">ブラウザーでは、`msgpack5` ライブラリも参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="abae0-141">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="abae0-142">参照を作成するには、`<script>` タグを使用します。</span><span class="sxs-lookup"><span data-stu-id="abae0-142">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="abae0-143">ライブラリは node_modules にあります。- *msgpack5-dist-msgpack5js*</span><span class="sxs-lookup"><span data-stu-id="abae0-143">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="abae0-144">`<script>` 要素を使用する場合、順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="abae0-144">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="abae0-145">*Msgpack5*の前に*signalr-protocol-msgpack*が参照されている場合、messagepack に接続しようとするとエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="abae0-145">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="abae0-146">*signalr*は、 *signalr-protocol-msgpack*の前にも必要です。</span><span class="sxs-lookup"><span data-stu-id="abae0-146">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="abae0-147">`HubConnectionBuilder` に `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` を追加すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-147">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="abae0-148">現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。</span><span class="sxs-lookup"><span data-stu-id="abae0-148">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="abae0-149">MessagePack の特性</span><span class="sxs-lookup"><span data-stu-id="abae0-149">MessagePack quirks</span></span>

<span data-ttu-id="abae0-150">MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="abae0-150">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="abae0-151">MessagePack では大文字と小文字が区別されます</span><span class="sxs-lookup"><span data-stu-id="abae0-151">MessagePack is case-sensitive</span></span>

<span data-ttu-id="abae0-152">MessagePack プロトコルでは大文字と小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-152">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="abae0-153">たとえば、次C#のクラスについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="abae0-153">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="abae0-154">JavaScript クライアントから送信する場合は、`PascalCased` プロパティ名を使用する必要があります。これC#は、大文字と小文字がクラスに正確に一致する必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="abae0-154">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="abae0-155">例 :</span><span class="sxs-lookup"><span data-stu-id="abae0-155">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="abae0-156">`camelCased` 名を使用しても、 C#クラスに正しくバインドされません。</span><span class="sxs-lookup"><span data-stu-id="abae0-156">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="abae0-157">この問題を回避するには、`Key` 属性を使用して、MessagePack プロパティに別の名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="abae0-157">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="abae0-158">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abae0-158">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="abae0-159">DateTime.Kind はシリアル化/逆シリアル化時に保持されません</span><span class="sxs-lookup"><span data-stu-id="abae0-159">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="abae0-160">MessagePack プロトコルは、`DateTime`の `Kind` 値をエンコードする手段を提供していません。</span><span class="sxs-lookup"><span data-stu-id="abae0-160">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="abae0-161">その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。</span><span class="sxs-lookup"><span data-stu-id="abae0-161">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="abae0-162">`DateTime` 値を現地時刻で使用している場合は、送信する前に UTC に変換することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="abae0-162">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="abae0-163">これらを受信時に UTC からローカル時刻に変換します。</span><span class="sxs-lookup"><span data-stu-id="abae0-163">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="abae0-164">この制限の詳細については、「GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abae0-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="abae0-165">DateTime.MinValue は、JavaScript の MessagePack ではサポートされていません</span><span class="sxs-lookup"><span data-stu-id="abae0-165">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="abae0-166">SignalR JavaScript クライアントによって使用されている[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、messagepack の `timestamp96` の種類をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="abae0-166">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="abae0-167">この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。</span><span class="sxs-lookup"><span data-stu-id="abae0-167">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="abae0-168">`DateTime.MinValue` の値は `January 1, 0001` `timestamp96` 値でエンコードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="abae0-168">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="abae0-169">このため、JavaScript クライアントへの `DateTime.MinValue` の送信はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="abae0-169">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="abae0-170">`DateTime.MinValue` が JavaScript クライアントによって受信されると、次のエラーがスローされます。</span><span class="sxs-lookup"><span data-stu-id="abae0-170">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="abae0-171">通常、`DateTime.MinValue` は、"missing" または `null` 値をエンコードするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-171">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="abae0-172">MessagePack でその値をエンコードする必要がある場合は、null 許容の `DateTime` 値 (`DateTime?`) を使用するか、日付が存在するかどうかを示す別の `bool` 値をエンコードします。</span><span class="sxs-lookup"><span data-stu-id="abae0-172">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="abae0-173">この制限の詳細については、「GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abae0-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="abae0-174">"事前" コンパイル環境での MessagePack のサポート</span><span class="sxs-lookup"><span data-stu-id="abae0-174">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="abae0-175">.NET クライアントとサーバーで使用される[Messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8)ライブラリは、コード生成を使用してシリアル化を最適化します。</span><span class="sxs-lookup"><span data-stu-id="abae0-175">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="abae0-176">その結果、"事前に" コンパイル (Xamarin iOS や Unity など) を使用する環境では、既定ではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="abae0-176">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="abae0-177">これらの環境では、シリアライザー/デシリアライザー コードを "事前に生成する" ことで MessagePack を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="abae0-177">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="abae0-178">詳細については、 [MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abae0-178">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="abae0-179">シリアライザーを事前に生成したら、`AddMessagePackProtocol`に渡される構成デリゲートを使用してそれらを登録できます。</span><span class="sxs-lookup"><span data-stu-id="abae0-179">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="abae0-180">MessagePack での型チェックの方が厳しい</span><span class="sxs-lookup"><span data-stu-id="abae0-180">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="abae0-181">JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。</span><span class="sxs-lookup"><span data-stu-id="abae0-181">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="abae0-182">たとえば、受信したオブジェクトのプロパティ値が数値 (`{ foo: 42 }`) であっても、.NET クラスのプロパティの型が `string`である場合、値は変換されます。</span><span class="sxs-lookup"><span data-stu-id="abae0-182">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="abae0-183">ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="abae0-183">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="abae0-184">この制限の詳細については、「GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="abae0-184">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="abae0-185">関連リソース</span><span class="sxs-lookup"><span data-stu-id="abae0-185">Related resources</span></span>

* [<span data-ttu-id="abae0-186">開始するには</span><span class="sxs-lookup"><span data-stu-id="abae0-186">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="abae0-187">.NET クライアント</span><span class="sxs-lookup"><span data-stu-id="abae0-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="abae0-188">JavaScript クライアント</span><span class="sxs-lookup"><span data-stu-id="abae0-188">JavaScript client</span></span>](xref:signalr/javascript-client)
