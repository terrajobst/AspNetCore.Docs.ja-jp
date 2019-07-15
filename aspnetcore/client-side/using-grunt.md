---
title: ASP.NET Core で Grunt を使用します。
author: rick-anderson
description: ASP.NET Core で Grunt を使用します。
ms.author: riande
ms.date: 06/18/2019
uid: client-side/using-grunt
ms.openlocfilehash: f3832bd1fe5721fbda114103ac11a8d55312bcb2
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813557"
---
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core で Grunt を使用します。

Grunt は、スクリプトの縮小、TypeScript コンパイル、コード品質"lint"ツール、CSS プリプロセッサ クライアント開発をサポートするために行う必要がある繰り返し、作業だけを自動化する JavaScript タスク ランナーです。 Grunt は、Visual Studio で完全にサポートします。

この例では、開始位置として空の ASP.NET Core プロジェクトを使用して、最初からクライアントのビルド プロセスを自動化する方法について説明します。

完成した例では、ターゲットの配置ディレクトリを消去します、JavaScript ファイルを組み合わせて、コードの品質を確認します、JavaScript ファイルの内容でまとめますおよび web アプリケーションのルートにデプロイします。 次のパッケージを使用します。

* **grunt**:Grunt タスク ランナー パッケージです。

* **grunt-contrib-clean**:ファイルまたはディレクトリを削除するプラグイン。

* **grunt-contrib-jshint**:JavaScript コードの品質をレビューするプラグイン。

* **grunt-contrib-concat**:ファイルを 1 つのファイルに結合するプラグイン。

* **grunt contrib uglify**:サイズを小さく JavaScript の縮小をプラグインします。

* **grunt の contrib-ウォッチ**:ファイルのアクティビティを監視するプラグイン。

## <a name="preparing-the-application"></a>アプリケーションを準備します。

まず、新しい空の web アプリケーションをセットアップし、TypeScript ファイルの例を追加します。 TypeScript ファイルが自動的に既定の Visual Studio の設定を使用して JavaScript にコンパイルし、Grunt を使用して処理する材料になります。

1. Visual Studio で、作成、新しい`ASP.NET Web Application`します。

2. **新しい ASP.NET プロジェクト**ダイアログ ボックスで、ASP.NET Core を選択します。**空**テンプレート、[ok] ボタンをクリックします。

3. ソリューション エクスプ ローラーで、プロジェクトの構造を確認します。 `\src`フォルダーには、空`wwwroot`と`Dependencies`ノード。

    ![空の web ソリューション](using-grunt/_static/grunt-solution-explorer.png)

4. という名前の新しいフォルダーを追加`TypeScript`プロジェクト ディレクトリにします。

5. すべてのファイルを追加する前に Visual Studio オプションになっていることを確認します 'コンパイル保存時に' の TypeScript ファイルをチェックします。 移動します**ツール** > **オプション** > **テキスト エディター** > **Typescript**  > **プロジェクト**:

    ![TypeScript ファイルの自動コンパイルの設定のオプション](using-grunt/_static/typescript-options.png)

6. 右クリックし、`TypeScript`ディレクトリを選択します**追加 > 新しい項目の**コンテキスト メニュー。 選択、 **JavaScript ファイル**項目し、ファイルの名前*Tastes.ts* (注、 \*.ts 拡張機能)。 TypeScript コードの下の行をファイルにコピー (を保存すると、新しい*Tastes.js* JavaScript ソース ファイルが表示されます)。

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. 2 番目のファイルを追加、 **TypeScript**ディレクトリと名前を付けます`Food.ts`します。 ファイルに次のコードをコピーします。

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

次に、grunt、および grunt タスクをダウンロードする NPM を構成します。

1. ソリューション エクスプ ローラーでプロジェクトを右クリックし、選択**追加 > 新しい項目の**コンテキスト メニュー。 選択、 **NPM 構成ファイル**項目で、既定の名前をそのまま使用*package.json*、 をクリックし、**追加**ボタンをクリックします。

2. *Package.json*ファイル内、`devDependencies`中かっこをオブジェクトに、"grunt"を入力します。 選択`grunt`Intellisense を一覧から、Enter キーを押します。 Visual Studio は grunt パッケージ名を引用符で囲むし、コロンを追加します。 コロンの右側に、Intellisense リストの先頭から、パッケージの最新の安定バージョンを選択します。 (キーを押して`Ctrl-Space`Intellisense が表示されない場合)。

    ![grunt の Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM を使用して[セマンティック バージョニング](https://semver.org/)依存関係を整理します。 セマンティック バージョニング、SemVer とも呼ばれる、パッケージを識別する番号付けスキームで\<メジャー >.\<マイナー >。\<パッチ >。 Intellisense は、いくつかの一般的な選択肢のみを表示することによって、セマンティック バージョン管理を簡略化します。 Intellisense の一覧 (上記の例では 0.4.5) の一番上の項目は、パッケージの最新の安定バージョンと見なされます。 キャレット (^) 記号が最新のメジャー バージョンと一致して、一致する最新のマイナー バージョンをティルダ (~)。 参照してください、 [NPM semver バージョン パーサー参照](https://www.npmjs.com/package/semver)SemVer を提供する完全な表現へのガイドとして。

3. 依存関係の詳細を読み込む grunt の追加-contrib -\*のパッケージ*クリーン*、 *jshint*、 *concat*、 *uglify*、および*ウォッチ*次の例で示すようにします。 バージョンは、例では、一致する必要はありません。

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

4. 保存、 *package.json*ファイル。

各パッケージ`devDependencies`項目は、各パッケージに必要なすべてのファイルと共にダウンロードされます。 内のパッケージ ファイルを見つけることができます、 *node_modules*を有効にするディレクトリ、 **すべてのファイル**ボタン**ソリューション エクスプ ローラー**します。

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 内の依存関係を手動で復元する場合は、**ソリューション エクスプ ローラー**を右クリックして`Dependencies\NPM`を選択して、**パッケージの復元**メニュー オプション。

![パッケージを復元します。](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt の構成

Grunt がという名前のマニフェストで構成されている*Gruntfile.js*定義の読み込みし、登録を手動で実行または Visual Studio でのイベントに基づいて自動的に実行するように構成できるタスクです。

1. プロジェクトを右クリックして**追加** > **新しい項目の**します。 選択、 **JavaScript ファイル**項目テンプレート、名を変更して*Gruntfile.js*、 をクリックし、**追加**ボタンをクリックします。

1. 次のコードを追加*Gruntfile.js*します。 `initConfig`関数は、各パッケージのオプションを設定し、残りの部分が読み込みをタスクを登録します。

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. 内で、`initConfig`関数を追加するためのオプション、`clean`タスクの例で示すように*Gruntfile.js*以下。 `clean`タスクは、ディレクトリの文字列の配列を受け取ります。 このタスクからファイルを削除します。 *wwwroot/lib*全体を削除します。 *temp/* ディレクトリ。

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. 以下、`initConfig`関数を呼び出しを追加して`grunt.loadNpmTasks`します。 これにより、タスク実行可能な Visual Studio から。

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. 保存*Gruntfile.js*します。 ファイルの内容は次のスクリーン ショットのようになります。

    ![初期の gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. 右クリック*Gruntfile.js*選択**Task Runner Explorer**コンテキスト メニュー。 **Task Runner Explorer**ウィンドウが開きます。

    ![タスク ランナー エクスプ ローラーのメニュー](using-grunt/_static/task-runner-explorer-menu.png)

1. いることを確認`clean`下に表示される**タスク**で、 **Task Runner Explorer**します。

    ![タスク ランナー エクスプ ローラーのタスク一覧](using-grunt/_static/task-runner-explorer-tasks.png)

1. Clean タスクを右クリックして**実行**コンテキスト メニュー。 コマンド ウィンドウには、タスクの進行状況が表示されます。

    ![タスク ランナー エクスプ ローラーの実行クリーン タスク](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > まだをクリーンアップするファイルまたはディレクトリはありません。 必要に応じて、ソリューション エクスプ ローラーでそれらを手動で作成し、テストとして clean タスクを実行できます。

1. `initConfig`のエントリを追加、関数`concat`次のコードを使用します。

    `src`プロパティ配列を組み合わせる必要がありますの順序で結合します。 ファイルを一覧表示します。 `dest`プロパティが生成される結合されたファイルにパスを割り当てます。

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > `all`上記のコードでプロパティは、ターゲットの名前。 ターゲットは、複数のビルド環境を許可するいくつかの Grunt タスクで使用されます。 IntelliSense を使用して組み込みのターゲットを表示または独自に割り当てることができます。

1. 追加、`jshint`タスクの次のコードを使用します。

    Jshint`code-quality`で見つかったすべての JavaScript ファイルに対してユーティリティを実行、 *temp*ディレクトリ。

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > オプション"-W069"がエラー時に生成される jshint JavaScript はつまり、ドット表記ではなくプロパティを割り当てるの構文を角かっこ`Tastes["Sweet"]`の代わりに`Tastes.Sweet`します。 オプションは、他のプロセスの続行を許可する警告をオフにします。

1. 追加、`uglify`タスクの次のコードを使用します。

    タスクの縮小、 *combined.js* ファイルが、一時ディレクトリにあるし、標準の命名規則に従った wwwroot/lib で結果ファイルを作成 *\<ファイル名\>min.js* 。

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. 呼び出しの下`grunt.loadNpmTasks`を読み込む`grunt-contrib-clean`uglify の次のコードを使用して、jshint、concat の同じ呼び出しが含まれています。

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. 保存*Gruntfile.js*します。 ファイルの内容は次の例のようになります。

    ![grunt の完全なファイルの例](using-grunt/_static/gruntfile-js-complete.png)

1. 注意、 **Task Runner Explorer**タスクの一覧が含まれます`clean`、 `concat`、`jshint`と`uglify`タスク。 順序で各タスクを実行してで結果を観察**ソリューション エクスプ ローラー**します。 各タスクは、エラーなく実行する必要があります。

    ![タスク ランナー エクスプ ローラーの各タスクの実行](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat タスクを作成、新しい*combined.js*ファイルし、temp ディレクトリに配置します。 `jshint`単純にタスクを実行して出力を生成しません。 `uglify`タスクを作成する新しい*combined.min.js*ファイルおよび配置に*wwwroot/lib*します。 完了時に次のスクリーン ショットのようなもの、ソリューションはなります。

    ![ソリューション エクスプ ローラーのすべてのタスクします。](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 各パッケージのオプションの詳細については、次を参照してください。 [ https://www.npmjs.com/ ](https://www.npmjs.com/)と参照の検索ボックスに、メイン ページで、パッケージ名。 たとえば、すべてのパラメーターを説明するドキュメント リンクを取得する grunt contrib クリーン パッケージを検索できます。

### <a name="all-together-now"></a>まとめ

Grunt を使用して、`registerTask()`メソッドを特定のシーケンスで一連のタスクを実行します。 たとえば、クリーンの順序で上記の手順 -> の例を実行する concat]-> [jshint]-> [uglify、モジュールに次のコードを追加します。 InitConfig 以外、loadNpmTasks() 呼び出しと同じレベルには、コードを追加する必要があります。

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

エイリアス タスク Task Runner Explorer に新しいタスクが表示されます。 右クリックし、その他のタスクと同様に実行できます。 `all`タスクが実行`clean`、 `concat`、`jshint`と`uglify`の順序で。

![エイリアスの grunt タスク](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>変更の監視

A`watch`タスク保持ファイルとディレクトリを監視します。 ウォッチは変更を検出した場合、タスクを自動的にトリガーされます。 InitConfig への変更を監視するに次のコードを追加\*TypeScript ディレクトリ内の .js ファイル。 JavaScript ファイルが変更された場合`watch`実行は、`all`タスク。

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

呼び出しを追加して`loadNpmTasks()`を表示する、`watch`タスク ランナー エクスプ ローラーで作業します。

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Task Runner Explorer で監視タスクを右クリックし、コンテキスト メニューから実行を選択します。 「待機しています...」が実行中の監視タスクを表示するコマンド ウィンドウに表示されます。メッセージ。 TypeScript ファイルのいずれかを開き、スペースを追加し、ファイルを保存します。 監視タスクのトリガーを順番に実行するその他のタスクをトリガーします。 次のスクリーン ショットは、サンプルの実行を示しています。

![タスク出力を実行しています。](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio イベントへのバインド

タスクをバインドするには Visual Studio で作業するたびに、タスクを手動で開始する場合を除き、 **Before Build**、**後にビルド**、**クリーン**、および**プロジェクトのオープン**イベント。

バインドしてみます`watch`が Visual Studio を開くたびに実行されるようにします。 タスク ランナー エクスプ ローラーでは、監視タスクを右クリックして**バインド > プロジェクトを開く**コンテキスト メニュー。

![プロジェクトを開くタスクにバインドします。](using-grunt/_static/bindings-project-open.png)

アンロードし、プロジェクトの再読み込みします。 プロジェクトが再度読み込まれると、自動的に実行する監視タスクが開始されます。

## <a name="summary"></a>まとめ

Grunt は、ほとんどのクライアント ビルド タスクの自動化に使用できる強力なタスク ランナーです。 Grunt は、そのパッケージ、およびツールの Visual Studio との統合機能を提供する NPM を活用します。 Visual Studio のタスク ランナー エクスプ ローラーでは、構成ファイルへの変更を検出し、タスクの実行、実行中のタスクを表示、および Visual Studio のイベントにタスクを関連付けるに便利なインターフェイスを提供します。
