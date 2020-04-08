---
title: ASP.NET Core で Angular プロジェクト テンプレートを使用する
author: SteveSandersonMS
description: Angular と Angular CLI 用の ASP.NET Core シングル ページ アプリケーション (SPA) プロジェクト テンプレートの使用を開始する方法を説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
uid: spa/angular
ms.openlocfilehash: fee872ff237e14cbe491efed9b320809df4c5654
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646460"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>ASP.NET Core で Angular プロジェクト テンプレートを使用する

更新された Angular プロジェクト テンプレートは、Angular と Angular CLI を使用してリッチなクライアント側ユーザー インターフェイス (UI) を実装する ASP.NET Core アプリのための便利な開始点を提供します。

このテンプレートは、API バックエンドとして機能する ASP.NET Core プロジェクトと、UI として機能する Angular CLI プロジェクトの作成に相当します。 このテンプレートは、1 つのアプリ プロジェクトの中で、両方のプロジェクトの種類をホストするという利便性を提供します。 その結果、アプリ プロジェクトを 1 つのユニットとしてビルドして発行できます。

## <a name="create-a-new-app"></a>新しいアプリを作成する

ASP.NET Core 2.1 がインストールされている場合は、Angular プロジェクト テンプレートをインストールする必要はありません。

コマンド プロンプトで `dotnet new angular` コマンドを使用して、空のディレクトリの中に新しいプロジェクトを作成します。 たとえば、次のコマンドは、*my-new-app* ディレクトリにアプリを作成し、そのディレクトリに切り替えます。

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

Visual Studio または .NET Core CLI からアプリを実行します。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

生成された *.csproj* ファイルを開き、そこから通常の方法でアプリを実行します。

ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。 以降のビルドは非常に高速になります。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

値が `Development` である `ASPNETCORE_Environment` という名前の環境変数があることを確認します。 Windows では、PowerShell 以外のプロンプトで `SET ASPNETCORE_Environment=Development` を実行します。 Linux または macOS では、`export ASPNETCORE_Environment=Development` を実行します。

[dotnet build](/dotnet/core/tools/dotnet-build) を実行して、アプリが正しくビルドされていることを確認します。 ビルド プロセスは、初回の実行で npm の依存関係を復元します。これには数分かかる可能性があります。 以降のビルドは非常に高速になります。

[dotnet run](/dotnet/core/tools/dotnet-run) を実行してアプリを起動します。 次のようなメッセージがログに記録されます。

```console
Now listening on: http://localhost:<port>
```

ブラウザーでこの URL に移動します。

> [!WARNING]
> アプリは、Angular CLI サーバーのインスタンスをバックグラウンドで開始します。 次のようなメッセージがログに記録されます。*NG ライブ開発サーバーが localhost でリッスンしています。他のポート&gt;&lt;ブラウザーを開いて http://localhost:&lt し、その他のポート&gt;/* 。 このメッセージは無視してください。それは、結合された ASP.NET Core と Angular CLI アプリの URLでは**ありません**。

---

プロジェクト テンプレートは、ASP.NET Core アプリと Angular アプリを作成します。 ASP.NET Core アプリは、データ アクセス、承認、およびその他のサーバー側の操作で使用することを目的としています。 *ClientApp* サブディレクトリ内の Angular アプリは、UI に関するすべての 操作で使用することを目的としています。

## <a name="add-pages-images-styles-modules-etc"></a>ページ、画像、スタイル、モジュールなどを追加する

*ClientApp* ディレクトリには、標準的な Angular CLI アプリが含まれます。 詳細については、公式の [Angular ドキュメント](https://angular.io)を参照してください。

このテンプレートによって作成される Angular アプリと Angular CLI 自体によって (`ng new` で) 作成されるアプリにはわずかな違いがありますが、アプリの機能は変わりません。 テンプレートによって作成されるアプリには、[ブートストラップ](https://getbootstrap.com/)ベースのレイアウトと基本的なルーティングの例が含まれます。

## <a name="run-ng-commands"></a>ng コマンドを実行する

コマンド プロンプトで、*ClientApp* サブディレクトリに切り替えます。

```console
cd ClientApp
```

`ng` ツールがグローバルにインストールされている場合は、そのコマンドのすべてを実行できます。 たとえば、`ng lint`、`ng test`、またはその他の [Angular CLI コマンド](https://angular.io/cli) を実行できます。 ただし、`ng serve` を実行する必要はありません。これは、アプリのサーバー側とクライアント側の両方に関わる部分は、ASP.NET Core アプリが処理するためです。 内部的には、それは、開発時に `ng serve` を使用します。

`ng` ツールがインストールされていない場合は、代わりに `npm run ng` を実行します。 たとえば、`npm run ng lint` または `npm run ng test` を実行できます。

## <a name="install-npm-packages"></a>npm パッケージをインストールする

サードパーティ製の npm パッケージをインストールするには、*ClientApp* サブディレクトリでコマンド プロンプトを使用します。 次に例を示します。

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>発行と配置

開発中、アプリは、開発者に便利なように最適化されたモードで実行されます。 たとえば、JavaScript バンドルには、ソース マップが含まれます (デバッグ時に元の TypeScript コードを確認できるようにするためです)。 アプリは、ディスク上の TypeScript、HTML および CSS ファイルの変更を監視し、これらのファイルの変更が発生した場合は、再コンパイルと再読み込みを自動的に実行します。

運用時は、パフォーマンスが最適化されたバージョンのアプリが提供されます。 これは、自動的に実行されるように構成されています。 発行すると、ビルド構成によって、クライアント側コードの縮小された Ahead Of Time (AoT) コンパイルが行われたビルドが生成されます。 開発ビルドとは異なり、運用ビルドは、サーバーへの Node.js のインストールを必要としません (サーバー側のレンダリングを有効にしている場合は除きます (SSR))。

標準的な [ASP.NET Core のホストと展開方法](xref:host-and-deploy/index)を使用できます。

## <a name="run-ng-serve-independently"></a>"ng serve" を個別に実行する

ASP.NET Core アプリが開発モードで起動された場合、プロジェクトは、Angular CLI サーバーの独自のインスタンスをバックグラウンドで開始するように構成されます。 これが便利なのは、別のサーバーを手動で実行する必要がないためです。

この既定の設定には欠点があります。 C# コードを変更し、ASP.NET Core アプリを再起動する必要がある場合、Angular CLI サーバーが毎回再起動します。 再起動には、約 10 秒必要です。 C# コードを何度も編集するが、Angular CLI が再起動するまで待ちたくない場合は、ASP.NET Core プロセスから独立した Angular CLI サーバーを外部で実行します。 次の手順に従います。

1. コマンド プロンプトで、*ClientApp* サブディレクトリに切り替え、Angular CLI 開発サーバーを起動します。

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Angular CLI 開発サーバーを起動するには、`ng serve` ではなく `npm start` を使用して、*package.json* の構成が使用されるようにします。 Angular CLI サーバーに追加パラメーターを渡すには、*package.json* ファイルの関連する `scripts` 行にそれらを追加します。

2. 独自のインスタンスを起動する代わりに外部の Angular CLI インスタンスを使用するように ASP.NET Core アプリケーションを変更します。 *Startup* クラスで、`spa.UseAngularCliServer` の呼び出しを以下に置き換えます。

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

ASP.NET Core アプリの起動時に Angular CLI サーバーが起動されなくなります。 代わりに、手動で開始したインスタンスが使用されます。 これにより、起動と再起動を高速化できます。 Angular CLI がクライアント アプリを毎回リビルドするまで待つ必要はなくなります。

### <a name="pass-data-from-net-code-into-typescript-code"></a>.NET コードから TypeScript コードに データを渡す

SSR 中は、要求ごとのデータを ASP.NET Core アプリから Angular アプリに渡すことができます。 たとえば、cookie 情報やデータベースから読み取ったデータを渡すことができます。 これを行うには、*Startup* クラスを編集します。 `UseSpaPrerendering` のコールバックに、次のような `options.SupplyData` の値を設定します。

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

`SupplyData` コールバックでは、任意の要求ごとの JSON でシリアル可能なデータ (文字列、ブール値、数値など) を渡すことができます。 *main.server.ts* コードは、これを `params.data` として受け取ります。 たとえば、上記のコード サンプルは、ブール値を `params.data.isHttpsRequest` として `createServerRenderer` コールバックに渡しています。 Angular でサポートされている方法で、アプリの他の部分にこれを渡すことができます。 たとえば、*main.server.ts* が、`BASE_URL` 値をコンストラクターが受け取ることを宣言されているコンポーネントにその値を渡す方法を参照してください。

### <a name="drawbacks-of-ssr"></a>SSR の欠点

すべてのアプリが SSR から利益を得られるわけではありません。 主な利益は、見かけ上のパフォーマンスです。 JavaScript バンドルのフェッチまたは解析にしばらく時間がかかる場合でも、低速のネットワーク接続または低速のモバイル デバイスでアプリに到達したユーザーに、初期 UI がすぐに表示されます。 ただし、多くの SPA は、主に高速の社内ネットワーク コンピューターで使用されているため、アプリはほぼ瞬時に表示されます。

同時に、SSR の有効化には重大な欠点があります。 それは、開発プロセスの複雑さを深めます。 ASP.NET Core から呼び出される Node.js 環境の中で、クライアント側とサーバー側 という 2 つの異なる環境でコードを実行する必要があります。 考慮すべき点を次に示します。

* SSR では、運用サーバー上に Node.js をインストールする必要があります。 これは、Azure App Services などの一部のデプロイ シナリオでは自動的に実行されますが、Azure Service Fabric などの他のシナリオではそうではありません。
* `BuildServerSideRenderer` ビルド フラグを有効にすると、*node_modules* ディレクトリが発行されます。 このフォルダーには、20,000 以上のファイルが含まれているため、配置時間が増加します。
* Node.js 環境でコードを実行するために、`window` や `localStorage` などのブラウザー固有の JavaScript API の存在に頼ることはできません。 コード (または参照された一部のサードパーティ製ライブラリ) がこれらの API を使用しようとすると、SSR 中にエラーが発生します。 たとえば、jQuery は多くの場所でブラウザー固有の API を参照するため、それを使用しないでください。 エラーを防ぐには、SSR を有効にしないか、ブラウザー固有の API またはライブラリを使用しないようにする必要があります。 SSR 中に呼び出されることがないように、このような API への呼び出しをチェックしてラップできます。 たとえば、JavaScript または TypeScript コードで、次のようなチェックを使用します。

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>その他の技術情報

* <xref:security/authentication/identity/spa>
