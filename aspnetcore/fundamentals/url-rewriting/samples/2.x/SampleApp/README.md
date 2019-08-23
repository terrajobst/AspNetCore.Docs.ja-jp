# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="1f53a-101">ASP.NET Core の URL リライト サンプル</span><span class="sxs-lookup"><span data-stu-id="1f53a-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="1f53a-102">このサンプルでは、ASP.NET Core URL リライト ミドルウェアの使用法を示します。</span><span class="sxs-lookup"><span data-stu-id="1f53a-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="1f53a-103">このアプリでは、URL リダイレクトと URL リライト オプションを示します。</span><span class="sxs-lookup"><span data-stu-id="1f53a-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="1f53a-104">サンプルを実行すると、いずれかのルールが要求 URL に適用されるときに書き換えられる URL またはリダイレクトされる URL が、非ファイル応答で返されます。</span><span class="sxs-lookup"><span data-stu-id="1f53a-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="1f53a-105">XML とテキスト ファイルの例では、ミドルウェアによって要求の URL が書き換えられた後、静的ファイル ミドルウェアによってファイルが提供されます。</span><span class="sxs-lookup"><span data-stu-id="1f53a-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="1f53a-106">このサンプルの例</span><span class="sxs-lookup"><span data-stu-id="1f53a-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="1f53a-107">成功状態コード: 302 (検出)</span><span class="sxs-lookup"><span data-stu-id="1f53a-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="1f53a-108">例 (リダイレクト): **/redirect-rule/{capture_group}** から **/redirected/{capture_group}** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="1f53a-109">成功状態コード: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="1f53a-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="1f53a-110">例 (リライト): **/rewrite-rule/{capture_group_1}/{capture_group_2}** から **/rewritten?var1={capture_group_1}&var2={capture_group_2}** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="1f53a-111">成功状態コード: 302 (検出)</span><span class="sxs-lookup"><span data-stu-id="1f53a-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="1f53a-112">例 (リダイレクト): **/apache-mod-rules-redirect/{capture_group}** から **/redirected?id={capture_group}** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="1f53a-113">成功状態コード: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="1f53a-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="1f53a-114">例 (リライト): **/iis-rules-rewrite/{capture_group}** から **/rewritten?id={capture_group}** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="1f53a-115">成功状態コード: 301 (永続的に移動)</span><span class="sxs-lookup"><span data-stu-id="1f53a-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="1f53a-116">例 (リダイレクト): **/file.xml** から **/xmlfiles/file.xml** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="1f53a-117">成功状態コード: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="1f53a-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="1f53a-118">例 (書き換え): **/some_file.txt** から **/file.txt** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="1f53a-119">成功状態コード: 301 (永続的に移動)</span><span class="sxs-lookup"><span data-stu-id="1f53a-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="1f53a-120">例 (リダイレクト): **/image.png** から **/png-images/image.png** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="1f53a-121">例 (リダイレクト): **/image.jpg** から **/jpg-images/image.jpg** へ</span><span class="sxs-lookup"><span data-stu-id="1f53a-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="1f53a-122">PhysicalFileProvider を使用する</span><span class="sxs-lookup"><span data-stu-id="1f53a-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="1f53a-123">`AddApacheModRewrite()` メソッドと `AddIISUrlRewrite()` メソッドに渡す `PhysicalFileProvider` を作成して、`IFileProvider` を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="1f53a-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="1f53a-124">セキュリティで保護されたリダイレクトの拡張機能</span><span class="sxs-lookup"><span data-stu-id="1f53a-124">Secure redirection extensions</span></span>

<span data-ttu-id="1f53a-125">このサンプルには、URL (`https://localhost:5001`、`https://localhost`) とテスト証明書 (*testCert.pfx*) を使用してこれらのセキュリティ保護されたリダイレクト方法を調べるための `WebHostBuilder` 構成が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f53a-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="1f53a-126">サーバーでポート 443 が既に割り当て済みまたは使用中の場合、`https://localhost` の例は機能しません。*Program.cs* ファイルの `CreateWebHostBuilder` メソッドでポート 443 に対する `ListenOptions` を削除するか、またはサーバーでポート 443 をバインド解除して、Kestrel がポートを使用できるようにしてください。</span><span class="sxs-lookup"><span data-stu-id="1f53a-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="1f53a-127">メソッド</span><span class="sxs-lookup"><span data-stu-id="1f53a-127">Method</span></span>                           | <span data-ttu-id="1f53a-128">状態コード</span><span class="sxs-lookup"><span data-stu-id="1f53a-128">Status Code</span></span> |    <span data-ttu-id="1f53a-129">ポート</span><span class="sxs-lookup"><span data-stu-id="1f53a-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="1f53a-130">301</span><span class="sxs-lookup"><span data-stu-id="1f53a-130">301</span></span>     | <span data-ttu-id="1f53a-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="1f53a-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="1f53a-132">302</span><span class="sxs-lookup"><span data-stu-id="1f53a-132">302</span></span>     | <span data-ttu-id="1f53a-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="1f53a-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="1f53a-134">301</span><span class="sxs-lookup"><span data-stu-id="1f53a-134">301</span></span>     | <span data-ttu-id="1f53a-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="1f53a-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="1f53a-136">301</span><span class="sxs-lookup"><span data-stu-id="1f53a-136">301</span></span>     |    <span data-ttu-id="1f53a-137">5001</span><span class="sxs-lookup"><span data-stu-id="1f53a-137">5001</span></span>    |
