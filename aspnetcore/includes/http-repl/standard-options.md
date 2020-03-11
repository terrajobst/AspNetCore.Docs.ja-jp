* `-F|--no-formatting`

  存在することで HTTP 応答の書式設定を抑制するフラグ。

* `-h|--header`

  HTTP 要求ヘッダーを設定します。 次の 2 つの値の形式がサポートされています。

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  HTTP 応答全体 (ヘッダーと本文を含む) を書き込むファイルを指定します。 たとえば、「 `--response "C:\response.txt"` 」のように入力します。 ファイルが存在しない場合は作成されます。

* `--response:body`

  HTTP 応答の本文を書き込むファイルを指定します。 たとえば、「 `--response:body "C:\response.json"` 」のように入力します。 ファイルが存在しない場合は作成されます。

* `--response:headers`

  HTTP 応答のヘッダーを書き込むファイルを指定します。 たとえば、「 `--response:headers "C:\response.txt"` 」のように入力します。 ファイルが存在しない場合は作成されます。

* `-s|--streaming`

  存在することで HTTP 応答のストリーミングを有効にするフラグ。
