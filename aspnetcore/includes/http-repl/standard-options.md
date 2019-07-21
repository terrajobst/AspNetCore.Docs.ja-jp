* `-F|--no-formatting`

  <span data-ttu-id="10bf2-101">存在することで HTTP 応答の書式設定を抑制するフラグ。</span><span class="sxs-lookup"><span data-stu-id="10bf2-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="10bf2-102">HTTP 要求ヘッダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="10bf2-102">Sets an HTTP request header.</span></span> <span data-ttu-id="10bf2-103">次の 2 つの値の形式がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="10bf2-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="10bf2-104">HTTP 応答全体 (ヘッダーと本文を含む) を書き込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="10bf2-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="10bf2-105">たとえば、`--response "C:\response.txt"` のようにします。</span><span class="sxs-lookup"><span data-stu-id="10bf2-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="10bf2-106">ファイルが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="10bf2-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="10bf2-107">HTTP 応答の本文を書き込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="10bf2-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="10bf2-108">たとえば、`--response:body "C:\response.json"` のようにします。</span><span class="sxs-lookup"><span data-stu-id="10bf2-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="10bf2-109">ファイルが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="10bf2-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="10bf2-110">HTTP 応答のヘッダーを書き込むファイルを指定します。</span><span class="sxs-lookup"><span data-stu-id="10bf2-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="10bf2-111">たとえば、`--response:headers "C:\response.txt"` のようにします。</span><span class="sxs-lookup"><span data-stu-id="10bf2-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="10bf2-112">ファイルが存在しない場合は作成されます。</span><span class="sxs-lookup"><span data-stu-id="10bf2-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="10bf2-113">存在することで HTTP 応答のストリーミングを有効にするフラグ。</span><span class="sxs-lookup"><span data-stu-id="10bf2-113">A flag whose presence enables streaming of the HTTP response.</span></span>
