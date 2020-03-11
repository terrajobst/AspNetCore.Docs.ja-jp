---
title: ASP.NET Core SignalR Java クライアント
author: mikaelm12
description: Java クライアント SignalR ASP.NET Core を使用する方法について説明します。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 6919eabf454f16887e012161a454a4848c45002b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652214"
---
# <a name="aspnet-core-opno-locsignalr-java-client"></a><span data-ttu-id="7203e-103">ASP.NET Core SignalR Java クライアント</span><span class="sxs-lookup"><span data-stu-id="7203e-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="7203e-104">[Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="7203e-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="7203e-105">Java クライアントを使用すると、Android アプリを含む Java コードから ASP.NET Core SignalR サーバーに接続できます。</span><span class="sxs-lookup"><span data-stu-id="7203e-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="7203e-106">[JavaScript クライアント](xref:signalr/javascript-client)や[.net クライアント](xref:signalr/dotnet-client)と同様に、Java クライアントを使用すると、メッセージを受信してハブにリアルタイムで送信することができます。</span><span class="sxs-lookup"><span data-stu-id="7203e-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="7203e-107">Java クライアントは ASP.NET Core 2.2 以降で使用できます。</span><span class="sxs-lookup"><span data-stu-id="7203e-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="7203e-108">この記事で参照されているサンプルの Java コンソールアプリは、SignalR Java クライアントを使用します。</span><span class="sxs-lookup"><span data-stu-id="7203e-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="7203e-109">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="7203e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-java-client-package"></a><span data-ttu-id="7203e-110">SignalR Java クライアントパッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="7203e-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="7203e-111">*Signalr*の JAR ファイルを使用すると、クライアントは SignalR hub に接続できます。</span><span class="sxs-lookup"><span data-stu-id="7203e-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="7203e-112">最新の JAR ファイルのバージョン番号を確認するには、 [Maven の検索結果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7203e-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="7203e-113">Gradle を使用する場合は、 *Gradle*ファイルの `dependencies` セクションに次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="7203e-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="7203e-114">Maven を使用する場合は、次の行を*pom*ファイルの `<dependencies>` 要素の内部に追加します。</span><span class="sxs-lookup"><span data-stu-id="7203e-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="7203e-115">ハブへの接続します。</span><span class="sxs-lookup"><span data-stu-id="7203e-115">Connect to a hub</span></span>

<span data-ttu-id="7203e-116">`HubConnection`を確立するには、`HubConnectionBuilder` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7203e-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="7203e-117">ハブの URL とログレベルは、接続の作成時に構成できます。</span><span class="sxs-lookup"><span data-stu-id="7203e-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="7203e-118">`build`する前に、`HubConnectionBuilder` のメソッドのいずれかを呼び出して必要なオプションを構成します。</span><span class="sxs-lookup"><span data-stu-id="7203e-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="7203e-119">`start`との接続を開始します。</span><span class="sxs-lookup"><span data-stu-id="7203e-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7203e-120">クライアントからのハブ メソッドの呼び出し</span><span class="sxs-lookup"><span data-stu-id="7203e-120">Call hub methods from client</span></span>

<span data-ttu-id="7203e-121">`send` を呼び出すと、ハブメソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7203e-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="7203e-122">ハブメソッドの名前と、ハブメソッドで定義されているすべての引数を `send`に渡します。</span><span class="sxs-lookup"><span data-stu-id="7203e-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="7203e-123">*サーバーレスモード*で Azure SignalR サービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。</span><span class="sxs-lookup"><span data-stu-id="7203e-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="7203e-124">詳細については、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7203e-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7203e-125">ハブからのクライアント メソッドを呼び出す</span><span class="sxs-lookup"><span data-stu-id="7203e-125">Call client methods from hub</span></span>

<span data-ttu-id="7203e-126">`hubConnection.on` を使用して、ハブが呼び出すことができるクライアント上のメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="7203e-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="7203e-127">ビルドの後、接続を開始する前に、メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="7203e-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="7203e-128">ログの追加</span><span class="sxs-lookup"><span data-stu-id="7203e-128">Add logging</span></span>

<span data-ttu-id="7203e-129">SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。</span><span class="sxs-lookup"><span data-stu-id="7203e-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="7203e-130">これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。</span><span class="sxs-lookup"><span data-stu-id="7203e-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="7203e-131">次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7203e-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="7203e-132">依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。</span><span class="sxs-lookup"><span data-stu-id="7203e-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="7203e-133">これは無視してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="7203e-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="7203e-134">Android 開発ノート</span><span class="sxs-lookup"><span data-stu-id="7203e-134">Android development notes</span></span>

<span data-ttu-id="7203e-135">SignalR クライアント機能の Android SDK 互換性に関しては、ターゲット Android SDK バージョンを指定するときに、次の項目を考慮してください。</span><span class="sxs-lookup"><span data-stu-id="7203e-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="7203e-136">SignalR Java クライアントは、Android API レベル16以降で実行されます。</span><span class="sxs-lookup"><span data-stu-id="7203e-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="7203e-137">Azure [SignalR サービス](/azure/azure-signalr/signalr-overview)では TLS 1.2 が必要であり、SHA-1 ベースの暗号スイートはサポートされていないため、Azure SignalR サービスを介して接続するには、Android API レベル20以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="7203e-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="7203e-138">Android では、API レベル20で[SHA-256 (およびそれ以降) の暗号スイートのサポートが追加されました](https://developer.android.com/reference/javax/net/ssl/SSLSocket)。</span><span class="sxs-lookup"><span data-stu-id="7203e-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="7203e-139">ベアラートークン認証を構成する</span><span class="sxs-lookup"><span data-stu-id="7203e-139">Configure bearer token authentication</span></span>

<span data-ttu-id="7203e-140">SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)に "アクセストークンファクトリ" を提供することによって、認証に使用するベアラートークンを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7203e-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="7203e-141">[WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。</span><span class="sxs-lookup"><span data-stu-id="7203e-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="7203e-142">[単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。</span><span class="sxs-lookup"><span data-stu-id="7203e-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="7203e-143">既知の制限事項</span><span class="sxs-lookup"><span data-stu-id="7203e-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="7203e-144">JSON プロトコルのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7203e-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="7203e-145">トランスポートフォールバックおよびサーバー送信イベントトランスポートはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="7203e-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="7203e-146">JSON プロトコルのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7203e-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="7203e-147">Websocket トランスポートのみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="7203e-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="7203e-148">ストリーミングはまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="7203e-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7203e-149">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="7203e-149">Additional resources</span></span>

* [<span data-ttu-id="7203e-150">Java API リファレンス</span><span class="sxs-lookup"><span data-stu-id="7203e-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* <span data-ttu-id="7203e-151">[Azure SignalR サービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="7203e-151">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
