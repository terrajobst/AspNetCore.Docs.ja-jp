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
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="07062-103">ASP.NET Core で Grunt を使用します。</span><span class="sxs-lookup"><span data-stu-id="07062-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="07062-104">Grunt は、スクリプトの縮小、TypeScript コンパイル、コード品質"lint"ツール、CSS プリプロセッサ クライアント開発をサポートするために行う必要がある繰り返し、作業だけを自動化する JavaScript タスク ランナーです。</span><span class="sxs-lookup"><span data-stu-id="07062-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="07062-105">Grunt は、Visual Studio で完全にサポートします。</span><span class="sxs-lookup"><span data-stu-id="07062-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="07062-106">この例では、開始位置として空の ASP.NET Core プロジェクトを使用して、最初からクライアントのビルド プロセスを自動化する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="07062-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="07062-107">完成した例では、ターゲットの配置ディレクトリを消去します、JavaScript ファイルを組み合わせて、コードの品質を確認します、JavaScript ファイルの内容でまとめますおよび web アプリケーションのルートにデプロイします。</span><span class="sxs-lookup"><span data-stu-id="07062-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="07062-108">次のパッケージを使用します。</span><span class="sxs-lookup"><span data-stu-id="07062-108">We will use the following packages:</span></span>

* <span data-ttu-id="07062-109">**grunt**:Grunt タスク ランナー パッケージです。</span><span class="sxs-lookup"><span data-stu-id="07062-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="07062-110">**grunt-contrib-clean**:ファイルまたはディレクトリを削除するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="07062-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="07062-111">**grunt-contrib-jshint**:JavaScript コードの品質をレビューするプラグイン。</span><span class="sxs-lookup"><span data-stu-id="07062-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="07062-112">**grunt-contrib-concat**:ファイルを 1 つのファイルに結合するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="07062-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="07062-113">**grunt contrib uglify**:サイズを小さく JavaScript の縮小をプラグインします。</span><span class="sxs-lookup"><span data-stu-id="07062-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="07062-114">**grunt の contrib-ウォッチ**:ファイルのアクティビティを監視するプラグイン。</span><span class="sxs-lookup"><span data-stu-id="07062-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="07062-115">アプリケーションを準備します。</span><span class="sxs-lookup"><span data-stu-id="07062-115">Preparing the application</span></span>

<span data-ttu-id="07062-116">まず、新しい空の web アプリケーションをセットアップし、TypeScript ファイルの例を追加します。</span><span class="sxs-lookup"><span data-stu-id="07062-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="07062-117">TypeScript ファイルが自動的に既定の Visual Studio の設定を使用して JavaScript にコンパイルし、Grunt を使用して処理する材料になります。</span><span class="sxs-lookup"><span data-stu-id="07062-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="07062-118">Visual Studio で、作成、新しい`ASP.NET Web Application`します。</span><span class="sxs-lookup"><span data-stu-id="07062-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="07062-119">**新しい ASP.NET プロジェクト**ダイアログ ボックスで、ASP.NET Core を選択します。**空**テンプレート、[ok] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="07062-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="07062-120">ソリューション エクスプ ローラーで、プロジェクトの構造を確認します。</span><span class="sxs-lookup"><span data-stu-id="07062-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="07062-121">`\src`フォルダーには、空`wwwroot`と`Dependencies`ノード。</span><span class="sxs-lookup"><span data-stu-id="07062-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![空の web ソリューション](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="07062-123">という名前の新しいフォルダーを追加`TypeScript`プロジェクト ディレクトリにします。</span><span class="sxs-lookup"><span data-stu-id="07062-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="07062-124">すべてのファイルを追加する前に Visual Studio オプションになっていることを確認します 'コンパイル保存時に' の TypeScript ファイルをチェックします。</span><span class="sxs-lookup"><span data-stu-id="07062-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="07062-125">移動します**ツール** > **オプション** > **テキスト エディター** > **Typescript**  > **プロジェクト**:</span><span class="sxs-lookup"><span data-stu-id="07062-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![TypeScript ファイルの自動コンパイルの設定のオプション](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="07062-127">右クリックし、`TypeScript`ディレクトリを選択します**追加 > 新しい項目の**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="07062-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="07062-128">選択、 **JavaScript ファイル**項目し、ファイルの名前*Tastes.ts* (注、 \*.ts 拡張機能)。</span><span class="sxs-lookup"><span data-stu-id="07062-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="07062-129">TypeScript コードの下の行をファイルにコピー (を保存すると、新しい*Tastes.js* JavaScript ソース ファイルが表示されます)。</span><span class="sxs-lookup"><span data-stu-id="07062-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="07062-130">2 番目のファイルを追加、 **TypeScript**ディレクトリと名前を付けます`Food.ts`します。</span><span class="sxs-lookup"><span data-stu-id="07062-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="07062-131">ファイルに次のコードをコピーします。</span><span class="sxs-lookup"><span data-stu-id="07062-131">Copy the code below into the file.</span></span>

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

## <a name="configuring-npm"></a><span data-ttu-id="07062-132">NPM の構成</span><span class="sxs-lookup"><span data-stu-id="07062-132">Configuring NPM</span></span>

<span data-ttu-id="07062-133">次に、grunt、および grunt タスクをダウンロードする NPM を構成します。</span><span class="sxs-lookup"><span data-stu-id="07062-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="07062-134">ソリューション エクスプ ローラーでプロジェクトを右クリックし、選択**追加 > 新しい項目の**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="07062-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="07062-135">選択、 **NPM 構成ファイル**項目で、既定の名前をそのまま使用*package.json*、 をクリックし、**追加**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="07062-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="07062-136">*Package.json*ファイル内、`devDependencies`中かっこをオブジェクトに、"grunt"を入力します。</span><span class="sxs-lookup"><span data-stu-id="07062-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="07062-137">選択`grunt`Intellisense を一覧から、Enter キーを押します。</span><span class="sxs-lookup"><span data-stu-id="07062-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="07062-138">Visual Studio は grunt パッケージ名を引用符で囲むし、コロンを追加します。</span><span class="sxs-lookup"><span data-stu-id="07062-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="07062-139">コロンの右側に、Intellisense リストの先頭から、パッケージの最新の安定バージョンを選択します。 (キーを押して`Ctrl-Space`Intellisense が表示されない場合)。</span><span class="sxs-lookup"><span data-stu-id="07062-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt の Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="07062-141">NPM を使用して[セマンティック バージョニング](https://semver.org/)依存関係を整理します。</span><span class="sxs-lookup"><span data-stu-id="07062-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="07062-142">セマンティック バージョニング、SemVer とも呼ばれる、パッケージを識別する番号付けスキームで\<メジャー >.\<マイナー >。\<パッチ >。</span><span class="sxs-lookup"><span data-stu-id="07062-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>.</span></span> <span data-ttu-id="07062-143">Intellisense は、いくつかの一般的な選択肢のみを表示することによって、セマンティック バージョン管理を簡略化します。</span><span class="sxs-lookup"><span data-stu-id="07062-143">Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="07062-144">Intellisense の一覧 (上記の例では 0.4.5) の一番上の項目は、パッケージの最新の安定バージョンと見なされます。</span><span class="sxs-lookup"><span data-stu-id="07062-144">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="07062-145">キャレット (^) 記号が最新のメジャー バージョンと一致して、一致する最新のマイナー バージョンをティルダ (~)。</span><span class="sxs-lookup"><span data-stu-id="07062-145">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="07062-146">参照してください、 [NPM semver バージョン パーサー参照](https://www.npmjs.com/package/semver)SemVer を提供する完全な表現へのガイドとして。</span><span class="sxs-lookup"><span data-stu-id="07062-146">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="07062-147">依存関係の詳細を読み込む grunt の追加-contrib -\*のパッケージ*クリーン*、 *jshint*、 *concat*、 *uglify*、および*ウォッチ*次の例で示すようにします。</span><span class="sxs-lookup"><span data-stu-id="07062-147">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="07062-148">バージョンは、例では、一致する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="07062-148">The versions don't need to match the example.</span></span>

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

4. <span data-ttu-id="07062-149">保存、 *package.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="07062-149">Save the *package.json* file.</span></span>

<span data-ttu-id="07062-150">各パッケージ`devDependencies`項目は、各パッケージに必要なすべてのファイルと共にダウンロードされます。</span><span class="sxs-lookup"><span data-stu-id="07062-150">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="07062-151">内のパッケージ ファイルを見つけることができます、 *node_modules*を有効にするディレクトリ、 **すべてのファイル**ボタン**ソリューション エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="07062-151">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="07062-153">内の依存関係を手動で復元する場合は、**ソリューション エクスプ ローラー**を右クリックして`Dependencies\NPM`を選択して、**パッケージの復元**メニュー オプション。</span><span class="sxs-lookup"><span data-stu-id="07062-153">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![パッケージを復元します。](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="07062-155">Grunt の構成</span><span class="sxs-lookup"><span data-stu-id="07062-155">Configuring Grunt</span></span>

<span data-ttu-id="07062-156">Grunt がという名前のマニフェストで構成されている*Gruntfile.js*定義の読み込みし、登録を手動で実行または Visual Studio でのイベントに基づいて自動的に実行するように構成できるタスクです。</span><span class="sxs-lookup"><span data-stu-id="07062-156">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="07062-157">プロジェクトを右クリックして**追加** > **新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="07062-157">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="07062-158">選択、 **JavaScript ファイル**項目テンプレート、名を変更して*Gruntfile.js*、 をクリックし、**追加**ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="07062-158">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="07062-159">次のコードを追加*Gruntfile.js*します。</span><span class="sxs-lookup"><span data-stu-id="07062-159">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="07062-160">`initConfig`関数は、各パッケージのオプションを設定し、残りの部分が読み込みをタスクを登録します。</span><span class="sxs-lookup"><span data-stu-id="07062-160">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="07062-161">内で、`initConfig`関数を追加するためのオプション、`clean`タスクの例で示すように*Gruntfile.js*以下。</span><span class="sxs-lookup"><span data-stu-id="07062-161">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="07062-162">`clean`タスクは、ディレクトリの文字列の配列を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="07062-162">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="07062-163">このタスクからファイルを削除します。 *wwwroot/lib*全体を削除します。 *temp/* ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="07062-163">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="07062-164">以下、`initConfig`関数を呼び出しを追加して`grunt.loadNpmTasks`します。</span><span class="sxs-lookup"><span data-stu-id="07062-164">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="07062-165">これにより、タスク実行可能な Visual Studio から。</span><span class="sxs-lookup"><span data-stu-id="07062-165">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="07062-166">保存*Gruntfile.js*します。</span><span class="sxs-lookup"><span data-stu-id="07062-166">Save *Gruntfile.js*.</span></span> <span data-ttu-id="07062-167">ファイルの内容は次のスクリーン ショットのようになります。</span><span class="sxs-lookup"><span data-stu-id="07062-167">The file should look something like the screenshot below.</span></span>

    ![初期の gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="07062-169">右クリック*Gruntfile.js*選択**Task Runner Explorer**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="07062-169">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="07062-170">**Task Runner Explorer**ウィンドウが開きます。</span><span class="sxs-lookup"><span data-stu-id="07062-170">The **Task Runner Explorer** window will open.</span></span>

    ![タスク ランナー エクスプ ローラーのメニュー](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="07062-172">いることを確認`clean`下に表示される**タスク**で、 **Task Runner Explorer**します。</span><span class="sxs-lookup"><span data-stu-id="07062-172">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![タスク ランナー エクスプ ローラーのタスク一覧](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="07062-174">Clean タスクを右クリックして**実行**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="07062-174">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="07062-175">コマンド ウィンドウには、タスクの進行状況が表示されます。</span><span class="sxs-lookup"><span data-stu-id="07062-175">A command window displays progress of the task.</span></span>

    ![タスク ランナー エクスプ ローラーの実行クリーン タスク](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="07062-177">まだをクリーンアップするファイルまたはディレクトリはありません。</span><span class="sxs-lookup"><span data-stu-id="07062-177">There are no files or directories to clean yet.</span></span> <span data-ttu-id="07062-178">必要に応じて、ソリューション エクスプ ローラーでそれらを手動で作成し、テストとして clean タスクを実行できます。</span><span class="sxs-lookup"><span data-stu-id="07062-178">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="07062-179">`initConfig`のエントリを追加、関数`concat`次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="07062-179">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="07062-180">`src`プロパティ配列を組み合わせる必要がありますの順序で結合します。 ファイルを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="07062-180">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="07062-181">`dest`プロパティが生成される結合されたファイルにパスを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="07062-181">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="07062-182">`all`上記のコードでプロパティは、ターゲットの名前。</span><span class="sxs-lookup"><span data-stu-id="07062-182">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="07062-183">ターゲットは、複数のビルド環境を許可するいくつかの Grunt タスクで使用されます。</span><span class="sxs-lookup"><span data-stu-id="07062-183">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="07062-184">IntelliSense を使用して組み込みのターゲットを表示または独自に割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="07062-184">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="07062-185">追加、`jshint`タスクの次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="07062-185">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="07062-186">Jshint`code-quality`で見つかったすべての JavaScript ファイルに対してユーティリティを実行、 *temp*ディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="07062-186">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="07062-187">オプション"-W069"がエラー時に生成される jshint JavaScript はつまり、ドット表記ではなくプロパティを割り当てるの構文を角かっこ`Tastes["Sweet"]`の代わりに`Tastes.Sweet`します。</span><span class="sxs-lookup"><span data-stu-id="07062-187">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="07062-188">オプションは、他のプロセスの続行を許可する警告をオフにします。</span><span class="sxs-lookup"><span data-stu-id="07062-188">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="07062-189">追加、`uglify`タスクの次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="07062-189">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="07062-190">タスクの縮小、 *combined.js* ファイルが、一時ディレクトリにあるし、標準の命名規則に従った wwwroot/lib で結果ファイルを作成 *\<ファイル名\>min.js* 。</span><span class="sxs-lookup"><span data-stu-id="07062-190">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="07062-191">呼び出しの下`grunt.loadNpmTasks`を読み込む`grunt-contrib-clean`uglify の次のコードを使用して、jshint、concat の同じ呼び出しが含まれています。</span><span class="sxs-lookup"><span data-stu-id="07062-191">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="07062-192">保存*Gruntfile.js*します。</span><span class="sxs-lookup"><span data-stu-id="07062-192">Save *Gruntfile.js*.</span></span> <span data-ttu-id="07062-193">ファイルの内容は次の例のようになります。</span><span class="sxs-lookup"><span data-stu-id="07062-193">The file should look something like the example below.</span></span>

    ![grunt の完全なファイルの例](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="07062-195">注意、 **Task Runner Explorer**タスクの一覧が含まれます`clean`、 `concat`、`jshint`と`uglify`タスク。</span><span class="sxs-lookup"><span data-stu-id="07062-195">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="07062-196">順序で各タスクを実行してで結果を観察**ソリューション エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="07062-196">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="07062-197">各タスクは、エラーなく実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07062-197">Each task should run without errors.</span></span>

    ![タスク ランナー エクスプ ローラーの各タスクの実行](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="07062-199">Concat タスクを作成、新しい*combined.js*ファイルし、temp ディレクトリに配置します。</span><span class="sxs-lookup"><span data-stu-id="07062-199">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="07062-200">`jshint`単純にタスクを実行して出力を生成しません。</span><span class="sxs-lookup"><span data-stu-id="07062-200">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="07062-201">`uglify`タスクを作成する新しい*combined.min.js*ファイルおよび配置に*wwwroot/lib*します。</span><span class="sxs-lookup"><span data-stu-id="07062-201">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="07062-202">完了時に次のスクリーン ショットのようなもの、ソリューションはなります。</span><span class="sxs-lookup"><span data-stu-id="07062-202">On completion, the solution should look something like the screenshot below:</span></span>

    ![ソリューション エクスプ ローラーのすべてのタスクします。](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="07062-204">各パッケージのオプションの詳細については、次を参照してください。 [ https://www.npmjs.com/ ](https://www.npmjs.com/)と参照の検索ボックスに、メイン ページで、パッケージ名。</span><span class="sxs-lookup"><span data-stu-id="07062-204">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="07062-205">たとえば、すべてのパラメーターを説明するドキュメント リンクを取得する grunt contrib クリーン パッケージを検索できます。</span><span class="sxs-lookup"><span data-stu-id="07062-205">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="07062-206">まとめ</span><span class="sxs-lookup"><span data-stu-id="07062-206">All together now</span></span>

<span data-ttu-id="07062-207">Grunt を使用して、`registerTask()`メソッドを特定のシーケンスで一連のタスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="07062-207">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="07062-208">たとえば、クリーンの順序で上記の手順 -> の例を実行する concat]-> [jshint]-> [uglify、モジュールに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="07062-208">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="07062-209">InitConfig 以外、loadNpmTasks() 呼び出しと同じレベルには、コードを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07062-209">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="07062-210">エイリアス タスク Task Runner Explorer に新しいタスクが表示されます。</span><span class="sxs-lookup"><span data-stu-id="07062-210">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="07062-211">右クリックし、その他のタスクと同様に実行できます。</span><span class="sxs-lookup"><span data-stu-id="07062-211">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="07062-212">`all`タスクが実行`clean`、 `concat`、`jshint`と`uglify`の順序で。</span><span class="sxs-lookup"><span data-stu-id="07062-212">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![エイリアスの grunt タスク](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="07062-214">変更の監視</span><span class="sxs-lookup"><span data-stu-id="07062-214">Watching for changes</span></span>

<span data-ttu-id="07062-215">A`watch`タスク保持ファイルとディレクトリを監視します。</span><span class="sxs-lookup"><span data-stu-id="07062-215">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="07062-216">ウォッチは変更を検出した場合、タスクを自動的にトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="07062-216">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="07062-217">InitConfig への変更を監視するに次のコードを追加\*TypeScript ディレクトリ内の .js ファイル。</span><span class="sxs-lookup"><span data-stu-id="07062-217">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="07062-218">JavaScript ファイルが変更された場合`watch`実行は、`all`タスク。</span><span class="sxs-lookup"><span data-stu-id="07062-218">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="07062-219">呼び出しを追加して`loadNpmTasks()`を表示する、`watch`タスク ランナー エクスプ ローラーで作業します。</span><span class="sxs-lookup"><span data-stu-id="07062-219">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="07062-220">Task Runner Explorer で監視タスクを右クリックし、コンテキスト メニューから実行を選択します。</span><span class="sxs-lookup"><span data-stu-id="07062-220">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="07062-221">「待機しています...」が実行中の監視タスクを表示するコマンド ウィンドウに表示されます。メッセージ。</span><span class="sxs-lookup"><span data-stu-id="07062-221">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="07062-222">TypeScript ファイルのいずれかを開き、スペースを追加し、ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="07062-222">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="07062-223">監視タスクのトリガーを順番に実行するその他のタスクをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="07062-223">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="07062-224">次のスクリーン ショットは、サンプルの実行を示しています。</span><span class="sxs-lookup"><span data-stu-id="07062-224">The screenshot below shows a sample run.</span></span>

![タスク出力を実行しています。](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="07062-226">Visual Studio イベントへのバインド</span><span class="sxs-lookup"><span data-stu-id="07062-226">Binding to Visual Studio events</span></span>

<span data-ttu-id="07062-227">タスクをバインドするには Visual Studio で作業するたびに、タスクを手動で開始する場合を除き、 **Before Build**、**後にビルド**、**クリーン**、および**プロジェクトのオープン**イベント。</span><span class="sxs-lookup"><span data-stu-id="07062-227">Unless you want to manually start your tasks every time you work in Visual Studio, you can bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="07062-228">バインドしてみます`watch`が Visual Studio を開くたびに実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="07062-228">Let’s bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="07062-229">タスク ランナー エクスプ ローラーでは、監視タスクを右クリックして**バインド > プロジェクトを開く**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="07062-229">In Task Runner Explorer, right-click the watch task and select **Bindings > Project Open** from the context menu.</span></span>

![プロジェクトを開くタスクにバインドします。](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="07062-231">アンロードし、プロジェクトの再読み込みします。</span><span class="sxs-lookup"><span data-stu-id="07062-231">Unload and reload the project.</span></span> <span data-ttu-id="07062-232">プロジェクトが再度読み込まれると、自動的に実行する監視タスクが開始されます。</span><span class="sxs-lookup"><span data-stu-id="07062-232">When the project loads again, the watch task will start running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="07062-233">まとめ</span><span class="sxs-lookup"><span data-stu-id="07062-233">Summary</span></span>

<span data-ttu-id="07062-234">Grunt は、ほとんどのクライアント ビルド タスクの自動化に使用できる強力なタスク ランナーです。</span><span class="sxs-lookup"><span data-stu-id="07062-234">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="07062-235">Grunt は、そのパッケージ、およびツールの Visual Studio との統合機能を提供する NPM を活用します。</span><span class="sxs-lookup"><span data-stu-id="07062-235">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="07062-236">Visual Studio のタスク ランナー エクスプ ローラーでは、構成ファイルへの変更を検出し、タスクの実行、実行中のタスクを表示、および Visual Studio のイベントにタスクを関連付けるに便利なインターフェイスを提供します。</span><span class="sxs-lookup"><span data-stu-id="07062-236">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
