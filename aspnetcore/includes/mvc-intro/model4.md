<span data-ttu-id="42116-101">次の表で、ASP.NET Core コード ジェネレーターのパラメーターについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="42116-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="42116-102">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42116-102">Parameter</span></span>               | <span data-ttu-id="42116-103">説明</span><span class="sxs-lookup"><span data-stu-id="42116-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="42116-104">-m</span><span class="sxs-lookup"><span data-stu-id="42116-104">-m</span></span>  | <span data-ttu-id="42116-105">モデルの名前。</span><span class="sxs-lookup"><span data-stu-id="42116-105">The name of the model.</span></span> |
| <span data-ttu-id="42116-106">-dc</span><span class="sxs-lookup"><span data-stu-id="42116-106">-dc</span></span>  | <span data-ttu-id="42116-107">データ コンテキスト。</span><span class="sxs-lookup"><span data-stu-id="42116-107">The data context.</span></span> |
| <span data-ttu-id="42116-108">-udl</span><span class="sxs-lookup"><span data-stu-id="42116-108">-udl</span></span> | <span data-ttu-id="42116-109">既定のレイアウトを使用します。</span><span class="sxs-lookup"><span data-stu-id="42116-109">Use the default layout.</span></span> |
| <span data-ttu-id="42116-110">--relativeFolderPath</span><span class="sxs-lookup"><span data-stu-id="42116-110">--relativeFolderPath</span></span> | <span data-ttu-id="42116-111">ファイルを作成するための相対出力フォルダー パス。</span><span class="sxs-lookup"><span data-stu-id="42116-111">The relative output folder path to create the files.</span></span> |
| <span data-ttu-id="42116-112">--useDefaultLayout</span><span class="sxs-lookup"><span data-stu-id="42116-112">--useDefaultLayout</span></span> | <span data-ttu-id="42116-113">ビューには既定のレイアウトを使用してください。</span><span class="sxs-lookup"><span data-stu-id="42116-113">The default layout should be used for the views.</span></span> |
| <span data-ttu-id="42116-114">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="42116-114">--referenceScriptLibraries</span></span> | <span data-ttu-id="42116-115">[編集] および [作成] ページに `_ValidationScriptsPartial` を追加します。</span><span class="sxs-lookup"><span data-stu-id="42116-115">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="42116-116">次のように、`h` スイッチを使用して、`aspnet-codegenerator controller` コマンドに関するヘルプを取得します。</span><span class="sxs-lookup"><span data-stu-id="42116-116">Use the `h` switch to get help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="42116-117">詳細については、「[dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)」を参照してください</span><span class="sxs-lookup"><span data-stu-id="42116-117">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span></span>
