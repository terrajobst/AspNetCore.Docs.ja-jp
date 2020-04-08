---
title: ASP.NET Core での Grunt の使用
author: rick-anderson
description: ASP.NET Core での Grunt の使用
ms.author: riande
ms.date: 12/05/2019
uid: client-side/using-grunt
ms.openlocfilehash: e516b85da7e94d0c93be642086fede0a11fea3c2
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646424"
---
# <a name="use-grunt-in-aspnet-core"></a>ASP.NET Core での Grunt の使用

Grunt は、スクリプトの縮小、TypeScript のコンパイル、コード品質の "lint" ツール、CSS プリプロセッサなど、クライアント開発をサポートするために必要なほとんどの反復作業を自動化する JavaScript タスク ランナーです。 Grunt は、Visual Studio で完全にサポートされています。

この例では、空の ASP.NET Core プロジェクトを開始点として使用して、クライアントのビルド プロセスをゼロから自動化する方法を示します。

完成した例では、ターゲットの展開ディレクトリのクリーンアップ、JavaScript ファイルの結合、コード品質の確認、JavaScript ファイル コンテンツの圧縮、および Web アプリケーションのルートへの展開を行います。 次のパッケージを使用します。

* **grunt**:Grunt タスク ランナー パッケージ。

* **grunt-contrib-clean**:ファイルまたはディレクトリを削除するプラグイン。

* **grunt-contrib-jshint**:JavaScript コードの品質を確認するプラグイン。

* **grunt-contrib-concat**:ファイルを 1 つのファイルに結合するプラグイン。

* **grunt-contrib-uglify**:サイズを小さくするために JavaScript を縮小するプラグイン。

* **grunt-contrib-watch**:ファイル アクティビティを監視するプラグイン。

## <a name="preparing-the-application"></a>アプリケーションの準備

最初に、空の新しい Web アプリケーションを設定し、TypeScript サンプル ファイルを追加します。 TypeScript ファイルは、既定の Visual Studio 設定を使用して JavaScript に自動的にコンパイルされ、Grunt を使用して処理する未加工の素材になります。

1. Visual Studio で、新しい `ASP.NET Web Application` を作成します。

2. **[新しい ASP.NET プロジェクト]** ダイアログで、ASP.NET Core の **[空]** テンプレートを選択し、[OK] ボタンをクリックします。

3. ソリューション エクスプローラーで、プロジェクトの構造を確認します。 `\src` フォルダーには、空の `wwwroot` ノードと `Dependencies` ノードが含まれます。

    ![空の Web ソリューション](using-grunt/_static/grunt-solution-explorer.png)

4. `TypeScript` という名前の新しいフォルダーをプロジェクト ディレクトリに追加します。

5. ファイルを追加する前に、Visual Studio で TypeScript ファイルに対して [保存時にコンパイル] オプションがオンになっていることを確認してください。 **[ツール]**  >  **[オプション]**  >  **[テキスト エディター]**  >  **[TypeScript]**  >  **[プロジェクト]** に移動します。

    ![TypeScript ファイルの自動コンパイルを設定するオプション](using-grunt/_static/typescript-options.png)

6. `TypeScript` ディレクトリを右クリックし、コンテキスト メニューで **[追加] > [新しい項目]** を選択します。 **[JavaScript ファイル]** 項目を選択し、ファイルに *Tastes.ts* という名前を指定します (\*.ts 拡張子に注意してください)。 次の TypeScript コード行をファイルにコピーします (保存すると、新しい *Tastes.js* ファイルが JavaScript ソースと共に表示されます)。

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. 2 つ目のファイルを **TypeScript** ディレクトリに追加し、`Food.ts` という名前を指定します。 次のコードをファイルにコピーします。

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

次に、grunt と grunt タスクをダウンロードするように NPM を構成します。

1. ソリューション エクスプローラーでプロジェクトを右クリックし、コンテキスト メニューで **[追加] > [新しい項目]** を選択します。 **[NPM 構成ファイル]** 項目を選択し、既定の名前 *package.json* をそのまま使用して、 **[追加]** ボタンをクリックします。

2. *package.json* ファイルで、`devDependencies` オブジェクトの中かっこの内側に「grunt」と入力します。 Intellisense の一覧から [`grunt`] を選択し、Enter キーを押します。 Visual Studio によって grunt パッケージ名が引用され、コロンが追加されます。 コロンの右側で、Intellisense の一覧の一番上にある最新の安定バージョンのパッケージを選択します (Intellisense が表示されない場合は `Ctrl-Space` キーを押します)。

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM では、依存関係を整理するために[セマンティック バージョニング](https://semver.org/)が使用されます。 セマンティック バージョニングは SemVer とも呼ばれ、\<メジャー>.\<マイナー>.\<パッチ> の番号設定によってパッケージを識別するものです。 Intellisense では、いくつかの共通の選択肢だけを表示することで、セマンティック バージョニングが簡略化されています。 Intellisense の一覧の一番上にある項目 (上の例では 0.4.5) は、最新の安定バージョンのパッケージと見なされます。 キャレット (^) 記号は最新のメジャー バージョンと一致し、チルダ (~) は最新のマイナー バージョンと一致します。 SemVer で提供される完全な表現のガイドとして、[NPM SemVer バージョン パーサーのリファレンス](https://www.npmjs.com/package/semver)を参照してください。

3. 次の例に示すように、*clean*、*jshint*、*concat*、*uglify*、および *watch* に対して grunt-contrib-\* パッケージを読み込むよう、さらに依存関係を追加します。 バージョンは例と一致している必要はありません。

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

4. *package.json* ファイルを保存します。

各 `devDependencies` 項目のパッケージが、各パッケージに必要なすべてのファイルと共にダウンロードされます。 **ソリューション エクスプローラー**で **[すべてのファイルを表示]** ボタンを有効にすると、*node_modules* ディレクトリ内のパッケージ ファイルを確認できます。

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 必要に応じて、**ソリューション エクスプローラー**で `Dependencies\NPM` を右クリックし、 **[パッケージの復元]** メニュー オプションを選択することで、依存関係を手動で復元できます。

![パッケージの復元](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Grunt の構成

Grunt は、*Gruntfile.js* という名前のマニフェストを使用して構成されます。このマニフェストでは、手動で実行したり、Visual Studio のイベントに基づいて自動的に実行されるように構成したりできるタスクの定義、読み込み、および登録が行われます。

1. プロジェクトを右クリックし、 **[追加]**  >  **[新しい項目]** を選択します。 **[JavaScript ファイル]** 項目テンプレートを選択し、名前を *Gruntfile.js* に変更して、 **[追加]** ボタンをクリックします。

1. 次のコードを *Gruntfile.js* に追加します。 `initConfig` 関数によって各パッケージのオプションが設定され、モジュールの残りの部分によってタスクの読み込みと登録が行われます。

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. 次の *Gruntfile.js* の例に示すように、`initConfig` 関数の内部に `clean` タスクのオプションを追加します。 `clean` タスクで、ディレクトリ文字列の配列が受け入れられます。 このタスクにより、*wwwroot/lib* からファイルが削除され、 */temp* ディレクトリ全体が削除されます。

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. `initConfig` 関数の下に、`grunt.loadNpmTasks` の呼び出しを追加します。 これにより、タスクが Visual Studio から実行できるようになります。

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. *Gruntfile.js* を保存します。 ファイルは次のスクリーンショットのようになります。

    ![初期の Gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. *[Gruntfile.js]* を右クリックし、コンテキスト メニューで **[タスク ランナー エクスプローラー]** を選択します。 **[タスク ランナー エクスプローラー]** ウィンドウが開きます。

    ![[タスク ランナー エクスプローラー] メニュー](using-grunt/_static/task-runner-explorer-menu.png)

1. **タスク ランナー エクスプローラー**の **[タスク]** の下に `clean` が表示されていることを確認します。

    ![タスク ランナー エクスプローラーの [タスク] 一覧](using-grunt/_static/task-runner-explorer-tasks.png)

1. [clean] タスクを右クリックし、コンテキスト メニューで **[実行]** を選択します。 コマンド ウィンドウに、タスクの進行状況が表示されます。

    ![タスク ランナー エクスプローラーでの clean タスクの実行](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > クリーンアップするファイルまたはディレクトリはまだありません。 必要に応じて、ソリューション エクスプローラーでこれらを手動で作成し、clean タスクをテストとして実行できます。

1. `initConfig` 関数で、次のコードを使用して `concat` のエントリを追加します。

    `src` プロパティの配列には、結合するファイルが、結合する順序でリストされます。 `dest` プロパティにより、生成された結合ファイルへのパスが割り当てられます。

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > 上記のコードの `all` プロパティは、ターゲットの名前です。 ターゲットは、複数のビルド環境が許可されるいくつかの Grunt タスクで使用されます。 IntelliSense を使用して組み込みのターゲットを表示するか、独自のターゲットを割り当てることができます。

1. 次のコードを使用して、`jshint` タスクを追加します。

    jshint `code-quality` ユーティリティは、*temp* ディレクトリにあるすべての JavaScript ファイルに対して実行されます。

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > オプション "-W069" は、JavaScript でドット表記の代わりに角かっこ構文 (たとえば、`Tastes.Sweet` の代わりに `Tastes["Sweet"]`) を使用してプロパティが割り当てられると、jshint によって生成されるエラーです。 このオプションによって警告がオフになり、残りのプロセスを続行できるようになります。

1. 次のコードを使用して、`uglify` タスクを追加します。

    このタスクでは、temp ディレクトリにある *combined.js* ファイルが縮小され、標準の名前付け規則 *\<ファイル名\>.min.js* に従って、結果ファイルが wwwroot/lib に作成されます。

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. `grunt-contrib-clean` を読み込む `grunt.loadNpmTasks` の呼び出しの下で、次のコードを使用して、jshint、concat、および uglify に対して同じ呼び出しを含めます。

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. *Gruntfile.js* を保存します。 ファイルは次の例のようになります。

    ![完全な grunt ファイルの例](using-grunt/_static/gruntfile-js-complete.png)

1. **タスク ランナー エクスプローラー**の [タスク] 一覧に、`clean`、`concat`、`jshint`、および `uglify` の各タスクが含まれることに注意してください。 各タスクを順番に実行し、**ソリューション エクスプローラー**の結果を確認します。 各タスクはエラーなしに実行されるはずです。

    ![タスク ランナー エクスプローラーでの各タスクの実行](using-grunt/_static/task-runner-explorer-run-each-task.png)

    concat タスクでは、新しい *combined.js* ファイルが作成され、temp ディレクトリに格納されます。 `jshint` タスクは単に実行されるだけで、出力は生成されません。 `uglify` タスクでは、新しい *combined.min.js* ファイルが作成され、*wwwroot/lib* に格納されます。 完了すると、ソリューションは次のスクリーンショットのようになります。

    ![すべてのタスク後のソリューション エクスプローラー](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 各パッケージのオプションの詳細については、[https://www.npmjs.com/](https://www.npmjs.com/) にアクセスし、メイン ページの検索ボックスでパッケージ名を検索してください。 たとえば、grunt-contrib-clean パッケージを参照して、すべてのパラメーターを説明するドキュメント リンクを取得できます。

### <a name="all-together-now"></a>すべてをまとめる

Grunt `registerTask()` メソッドを使用して、一連のタスクを特定のシーケンスで実行します。 たとえば、上記の手順の例を clean -> concat -> jshint -> uglify の順に実行するには、次のコードをモジュールに追加します。 このコードは、initConfig の外部で loadNpmTasks() 呼び出しと同じレベルに追加する必要があります。

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

新しいタスクが、タスク ランナー エクスプローラーの [エイリアス タスク] の下に表示されます。 このタスクは、他のタスクと同様に右クリックして実行できます。 `all` タスクでは、`clean`、`concat`、`jshint`、`uglify` が順番に実行されます。

![エイリアス grunt タスク](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>変更の監視

`watch` タスクでは、ファイルとディレクトリが監視されます。 watch によって変更が検出されると、タスクが自動的にトリガーされます。 次のコードを initConfig に追加して、TypeScript ディレクトリ内の \*.js ファイルに対する変更を監視します。 JavaScript ファイルが変更さると、`watch` によって `all` タスクが実行されます。

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

タスク ランナー エクスプローラーで `watch` タスクを表示するよう、`loadNpmTasks()` の呼び出しを追加します。

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

タスク ランナー エクスプローラーで [watch] タスクを右クリックし、コンテキスト メニューで [実行] を選択します。 実行中の watch タスクを表示するコマンド ウィンドウに、"Waiting…" メッセージが表示されます。 TypeScript ファイルの 1 つを開き、スペースを追加して、ファイルを保存します。 これにより、watch タスクがトリガーされ、他のタスクがトリガーされて順番に実行されます。 次のスクリーンショットは、サンプルの実行を示しています。

![実行中のタスクの出力](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Visual Studio イベントへのバインド

Visual Studio で作業するたびに手動でタスクを開始する場合を除き、 **[ビルド前]** 、 **[ビルド後]** 、 **[クリーン]** 、および **[プロジェクトを開く]** の各イベントにタスクをバインドします。

`watch` をバインドして、Visual Studio が開くたびに実行されるようにします。 タスク ランナー エクスプローラーで [watch] タスクを右クリックし、コンテキスト メニューで **[バインド]**  >  **[プロジェクトを開く]** を選択します。

![[プロジェクトを開く] にタスクをバインドする](using-grunt/_static/bindings-project-open.png)

プロジェクトをアンロードして再読み込みします。 プロジェクトが再度読み込まれると、watch タスクの実行が自動的に開始されます。

## <a name="summary"></a>まとめ

Grunt は、クライアントによってビルドされたほとんどのタスクを自動化するために使用できる強力なタスク ランナーです。 Grunt により、NPM を利用してパッケージが配信され、ツールが Visual Studio と統合されます。 Visual Studio のタスク ランナー エクスプローラーでは、構成ファイルへの変更が検出され、タスクの実行、実行中のタスクの表示、および Visual Studio イベントへのタスクのバインドを行うための便利なインターフェイスが提供されます。
