```console
npm run release
```

<span data-ttu-id="aa46b-101">このコマンドは、アプリの実行中に提供するクライアント側資産を生成します。</span><span class="sxs-lookup"><span data-stu-id="aa46b-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="aa46b-102">資産は、*wwwroot* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="aa46b-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="aa46b-103">Webpack は、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="aa46b-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="aa46b-104">*wwwroot* ディレクトリの内容を消去しました。</span><span class="sxs-lookup"><span data-stu-id="aa46b-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="aa46b-105">"*トランスコンパイル*" と呼ばれるプロセスで TypeScript を JavaScript に変換しました。</span><span class="sxs-lookup"><span data-stu-id="aa46b-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="aa46b-106">"*縮小*" と呼ばれるプロセスで、生成後の JavaScript ファイルのサイズを縮小しました。</span><span class="sxs-lookup"><span data-stu-id="aa46b-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="aa46b-107">処理済みの JavaScript、CSS、および HTML ファイルを *src* から *wwwroot* ディレクトリにコピーしました。</span><span class="sxs-lookup"><span data-stu-id="aa46b-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="aa46b-108">次の要素を *wwwroot/index.html* ファイルに挿入しました。</span><span class="sxs-lookup"><span data-stu-id="aa46b-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="aa46b-109">*wwwroot/main.\<hash\>.css* ファイルを参照している `<link>` タグ。</span><span class="sxs-lookup"><span data-stu-id="aa46b-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="aa46b-110">このタグは、終了 `</head>` タグの直前に置かれます。</span><span class="sxs-lookup"><span data-stu-id="aa46b-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="aa46b-111">縮小された *wwwroot/main.\<hash\>.js* ファイルを参照している `<script>` タグ。</span><span class="sxs-lookup"><span data-stu-id="aa46b-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="aa46b-112">このタグは、終了 `</body>` タグの直前に置かれます。</span><span class="sxs-lookup"><span data-stu-id="aa46b-112">This tag is placed immediately before the closing `</body>` tag.</span></span>