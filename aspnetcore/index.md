---
title: ASP.NET Core の概要
author: rick-anderson
description: インターネットに接続された最新のクラウド ベース アプリケーションを構築するための、クロス プラットフォームで高パフォーマンスのオープン ソース フレームワークである ASP.NET Core について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: index
ms.openlocfilehash: ec2b5b72eeff21d183397284b0d538681282d45b
ms.sourcegitcommit: 47cc13ab90913af9a2887cef0896bb4e9aba4dd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67399161"
---
# <a name="introduction-to-aspnet-core"></a>ASP.NET Core の概要

著者: [Daniel Roth](https://github.com/danroth27)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Shaun Luttin](https://twitter.com/dicshaunary)

ASP.NET Core は、インターネットに接続された最新のクラウド ベース アプリケーションを構築するための、クロス プラットフォームで高パフォーマンスの[オープン ソース](https://github.com/aspnet/home) フレームワークです。 ASP.NET Core では次のことができます。

* Web アプリ、Web サービス、[IoT](https://www.microsoft.com/internet-of-things/) アプリ、モバイル バックエンドを構築する。
* Windows、macOS、Linux で好みの開発ツールを使う。
* クラウドまたはオンプレミスに展開する。
* [.NET Core または .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) 上で実行する。

## <a name="why-choose-aspnet-core"></a>ASP.NET Core を選ぶ理由

何百万人もの開発者が、これまで、そして現在も、Web アプリの作成に [ASP.NET 4.x](/aspnet/overview) を使っています。 ASP.NET Core は ASP.NET 4.x を設計し直したものであり、無駄のないモジュール形式のフレームワークになるようにアーキテクチャが変更されています。

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>ASP.NET Core MVC を使って Web API と Web UI を構築する

ASP.NET Core MVC は、[Web API](xref:tutorials/first-web-api) と [Web アプリ](xref:tutorials/razor-pages/index)を構築する機能を備えています。

* [モデル ビュー コントローラー (MVC) パターン](xref:mvc/overview)は、Web API と Web アプリをテスト可能にするのに役立ちます。
* [Razor ページ](xref:razor-pages/index)はページ ベースのプログラミング モデルであり、Web UI の開発を容易にし、生産性を高めます。
* [Razor マークアップ](xref:mvc/views/razor)では、[Razor ページ](xref:razor-pages/index)および [MVC ビュー](xref:mvc/views/overview)用に生産性の高い構文が提供されます。
* [タグ ヘルパー](xref:mvc/views/tag-helpers/intro)を使うと、Razor ファイルでの HTML 要素の作成とレンダリングに、サーバー側コードを組み込むことができます。
* [複数のデータ形式とコンテンツ ネゴシエーション](xref:web-api/advanced/formatting)の組み込みサポートにより、Web API はブラウザーやモバイル デバイスなどのさまざまなクライアントと接続できます。
* [モデル バインド](xref:mvc/models/model-binding)は、HTTP 要求からアクション メソッドのパラメーターにデータを自動的にマップします。
* [モデル検証](xref:mvc/models/validation)では、クライアント側とサーバー側の検証が自動的に実行されます。

## <a name="client-side-development"></a>クライアント側の開発

ASP.NET Core は、人気のあるクライアント側のフレームワークとライブラリ ([Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react)、[Bootstrap](https://getbootstrap.com/) など) をシームレスに統合します。 詳細については、<xref:blazor/index> と "*クライアント側の開発*" の関連トピックを参照してください。

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>.NET Framework を対象とする ASP.NET Core

ASP.NET Core 2.x は、.NET Core または .NET Framework を対象にすることができます。 .NET Framework を対象とする ASP.NET Core アプリはクロスプラットフォームではありません&mdash;Windows でのみ実行されます。 一般に、ASP.NET Core 2.x は [.NET Standard](/dotnet/standard/net-standard) ライブラリで構成されています。 .NET Standard 2.0 を使って作成されたライブラリは、[.NET Standard 2.0 を実装しているすべての .NET プラットフォーム](/dotnet/standard/net-standard#net-implementation-support)上で動作します。

ASP.NET Core 2.x は、.NET Standard 2.0 を実装している .NET Framework バージョンにおいてサポートされています。

* .NET Framework の最新バージョンを強くお勧めします。
* .NET Framework 4.6.1 以降。

ASP.NET Core 3.0 以降は、.NET Core でのみ実行されます。 この変更に関する詳細については、「[ASP.NET Core 3.0 で導入される変更について](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/)」を参照してください。

.NET Core を対象とする利点はいくつかあり、リリースのたびにその利点が増えています。 .NET Framework 経由による .NET Core には次のような利点があります。

* クロスプラットフォームである。 macOS、Linux、Windows で実行できる。
* パフォーマンスの向上
* side-by-side でのバージョン管理
* 新しい API
* ソースを開く

.NET Framework と .NET Core の間にある API のギャップを埋めるため、鋭意作業中です。 [Windows 互換機能パック](/dotnet/core/porting/windows-compat-pack)により、多くの Windows 限定の API が .NET Core で利用できるようになりました。 このような API は .NET Core 1.x で利用できませんでした。

## <a name="recommended-learning-path"></a>推奨のラーニング パス

ASP.NET Core アプリを開発する場合の概要として、次の順序でチュートリアルと記事を読むことをお勧めします。

1. 開発または管理するアプリの種類別のチュートリアルは次のとおりです。

   |アプリの種類  |シナリオ  |チュートリアル  |
   |----------|----------|----------|
   |Web アプリ       | 新規の開発        |[Razor ページの概要](xref:tutorials/razor-pages/razor-pages-start) |
   |Web アプリ       | MVC アプリの管理 |[MVC の概要](xref:tutorials/first-mvc-app/start-mvc)|
   |Web API       |                            |[Web API の作成](xref:tutorials/first-web-api)\*  |
   |リアルタイムのアプリ |                            |[SignalR の概要](xref:tutorials/signalr) |

1. 基本のデータ アクセスの実行方法を示すチュートリアルは次のとおりです。

   |シナリオ  |チュートリアル  |
   |----------|----------|
   | 新規の開発        |[Entity Framework Core を使用した Razor Pages](xref:data/ef-rp/intro) |
   | MVC アプリの管理 |[Entity Framework Core を使用した MVC](xref:data/ef-mvc/intro)

1. すべての種類のアプリに該当する ASP.NET Core の機能の概要は、次を参照してください。

   * [Entity Framework の基礎](xref:fundamentals/index)

1. 興味のあるその他のトピックは、目次から参照してください。

\* [すべてブラウザーでたどる新しい Web API のチュートリアル](https://docs.microsoft.com/learn/modules/build-web-api-net-core)があります。ローカルに IDE をインストールする必要はありません。  このコードは、[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) で実行し、テストには [curl](https://curl.haxx.se/) を使用します。

## <a name="how-to-download-a-sample"></a>サンプルをダウンロードする方法

多くの記事やチュートリアルにサンプル コードへのリンクが含まれています。

1. [ASP.NET リポジトリの zip ファイルをダウンロード](https://codeload.github.com/aspnet/AspNetCore.Docs/zip/master)します。
1. *Docs-master.zip* ファイルを解凍します。
1. サンプル リンクの URL を使って、サンプル ディレクトリに移動します。

### <a name="preprocessor-directives-in-sample-code"></a>サンプル コードのプリプロセッサ ディレクティブ

複数のシナリオを示すため、サンプル アプリでは `#define` と `#if-#else/#elif-#endif` の C# ステートメントを使用してさまざまなサンプル コードのセクションを選択してコンパイルし、実行します。 このアプローチを活用するサンプルでは、C# ファイルの上部にある `#define` ステートメントを、実行するシナリオに関連付けられたシンボルに設定します。 一部のサンプルでは、シナリオを実行するために複数のファイルの上部でシンボルを設定する必要があります。

たとえば、次の `#define` のシンボル一覧は、4 つのシナリオが使用可能である (シンボルごとに 1 つのシナリオ) ことを示しています。 現在のサンプル構成では `TemplateCode` のシナリオが実行されます。

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

`ExpandDefault` のシナリオを実行するサンプルを変更するには、`ExpandDefault` のシンボルを定義し、残りのシンボルをコメント アウトしたままにします。

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

[C# プリプロセッサ ディレクティブ](/dotnet/csharp/language-reference/preprocessor-directives/)を使用してコードのセクションを選択的にコンパイルする方法の詳細については、「[#define (C# リファレンス)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define)」および「[#if (C# リファレンス)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)」を参照してください。

### <a name="regions-in-sample-code"></a>サンプル コードのリージョン

一部のサンプル アプリには、[#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) と [#end-region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) の C# ステートメントに囲まれたコードのセクションが含まれています。 ドキュメントのビルド システムによって、レンダリングされたドキュメントのトピックにこのリージョンが挿入されます。  

リージョン名には通常、"snippet" という単語が含まれています。 次の例は `snippet_FilterInCode` という名前のリージョンを示しています。

```csharp
#region snippet_FilterInCode
WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter("System", LogLevel.Debug)
            .AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Trace))
            .Build();
#endregion
```

先述の C# コード スニペットは、トピックのマークダウン ファイルの次の行で示されています。

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_FilterInCode)]
```

コードを囲む `#region` と `#endregion` のステートメントは安全に無視 (または削除) することができます。 トピックで説明されているサンプル シナリオを実行する予定がある場合は、これらのステートメント内のコードを変更しないでください。 他のシナリオを試す場合は、自由にコードを変更できます。

詳細については、「[Contribute to the ASP.NET documentation: Code snippets (ASP.NET に貢献する: コード スニペット)](https://github.com/aspnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets)」を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [ASP.NET Core の基礎](xref:fundamentals/index)
* [週 1 回の ASP.NET Community Standup](https://live.asp.net/) では、チームの進行状況とプランが報告され、 新しいブログやサード パーティ製ソフトウェアが取り上げられています。
