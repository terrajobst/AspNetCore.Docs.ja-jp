---
title: ASP.NET Core での Grunt の使用
author: rick-anderson
description: ASP.NET Core での Grunt の使用
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879790"
---
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core での Grunt の使用

Grunt は、スクリプトの縮小、TypeScript のコンパイル、コード品質の "糸くず" ツール、CSS プリプロセッサ、およびクライアント開発をサポートするために必要な反復的な作業を自動化する JavaScript タスクランナーです。 Grunt は、Visual Studio で完全にサポートされています。

この例では、空の ASP.NET Core プロジェクトを開始点として使用して、クライアントのビルドプロセスをゼロから自動化する方法を示します。

完成した例では、ターゲットの配置ディレクトリを消去し、JavaScript ファイルを結合し、コードの品質を確認し、JavaScript ファイルの内容をでは、して、web アプリケーションのルートにデプロイします。 次のパッケージを使用します。

* **grunt**: grunt task ランナーパッケージ。

* **grunt-contrib**: ファイルまたはディレクトリを削除するプラグインです。

* **grunt-contrib-jshint**: JavaScript コードの品質をレビューするプラグイン。

* **grunt-contrib**: 1 つのファイルにファイルを結合するプラグインです。

* **grunt-contrib-uglify**: サイズを小さくするために JavaScript を縮小するプラグイン。

* **grunt-contrib**: ファイルアクティビティを監視するプラグイン。

## <a name="preparing-the-application"></a>アプリケーションの準備

最初に、空の新しい web アプリケーションをセットアップし、TypeScript サンプルファイルを追加します。 TypeScript ファイルは、既定の Visual Studio 設定を使用して JavaScript に自動的にコンパイルされ、Grunt を使用して処理する未加工の素材になります。

1. Visual Studio で、新しい `ASP.NET Web Application`を作成します。

2. **[New ASP.NET Project]** ダイアログで、 **[Empty]** テンプレートを ASP.NET Core 選択し、[OK] をクリックします。

3. ソリューションエクスプローラーで、プロジェクトの構造を確認します。 `\src` フォルダーには、空の `wwwroot` と `Dependencies` ノードが含まれています。

    ![空の web ソリューション](using-grunt/_static/grunt-solution-explorer.png)

4. `TypeScript` という名前の新しいフォルダーをプロジェクトディレクトリに追加します。

5. ファイルを追加する前に、Visual Studio で [TypeScript ファイル用にコンパイルする] オプションがオンになっていることを確認してください。 **ツール** > **オプション** > **テキストエディター** > **Typescript** > **プロジェクト** に移動します。

    ![TypeScript ファイルの自動コンパイルを設定するオプション](using-grunt/_static/typescript-options.png)

6. `TypeScript` ディレクトリを右クリックし、コンテキストメニューの **[新しい項目の追加 >]** をクリックします。 **[JavaScript ファイル]** 項目を選択し、ファイルに「 *ts* 」という名前を指定します (\*に注意してください)。 次の TypeScript コード行をファイルにコピーします (保存すると、新しいユーザーの *.js*ファイルが JavaScript ソースと共に表示されます)。

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. **TypeScript**ディレクトリに2番目のファイルを追加し、`Food.ts`という名前を指定します。 次のコードをファイルにコピーします。

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a>NPM の構成

次に、NPM を構成して grunt と grunt をダウンロードします。

1. ソリューションエクスプローラーで、プロジェクトを右クリックし、コンテキストメニューの **[新しい項目の追加 >]** をクリックします。 **[Npm 構成ファイル]** 項目を選択し、既定の名前の [ *package. json*] をそのまま使用して、 **[追加]** ボタンをクリックします。

2. パッケージの*json*ファイルの `devDependencies` 中かっこの中に「grunt」と入力します。 Intellisense の一覧から [`grunt`] を選択し、Enter キーを押します。 Visual Studio は、grunt パッケージ名を引用し、コロンを追加します。 コロンの右側で、Intellisense の一覧の一番上にある最新の安定したバージョンのパッケージを選択します (Intellisense が表示されない場合は `Ctrl-Space` キーを押します)。

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM は、[セマンティックバージョン管理](https://semver.org/)を使用して依存関係を整理します。 セマンティックバージョン管理は、SemVer とも呼ばれ、\<メジャー > の番号付けスキームを持つパッケージを識別します。マイナー > を\<します。修正プログラムの > を\<します。 Intellisense は、いくつかの一般的な選択肢だけを表示することで、セマンティックバージョン管理を簡略化します。 Intellisense リストの一番上の項目 (上の例では 0.4.5) は、パッケージの最新の安定したバージョンと見なされます。 キャレット (^) 記号は最新のメジャーバージョンと一致し、チルダ (~) は最新のマイナーバージョンと一致します。 SemVer が提供する完全な表現性のガイドとして、 [Npm の semver バージョンパーサーのリファレンス](https://www.npmjs.com/package/semver)を参照してください。

3. 次の例に示すように、 *clean*、 *jshint*、 *concat*、 *uglify*、 *watch*の場合は、load grunt-contrib-\* パッケージに依存関係を追加します。 これらのバージョンは、例と一致している必要はありません。

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. パッケージの*json*ファイルを保存します。

各 `devDependencies` 項目のパッケージは、各パッケージに必要なすべてのファイルと共にダウンロードされます。 **ソリューションエクスプローラー**の **[すべてのファイルを表示]** ボタンを有効にすることで、 *node_modules*ディレクトリにパッケージファイルを見つけることができます。

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 必要に応じて、`Dependencies\NPM` を右クリックして **[パッケージの復元]** メニューオプションを選択することにより、**ソリューションエクスプローラー**の依存関係を手動で復元できます。

![パッケージの復元](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt の構成

Grunt は、 *Gruntfile*という名前のマニフェストを使用して構成されます。このマニフェストでは、手動で実行するか、Visual Studio のイベントに基づいて自動的に実行するように構成されたタスクを、読み込んで登録することができます。

1. プロジェクトを右クリックし、[**新しい項目**の**追加** > ] を選択します。 **JavaScript ファイル**項目テンプレートを選択し、名前を*Gruntfile*に変更して、 **[追加]** ボタンをクリックします。

1. *Gruntfile*に次のコードを追加します。 `initConfig` 関数は、各パッケージのオプションを設定し、モジュールの残りの部分でタスクを読み込んで登録します。

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. `initConfig` 関数内で、次の例の*Gruntfile*に示すように、`clean` タスクのオプションを追加します。 `clean` タスクは、ディレクトリ文字列の配列を受け入れます。 このタスクは、 *wwwroot/lib*からファイルを削除し、 */temp*ディレクトリ全体を削除します。

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. `initConfig` 関数の下に、`grunt.loadNpmTasks`への呼び出しを追加します。 これにより、タスクが Visual Studio から実行できるようになります。

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. *Gruntfile*を保存します。 ファイルは次のスクリーンショットのようになります。

    ![最初の gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. *Gruntfile*を右クリックし、コンテキストメニューから **[タスクランナーエクスプローラー]** を選択します。 **[タスクランナーエクスプローラー]** ウィンドウが開きます。

    ![タスクランナーエクスプローラーメニュー](using-grunt/_static/task-runner-explorer-menu.png)

1. **タスクランナーエクスプローラー**の **[タスク]** の下に `clean` が表示されていることを確認します。

    ![タスクランナーエクスプローラーのタスク一覧](using-grunt/_static/task-runner-explorer-tasks.png)

1. クリーンタスクを右クリックし、コンテキストメニューから **[実行]** を選択します。 コマンドウィンドウには、タスクの進行状況が表示されます。

    ![タスクランナーエクスプローラーのクリーンタスクの実行](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > クリーンアップするファイルまたはディレクトリがありません。 必要に応じて、ソリューションエクスプローラーで手動で作成してから、クリーンタスクをテストとして実行できます。

1. `initConfig` 関数で、次のコードを使用して `concat` のエントリを追加します。

    `src` プロパティの配列は、結合するファイルを、結合する順序で一覧表示します。 `dest` プロパティは、生成された結合ファイルへのパスを割り当てます。

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > 上記のコードの `all` プロパティは、ターゲットの名前です。 いくつかの Grunt タスクでターゲットを使用して、複数のビルド環境を許可します。 IntelliSense を使用して組み込みのターゲットを表示するか、独自のターゲットを割り当てることができます。

1. 次のコードを使用して、`jshint` タスクを追加します。

    Jshint `code-quality` ユーティリティは、 *temp*ディレクトリにあるすべての JavaScript ファイルに対して実行されます。

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > オプション "-W069" は、JavaScript が角かっこ構文を使用して `Tastes["Sweet"]`、`Tastes.Sweet`ではなく、ドット表記ではなくプロパティを割り当てるときに、jshint によって生成されるエラーです。 オプションは警告をオフにして、残りのプロセスを続行できるようにします。

1. 次のコードを使用して、`uglify` タスクを追加します。

    タスクの縮小、 *combined.js* ファイルが、一時ディレクトリにあるし、標準の命名規則に従った wwwroot/lib で結果ファイルを作成 *\<ファイル名\>min.js* 。

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. `grunt-contrib-clean`を読み込む `grunt.loadNpmTasks` の呼び出しの下で、次のコードを使用して、jshint、concat、および uglify に対して同じ呼び出しを含めます。

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. *Gruntfile*を保存します。 ファイルは次の例のようになります。

    ![完全な grunt ファイルの例](using-grunt/_static/gruntfile-js-complete.png)

1. **タスクランナーエクスプローラー**のタスク一覧に、`clean`、`concat`、`jshint`、および `uglify` のタスクが含まれていることに注意してください。 各タスクを順番に実行し、**ソリューションエクスプローラー**の結果を確認します。 各タスクは、エラーなしで実行する必要があります。

    ![タスクランナーエクスプローラーで各タスクを実行する](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat タスクは、新しい*組み合わせの .js*ファイルを作成して temp ディレクトリに配置します。 `jshint` タスクは単に実行するだけで、出力は生成されません。 `uglify` タスクでは、新しい*組み合わせの .js*ファイルを作成し、 *wwwroot/lib*に配置します。 完了すると、ソリューションは次のスクリーンショットのようになります。

    ![すべてのタスクの後のソリューションエクスプローラー](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 各パッケージのオプションの詳細については、 [https://www.npmjs.com/](https://www.npmjs.com/)にアクセスし、メインページの [検索] ボックスでパッケージ名を参照してください。 たとえば、grunt-contrib パッケージを参照して、すべてのパラメーターを説明するドキュメントリンクを取得することができます。

### <a name="all-together-now"></a>すべてをまとめる

Grunt `registerTask()` メソッドを使用して、一連のタスクを特定のシーケンスで実行します。 たとえば、上記の手順の例を > concat-> jshint-> uglify の順序で実行するには、モジュールに以下のコードを追加します。 このコードは、initConfig 以外の loadNpmTasks () 呼び出しと同じレベルに追加する必要があります。

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

新しいタスクは、タスクランナーエクスプローラーの [エイリアスタスク] の下に表示されます。 他のタスクと同様に、右クリックして実行できます。 `all` タスクは、`clean`、`concat`、`jshint` および `uglify`を順番に実行します。

![エイリアス grunt タスク](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>変更の監視

`watch` タスクは、ファイルとディレクトリを監視します。 ウォッチは、変更が検出された場合に自動的にタスクをトリガーします。 次のコードを initConfig に追加して、TypeScript ディレクトリ内の \*.js ファイルに対する変更を監視します。 JavaScript ファイルが変更された場合、`watch` は `all` タスクを実行します。

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

タスクランナーエクスプローラーで `watch` タスクを表示するには `loadNpmTasks()` の呼び出しを追加します。

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

タスクランナーエクスプローラーで [ウォッチ] タスクを右クリックし、コンテキストメニューから [実行] を選択します。 実行中のウォッチタスクを表示するコマンドウィンドウには、"待機中..." と表示されます。メッセージ。 TypeScript ファイルの1つを開き、スペースを追加して、ファイルを保存します。 これにより、ウォッチタスクがトリガーされ、他のタスクが順番に実行されます。 次のスクリーンショットは、サンプルの実行を示しています。

![実行中のタスクの出力](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio イベントへのバインド

Visual Studio で作業するたびに手動でタスクを開始する場合を除き、**ビルド**、ビルド、**クリーン**、および**プロジェクトオープン**イベントの**後**にタスクをバインドします。

`watch` バインドして、Visual Studio が開くたびに実行されるようにします。 タスクランナーエクスプローラーで、ウォッチタスクを右クリックし、コンテキストメニューから [**バインド** > **プロジェクトを開く**] を選択します。

![開いているプロジェクトにタスクをバインドする](using-grunt/_static/bindings-project-open.png)

プロジェクトをアンロードして再読み込みします。 プロジェクトが再度読み込まれると、ウォッチタスクが自動的に実行を開始します。

## <a name="summary"></a>要約

Grunt は、ほとんどのクライアントビルドタスクを自動化するために使用できる強力なタスクランナーです。 Grunt は、NPM を利用してパッケージを配信し、ツールを Visual Studio と統合します。 Visual Studio のタスクランナーエクスプローラーは、構成ファイルへの変更を検出し、タスクの実行、実行中のタスクの表示、および Visual Studio イベントへのタスクのバインドを行うための便利なインターフェイスを提供します。
