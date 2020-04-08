```console
npm run release
```

このコマンドは、アプリの実行中に提供するクライアント側資産を生成します。 資産は、*wwwroot* フォルダーに配置されます。

Webpack は、次のタスクを完了しました。

* *wwwroot* ディレクトリの内容を消去しました。
* "*トランスコンパイル*" と呼ばれるプロセスで TypeScript を JavaScript に変換しました。
* "*縮小*" と呼ばれるプロセスで、生成後の JavaScript ファイルのサイズを縮小しました。
* 処理済みの JavaScript、CSS、および HTML ファイルを *src* から *wwwroot* ディレクトリにコピーしました。
* 次の要素を *wwwroot/index.html* ファイルに挿入しました。
  * `<link>`wwwroot/main.*hash\<.css\> ファイルを参照している*  タグ。 このタグは、終了 `</head>` タグの直前に置かれます。
  * 縮小された `<script>`wwwroot/main.*hash\<.js\> ファイルを参照している*  タグ。 このタグは、終了 `</body>` タグの直前に置かれます。