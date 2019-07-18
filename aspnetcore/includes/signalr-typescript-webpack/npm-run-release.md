```console
npm run release
```

<span data-ttu-id="444ff-101">このコマンドは、アプリケーションの実行中に提供するクライアント側資産を生成します。</span><span class="sxs-lookup"><span data-stu-id="444ff-101">This command yields the client-side assets to be served when running the app.</span></span> <span data-ttu-id="444ff-102">資産は、*wwwroot* フォルダーに配置されます。</span><span class="sxs-lookup"><span data-stu-id="444ff-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="444ff-103">Webpack は、次のタスクを完了しました。</span><span class="sxs-lookup"><span data-stu-id="444ff-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="444ff-104">*wwwroot* ディレクトリの内容を消去しました。</span><span class="sxs-lookup"><span data-stu-id="444ff-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="444ff-105">TypeScript を JavaScript に変換しました&mdash;*トランスコンパイル*と呼ばれるプロセス。</span><span class="sxs-lookup"><span data-stu-id="444ff-105">Converted the TypeScript to JavaScript&mdash;a process known as *transpilation*.</span></span>
* <span data-ttu-id="444ff-106">生成後の JavaScript のファイルのサイズを縮小しました&mdash;*縮小*と呼ばれるプロセス。</span><span class="sxs-lookup"><span data-stu-id="444ff-106">Mangled the generated JavaScript to reduce file size&mdash;a process known as *minification*.</span></span>
* <span data-ttu-id="444ff-107">処理済みの JavaScript、CSS、および HTML ファイルを *src* から *wwwroot* ディレクトリにコピーしました。</span><span class="sxs-lookup"><span data-stu-id="444ff-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="444ff-108">次の要素を *wwwroot/index.html* ファイルに挿入しました。</span><span class="sxs-lookup"><span data-stu-id="444ff-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="444ff-109">*wwwroot/main.\<hash\>.css* ファイルを参照している `<link>` タグ。</span><span class="sxs-lookup"><span data-stu-id="444ff-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="444ff-110">このタグは、終了 `</head>` タグの直前に置かれます。</span><span class="sxs-lookup"><span data-stu-id="444ff-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="444ff-111">縮小された *wwwroot/main.\<hash\>.js* ファイルを参照している `<script>` タグ。</span><span class="sxs-lookup"><span data-stu-id="444ff-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="444ff-112">このタグは、終了 `</body>` タグの直前に置かれます。</span><span class="sxs-lookup"><span data-stu-id="444ff-112">This tag is placed immediately before the closing `</body>` tag.</span></span>
