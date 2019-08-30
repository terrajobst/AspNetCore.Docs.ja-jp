---
page_type: sample
description: Swashbuckle を ASP.NET Core Web API プロジェクトに追加し、Swagger UI を統合する方法について説明します。
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: 2b1da1d524eb18f1048314c544c64f82c22761e9
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69988968"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="71778-102">Swashbuckle と ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="71778-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="71778-103">Web API を使用する場合、さまざまなメソッドを理解することは開発者にとって困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="71778-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="71778-104">[Swagger](https://swagger.io/) ([OpenAPI](https://www.openapis.org/) とも呼ばれる) では、Web API の役立つドキュメントとヘルプ ページの生成に関する問題を解決します。</span><span class="sxs-lookup"><span data-stu-id="71778-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="71778-105">Swagger では、対話型のドキュメント、クライアント SDK の生成、API の発見可能性などの利点を提供します。</span><span class="sxs-lookup"><span data-stu-id="71778-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="71778-106">このサンプルでは、.NET 実装の [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) が示されています。</span><span class="sxs-lookup"><span data-stu-id="71778-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="71778-107">Swagger ミドルウェアを追加して構成する</span><span class="sxs-lookup"><span data-stu-id="71778-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="71778-108">`Startup.Configure` メソッドで、生成された JSON ドキュメントと Swagger UI 対応のミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="71778-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");In the 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="71778-109">前述の `UseSwaggerUI` メソッド呼び出しにより、[静的ファイル ミドルウェア](https://docs.microsoft.com/aspnet/core/fundamentals/static-files)が有効になります。</span><span class="sxs-lookup"><span data-stu-id="71778-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="71778-110">.NET Framework または .NET Core 1.x を対象とする場合は、[Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="71778-111">アプリを起動し、`http://localhost:<port>/swagger/v1/swagger.json` に移動します。</span><span class="sxs-lookup"><span data-stu-id="71778-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="71778-112">[Swagger 仕様 (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson) に基づき、エンドポイントについて説明するドキュメントが生成され、表示されます。</span><span class="sxs-lookup"><span data-stu-id="71778-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="71778-113">Swagger UI は `http://localhost:<port>/swagger` にあります。</span><span class="sxs-lookup"><span data-stu-id="71778-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="71778-114">Swagger UI から API を探し、他のプログラムに組み込みます。</span><span class="sxs-lookup"><span data-stu-id="71778-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="71778-115">アプリのルート (`http://localhost:<port>/`) で Swagger UI にサービスを提供するには、`RoutePrefix` プロパティを空の文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="71778-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="71778-116">IIS やリバース プロキシを使用するディレクトリを使用している場合は、`./` プレフィックスを使用して Swagger のエンドポイントを相対パスに設定します。</span><span class="sxs-lookup"><span data-stu-id="71778-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="71778-117">たとえば、`./swagger/v1/swagger.json` のようにします。</span><span class="sxs-lookup"><span data-stu-id="71778-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="71778-118">`/swagger/v1/swagger.json` を使用することで、アプリが URL の真のルート (使用されている場合は、これに加えてルート プレフィックス) にある JSON ファイルを検索するように指示されます。</span><span class="sxs-lookup"><span data-stu-id="71778-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="71778-119">たとえば、`http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` の代わりに `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` を使用します。</span><span class="sxs-lookup"><span data-stu-id="71778-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="71778-120">カスタマイズと拡張</span><span class="sxs-lookup"><span data-stu-id="71778-120">Customize and extend</span></span>

<span data-ttu-id="71778-121">Swagger は、テーマに合わせてオブジェクト モデルを文書化して、UI をカスタマイズするオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="71778-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="71778-122">`Startup` クラスに、次の名前空間を追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="71778-123">API 情報と説明</span><span class="sxs-lookup"><span data-stu-id="71778-123">API info and description</span></span>

<span data-ttu-id="71778-124">`AddSwaggerGen` メソッドに渡された構成アクションによって、作成者、ライセンス、説明などの情報が追加されます。</span><span class="sxs-lookup"><span data-stu-id="71778-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="71778-125">Swagger UI には、バージョンの情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="71778-125">The Swagger UI displays the version's information:</span></span>

![バージョン情報を含む Swagger UI: 説明、作成者、およびその他のリンクの表示](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="71778-127">XML コメント</span><span class="sxs-lookup"><span data-stu-id="71778-127">XML comments</span></span>

<span data-ttu-id="71778-128">XML コメントは、次の方法で有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="71778-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71778-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71778-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="71778-130">**ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[<project_name>.csproj の編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="71778-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="71778-131">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="71778-132">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="71778-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="71778-133">*Solution Pad* から **Ctrl** キーを押しながらプロジェクト名をクリックします。</span><span class="sxs-lookup"><span data-stu-id="71778-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="71778-134">**[ツール]**  >  **[ファイルの編集]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="71778-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="71778-135">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71778-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71778-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="71778-137">強調表示された行を手動で *.csproj* ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="71778-138">XML コメントを有効にすると、ドキュメントに未記載のパブリック型とメンバーのデバッグ情報を提供することができます。</span><span class="sxs-lookup"><span data-stu-id="71778-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="71778-139">文書化されない型とメンバーは警告メッセージで示されます。</span><span class="sxs-lookup"><span data-stu-id="71778-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="71778-140">たとえば、次のメッセージは警告コード 1591 の違反を示します。</span><span class="sxs-lookup"><span data-stu-id="71778-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="71778-141">プロジェクト全体で警告を非表示にするには、プロジェクト ファイルで無視する警告コードの一覧をセミコロン区切りで定義します。</span><span class="sxs-lookup"><span data-stu-id="71778-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="71778-142">警告コードを `$(NoWarn);` に追加すると、[C# の既定値](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)も適用されます。</span><span class="sxs-lookup"><span data-stu-id="71778-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="71778-143">特定のメンバーに向けた警告のみを非表示にするには、コードを [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) プリプロセッサ ディレクティブで囲みます。</span><span class="sxs-lookup"><span data-stu-id="71778-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="71778-144">この方法は、API ドキュメント経由で公開すべきではないコードの場合に役立ちます。次の例では、警告コード CS1591 が `Program` クラス全体で無視されます。</span><span class="sxs-lookup"><span data-stu-id="71778-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="71778-145">警告コードの強制は、クラス定義の終了時に復元されます。</span><span class="sxs-lookup"><span data-stu-id="71778-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="71778-146">コンマ区切りのリストで複数の警告コードを指定します。</span><span class="sxs-lookup"><span data-stu-id="71778-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="71778-147">上記の手順で生成される XML ファイルを使用するように、Swagger を構成します。</span><span class="sxs-lookup"><span data-stu-id="71778-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="71778-148">Linux または Windows 以外のオペレーティング システムでは、ファイル名やパスで大文字小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="71778-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="71778-149">たとえば、*TodoApi.XML* ファイルは Windows では有効ですが、CentOS では無効です。</span><span class="sxs-lookup"><span data-stu-id="71778-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="71778-150">前述のコードでは、[Reflection](/dotnet/csharp/programming-guide/concepts/reflection) を使用し、Web API プロジェクトの名前に一致する XML ファイル名が構築されます。</span><span class="sxs-lookup"><span data-stu-id="71778-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="71778-151">[AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) プロパティは、XML ファイルのパス構築に使用されます。</span><span class="sxs-lookup"><span data-stu-id="71778-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="71778-152">Swagger の一部の機能 (たとえば、入力パラメーターや HTTP メソッドのスキーマ、それぞれの属性からの応答コード) は、XML ドキュメント ファイルを使わなくても動作します。</span><span class="sxs-lookup"><span data-stu-id="71778-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="71778-153">メソッドのサマリーや、パラメーターと応答コードの記述など、ほとんどの機能には、XML ファイルの使用が必須です。</span><span class="sxs-lookup"><span data-stu-id="71778-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="71778-154">アクションにトリプル スラッシュのコメントを追加すると、セクション ヘッダーに説明が追加され、Swagger UI が向上します。</span><span class="sxs-lookup"><span data-stu-id="71778-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="71778-155">`Delete` アクションの上に [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) 要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="71778-156">Swagger UI には、前述のコードの `<summary>` 要素の内部テキストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="71778-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![DELETE メソッドの XML コメント 'Deletes a specific TodoItem.' を](sample_images/triple-slash-comments.png)

<span data-ttu-id="71778-159">UI は、生成された JSON スキーマによって決まります。</span><span class="sxs-lookup"><span data-stu-id="71778-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="71778-160">[\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 要素を `Create` アクション メソッドのドキュメントに追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="71778-161">これは、`<summary>` 要素で指定された情報を補足し、Swagger UI をより堅牢にします。</span><span class="sxs-lookup"><span data-stu-id="71778-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="71778-162">`<remarks>` 要素の内容は、テキスト、JSON、または XML で構成されます。</span><span class="sxs-lookup"><span data-stu-id="71778-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="71778-163">追加コメントで UI が向上している点にご注目ください。</span><span class="sxs-lookup"><span data-stu-id="71778-163">Notice the UI enhancements with these additional comments:</span></span>

![追加のコメントが表示された Swagger UI](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="71778-165">データの注釈</span><span class="sxs-lookup"><span data-stu-id="71778-165">Data annotations</span></span>

<span data-ttu-id="71778-166">[System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 名前空間にある属性をモデルに追加し、Swagger UI コンポーネントの向上に役立てます。</span><span class="sxs-lookup"><span data-stu-id="71778-166">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="71778-167">`[Required]` 属性を `TodoItem` クラスの `Name` プロパティに追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="71778-168">この属性の有無は、UI 動作を変更し、基になる JSON スキーマを変更します。</span><span class="sxs-lookup"><span data-stu-id="71778-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="71778-169">API コントローラーに `[Produces("application/json")]` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="71778-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="71778-170">その目的は、コントローラーのアクションが *application/json* の応答コンテンツ タイプをサポートすることを宣言することです。</span><span class="sxs-lookup"><span data-stu-id="71778-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="71778-171">**[応答コンテンツ タイプ]** ドロップダウンがコント ローラーの GET 操作の既定値としてこのコンテンツ タイプを選択します。</span><span class="sxs-lookup"><span data-stu-id="71778-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![既定の応答のコンテンツ タイプを持つ Swagger UI](sample_images/json-response-content-type.png)

<span data-ttu-id="71778-173">Web API のデータの注釈の使用量が多いほど、UI と API のヘルプ ページはわかりやすく便利になります。</span><span class="sxs-lookup"><span data-stu-id="71778-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="71778-174">応答の種類の説明</span><span class="sxs-lookup"><span data-stu-id="71778-174">Describe response types</span></span>

<span data-ttu-id="71778-175">Web API を使用する開発者は、何が返されるかを最も考慮します&mdash;具体的には、応答の種類とエラー コードです (標準以外の場合)。</span><span class="sxs-lookup"><span data-stu-id="71778-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="71778-176">応答の種類とエラー コードは、XML コメントとデータ注釈に示されます。</span><span class="sxs-lookup"><span data-stu-id="71778-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="71778-177">`Create` アクションでは、成功すると HTTP 201 状態コードが返されます。</span><span class="sxs-lookup"><span data-stu-id="71778-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="71778-178">HTTP 400 状態コードは、投稿された要求本文が null のときに返されます。</span><span class="sxs-lookup"><span data-stu-id="71778-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="71778-179">Swagger UI に適切なドキュメントがないと、利用者にはこれらの予期される結果の知識がありません。</span><span class="sxs-lookup"><span data-stu-id="71778-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="71778-180">その問題を解決するために、次の例では強調表示された行を追加しています。</span><span class="sxs-lookup"><span data-stu-id="71778-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(201)]
[ProducesResponseType(400)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="71778-181">Swagger UI は、予期される HTTP 応答コードを明確に記述するようになりました。</span><span class="sxs-lookup"><span data-stu-id="71778-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![ステータスコードの POST 応答クラスの説明 'Returns the newly created Todo item' and '400 - If the item is null' および応答メッセージの下に理由を示す Swagger UI](sample_images/data-annotations-response-types.png)

<span data-ttu-id="71778-183">ASP.NET Core 2.2 以降では、明示的に個別のアクションを `[ProducesResponseType]` で装飾する代わりに、規約を使用できます。</span><span class="sxs-lookup"><span data-stu-id="71778-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="71778-184">詳しくは、「[Web API 規約を使用する](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="71778-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="71778-185">UI のカスタマイズについては、次をご覧ください。[UI のカスタマイズ](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="71778-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
