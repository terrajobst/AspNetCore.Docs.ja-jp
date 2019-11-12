---
title: ASP.NET Core の基礎
author: rick-anderson
description: ASP.NET Core アプリの構築に関する基本概念について学習します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/index
ms.openlocfilehash: 7173a732a04bf3e598adef298fa9120c15dd52fb
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799373"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core の基礎

この記事では、ASP.NET Core アプリの開発方法を理解するための主要なトピックをまとめています。

## <a name="the-startup-class"></a>Startup クラス

`Startup` クラスとは、次のとおりです。

* アプリで必要なサービスが構成されています。
* 要求を処理するパイプラインが定義されています。

*サービス*とは、アプリが使用するコンポーネントです。 たとえば、ログ コンポーネントは、サービスです。 サービスを構成 (または*登録*) するコードが `Startup.ConfigureServices` メソッドに追加されています。

要求を処理するパイプラインは、一連の*ミドルウェア* コンポーネントとして構成されています。 たとえば、ミドルウェアは、静的ファイルに対する要求を処理したり、HTTPS に HTTP 要求をリダイレクトします。 各ミドルウェアは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。 `Startup.Configure` メソッドには、要求を処理するパイプラインを構成するコードが追加されます。

`Startup` クラスの例を次に示します。

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

詳細については、<xref:fundamentals/startup> を参照してください。

## <a name="dependency-injection-services"></a>依存性の注入 (サービス)

ASP.NET Core には、アプリのクラスが構成済みのサービスを利用できるようにする依存性の注入 (DI) フレームワークが組み込まれています。 クラスのサービスのインスタンスを取得する 1 つの方法は、必要な型のパラメーターを使用したコンストラクターを作成することです。 このパラメーターには、サービスの種類またはインターフェイスが可能です。 DI システムは、実行時にこのサービスを提供します。

Entity Framework Core コンテキスト オブジェクトを取得するために DI を使用するクラスを次に示します。 強調表示されている行は、コンストラクターの挿入の例です。

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

DI が組み込まれており、必要に応じてサードパーティ製の制御の反転 (IoC) コンテナーを組み込むことができるよう設計されています。

詳細については、<xref:fundamentals/dependency-injection> を参照してください。

## <a name="middleware"></a>ミドルウェア

要求を処理するパイプラインは、一連のミドルウェア コンポーネントとして構成されています。 各コンポーネントは `HttpContext` に非同期操作を実行してから、パイプラインの次のミドルウェアを呼び出すか、要求を終了します。

通常、ミドルウェア コンポーネントは、`Startup.Configure` メソッドのその `Use...` 拡張メソッドを呼び出してパイプラインに追加されます。 たとえば、静的ファイルのレンダリングを有効にするには、`UseStaticFiles` を呼び出します。

次の例の強調表示されているコードは、要求を処理するパイプラインを構成します。

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

ASP.NET Core にはミドルウェアのセットが豊富に組み込まれており、カスタム ミドルウェアをユーザーが記述できます。

詳細については、<xref:fundamentals/middleware/index> を参照してください。

## <a name="host"></a>Host

ASP.NET Core アプリは起動時に*ホスト*をビルドします。 ホストとは、次などのアプリのすべてのリソースをカプセル化するオブジェクトです。

* HTTP サーバーの実装
* ミドルウェア コンポーネント
* ログの記録
* DI
* 構成

アプリの相互依存するすべてのリソースを 1 つのオブジェクトに含める主な理由は、アプリの起動と正常なシャットダウンの制御の有効期間の管理のためです。

::: moniker range=">= aspnetcore-3.0"

汎用ホストと Web ホストの 2 つのホストが利用可能です。 汎用ホストが推奨されます。Web ホストは下位互換性のためにのみ使用できます。

ホストを作成するコードは `Program.Main` にあります。

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

`CreateDefaultBuilder` メソッドと `ConfigureWebHostDefaults` メソッドは、次のようなよく使用されるオプションと共にホストを構成します。

* Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。
* *appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。
* ログ出力をコンソールとデバッグ プロバイダーに送ります。

詳細については、<xref:fundamentals/host/generic-host> を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Web ホストと汎用ホストの 2 つのホストが利用可能です。 ASP.NET core 2.x では、汎用ホストは Web 以外のシナリオのみを対象としています。

ホストを作成するコードは `Program.Main` にあります。

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

`CreateDefaultBuilder` メソッドは、次のようなよく使用されるオプションと共にホストを構成します。

* Web サーバーとして [Kestrel](#servers) を使用し、IIS の統合を有効にします。
* *appsettings.json*、"*appsettings.{環境名}.json*"、環境変数、コマンド ライン引数、およびその他の構成ソースから構成を読み込みます。
* ログ出力をコンソールとデバッグ プロバイダーに送ります。

詳細については、<xref:fundamentals/host/web-host> を参照してください。

::: moniker-end

### <a name="non-web-scenarios"></a>Web 以外のシナリオ

汎用ホストにより、他の種類のアプリで、ログ記録、依存性の注入 (DI)、構成、およびアプリの有効期間管理などの横断的なフレームワーク拡張機能を使えるようになります。 詳細については、次のトピックを参照してください。 <xref:fundamentals/host/generic-host> および <xref:fundamentals/host/hosted-services>

## <a name="servers"></a>サーバー

ASP.NET Core アプリは、HTTP 要求をリッスンするために HTTP サーバー実装を使用します。 サーバーは、`HttpContext` に構成した[要求機能](xref:fundamentals/request-features)のセットとして、アプリへの要求を公開します。

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core では、次のサーバー実装が提供されます。

* *Kestrel* は、クロスプラットフォームの Web サーバーです。 Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。 Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。
* *IIS HTTP サーバー*は、IIS を使用する Windows のサーバーです。 このサーバーでは、ASP.NET Core アプリと IIS が同じプロセスで実行されます。
* *HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。 Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。 Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。 Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。 Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core では、次のサーバー実装が提供されます。

* *Kestrel* は、クロスプラットフォームの Web サーバーです。 Kestrel は [IIS](https://www.iis.net/) を使用してリバース プロキシ構成で実行されることがよくあります。 Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。
* *HTTP.sys* は、IIS とは一緒に使用しない Windows のサーバーです。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。 Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。 Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core は、*Kestrel* クロスプラットフォーム サーバーの実装を提供します。 Kestrel は、ASP.NET Core 2.0 以降で、インターネットに直接公開される一般向けエッジ サーバーとして実行することもできます。 Kestrel は [Nginx](https://nginx.org) または [Apache](https://httpd.apache.org/) を使用してリバース プロキシ構成で実行されることがよくあります。

---

::: moniker-end

詳細については、<xref:fundamentals/servers/index> を参照してください。

## <a name="configuration"></a>構成

ASP.NET Core は、構成プロバイダーの順序付けされたセットから、名前と値のペアの設定を取得する構成フレームワークとなります。 *.json* ファイル、 *.xml* ファイル、環境変数、コマンドライン引数など、さまざまなソース用に構成プロバイダーが組み込まれています。 独自のカスタム構成プロバイダーを記述することもできます。

たとえば、構成は *appsettings.json* と環境変数から取得したものであると指定できます。 このとき *ConnectionString* 値が要求されると、フレームワークはまず *appsettings.json* ファイルを参照します。 値がそこにあり、しかし環境変数にもある場合、環境変数の値が優先されます。

ASP.NET Core には、パスワードなどの機密の構成データの管理に[シークレット マネージャー ツール](xref:security/app-secrets)が用意されています。 実稼働の機密情報には、[Azure Key Vault](xref:security/key-vault-configuration) を使用することをお勧めします。

詳細については、<xref:fundamentals/configuration/index> を参照してください。

## <a name="options"></a>オプション

ASP.NET Core では、構成値の格納と取得に、可能な限り*オプション パターン*を使用します。 オプション パターンではクラスを使用して、関連する設定のグループを表します。

たとえば、以下のコードでは WebSockets のオプションが設定されます。

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

詳細については、<xref:fundamentals/configuration/options> を参照してください。

## <a name="environments"></a>環境

*開発*、*ステージング*、および*実稼働*などの実行環境は ASP.NET Core の最上の概念です。 アプリが実行している環境は、`ASPNETCORE_ENVIRONMENT` 環境変数を設定することにより指定できます。 ASP.NET Core は、アプリの起動時にその環境変数を読み取り、その値を `IHostingEnvironment` 実装に格納します。 この環境オブジェクトは、DI を介しアプリの任意の場所で使用されます。

`Startup` クラスの次のサンプル コードは、それが開発環境で実行された場合のみ、詳細なエラー情報を提供するようアプリを構成します。

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

詳細については、<xref:fundamentals/environments> を参照してください。

## <a name="logging"></a>ログの記録

ASP.NET Core では、組み込みやサード パーティ製のさまざまなログ プロバイダーと連携するログ API がサポートされています。 利用可能なプロバイダーは次のとおりです。

* コンソール
* デバッグ
* Event Tracing on Windows
* Windows イベント ログ
* TraceSource
* Azure App Service
* Azure Application Insights

DI からの `ILogger` オブジェクトの取得およびログ メソッドの呼び出しによってアプリのコードの任意の場所からログを記述します。

コンストラクターの挿入とログ記録メソッドの呼び出しが強調表示されている `ILogger` オブジェクトを使用するサンプル コードを次に示します。

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

`ILogger` インターフェイスは、ログ プロバイダーに任意の数のフィールドを渡すことができます。 このフィールドは、一般的にメッセージの文字列を構築するために使用しますが、プロバイダーがデータ ストアに別のフィールドとして送信することも可能です。 この機能は、[構造化ロギングとも呼ばれるセマンティック ロギング](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)をログ プロバイダーが実装するのを可能にします。

詳細については、<xref:fundamentals/logging/index> を参照してください。

## <a name="routing"></a>ルーティング

*ルート*とは、ハンドラーにマップされている URL のパターンです。 このハンドラーは一般的には Razor Pages、MVC コントローラーのアクション メソッドまたはミドルウェアです。 ASP.NET Core のルーティングでは、アプリで使用する URL を制御できます。

詳細については、<xref:fundamentals/routing> を参照してください。

## <a name="error-handling"></a>エラー処理

ASP.NET Core には、次などのエラー処理用の機能が組み込まれています。

* 開発者例外ページ
* カスタム エラー ページ
* 静的状態コード ページ
* 起動時の例外処理

詳細については、<xref:fundamentals/error-handling> を参照してください。

## <a name="make-http-requests"></a>HTTP 要求を行う

`HttpClient` インスタンスの作成に、`IHttpClientFactory` の実装を使用できます。 ファクトリは次のことを行います。

* 論理 `HttpClient` インスタンスの名前付けと構成を一元化します。 たとえば、*github* クライアントを登録して、GitHub にアクセスするように構成できます。 既定のクライアントは、他の目的に登録できます。
* 複数のデリゲート ハンドラーを登録してチェーン化し、送信要求ミドルウェア パイプラインを構築するのをサポートしています。 このパターンは、ASP.NET Core での受信ミドルウェア パイプラインに似ています。 このパターンは、キャッシュ、エラー処理、シリアル化、ログ記録など、HTTP 要求に関する横断的関心事を管理するためのメカニズムを提供します。
* 一時的な障害処理用の人気のサードパーティ製ライブラリ、*Polly* と統合できます。
* 基になっている `HttpClientMessageHandler` インスタンスのプールと有効期間を管理し、`HttpClient` の有効期間を手動で管理するときに発生する一般的な DNS の問題を防ぎます。
* ファクトリによって作成されたクライアントから送信されるすべての要求に対し、(`ILogger` によって) 構成可能なログ エクスペリエンスを追加します。

詳細については、<xref:fundamentals/http-requests> を参照してください。

## <a name="content-root"></a>コンテンツ ルート

コンテンツ ルートは、以下に対する基本パスです。

* アプリをホストしている実行可能ファイル ( *.exe*)。
* アプリを構成するコンパイル済みアセンブリ ( *.dll*)。
* 次のような、アプリで使用される非コード コンテンツ ファイル。
  * Razor ファイル ( *.cshtml*、 *.razor*)
  * 構成ファイル ( *.json*、 *.xml*)
  * データ ファイル ( *.db*)
* [Web ルート](#web-root) (通常、発行された *wwwroot* フォルダー)。

開発中:

* コンテンツ ルートの規定値は、プロジェクトのルート ディレクトリです。
* プロジェクトのルート ディレクトリは、次を作成するために使用されます。
  * プロジェクトのルート ディレクトリ内にある、アプリの非コード コンテンツ ファイルへのパス。
  * [Web ルート](#web-root) (通常は、プロジェクトのルート ディレクトリ内の *wwwroot* フォルダー)。

::: moniker range=">= aspnetcore-3.0"

[ホストの構築時](#host)には、別のコンテンツ ルート パスを指定できます。 詳細については、<xref:fundamentals/host/generic-host#contentrootpath> を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ホストの構築時](#host)には、別のコンテンツ ルート パスを指定できます。 詳細については、<xref:fundamentals/host/web-host#content-root> を参照してください。

::: moniker-end

## <a name="web-root"></a>Web ルート

Web ルートは、次のような、パブリックで非コードの静的なリソース ファイルへの基本パスです。

* スタイルシート ( *.css*)
* JavaScript ( *.js*)
* 画像 ( *.png*、*jpg*)

既定で、静的ファイルは Web ルート ディレクトリ (とサブディレクトリ) からのみ提供されます。

::: moniker range=">= aspnetcore-3.0"

Web ルートのパスの既定値は、" *{コンテンツ ルート}/wwwroot*" ですが、[ホストの構築](#host)時に別の Web ルートを指定することも可能です。 詳細については、<xref:fundamentals/host/generic-host#webroot> を参照してください。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Web ルートのパスの既定値は、" *{コンテンツ ルート}/wwwroot*" ですが、[ホストの構築](#host)時に別の Web ルートを指定することも可能です。 詳細については、「[Web ルート](xref:fundamentals/host/web-host#web-root)」を参照してください。

::: moniker-end

プロジェクト ファイル内の [\<コンテンツ > プロジェクト項目](/visualstudio/msbuild/common-msbuild-project-items#content) を使用して *wwwroot* にファイルを発行できないようにします。 次の例では、*wwwroot/local* ディレクトリおよびサブディレクトリにコンテンツを公開しないようにします。

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Razor ( *.cshtml*) ファイルの場合、チルダとスラッシュ (`~/`) が Web ルートを指します。 `~/` で始まるパスは、"*仮想パス*" と呼ばれます。

詳細については、<xref:fundamentals/static-files> を参照してください。
