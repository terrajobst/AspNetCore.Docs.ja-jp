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
# <a name="aspnet-core-opno-locsignalr-java-client"></a>ASP.NET Core SignalR Java クライアント

[Mikael Mengistu](https://twitter.com/MikaelM_12)

Java クライアントを使用すると、Android アプリを含む Java コードから ASP.NET Core SignalR サーバーに接続できます。 [JavaScript クライアント](xref:signalr/javascript-client)や[.net クライアント](xref:signalr/dotnet-client)と同様に、Java クライアントを使用すると、メッセージを受信してハブにリアルタイムで送信することができます。 Java クライアントは ASP.NET Core 2.2 以降で使用できます。

この記事で参照されているサンプルの Java コンソールアプリは、SignalR Java クライアントを使用します。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="install-the-opno-locsignalr-java-client-package"></a>SignalR Java クライアントパッケージをインストールする

*Signalr*の JAR ファイルを使用すると、クライアントは SignalR hub に接続できます。 最新の JAR ファイルのバージョン番号を確認するには、 [Maven の検索結果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)を参照してください。

Gradle を使用する場合は、 *Gradle*ファイルの `dependencies` セクションに次の行を追加します。

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Maven を使用する場合は、次の行を*pom*ファイルの `<dependencies>` 要素の内部に追加します。

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>ハブへの接続します。

`HubConnection`を確立するには、`HubConnectionBuilder` を使用する必要があります。 ハブの URL とログレベルは、接続の作成時に構成できます。 `build`する前に、`HubConnectionBuilder` のメソッドのいずれかを呼び出して必要なオプションを構成します。 `start`との接続を開始します。

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>クライアントからのハブ メソッドの呼び出し

`send` を呼び出すと、ハブメソッドが呼び出されます。 ハブメソッドの名前と、ハブメソッドで定義されているすべての引数を `send`に渡します。

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> *サーバーレスモード*で Azure SignalR サービスを使用している場合は、クライアントからハブメソッドを呼び出すことはできません。 詳細については、 [SignalR サービスのドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)を参照してください。

## <a name="call-client-methods-from-hub"></a>ハブからのクライアント メソッドを呼び出す

`hubConnection.on` を使用して、ハブが呼び出すことができるクライアント上のメソッドを定義します。 ビルドの後、接続を開始する前に、メソッドを定義します。

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>ログの追加

SignalR Java クライアントは、 [SLF4J](https://www.slf4j.org/)ライブラリを使用してログを記録します。 これは、ライブラリのユーザーが特定のログの依存関係を使用して独自のログの実装を選択できるようにする、高レベルのログ記録 API です。 次のコードスニペットは、SignalR Java クライアントで `java.util.logging` を使用する方法を示しています。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

依存関係にログ記録を構成しない場合、SLF4J は既定の非操作 logger を読み込み、次の警告メッセージを表示します。

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

これは無視してもかまいません。

## <a name="android-development-notes"></a>Android 開発ノート

SignalR クライアント機能の Android SDK 互換性に関しては、ターゲット Android SDK バージョンを指定するときに、次の項目を考慮してください。

* SignalR Java クライアントは、Android API レベル16以降で実行されます。
* Azure [SignalR サービス](/azure/azure-signalr/signalr-overview)では TLS 1.2 が必要であり、SHA-1 ベースの暗号スイートはサポートされていないため、Azure SignalR サービスを介して接続するには、Android API レベル20以降が必要です。 Android では、API レベル20で[SHA-256 (およびそれ以降) の暗号スイートのサポートが追加されました](https://developer.android.com/reference/javax/net/ssl/SSLSocket)。

## <a name="configure-bearer-token-authentication"></a>ベアラートークン認証を構成する

SignalR Java クライアントでは、 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)に "アクセストークンファクトリ" を提供することによって、認証に使用するベアラートークンを構成できます。 [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)を使用して、 [RxJava](https://github.com/ReactiveX/RxJava)の[単一\<文字列 >](https://reactivex.io/documentation/single.html)を指定します。 [単一の defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)を呼び出すことで、クライアントのアクセストークンを生成するロジックを作成できます。

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>既知の制限事項

::: moniker range=">= aspnetcore-3.0"

* JSON プロトコルのみがサポートされています。
* トランスポートフォールバックおよびサーバー送信イベントトランスポートはサポートされていません。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* JSON プロトコルのみがサポートされています。
* Websocket トランスポートのみがサポートされています。
* ストリーミングはまだサポートされていません。

::: moniker-end

## <a name="additional-resources"></a>その他のリソース

* [Java API リファレンス](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR サービスのサーバーレスドキュメント](/azure/azure-signalr/signalr-concept-serverless-development-config)
