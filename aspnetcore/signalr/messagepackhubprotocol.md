---
title: ASP.NET Core SignalR で MessagePack ハブ プロトコルを使用する
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
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>ASP.NET Core SignalR で MessagePack ハブプロトコルを使用する

作成者: [Brennan Conroy](https://github.com/BrennanConroy)

この記事では、「[作業の開始](xref:tutorials/signalr)」で説明されているトピックについての知識がある読者を想定しています。

## <a name="what-is-messagepack"></a>MessagePack とは

[MessagePack](https://msgpack.org/index.html)は、高速でコンパクトなバイナリシリアル化形式です。 これは、 [JSON](https://www.json.org/)と比較して小さいメッセージを作成するため、パフォーマンスと帯域幅が問題になる場合に便利です。 バイナリ形式であるため、メッセージが MessagePack パーサーによって渡されない限り、ネットワークトレースとログを確認するときにメッセージを読み取ることはできません。 SignalR には MessagePack 形式のサポートが組み込まれており、クライアントとサーバーが使用する API が用意されています。

## <a name="configure-messagepack-on-the-server"></a>サーバーでの MessagePack の構成

サーバーで MessagePack ハブプロトコルを有効にするには、アプリに `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` パッケージをインストールします。 Startup.cs ファイルで `AddMessagePackProtocol` を `AddSignalR` の呼び出しに追加して、サーバーで MessagePack のサポートを有効にします。

> [!NOTE]
> JSON は既定で有効になっています。 MessagePack を追加すると、JSON と MessagePack クライアントの両方のサポートが有効になります。

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

MessagePack によるデータの書式設定方法をカスタマイズするために、`AddMessagePackProtocol` はオプションを構成するためのデリゲートを受け取ります。 このデリゲートでは、`FormatterResolvers` プロパティを使用して MessagePack のシリアル化オプションを構成できます。 リゾルバーの動作の詳細については、 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)の MessagePack ライブラリを参照してください。 属性は、シリアル化するオブジェクトに対して使用して、どのように処理するかを定義できます。

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

## <a name="configure-messagepack-on-the-client"></a>クライアントでの MessagePack の構成

> [!NOTE]
> JSON は、サポートされているクライアントに対して既定で有効になっています。 クライアントは、1つのプロトコルのみをサポートできます。 MessagePack サポートを追加すると、以前に構成したプロトコルがすべて置き換えられます。

### <a name="net-client"></a>.NET クライアント

.NET クライアントで MessagePack を有効にするには、`Microsoft.AspNetCore.SignalR.Protocols.MessagePack` のパッケージをインストールし、`HubConnectionBuilder` で `AddMessagePackProtocol` を呼び出します。

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> この `AddMessagePackProtocol` 呼び出しは、サーバーと同様にオプションを構成するためのデリゲートを受け取ります。

### <a name="javascript-client"></a>JavaScript クライアント

::: moniker range=">= aspnetcore-3.0"

JavaScript クライアントの MessagePack のサポートは、`@microsoft/signalr-protocol-msgpack` npm パッケージによって提供されます。 コマンドシェルで次のコマンドを実行して、パッケージをインストールします。

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

JavaScript クライアントの MessagePack のサポートは、`@aspnet/signalr-protocol-msgpack` npm パッケージによって提供されます。 コマンドシェルで次のコマンドを実行して、パッケージをインストールします。

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

npm パッケージをインストールした後、モジュールは JavaScript モジュールローダーで直接使用することも、次のファイルを参照してブラウザーにインポートすることもできます。

::: moniker range=">= aspnetcore-3.0"


*node_modules\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 


::: moniker-end

::: moniker range="< aspnetcore-3.0"


*node_modules\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

::: moniker-end

ブラウザーでは、`msgpack5` ライブラリも参照する必要があります。 参照を作成するには、`<script>` タグを使用します。 ライブラリは*node_modules\msgpack5\dist\msgpack5.js*にあります。


> [!NOTE]
> `<script>` 要素を使用する場合、順序は重要です。 *msgpack5.js*の前に*signalr-protocol-msgpack.js*が参照されている場合、MessagePack に接続しようとするとエラーが発生します。 *signalr.js*は、 *signalr-protocol-msgpack.js*の前にも必要です。

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

@No__t-1 に `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` を追加すると、サーバーに接続するときに MessagePack プロトコルを使用するようにクライアントが構成されます。

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> 現時点では、JavaScript クライアントの MessagePack プロトコルの構成オプションはありません。

## <a name="messagepack-quirks"></a>MessagePack の特性

MessagePack ハブプロトコルを使用する場合に注意する必要がある問題がいくつかあります。

### <a name="messagepack-is-case-sensitive"></a>MessagePack では大文字と小文字が区別されます

MessagePack プロトコルでは大文字と小文字が区別されます。 たとえば、次C#のクラスについて考えてみます。

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```


JavaScript クライアントから送信する場合は、大文字と小文字が正確に C# のクラスと一致する必要があるため、`PascalCased` のプロパティ名を使用する必要があります。以下に例を示します。


```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

`camelCased` の名前を使用しても、C# のクラスに正しくバインドされません。この問題を回避するには、`Key` 属性を使用して、MessagePack プロパティに別の名前を指定します。詳細については、[MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#object-serialization)を参照してください。

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind はシリアル化/逆シリアル化時に保持されません

MessagePack プロトコルは、`DateTime` の `Kind` 値をエンコードする手段を提供していません。 その結果、MessagePack ハブプロトコルでは、日付を逆シリアル化するときに、受信日が UTC 形式であると見なされます。 現地時刻で `DateTime` の値を使用している場合は、送信する前に UTC に変換することをお勧めします。 これらを受信時に UTC からローカル時刻に変換します。

この制限の詳細については、「GitHub issue [aspnet/SignalR # 2632](https://github.com/aspnet/SignalR/issues/2632)」を参照してください。

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue は、JavaScript の MessagePack ではサポートされていません


SignalR JavaScript クライアントによって使用される[msgpack5](https://github.com/mcollina/msgpack5)ライブラリは、MessagePack の `timestamp96` 型をサポートしていません。 この型は、非常に大きな日付値をエンコードするために使用されます (過去またはそれ以降の非常に早い段階)。 `DateTime.MinValue` の値は `January 1, 0001` で、`timestamp96` の値でエンコードする必要があります。 このため、JavaScript クライアントへの `DateTime.MinValue` の送信はサポートされていません。 `DateTime.MinValue` が JavaScript クライアントによって受信されると、次のエラーがスローされます。


```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

通常、`DateTime.MinValue` は、"missing" 値または `null` 値をエンコードするために使用されます。 MessagePack でその値をエンコードする必要がある場合は、null 許容の `DateTime` の値 (`DateTime?`) を使用するか、日付が存在するかどうかを示す別個の `bool` の値をエンコードします。

この制限の詳細については、「GitHub issue [aspnet/SignalR # 2228](https://github.com/aspnet/SignalR/issues/2228)」を参照してください。

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>"事前" コンパイル環境での MessagePack のサポート

.NET クライアントとサーバーで使用される [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) ライブラリは、コード生成を使用してシリアル化を最適化します。その結果、"事前" コンパイル (Xamarin iOS や Unity など) を使用する環境で、既定ではサポートされません。これらの環境では、シリアライザー/デシリアライザー コードを "事前に生成する" ことで MessagePack を使用することができます。詳細については、[MessagePack-CSharp のドキュメント](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports)を参照してください。シリアライザーを事前に生成したら、`AddMessagePackProtocol` に渡された構成デリゲートを使用してそれらを登録できます。


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

### <a name="type-checks-are-more-strict-in-messagepack"></a>MessagePack での型チェックの方が厳しい

JSON ハブプロトコルは、逆シリアル化中に型変換を実行します。 たとえば、受信したオブジェクトのプロパティ値が数値 (`{ foo: 42 }`) であっても、.NET クラスのプロパティの型が `string` の場合、その値は変換されます。 ただし、MessagePack では、この変換は実行されず、サーバー側のログ (およびコンソール) で確認できる例外がスローされます。

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

この制限の詳細については、「GitHub issue [aspnet/SignalR # 2937](https://github.com/aspnet/SignalR/issues/2937)」を参照してください。

## <a name="related-resources"></a>関連資料

* [開始するには](xref:tutorials/signalr)
* [.NET クライアント](xref:signalr/dotnet-client)
* [JavaScript クライアント](xref:signalr/javascript-client)
