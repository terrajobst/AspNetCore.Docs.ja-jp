<a name="codegenerator"></a> <span data-ttu-id="02355-101">次の表で、ASP.NET Core コード ジェネレーターのパラメーターについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="02355-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="02355-102">パラメーター</span><span class="sxs-lookup"><span data-stu-id="02355-102">Parameter</span></span>               | <span data-ttu-id="02355-103">説明</span><span class="sxs-lookup"><span data-stu-id="02355-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="02355-104">-m</span><span class="sxs-lookup"><span data-stu-id="02355-104">-m</span></span>  | <span data-ttu-id="02355-105">モデルの名前。</span><span class="sxs-lookup"><span data-stu-id="02355-105">The name of the model.</span></span> |
| <span data-ttu-id="02355-106">-dc</span><span class="sxs-lookup"><span data-stu-id="02355-106">-dc</span></span>  | <span data-ttu-id="02355-107">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="02355-107">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="02355-108">-udl</span><span class="sxs-lookup"><span data-stu-id="02355-108">-udl</span></span> | <span data-ttu-id="02355-109">既定のレイアウトを使用します。</span><span class="sxs-lookup"><span data-stu-id="02355-109">Use the default layout.</span></span> |
| <span data-ttu-id="02355-110">-outDir</span><span class="sxs-lookup"><span data-stu-id="02355-110">-outDir</span></span> | <span data-ttu-id="02355-111">ビューを作成するための相対出力フォルダー パス。</span><span class="sxs-lookup"><span data-stu-id="02355-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="02355-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="02355-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="02355-113">[編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。</span><span class="sxs-lookup"><span data-stu-id="02355-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="02355-114">次のように、`h` スイッチを使用して、`aspnet-codegenerator razorpage` コマンドに関するヘルプを取得します。</span><span class="sxs-lookup"><span data-stu-id="02355-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="02355-115">詳細については、「[dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="02355-115">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
