---
title: ASP.NET Core SignalR の Java クライアント
author: mikaelm12
description: ASP.NET Core SignalR の Java クライアントを使用する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 09/06/2018
uid: signalr/java-client
ms.openlocfilehash: f110f5391ac34f5cb4a72f64c16d86c8a37369a2
ms.sourcegitcommit: 4cd8dce371d63a66d780e4af1baab2bcf9d61b24
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "43995416"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="339df-103">ASP.NET Core SignalR の Java クライアント</span><span class="sxs-lookup"><span data-stu-id="339df-103">ASP.NET Core SignalR Java Client</span></span>

<span data-ttu-id="339df-104">によって[Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="339df-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="339df-105">Java クライアントでは、Java のコードは、Android アプリを含む、ASP.NET Core SignalR サーバーに接続します。</span><span class="sxs-lookup"><span data-stu-id="339df-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="339df-106">ように、 [JavaScript クライアント](xref:signalr/javascript-client)と[.NET クライアント](xref:signalr/dotnet-client)、Java クライアントでは、リアルタイムでハブにメッセージを送受信することができます。</span><span class="sxs-lookup"><span data-stu-id="339df-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="339df-107">Java クライアントとは、ASP.NET Core 2.2 で使用可能な以降です。</span><span class="sxs-lookup"><span data-stu-id="339df-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="339df-108">この記事で参照されているサンプルの Java コンソール アプリでは、SignalR の Java クライアントを使用します。</span><span class="sxs-lookup"><span data-stu-id="339df-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="339df-109">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample)します ([ダウンロード方法](xref:tutorials/index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="339df-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="339df-110">SignalR の Java クライアント パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="339df-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="339df-111">*Signalr 0.1.0 preview1 35029* JAR ファイルは、SignalR ハブに接続するクライアントを使用できます。</span><span class="sxs-lookup"><span data-stu-id="339df-111">The *signalr-0.1.0-preview1-35029* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="339df-112">JAR ファイルの最新のバージョン番号を検索するには、次を参照してください。、 [Maven 検索結果](https://search.maven.org/search?q=g:com.microsoft.aspnet%20AND%20a:signalr&core=gav)します。</span><span class="sxs-lookup"><span data-stu-id="339df-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.aspnet%20AND%20a:signalr&core=gav).</span></span>

<span data-ttu-id="339df-113">Gradle を使用する場合に、次の行を追加、`dependencies`のセクション、 *build.gradle*ファイル。</span><span class="sxs-lookup"><span data-stu-id="339df-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.aspnet:signalr:0.1.0-preview1-35029'
```

<span data-ttu-id="339df-114">内の次の行を追加して Maven を使用した場合、`<dependencies>`の要素、 *pom.xml*ファイル。</span><span class="sxs-lookup"><span data-stu-id="339df-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="339df-115">ハブへの接続します。</span><span class="sxs-lookup"><span data-stu-id="339df-115">Connect to a hub</span></span>

<span data-ttu-id="339df-116">確立するために、 `HubConnection`、`HubConnectionBuilder`使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="339df-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="339df-117">接続の作成中には、ハブの URL とログ レベルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="339df-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="339df-118">いずれかを呼び出すことによって、必要なオプションを構成、`HubConnectionBuilder`前にメソッド`build`します。</span><span class="sxs-lookup"><span data-stu-id="339df-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="339df-119">接続を開始`start`します。</span><span class="sxs-lookup"><span data-stu-id="339df-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=17-20)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="339df-120">クライアントからのハブ メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="339df-120">Call hub methods from client</span></span>

<span data-ttu-id="339df-121">呼び出し`send`ハブ メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="339df-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="339df-122">ハブ メソッドの名前およびハブ メソッドで定義されている引数を渡す`send`します。</span><span class="sxs-lookup"><span data-stu-id="339df-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=31)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="339df-123">ハブからのクライアント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="339df-123">Call client methods from hub</span></span>

<span data-ttu-id="339df-124">使用`hubConnection.on`ハブで呼び出すことができるクライアントでメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="339df-124">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="339df-125">作成した後は、接続を開始する前に、メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="339df-125">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=22-24)]

## <a name="known-limitations"></a><span data-ttu-id="339df-126">既知の制限事項</span><span class="sxs-lookup"><span data-stu-id="339df-126">Known limitations</span></span>

<span data-ttu-id="339df-127">これは、Java クライアントの初期のプレビュー リリースです。</span><span class="sxs-lookup"><span data-stu-id="339df-127">This is an early preview release of the Java client.</span></span> <span data-ttu-id="339df-128">まだサポートされていない多くの機能があります。</span><span class="sxs-lookup"><span data-stu-id="339df-128">There are many features that aren't supported yet.</span></span> <span data-ttu-id="339df-129">次のギャップは、今後のリリースで作業中は。</span><span class="sxs-lookup"><span data-stu-id="339df-129">The following gaps are being worked on for future releases:</span></span>

* <span data-ttu-id="339df-130">プリミティブ型だけでは、パラメーターとして受け入れることができ、型を返します。</span><span class="sxs-lookup"><span data-stu-id="339df-130">Only primitive types can be accepted as parameters and return types.</span></span>
* <span data-ttu-id="339df-131">Api は同期的です。</span><span class="sxs-lookup"><span data-stu-id="339df-131">The APIs are synchronous.</span></span>
* <span data-ttu-id="339df-132">「送信」呼び出しの種類のみが現時点でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="339df-132">Only the "Send" call type is supported at this time.</span></span> <span data-ttu-id="339df-133">「呼び出し」と戻り値のストリーミングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="339df-133">"Invoke" and streaming of return values aren't supported.</span></span>
* <span data-ttu-id="339df-134">クライアントがサポートされていない、 [Azure SignalR サービス](/azure/azure-signalr/)します。</span><span class="sxs-lookup"><span data-stu-id="339df-134">The client doesn't currently support the [Azure SignalR Service](/azure/azure-signalr/).</span></span>
* <span data-ttu-id="339df-135">JSON プロトコルのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="339df-135">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="339df-136">Websocket トランスポートのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="339df-136">Only the WebSockets transport is supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="339df-137">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="339df-137">Additional resources</span></span>

* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>