---
title: ASP.NET Core Blazor のホスティング モデル
author: guardrex
description: Blazor WebAssembly と Blazor サーバーのホスティング モデルについて学習します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: e6ce2be53c35268854e0e8d408b649a8c6ef497e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646802"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>ASP.NET Core Blazor のホスティング モデル

作成者: [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor は、[WebAssembly](https://webassembly.org/) ベースの .NET ランタイム ( *Blazor WebAssembly*) 上のブラウザーのクライアント側で、または ASP.NET Core ( *Blazor サーバー*) のサーバー側で実行されるように設計された Web フレームワークです。 ホスティング モデルに関係なく、アプリ モデルとコンポーネント モデルは "*同じ*" です。

この記事で説明されているホスティング モデルのプロジェクトを作成するには、「<xref:blazor/get-started>」を参照してください。

詳細な構成については、「<xref:blazor/hosting-model-configuration>」を参照してください。

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Blazor のプリンシパル ホスティング モデルは、WebAssembly 上のブラウザーのクライアント側で実行されます。 Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。 アプリがブラウザー UI スレッド上で直接実行されます。 UI の更新とイベントの処理は、同じプロセス内で行われます。 アプリの資産は、静的コンテンツをクライアントに提供できる Web サーバーまたはサービスに静的ファイルとして展開されます。

![Blazor WebAssembly:Blazor アプリは、ブラウザー内の UI スレッドで実行されます。](hosting-models/_static/blazor-webassembly.png)

クライアント側のホスティング モデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ** テンプレート ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。

**Blazor WebAssembly アプリ** テンプレートを選択したら、 **[ASP.NET Core hosted]** チェック ボックス ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)) をオンにして、ASP.NET Core バックエンドを使用するようにアプリを構成することもできます。 ASP.NET Core アプリは、Blazor アプリをクライアントに提供します。 Blazor WebAssembly アプリは、Web API 呼び出しまたは [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>) を使用してネットワーク経由でサーバーと通信できます。

テンプレートには、次の処理を行う `blazor.webassembly.js` スクリプトが含まれます。

* .NET ランタイム、アプリ、およびアプリの依存関係のダウンロード。
* アプリを実行するランタイムの初期化。

Blazor WebAssembly ホスティング モデルには、次のいくつかの利点があります。

* .NET サーバー側の依存関係がありません。 アプリはクライアントにダウンロードされた後、完全に機能します。
* クライアントのリソースと機能が完全に活用されます。
* 作業がサーバーからクライアントにオフロードされます。
* アプリをホストするために ASP.NET Core Web サーバーが必要ありません。 サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。

Blazor WebAssembly ホスティングには、次の欠点があります。

* アプリがブラウザーの機能に制限されます。
* サポートされているクライアント ハードウェアおよびソフトウェア (たとえば、WebAssembly サポート) が必要です。
* ダウンロード サイズが大きくなり、アプリの読み込みに時間がかかります。
* .NET ランタイムとツールのサポートが十分ではありません。 たとえば、[.NET Standard](/dotnet/standard/net-standard) のサポートとデバッグには制限があります。

## <a name="opno-locblazor-server"></a>Blazor サーバー

Blazor サーバーのホスティング モデルを使用すると、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。

![ブラウザーは、SignalR 接続を介してサーバー上のアプリ (ASP.NET Core アプリ内でホストされている) とやりとりします。](hosting-models/_static/blazor-server.png)

Blazor サーバー ホスティング モデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor サーバー アプリ** テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。 ASP.NET Core アプリによって Blazor サーバー アプリがホストされ、クライアントによって接続される SignalR エンドポイントが作成されます。

ASP.NET Core アプリにより、次の項目を追加するためにアプリの `Startup` クラスが参照されます。

* サーバー側サービス。
* 要求処理パイプラインに対するアプリ。

`blazor.server.js` スクリプト&dagger; により、クライアント接続が確立されます。 アプリケーションの状態は、必要に応じてアプリによって永続化および復元する必要があります (ネットワーク接続が切断された場合など)。

Blazor サーバー ホスティング モデルには、次のいくつかの利点があります。

* ダウンロード サイズが Blazor WebAssembly アプリよりもかなり小さく、アプリの読み込み時間が大幅に短縮されます。
* このアプリでは、.NET Core と互換性のあるすべての API の使用を含め、サーバーの機能を最大限に活用できます。
* サーバー上の .NET Core はアプリの実行に使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。
* シン クライアントがサポートされています。 たとえば、Blazor サーバー アプリは、WebAssembly がサポートされていないブラウザーや、リソースが制限されたデバイスで動作します。
* アプリのコンポーネント コードを含め、アプリの .NET/C# コード ベースがクライアントに提供されません。

Blazor サーバー ホスティングには、次の欠点があります。

* 通常、遅延時間が長くなります。 すべてのユーザーの操作にネットワーク ホップが関与します。
* オフライン サポートがありません。 クライアント接続が失敗すると、アプリの動作が停止します。
* 多くのユーザーがいるアプリで、スケーラビリティが課題になります。 サーバーでは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。
* アプリを提供するのに ASP.NET Core サーバーが必要です。 サーバーレス展開シナリオは利用できません (たとえば、CDN からアプリを提供するなど)。

&dagger;`blazor.server.js` スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。

### <a name="comparison-to-server-rendered-ui"></a>サーバーでレンダリングされる UI との比較

Blazor サーバー アプリを理解する方法の 1 つは、Razor ビューまたは Razor Pages を使用して ASP.NET Core アプリで UI をレンダリングするための従来のモデルとの違いを理解することです。 どちらのモデルでも、Razor 言語を使用して HTML コンテンツが記述されますが、マークアップのレンダリング方法が大きく異なります。

Razor ページまたはビューがレンダリングされると、Razor コードのすべての行で HTML がテキスト形式で出力されます。 レンダリング後、サーバーでは、生成されたすべての状態を含むページ インスタンスまたはビュー インスタンスが破棄されます。 たとえば、サーバーの検証に失敗して検証の概要が表示される場合など、ページに対する別の要求が発生すると、次の処理が行われます。

* ページ全体が HTML テキストに再びレンダリングされます。
* ページがクライアントに送信されます。

Blazor アプリは、"*コンポーネント*" と呼ばれる UI の再利用可能な要素で構成されています。 コンポーネントには、C# コード、マークアップ、およびその他のコンポーネントが含まれています。 コンポーネントがレンダリングされると、Blazor により、HTML または XML ドキュメント オブジェクト モデル (DOM) に似ている、含まれるコンポーネントのグラフが生成されます。 このグラフには、プロパティとフィールドに保持されているコンポーネントの状態が含まれます。 Blazor により、コンポーネント グラフが評価されて、マークアップのバイナリ表現が生成されます。 バイナリ形式は次のように処理できます。

* HTML テキストに変換できます (プリレンダリング時&dagger;)。
* 通常のレンダリング時にマークアップを効率的に更新するために使用できます。

&dagger;*プリレンダリング*&ndash; 要求された Razor コンポーネントはサーバーで静的 HTML にコンパイルされ、クライアントに送信されて、そこでユーザーに対してレンダリングされます。 クライアントとサーバーの間で接続が確立されると、コンポーネントのプリレンダリング済みの静的な要素が対話型要素に置き換えられます。 プリレンダリングによって、ユーザーに対するアプリの応答性が向上します。

Blazor の UI の更新は、次の方法でトリガーされます。

* ユーザー操作 (ボタンの選択など)。
* タイマーなどのアプリ トリガー。

グラフが再レンダリングされ、UI *diff* (相違) が計算されます。 この diff は、クライアントで UI を更新するために必要な DOM 編集の最小セットです。 diff はバイナリ形式でクライアントに送信され、ブラウザーによって適用されます。

コンポーネントは、ユーザーがクライアント上でコンポーネントから移動すると破棄されます。 ユーザーがコンポーネントを操作している間、コンポーネントの状態 (サービス、リソース) はサーバーのメモリに保持されている必要があります。 多くのコンポーネントの状態はサーバーによって同時に維持される場合があるため、メモリ不足の問題に対処する必要があります。 サーバー メモリを最大限に活用できるよう Blazor サーバー アプリを作成する方法については、「<xref:security/blazor/server>」を参照してください。

### <a name="circuits"></a>回線

Blazor サーバー アプリは、[ASP.NET Core SignalR](xref:signalr/introduction) 上に構築されています。 各クライアントは、"*回線*" と呼ばれる 1 つ以上の SignalR 接続を介してサーバーと通信します。 回線は、一時的なネットワーク中断が許容される SignalR 接続を介した Blazor の抽象化です。 Blazor クライアントで、SignalR 接続が切断されていることが確認されると、新しい SignalR 接続を使用してサーバーへの再接続が試行されます。

Blazor サーバー アプリに接続されている各ブラウザー画面 (ブラウザー タブまたは iframe) では、SignalR 接続が使用されます。 これは、サーバーでレンダリングされる一般的なアプリとの、もう 1 つの重要な違いです。 サーバーでレンダリングされるアプリでは、複数のブラウザー画面で同じアプリを開いても、通常、サーバー上で追加のリソースは要求されません。 Blazor サーバー アプリでは、ブラウザー画面ごとに、個別の回線とコンポーネント状態の個別のインスタンスをサーバーで管理する必要があります。

Blazor では、ブラウザー タブを閉じるか、または外部 URL に移動して "*正常に終了*" することが検討されます。 正常な終了が行われた場合は、回線と関連リソースが直ちに解放されます。 ネットワークの中断などにより、クライアントが正常に切断されないこともあります。 Blazor サーバーでは、クライアントが再接続できるように、切断された回線が格納されます (その間隔は設定できます)。

Blazor サーバーを使用すると、コードで "*回線ハンドラー*" を定義できます。これにより、ユーザーの回線の状態の変更時にコードを実行できます。 詳細については、「<xref:blazor/advanced-scenarios#blazor-server-circuit-handler>」を参照してください。

### <a name="ui-latency"></a>UI 遅延時間

UI 遅延時間とは、アクションが開始されてから UI が更新されるまでにかかる時間のことです。 ユーザーに対するアプリの応答性を高めるには、UI 遅延時間の値を小さくすることが不可欠です。 Blazor サーバー アプリでは、各アクションがサーバーに送信され、処理されて、UI diff が返されます。 したがって、UI 遅延時間は、アクションの処理時のネットワーク遅延時間とサーバー遅延時間の合計になります。

企業のプライベート ネットワークに限定された基幹業務アプリでは、通常、ネットワーク遅延時間によってユーザーが遅延を感じる度合いはわずかです。 インターネット経由で展開されたアプリでは、特にユーザーが地理的に広く分散している場合、ユーザーが遅延を感じる可能性があります。

メモリ使用量も、アプリ遅延時間の一因となる場合があります。 メモリ使用量が増加すると、ガベージ コレクションまたはディスクへのメモリのページングが頻繁に発生します。どちらの場合も、アプリのパフォーマンスが低下し、その結果、UI 遅延時間が長くなります。 詳細については、「<xref:security/blazor/server>」を参照してください。

Blazor サーバー アプリは、ネットワーク遅延時間とメモリ使用量を低減することで、UI 遅延時間を最小限に抑えるように最適化する必要があります。 ネットワーク遅延時間を測定する方法については、「<xref:host-and-deploy/blazor/server#measure-network-latency>」を参照してください。 SignalR と Blazor の詳細については、以下を参照してください。

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>サーバーへの接続

Blazor サーバー アプリには、サーバーへのアクティブな SignalR 接続が必要です。 接続が失われた場合、アプリではサーバーへの再接続が試行されます。 クライアントの状態がまだメモリ内にある限り、クライアント セッションは状態を失うことなく再開されます。

最初のクライアント要求への応答として、Blazor サーバー アプリによってプリレンダリングされます。これにより、サーバー上で UI の状態が設定されます。 クライアントで SignalR 接続の作成が再試行される際は、クライアントを同じサーバーに再接続する必要があります。 複数のバックエンド サーバーを使用する Blazor サーバー アプリでは、SignalR 接続に "*スティッキー セッション*" を実装する必要があります。

Blazor サーバー アプリには [Azure SignalR Service](/azure/azure-signalr) を使用することをお勧めします。 このサービスでは、多数の同時 SignalR 接続に対して Blazor Server アプリをスケールアップできます。 Azure SignalR サービスでは、サービスの `ServerStickyMode` オプションまたは構成値を `Required` に設定することにより、スティッキー セッションが有効になります。 詳細については、「<xref:host-and-deploy/blazor/server#signalr-configuration>」を参照してください。

IIS を使用すると、スティッキー セッションはアプリケーション要求ルーティングによって有効になります。 詳しくは、「[アプリケーション要求ルーティングを使用した HTTP 負荷分散](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」をご覧ください。

## <a name="additional-resources"></a>その他の技術情報

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
