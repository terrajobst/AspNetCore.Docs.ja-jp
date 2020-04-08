<!-- Options common to Razor Pages and Controller -->
| オプション               | 説明|
| ----------------- | ------------ |
| --model または -m  | 使用するモデル クラス。 |
| --dataContext または -dc  | 使用する `DbContext` クラス。 |
| --bootstrapVersion または -b  | ブートストラップのバージョンを指定します。 有効な値は `3` または `4`です。 既定値は `4` です。 指定されたバージョンのブートストラップ ファイルを含む *wwwroot* ディレクトリが必要で存在しない場合は、作成されます。 |
| --referenceScriptLibraries または -scripts |  生成されたビューでスクリプト ライブラリを参照します。 [編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。 |
| --layout または -l | 使用するカスタム レイアウト ページ。 |
| --useDefaultLayout または -udl | ビューの既定のレイアウトを使用します。 |
| --force または -f | 既存のファイルを上書きします。 |
| --relativeFolderPath または -outDir | ファイルが生成されるプロジェクトの相対出力フォルダー パス。 指定しない場合、ファイルはプロジェクト フォルダーに生成されます。 |