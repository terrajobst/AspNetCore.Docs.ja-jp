---
title: Blazor ホスティングモデルの ASP.NET Core
author: guardrex
description: Blazor Webasと Blazor サーバーホスティングモデルについて理解します。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 54be0e032a60c69880f428e52f9d778032385dc5
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447049"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a>Blazor ホスティングモデルの ASP.NET Core

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor は、ブラウザーでブラウザーでクライアント側を実行するために設計された web フレームワークであり、 [webasに](https://webassembly.org/)基づく .net ランタイム ( *Blazor webassembly*、または ASP.NET Core ( *Blazor サーバー*) のサーバー側で実行されます。 ホスティングモデルに関係なく、アプリモデルとコンポーネントモデル*は同じ*です。

この記事で説明されているホスティングモデルのプロジェクトを作成するには、「<xref:blazor/get-started>」を参照してください。

詳細な構成については、「<xref:blazor/hosting-model-configuration>」を参照してください。

## <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

Blazor のプリンシパルホスティングモデルは、ブラウザーでクライアント側で実行されます。 Blazor アプリ、その依存関係、.NET ランタイムがブラウザーにダウンロードされます。 アプリがブラウザー UI スレッド上で直接実行されます。 UI の更新とイベントの処理は、同じプロセス内で行われます。 アプリの資産は静的ファイルとして、静的コンテンツをクライアントに提供できる web サーバーまたはサービスに展開されます。

![[!ファンド.NO LOC (Blazor)] WebAssembly [!ファンド.NO LOC (Blazor)] アプリは、ブラウザー内の UI スレッドで実行されます。](hosting-models/_static/blazor-webassembly.png)

クライアント側のホスティングモデルを使用して Blazor アプリを作成するには、 **Blazor WebAssembly アプリ**テンプレート ([new blazorwasm](/dotnet/core/tools/dotnet-new)) を使用します。

**Blazor WebAssembly**テンプレートを選択した後、[ホストされている**ASP.NET Core** ] チェックボックス ([dotnet new blazorwasm--hosted](/dotnet/core/tools/dotnet-new)) を選択して、ASP.NET Core バックエンドを使用するようにアプリを構成することができます。 ASP.NET Core アプリは、クライアントに対して Blazor アプリを提供します。 Blazor WebAssembly は、web API 呼び出しまたは[SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>) を使用して、ネットワーク経由でサーバーと対話できます。

テンプレートには、を処理する `blazor.webassembly.js` スクリプトが含まれています。

* .NET ランタイム、アプリ、およびアプリの依存関係をダウンロードしています。
* アプリを実行するランタイムの初期化。

Blazor WebAssembly ホスティングモデルには、いくつかの利点があります。

* .NET サーバー側の依存関係はありません。 クライアントにダウンロードされた後、アプリは完全に機能しています。
* クライアントのリソースと機能は完全に活用されています。
* 作業はサーバーからクライアントにオフロードされます。
* アプリケーションをホストするために ASP.NET Core web サーバーは必要ありません。 サーバーレスの展開シナリオが可能です (たとえば、CDN からアプリを提供するなど)。

Blazor Webasホストには欠点があります。

* アプリは、ブラウザーの機能に制限されています。
* サポートされているクライアントハードウェアとソフトウェア (たとえば、WebAssembly サポート) が必要です。
* ダウンロードサイズが大きくなり、アプリの読み込みに時間がかかります。
* .NET ランタイムとツールのサポートの成熟度は低くなります。 たとえば、 [.NET Standard](/dotnet/standard/net-standard)のサポートとデバッグには制限があります。

## <a name="opno-locblazor-server"></a>Blazor サーバー

Blazor サーバーホスティングモデルでは、アプリは ASP.NET Core アプリ内からサーバー上で実行されます。 UI の更新、イベント処理、JavaScript の呼び出しは、[SignalR](xref:signalr/introduction) 接続経由で処理されます。

![ブラウザーは、サーバー上の (ASP.NET Core アプリ内でホストされている) アプリと対話します。ファンド.NO LOC (SignalR)] 接続。](hosting-models/_static/blazor-server.png)

Blazor Server ホスティングモデルを使用して Blazor アプリを作成するには、ASP.NET Core **Blazor サーバーアプリ**テンプレート ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)) を使用します。 ASP.NET Core アプリは、Blazor Server アプリをホストし、クライアントが接続する SignalR エンドポイントを作成します。

ASP.NET Core アプリは、追加するアプリの `Startup` クラスを参照します。

* サーバー側サービス。
* 要求処理パイプラインに対するアプリ。

`blazor.server.js` スクリプト&dagger; クライアント接続を確立します。 アプリケーションの状態は、必要に応じて永続化および復元する必要があります (ネットワーク接続が切断された場合など)。

Blazor サーバーホスティングモデルには、いくつかの利点があります。

* ダウンロードサイズは Blazor Webasapp よりも大幅に小さく、アプリの読み込みにかかる時間が大幅に短縮されます。
* このアプリでは、.NET Core と互換性のある Api の使用を含め、サーバーの機能を最大限に活用できます。
* サーバー上の .NET Core はアプリを実行するために使用されるため、デバッグなどの既存の .NET ツールは想定どおりに動作します。
* シンクライアントがサポートされています。 たとえば、Blazor サーバーアプリは、WebAssembly サポートされていないブラウザーや、リソースが制限されたデバイスで動作します。
* アプリのコンポーネントコードをC#含む、アプリの .net/コードベースはクライアントに提供されません。

Blazor サーバーのホストには欠点があります。

* 通常、待機時間が長くなります。 すべてのユーザーの操作には、ネットワークホップが関係します。
* オフラインサポートはありません。 クライアント接続が失敗した場合、アプリは動作を停止します。
* 多くのユーザーがいるアプリでは、スケーラビリティが困難です。 サーバーは、複数のクライアント接続を管理し、クライアントの状態を処理する必要があります。
* アプリを提供するには、ASP.NET Core サーバーが必要です。 サーバーレス展開シナリオは使用できません (たとえば、CDN からアプリを提供するなど)。

&dagger;`blazor.server.js` スクリプトは、ASP.NET Core 共有フレームワークの埋め込みリソースから提供されます。

### <a name="comparison-to-server-rendered-ui"></a>サーバーレンダリングの UI との比較

Blazor サーバーアプリを理解する方法の1つは、Razor ビューまたは Razor Pages を使用する ASP.NET Core アプリで UI をレンダリングするための従来のモデルとの違いを理解することです。 どちらのモデルでも、Razor 言語を使用して HTML コンテンツを記述しますが、マークアップのレンダリング方法が大きく異なります。

Razor ページまたはビューがレンダリングされると、Razor コードのすべての行で HTML がテキスト形式で出力されます。 レンダリング後、サーバーは、生成されたすべての状態を含むページまたはビューインスタンスを破棄します。 サーバーの検証に失敗し、検証の概要が表示される場合など、ページに対する別の要求が発生したとき。

* ページ全体が HTML テキストに再び表示されます。
* ページがクライアントに送信されます。

Blazor アプリは、*コンポーネント*と呼ばれる UI の再利用可能な要素で構成されます。 コンポーネントにはC# 、コード、マークアップ、およびその他のコンポーネントが含まれています。 コンポーネントがレンダリングされると、Blazor は HTML または XML ドキュメントオブジェクトモデル (DOM) のような、含まれているコンポーネントのグラフを生成します。 このグラフには、プロパティとフィールドに保持されているコンポーネントの状態が含まれます。 Blazor は、コンポーネントグラフを評価して、マークアップのバイナリ表現を生成します。 バイナリ形式は次のようになります。

* HTML テキストに変換されます (&dagger;のプリレンダリング中)。
* 通常のレンダリング中にマークアップを効率的に更新するために使用されます。

要求された Razor コンポーネントは、サーバー上で静的 HTML にコンパイルされ、クライアントに送信され、ユーザーに表示されるように、*プリレンダリング*&ndash; &dagger;ます。 クライアントとサーバーの間で接続が確立されると、コンポーネントの静的な prerendered 要素が対話型要素に置き換えられます。 プリレンダリングによって、ユーザーによるアプリの応答性が向上します。

Blazor の UI の更新は、次の方法でトリガーされます。

* ユーザー操作 (ボタンの選択など)。
* タイマーなどのアプリトリガー。

グラフが再ピアリングされ、UI *diff* (差分) が計算されます。 この diff は、クライアントで UI を更新するために必要な DOM 編集の最小セットです。 Diff はバイナリ形式でクライアントに送信され、ブラウザーによって適用されます。

コンポーネントは、ユーザーがクライアント上で移動した後に破棄されます。 ユーザーがコンポーネントを操作している間、コンポーネントの状態 (サービス、リソース) は、サーバーのメモリに保持されている必要があります。 多くのコンポーネントの状態は同時にサーバーによって維持される可能性があるため、メモリ不足に対処する必要があります。 サーバーメモリを最大限に活用するために Blazor Server アプリを作成する方法については、「<xref:security/blazor/server>」を参照してください。

### <a name="circuits"></a>回線

Blazor サーバーアプリは[ASP.NET Core SignalR](xref:signalr/introduction)の上に構築されています。 各クライアントは、*回線*と呼ばれる1つ以上の SignalR 接続を介してサーバーと通信します。 回線は、一時的なネットワーク中断が許容される SignalR 接続に対して Blazor抽象化されています。 Blazor クライアントは、SignalR 接続が切断されていることを確認すると、新しい SignalR 接続を使用してサーバーへの再接続を試みます。

Blazor Server アプリに接続されている各ブラウザー画面 (ブラウザータブまたは iframe) は、SignalR 接続を使用します。 これは、サーバーでレンダリングされる一般的なアプリと比較して、もう1つ重要な違いです。 サーバー側でレンダリングされるアプリでは、複数のブラウザー画面で同じアプリを開くのは、通常、サーバーに対する追加のリソース要求には変換されません。 Blazor サーバーアプリでは、各ブラウザー画面に個別の回線が必要で、コンポーネント状態の個別のインスタンスがサーバーによって管理されます。

Blazor は、ブラウザータブを閉じるか、外部 URL に移動して*正常*に終了すると見なされます。 正常な終了が発生した場合、回線と関連するリソースが直ちに解放されます。 クライアントは、ネットワークの中断などによって、正常に切断されることもあります。 Blazor サーバーは、クライアントが再接続できるように、接続されていない回線を構成可能な間隔で格納します。

### <a name="ui-latency"></a>UI の待機時間

UI 待機時間とは、開始されたアクションから UI が更新されるまでにかかる時間のことです。 アプリがユーザーに応答できるようにするには、UI の待機時間の値を小さくすることが不可欠です。 Blazor Server アプリでは、各アクションがサーバーに送信され、処理されて、UI diff が返されます。 その結果、UI の待機時間は、ネットワーク待機時間の合計と、アクションを処理するサーバーの待機時間です。

企業のプライベートネットワークに限定された基幹業務アプリの場合、ネットワーク待機時間による待ち時間のユーザーへの影響は、通常はなるべくです。 インターネット経由で展開されたアプリの場合、ユーザーにとって待機時間が顕著になる可能性があります。ユーザーが地理的に広く分散している場合は特にそうです。

メモリ使用量は、アプリの待機時間に寄与する場合もあります。 メモリ使用量が増加すると、ガベージコレクションまたはメモリのページングが頻繁に発生します。どちらの場合も、アプリのパフォーマンスが低下し、その結果、UI の遅延が増加します。 詳細については、「<xref:security/blazor/server>」を参照してください。

Blazor サーバーアプリは、ネットワーク待機時間とメモリ使用量を削減することで、UI の待機時間を最小限に抑えるように最適化する必要があります。 ネットワーク待機時間を測定する方法については、「<xref:host-and-deploy/blazor/server#measure-network-latency>」を参照してください。 SignalR と Blazorの詳細については、以下を参照してください。

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>サーバーへの接続

Blazor サーバーアプリには、サーバーへのアクティブな SignalR 接続が必要です。 接続が失われた場合、アプリはサーバーへの再接続を試みます。 クライアントの状態がまだメモリ内にある限り、クライアントセッションは状態を失うことなく再開されます。

最初のクライアント要求への応答としての Blazor Server アプリ prerenders。これにより、サーバー上で UI の状態が設定されます。 クライアントが SignalR 接続を作成しようとすると、クライアントは同じサーバーに再接続する必要があります。 複数のバックエンドサーバーを使用する Blazor サーバーアプリは、SignalR 接続用の*固定セッション*を実装する必要があります。

[ サーバー アプリには SignalRAzure ](/azure/azure-signalr) ServiceBlazor を使用することをお勧めします。 このサービスでは、多数の同時 Blazor 接続に対して SignalR Server アプリをスケールアップできます。 Azure SignalR サービスでは、サービスの `ServerStickyMode` オプションまたは構成値を `Required`に設定することにより、固定セッションが有効になります。 詳細については、「<xref:host-and-deploy/blazor/server#signalr-configuration>」を参照してください。

IIS を使用すると、スティッキー セッションはアプリケーション要求ルーティングによって有効になります。 詳しくは、「[アプリケーション要求ルーティングを使用した HTTP 負荷分散](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* <xref:blazor/get-started>
* <xref:signalr/introduction>
* <xref:blazor/hosting-model-configuration>
* <xref:tutorials/signalr-blazor-webassembly>
