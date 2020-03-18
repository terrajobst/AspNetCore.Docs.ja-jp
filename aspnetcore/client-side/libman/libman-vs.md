---
title: Visual Studio の ASP.NET Core で LibMan を使用する
author: scottaddie
description: Visual Studio の ASP.NET Core プロジェクトで LibMan を使用する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78646796"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="882d4-103">Visual Studio の ASP.NET Core で LibMan を使用する</span><span class="sxs-lookup"><span data-stu-id="882d4-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="882d4-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="882d4-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="882d4-105">Visual Studio には、ASP.NET Core プロジェクトでの [LibMan](xref:client-side/libman/index) に対する次のようなサポートが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="882d4-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="882d4-106">ビルド時に LibMan の復元操作を構成して実行するためのサポート。</span><span class="sxs-lookup"><span data-stu-id="882d4-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="882d4-107">LibMan の復元操作とクリーン操作をトリガーするためのメニュー項目。</span><span class="sxs-lookup"><span data-stu-id="882d4-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="882d4-108">ライブラリを検索し、プロジェクトにファイルを追加するための検索ダイアログ。</span><span class="sxs-lookup"><span data-stu-id="882d4-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="882d4-109">LibMan マニフェスト ファイル &mdash; *libman.json* の編集サポート。</span><span class="sxs-lookup"><span data-stu-id="882d4-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="882d4-110">[サンプル コードを表示またはダウンロードします](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(ダウンロード方法)](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="882d4-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="882d4-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="882d4-111">Prerequisites</span></span>

* <span data-ttu-id="882d4-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="882d4-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="882d4-113">ライブラリ ファイルを追加する</span><span class="sxs-lookup"><span data-stu-id="882d4-113">Add library files</span></span>

<span data-ttu-id="882d4-114">ライブラリ ファイルは、次の 2 つの方法で ASP.NET Core プロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="882d4-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. <span data-ttu-id="882d4-115">[[クライアント側のライブラリを追加します] ダイアログを使用する](#use-the-add-client-side-library-dialog)</span><span class="sxs-lookup"><span data-stu-id="882d4-115">[Use the Add Client-Side Library dialog](#use-the-add-client-side-library-dialog)</span></span>
1. [<span data-ttu-id="882d4-116">LibMan マニフェスト ファイル エントリを手動で構成する</span><span class="sxs-lookup"><span data-stu-id="882d4-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="882d4-117">[クライアント側のライブラリを追加します] ダイアログを使用する</span><span class="sxs-lookup"><span data-stu-id="882d4-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="882d4-118">クライアント側ライブラリをインストールするには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="882d4-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="882d4-119">**ソリューション エクスプローラー**で、ファイルを追加するプロジェクト フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="882d4-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="882d4-120">**[追加]**  >  **[クライアント側のライブラリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="882d4-121">**[クライアント側のライブラリを追加します]** ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![[クライアント側のライブラリを追加します] ダイアログ](_static/add-library-dialog.png)

* <span data-ttu-id="882d4-123">**[プロバイダー]** ドロップ ダウンからライブラリ プロバイダーを選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="882d4-124">CDNJS が既定のプロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="882d4-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="882d4-125">**[ライブラリ]** テキスト ボックスに、フェッチするライブラリ名を入力します。</span><span class="sxs-lookup"><span data-stu-id="882d4-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="882d4-126">IntelliSense により、指定したテキストで始まるライブラリの一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="882d4-127">IntelliSense の一覧からライブラリを選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="882d4-128">ライブラリ名には、`@` 記号と、選択したプロバイダーによって認識される最新の安定バージョンがサフィックスとして付けられています。</span><span class="sxs-lookup"><span data-stu-id="882d4-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="882d4-129">含めるファイルを決定します。</span><span class="sxs-lookup"><span data-stu-id="882d4-129">Decide which files to include:</span></span>
  * <span data-ttu-id="882d4-130">すべてのライブラリ ファイルを含めるには、 **[Include all library files]\(すべてのライブラリ ファイルを含める\)** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="882d4-131">ライブラリのファイルのサブセットを含めるには、 **[Choose specific files]\(特定のファイルを選択する\)** ラジオ ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="882d4-132">ラジオ ボタンを選択すると、ファイル セレクター ツリーが有効になります。</span><span class="sxs-lookup"><span data-stu-id="882d4-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="882d4-133">ダウンロードするファイルの名前の左側にあるボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="882d4-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="882d4-134">**[ターゲットの場所]** テキスト ボックスで、ファイルを格納するプロジェクト フォルダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="882d4-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="882d4-135">各ライブラリを別々のフォルダーに保存することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="882d4-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="882d4-136">**[ターゲットの場所]** として提案されるフォルダーは、ダイアログを起動した場所に基づいています。</span><span class="sxs-lookup"><span data-stu-id="882d4-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="882d4-137">プロジェクト ルートから起動した場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="882d4-137">If launched from the project root:</span></span>
    * <span data-ttu-id="882d4-138">*wwwroot* が存在する場合は、*wwwroot/lib* が使用されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="882d4-139">*wwwroot* が存在しない場合は、*lib* が使用されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="882d4-140">プロジェクト フォルダーから起動した場合は、対応するフォルダー名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="882d4-141">提案されるフォルダーには、ライブラリ名がサフィックスとして付けられます。</span><span class="sxs-lookup"><span data-stu-id="882d4-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="882d4-142">次の表は、Razor Pages プロジェクトに jQuery をインストールするときに提案されるフォルダーを示しています。</span><span class="sxs-lookup"><span data-stu-id="882d4-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="882d4-143">起動場所</span><span class="sxs-lookup"><span data-stu-id="882d4-143">Launch location</span></span>                           |<span data-ttu-id="882d4-144">提案されるフォルダー</span><span class="sxs-lookup"><span data-stu-id="882d4-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="882d4-145">プロジェクト ルート (*wwwroot* が存在する場合)</span><span class="sxs-lookup"><span data-stu-id="882d4-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="882d4-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="882d4-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="882d4-147">プロジェクト ルート (*wwwroot* が存在しない場合)</span><span class="sxs-lookup"><span data-stu-id="882d4-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="882d4-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="882d4-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="882d4-149">プロジェクトの *Pages* フォルダー</span><span class="sxs-lookup"><span data-stu-id="882d4-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="882d4-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="882d4-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="882d4-151">**[インストール]** ボタンをクリックして、*libman. json* の構成に従ってファイルをダウンロードします。</span><span class="sxs-lookup"><span data-stu-id="882d4-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="882d4-152">インストールの詳細については、 **[出力]** ウィンドウの **[ライブラリ マネージャー]** フィードを確認してください。</span><span class="sxs-lookup"><span data-stu-id="882d4-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="882d4-153">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="882d4-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="882d4-154">LibMan マニフェスト ファイル エントリを手動で構成する</span><span class="sxs-lookup"><span data-stu-id="882d4-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="882d4-155">Visual Studio のすべての LibMan 操作は、プロジェクト ルートの LibMan マニフェスト (*libman. json*) のコンテンツに基づいています。</span><span class="sxs-lookup"><span data-stu-id="882d4-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="882d4-156">*libman. json* を手動で編集して、プロジェクトのライブラリ ファイルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="882d4-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="882d4-157">*libman. json* が保存されると、Visual Studio ではすべてのライブラリ ファイルが復元されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="882d4-158">編集のために *libman. json* を開くには、次のオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="882d4-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="882d4-159">**ソリューション エクスプローラー**で *libman. json* ファイルをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="882d4-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="882d4-160">**ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[クライアント側のライブラリを管理する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="882d4-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="882d4-161">**&#8224;**</span></span>
* <span data-ttu-id="882d4-162">Visual Studio の **[プロジェクト]** メニューで、 **[クライアント側のライブラリを管理する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="882d4-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="882d4-163">**&#8224;**</span></span>

<span data-ttu-id="882d4-164">**&#8224;** *libman. json* ファイルがプロジェクト ルートにまだ存在しない場合は、既定の項目テンプレート コンテンツを使用して作成されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="882d4-165">Visual Studio には、色付け、書式設定、IntelliSense、スキーマ検証などの豊富な JSON 編集サポートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="882d4-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="882d4-166">LibMan マニフェストの JSON スキーマは、[https://json.schemastore.org/libman](https://json.schemastore.org/libman) にあります。</span><span class="sxs-lookup"><span data-stu-id="882d4-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="882d4-167">次のマニフェスト ファイルを使用すると、LibMan では、`libraries` プロパティに定義されている構成に従ってファイルが取得されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="882d4-168">`libraries` 内で定義されているオブジェクト リテラルの説明を次に示します。</span><span class="sxs-lookup"><span data-stu-id="882d4-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="882d4-169">[jQuery](https://jquery.com/) バージョン 3.3.1 のサブセットは、CDNJS プロバイダーから取得されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="882d4-170">このサブセットは、`files` プロパティ &mdash; *jquery.min.js*、*jquery.js*、および *jquery.min.map* で定義されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="882d4-171">これらのファイルは、プロジェクトの *wwwroot/lib/jquery* フォルダーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="882d4-172">[ブートストラップ](https://getbootstrap.com/) バージョン 4.1.3 の全体が取得され、*wwwroot/lib/bootstrap* フォルダーに格納されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="882d4-173">オブジェクト リテラルの `provider` プロパティによって `defaultProvider` プロパティ値がオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="882d4-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="882d4-174">LibMan により、unpkg プロバイダーからブートストラップ ファイルが取得されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="882d4-175">[Lodash](https://lodash.com/) のサブセットは、組織内の管理機関によって承認されています。</span><span class="sxs-lookup"><span data-stu-id="882d4-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="882d4-176">*lodash.js* ファイルと *lodash.min.js* ファイルは、*C:\\temp\\lodash\\* のローカル ファイル システムから取得されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="882d4-177">これらのファイルは、プロジェクトの *wwwroot/lib/lodash* フォルダーにコピーされます。</span><span class="sxs-lookup"><span data-stu-id="882d4-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="882d4-178">LibMan では、各プロバイダーのライブラリごとに 1 つのバージョンのみがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="882d4-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="882d4-179">*libman. json* ファイルでは、特定のプロバイダーに対して同じライブラリ名を持つ 2 つのライブラリが含まれている場合、スキーマの検証に失敗します。</span><span class="sxs-lookup"><span data-stu-id="882d4-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="882d4-180">ライブラリ ファイルを復元する</span><span class="sxs-lookup"><span data-stu-id="882d4-180">Restore library files</span></span>

<span data-ttu-id="882d4-181">Visual Studio 内からライブラリ ファイルを復元するには、プロジェクト ルートに有効な *libman. json* ファイルが存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="882d4-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="882d4-182">復元されたファイルは、各ライブラリに指定された場所にあるプロジェクトに配置されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="882d4-183">ライブラリ ファイルは、ASP.NET Core プロジェクトで次の 2 つの方法で復元できます。</span><span class="sxs-lookup"><span data-stu-id="882d4-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="882d4-184">ビルド時にファイルを復元する</span><span class="sxs-lookup"><span data-stu-id="882d4-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="882d4-185">ファイルを手動で復元する</span><span class="sxs-lookup"><span data-stu-id="882d4-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="882d4-186">ビルド時にファイルを復元する</span><span class="sxs-lookup"><span data-stu-id="882d4-186">Restore files during build</span></span>

<span data-ttu-id="882d4-187">LibMan では、ビルド プロセスの一環として、定義済みのライブラリ ファイルを復元できます。</span><span class="sxs-lookup"><span data-stu-id="882d4-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="882d4-188">既定では、"*ビルド時の復元*" 動作は無効になっています。</span><span class="sxs-lookup"><span data-stu-id="882d4-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="882d4-189">ビルド時の復元動作を有効にしてテストするには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="882d4-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="882d4-190">**ソリューション エクスプローラー**で *[libman. json]* を右クリックし、コンテキスト メニューで **[ビルド時にクライアント側のライブラリの復元を有効にする]** をオンにします。</span><span class="sxs-lookup"><span data-stu-id="882d4-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="882d4-191">NuGet パッケージのインストールを求めるメッセージが表示されたら、 **[はい]** ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="882d4-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="882d4-192">プロジェクトに [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet パッケージが追加されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="882d4-193">プロジェクトをビルドして、LibMan ファイルの復元が実行されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="882d4-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="882d4-194">`Microsoft.Web.LibraryManager.Build` パッケージにより、プロジェクトのビルド操作中に LibMan を実行する MSBuild ターゲットが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="882d4-195">**[出力]** ウィンドウの **[ビルド]** フィードで LibMan アクティビティ ログを確認します。</span><span class="sxs-lookup"><span data-stu-id="882d4-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="882d4-196">ビルド時の復元動作が有効になっている場合は、 *[libman. json]* コンテキスト メニューに **[Disable Restore Client-Side Libraries on Build]\(ビルド時にクライアント側のライブラリの復元を無効にする\)** オプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="882d4-197">このオプションを選択すると、プロジェクト ファイルから `Microsoft.Web.LibraryManager.Build` パッケージ参照が削除されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="882d4-198">その結果、各ビルド時にクライアント側ライブラリは復元されなくなります。</span><span class="sxs-lookup"><span data-stu-id="882d4-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="882d4-199">ビルド時の復元設定に関係なく、*libman. json* コンテキスト メニューからいつでも手動で復元を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="882d4-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="882d4-200">詳細については、「[ファイルを手動で復元する](#restore-files-manually)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="882d4-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="882d4-201">ファイルを手動で復元する</span><span class="sxs-lookup"><span data-stu-id="882d4-201">Restore files manually</span></span>

<span data-ttu-id="882d4-202">ライブラリ ファイルを手動で復元するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="882d4-202">To manually restore library files:</span></span>

* <span data-ttu-id="882d4-203">ソリューション内のすべてのプロジェクトについて:</span><span class="sxs-lookup"><span data-stu-id="882d4-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="882d4-204">**ソリューション エクスプローラー**でソリューション名を右クリックします。</span><span class="sxs-lookup"><span data-stu-id="882d4-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="882d4-205">**[クライアント側のライブラリを復元する]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="882d4-206">特定のプロジェクトについて:</span><span class="sxs-lookup"><span data-stu-id="882d4-206">For a specific project:</span></span>
  * <span data-ttu-id="882d4-207">**ソリューション エクスプローラー**で *libman. json* ファイルを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="882d4-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="882d4-208">**[クライアント側のライブラリを復元する]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="882d4-209">復元操作の実行時には、次の処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="882d4-209">While the restore operation is running:</span></span>

* <span data-ttu-id="882d4-210">Visual Studio のステータス バーのタスク ステータス センター (TSC) アイコンがアニメーション化され、 *[復元操作が開始されました]* と表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="882d4-211">このアイコンをクリックすると、既知のバックグラウンド タスクを示すヒントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="882d4-212">ステータス バーと、 **[出力]** ウィンドウの **[ライブラリ マネージャー]** フィードに、メッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="882d4-213">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="882d4-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="882d4-214">ライブラリ ファイルを削除する</span><span class="sxs-lookup"><span data-stu-id="882d4-214">Delete library files</span></span>

<span data-ttu-id="882d4-215">Visual Studio で以前に復元されたライブラリ ファイルを削除するために "*クリーン*" 操作を実行するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="882d4-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="882d4-216">**ソリューション エクスプローラー**で *libman. json* ファイルを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="882d4-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="882d4-217">**[クライアント側のライブラリをクリーンアップする]** オプションを選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="882d4-218">ライブラリ ファイル以外が誤って削除されないように、クリーン操作ではディレクトリ全体が削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="882d4-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="882d4-219">以前の復元に含まれていたファイルのみが削除されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="882d4-220">クリーン操作の実行時には、次の処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="882d4-220">While the clean operation is running:</span></span>

* <span data-ttu-id="882d4-221">Visual Studio の TSC アイコンがアニメーション化され、 *[ライブラリのクリーン操作が開始されました]* と表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="882d4-222">このアイコンをクリックすると、既知のバックグラウンド タスクを示すヒントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="882d4-223">ステータス バーと、 **[出力]** ウィンドウの **[ライブラリ マネージャー]** フィードに、メッセージが送信されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="882d4-224">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="882d4-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="882d4-225">クリーン操作では、プロジェクトからファイルのみが削除されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="882d4-226">ライブラリ ファイルは、今後の復元操作で速やかに取得できるようにキャッシュに残っています。</span><span class="sxs-lookup"><span data-stu-id="882d4-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="882d4-227">ローカル コンピューターのキャッシュに格納されているライブラリ ファイルを管理するには、[LibMan CLI](xref:client-side/libman/libman-cli) を使用します。</span><span class="sxs-lookup"><span data-stu-id="882d4-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="882d4-228">ライブラリ ファイルをアンインストールする</span><span class="sxs-lookup"><span data-stu-id="882d4-228">Uninstall library files</span></span>

<span data-ttu-id="882d4-229">ライブラリ ファイルをアンインストールするには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="882d4-229">To uninstall library files:</span></span>

* <span data-ttu-id="882d4-230">*libman. json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="882d4-230">Open *libman.json*.</span></span>
* <span data-ttu-id="882d4-231">対応する `libraries` オブジェクト リテラル内にカレットを配置します。</span><span class="sxs-lookup"><span data-stu-id="882d4-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="882d4-232">左余白に表示される電球アイコンをクリックし、 **[\<library_name>@\<library_version> のアンインストール]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="882d4-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![ライブラリのアンインストール コンテキスト メニュー オプション](_static/uninstall-menu-option.png)

<span data-ttu-id="882d4-234">または、LibMan マニフェスト (*libman. json*) を手動で編集して保存することもできます。</span><span class="sxs-lookup"><span data-stu-id="882d4-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="882d4-235">ファイルが保存されると、[復元操作](#restore-library-files)が実行されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="882d4-236">*libman. json* で定義されなくなったライブラリ ファイルは、プロジェクトから削除されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="882d4-237">ライブラリ バージョンを更新する</span><span class="sxs-lookup"><span data-stu-id="882d4-237">Update library version</span></span>

<span data-ttu-id="882d4-238">更新されたライブラリ バージョンを確認するには、次の手順に従います。</span><span class="sxs-lookup"><span data-stu-id="882d4-238">To check for an updated library version:</span></span>

* <span data-ttu-id="882d4-239">*libman. json* を開きます。</span><span class="sxs-lookup"><span data-stu-id="882d4-239">Open *libman.json*.</span></span>
* <span data-ttu-id="882d4-240">対応する `libraries` オブジェクト リテラル内にカレットを配置します。</span><span class="sxs-lookup"><span data-stu-id="882d4-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="882d4-241">左余白に表示される電球アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="882d4-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="882d4-242">**[更新プログラムの確認]** にカーソルを合わせます。</span><span class="sxs-lookup"><span data-stu-id="882d4-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="882d4-243">LibMan により、インストールされているバージョンよりも新しいライブラリ バージョンがないか確認されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="882d4-244">次の結果が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="882d4-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="882d4-245">最新バージョンが既にインストールされている場合は、 **[更新プログラムが見つかりませんでした]** メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="882d4-246">まだインストールされていない場合は、最新の安定バージョンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-246">The latest stable version is displayed if not already installed.</span></span>

  ![[更新プログラムの確認] コンテキスト メニュー オプション](_static/update-menu-option.png)

* <span data-ttu-id="882d4-248">インストールされているバージョンよりも新しいプレリリースを使用できる場合は、プレリリースが表示されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="882d4-249">古いライブラリ バージョンにダウングレードするには、*libman. json* ファイルを手動で編集します。</span><span class="sxs-lookup"><span data-stu-id="882d4-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="882d4-250">ファイルが保存されると、LibMan [復元操作](#restore-library-files)によって次の処理が行われます。</span><span class="sxs-lookup"><span data-stu-id="882d4-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="882d4-251">以前のバージョンから冗長なファイルが削除されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="882d4-252">新しいバージョンから新しいファイルと更新済みのファイルが追加されます。</span><span class="sxs-lookup"><span data-stu-id="882d4-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="882d4-253">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="882d4-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="882d4-254">LibMan の GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="882d4-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
