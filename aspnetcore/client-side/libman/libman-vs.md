---
title: LibMan を Visual Studio で ASP.NET Core を使用します。
author: scottaddie
description: LibMan を Visual Studio を使用した ASP.NET Core プロジェクトで使用する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: ebfb405516d968bf5d5b8cff956a9892457027f2
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813457"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="5e71a-103">LibMan を Visual Studio で ASP.NET Core を使用します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="5e71a-104">作成者: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="5e71a-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="5e71a-105">Visual Studio はの組み込みサポート[LibMan](xref:client-side/libman/index)など、ASP.NET Core プロジェクトで。</span><span class="sxs-lookup"><span data-stu-id="5e71a-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="5e71a-106">構成とビルドで LibMan 復元操作の実行をサポートします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="5e71a-107">LibMan をトリガーするためのメニュー項目は、復元し、操作をクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="5e71a-108">ライブラリを検索して、ファイルをプロジェクトに追加する検索ダイアログ。</span><span class="sxs-lookup"><span data-stu-id="5e71a-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="5e71a-109">編集のサポート*libman.json*&mdash;LibMan マニフェスト ファイル。</span><span class="sxs-lookup"><span data-stu-id="5e71a-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="5e71a-110">[サンプル コードのダウンロードを表示または](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(ダウンロードする方法)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5e71a-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e71a-111">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="5e71a-111">Prerequisites</span></span>

* <span data-ttu-id="5e71a-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と **ASP.NET と Web 開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="5e71a-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="5e71a-113">ライブラリ ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-113">Add library files</span></span>

<span data-ttu-id="5e71a-114">ライブラリ ファイルは、2 つの方法で ASP.NET Core プロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="5e71a-115">クライアント側ライブラリを追加 ダイアログを使用します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="5e71a-116">LibMan マニフェスト ファイルのエントリを手動で構成します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="5e71a-117">クライアント側ライブラリを追加 ダイアログを使用します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="5e71a-118">クライアント側ライブラリをインストールする手順に従います。</span><span class="sxs-lookup"><span data-stu-id="5e71a-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="5e71a-119">**ソリューション エクスプ ローラー**ファイルを追加するプロジェクト フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="5e71a-120">選択**追加** > **クライアント側ライブラリ**します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="5e71a-121">**クライアント側ライブラリを追加**ダイアログが表示されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![クライアント側ライブラリを追加ダイアログ](_static/add-library-dialog.png)

* <span data-ttu-id="5e71a-123">ライブラリ プロバイダーの選択、**プロバイダー**ドロップダウンします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="5e71a-124">CDNJS は、既定のプロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="5e71a-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="5e71a-125">フェッチするライブラリの名前を入力、**ライブラリ**テキスト ボックス。</span><span class="sxs-lookup"><span data-stu-id="5e71a-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="5e71a-126">IntelliSense では、指定されたテキストで始まるライブラリの一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="5e71a-127">IntelliSense の一覧からライブラリを選択します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="5e71a-128">ライブラリ名が付くに注意してください、`@`記号と、選択したプロバイダーに既知の最新の安定バージョン。</span><span class="sxs-lookup"><span data-stu-id="5e71a-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="5e71a-129">含めるファイルを決定します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-129">Decide which files to include:</span></span>
  * <span data-ttu-id="5e71a-130">選択、**ライブラリのすべてのファイルを含める**ラジオ ボタンをすべてのライブラリのファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="5e71a-131">選択、**特定のファイルを選択**ラジオ ボタンは、ライブラリのファイルのサブセットが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="5e71a-132">ラジオ ボタンを選択すると、ファイルのセレクターのツリーが有効になります。</span><span class="sxs-lookup"><span data-stu-id="5e71a-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="5e71a-133">ダウンロードするファイル名の左側にあるボックスを確認します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="5e71a-134">内のファイルを格納するためのプロジェクト フォルダーを指定して、**ターゲットの場所**テキスト ボックス。</span><span class="sxs-lookup"><span data-stu-id="5e71a-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="5e71a-135">、推奨事項としては、別のフォルダーに各ライブラリを格納します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="5e71a-136">提案される**ターゲットの場所**フォルダーは、ダイアログの起動元の場所に基づきます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="5e71a-137">場合は、プロジェクトのルートから起動します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-137">If launched from the project root:</span></span>
    * <span data-ttu-id="5e71a-138">*wwwroot/lib*場合に使用*wwwroot*存在します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="5e71a-139">*lib*場合に使用*wwwroot*が存在しません。</span><span class="sxs-lookup"><span data-stu-id="5e71a-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="5e71a-140">プロジェクト フォルダーから起動する場合は、対応するフォルダー名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="5e71a-141">フォルダーの提案は、ライブラリ名サフィックスとして付けられます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="5e71a-142">次の表は、Razor ページ プロジェクトで jQuery をインストールするときに、フォルダーの推奨事項を示します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="5e71a-143">場所を起動します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-143">Launch location</span></span>                           |<span data-ttu-id="5e71a-144">推奨されるフォルダー</span><span class="sxs-lookup"><span data-stu-id="5e71a-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="5e71a-145">プロジェクトのルート (場合*wwwroot*が存在する)</span><span class="sxs-lookup"><span data-stu-id="5e71a-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="5e71a-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="5e71a-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="5e71a-147">プロジェクトのルート (場合*wwwroot*が存在しない)</span><span class="sxs-lookup"><span data-stu-id="5e71a-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="5e71a-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="5e71a-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="5e71a-149">*ページ*プロジェクト フォルダー</span><span class="sxs-lookup"><span data-stu-id="5e71a-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="5e71a-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="5e71a-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="5e71a-151">をクリックして、**インストール**ボタンで構成ごとのファイルをダウンロードする*libman.json*します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="5e71a-152">レビュー、**ライブラリ マネージャー**のフィード、**出力**インストールの詳細についてはウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="5e71a-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="5e71a-153">例:</span><span class="sxs-lookup"><span data-stu-id="5e71a-153">For example:</span></span>

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

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="5e71a-154">LibMan マニフェスト ファイルのエントリを手動で構成します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="5e71a-155">Visual Studio でのすべての LibMan 操作は、プロジェクトのルートの LibMan マニフェストの内容に基づきます (*libman.json*)。</span><span class="sxs-lookup"><span data-stu-id="5e71a-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="5e71a-156">手動で編集できます*libman.json*プロジェクトのライブラリ ファイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="5e71a-157">Visual Studio は、すべてのライブラリ ファイルを 1 回復元*libman.json*保存されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="5e71a-158">開くには*libman.json*編集に関しては、次のオプションが存在します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="5e71a-159">ダブルクリックして、 *libman.json*ファイル**ソリューション エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="5e71a-160">プロジェクトを右クリックして**ソリューション エクスプ ローラー**選択**クライアント側ライブラリを管理**します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="5e71a-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="5e71a-161">**&#8224;**</span></span>
* <span data-ttu-id="5e71a-162">選択**クライアント側ライブラリを管理**Visual Studio から**プロジェクト**メニュー。</span><span class="sxs-lookup"><span data-stu-id="5e71a-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="5e71a-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="5e71a-163">**&#8224;**</span></span>

<span data-ttu-id="5e71a-164">**&#8224;** 場合、 *libman.json*プロジェクトのルートにファイルが存在しない、既定の項目テンプレートの内容で作成されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="5e71a-165">Visual Studio では、豊富な JSON 編集の色付けなどのサポート、書式設定、IntelliSense、およびスキーマ検証を提供します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="5e71a-166">LibMan マニフェストの JSON スキーマで見つかった[ https://json.schemastore.org/libman](https://json.schemastore.org/libman)します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="5e71a-167">LibMan がで定義されている構成ごとにファイルを取得する次のマニフェスト ファイルで、`libraries`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="5e71a-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="5e71a-168">内で定義されているオブジェクト リテラルの説明`libraries`に従います。</span><span class="sxs-lookup"><span data-stu-id="5e71a-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="5e71a-169">サブセットの[jQuery](https://jquery.com/)バージョン 3.3.1 が CDNJS プロバイダーから取得します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="5e71a-170">サブセットが定義されている、`files`プロパティ&mdash;*jquery.min.js*、 *jquery.js*、および*jquery.min.map*します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="5e71a-171">ファイルはプロジェクトの配置*wwwroot/ライブラリ/jquery*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="5e71a-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="5e71a-172">全体[ブートス トラップ](https://getbootstrap.com/)4.1.3 のバージョンが取得され、配置、*ブートス トラップwwwroot/ライブラリ/* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="5e71a-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="5e71a-173">オブジェクト リテラルの`provider`プロパティのオーバーライド、`defaultProvider`プロパティの値。</span><span class="sxs-lookup"><span data-stu-id="5e71a-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="5e71a-174">LibMan は、unpkg プロバイダーからブートス トラップ ファイルを取得します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="5e71a-175">サブセットの[Lodash](https://lodash.com/)組織内で管理本体によって承認されました。</span><span class="sxs-lookup"><span data-stu-id="5e71a-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="5e71a-176">*Lodash.js*と*lodash.min.js*からローカル ファイル システムのファイルが取得*c:\\temp\\lodash\\* します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="5e71a-177">ファイルをプロジェクトのコピーは*wwwroot/ライブラリ/lodash*フォルダー。</span><span class="sxs-lookup"><span data-stu-id="5e71a-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="5e71a-178">LibMan は、各プロバイダーから各ライブラリの 1 つのバージョンのみをサポートします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="5e71a-179">*Libman.json*ファイル指定されたプロバイダーの同じライブラリ名を持つ 2 つのライブラリが含まれているスキーマの検証は失敗します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="5e71a-180">ライブラリ ファイルを復元します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-180">Restore library files</span></span>

<span data-ttu-id="5e71a-181">Visual Studio 内からのライブラリ ファイルを復元する必要があります、有効な*libman.json*プロジェクト ルートにあるファイル。</span><span class="sxs-lookup"><span data-stu-id="5e71a-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="5e71a-182">復元されたファイルは、各ライブラリの指定された場所にプロジェクトに配置されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="5e71a-183">2 つの方法で ASP.NET Core プロジェクトでは、ライブラリ ファイルを復元できます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="5e71a-184">ビルド中にファイルを復元します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="5e71a-185">ファイルを手動で復元します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="5e71a-186">ビルド中にファイルを復元します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-186">Restore files during build</span></span>

<span data-ttu-id="5e71a-187">LibMan は、ビルド プロセスの一部として定義されているライブラリ ファイルを復元できます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="5e71a-188">既定で、*ビルド時の復元*動作を無効にします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="5e71a-189">有効にして、ビルド時の復元の動作をテストします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="5e71a-190">右クリック*libman.json*で**ソリューション エクスプ ローラー**選択と**復元クライアント側のライブラリでビルドを有効にする**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="5e71a-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="5e71a-191">をクリックして、**はい**NuGet パッケージをインストールするように求められたらボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="5e71a-192">[Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet パッケージがプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="5e71a-193">LibMan ファイル復元が発生することを確認します。 プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="5e71a-194">`Microsoft.Web.LibraryManager.Build`パッケージは、プロジェクトのビルド操作中に LibMan を実行する MSBuild ターゲットを挿入します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="5e71a-195">レビュー、**ビルド**のフィード、**出力**LibMan アクティビティ ログのウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="5e71a-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

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

<span data-ttu-id="5e71a-196">ビルド時の復元動作が有効にすると、 *libman.json*コンテキスト メニューが表示されます、**復元クライアント側のライブラリでビルドを無効にする**オプション。</span><span class="sxs-lookup"><span data-stu-id="5e71a-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="5e71a-197">このオプションを選択すると削除、`Microsoft.Web.LibraryManager.Build`パッケージ、プロジェクト ファイルから参照します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="5e71a-198">その結果、クライアント側ライブラリは不要になった各ビルドで復元されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="5e71a-199">ビルド時の復元の設定に関係なく手動で復元できますから、いつでも、 *libman.json*コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="5e71a-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="5e71a-200">詳細については、次を参照してください。[ファイルを手動で復元](#restore-files-manually)します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="5e71a-201">ファイルを手動で復元します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-201">Restore files manually</span></span>

<span data-ttu-id="5e71a-202">ライブラリ ファイルを手動で復元するには。</span><span class="sxs-lookup"><span data-stu-id="5e71a-202">To manually restore library files:</span></span>

* <span data-ttu-id="5e71a-203">ソリューション内のすべてのプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5e71a-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="5e71a-204">ソリューション名を右クリックして**ソリューション エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="5e71a-205">選択、**クライアント側ライブラリを復元**オプション。</span><span class="sxs-lookup"><span data-stu-id="5e71a-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="5e71a-206">特定のプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="5e71a-206">For a specific project:</span></span>
  * <span data-ttu-id="5e71a-207">右クリックし、 *libman.json*ファイル**ソリューション エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="5e71a-208">選択、**クライアント側ライブラリを復元**オプション。</span><span class="sxs-lookup"><span data-stu-id="5e71a-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="5e71a-209">復元操作の実行: 中</span><span class="sxs-lookup"><span data-stu-id="5e71a-209">While the restore operation is running:</span></span>

* <span data-ttu-id="5e71a-210">Visual Studio のステータス バーにタスク ステータス センター (TSC) アイコンがアニメーション化して、読み取りは*復元操作が開始された*します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="5e71a-211">アイコンをクリックすると、既知のバック グラウンド タスクを一覧表示するツールヒントが開きます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="5e71a-212">メッセージは、ステータス バーに送信して、**ライブラリ マネージャー**のフィード、**出力**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="5e71a-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="5e71a-213">例:</span><span class="sxs-lookup"><span data-stu-id="5e71a-213">For example:</span></span>

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

## <a name="delete-library-files"></a><span data-ttu-id="5e71a-214">ライブラリ ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-214">Delete library files</span></span>

<span data-ttu-id="5e71a-215">実行する、*クリーン*操作で、Visual Studio で以前に復元されたライブラリ ファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="5e71a-216">右クリックし、 *libman.json*ファイル**ソリューション エクスプ ローラー**します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="5e71a-217">選択、**クライアント側ライブラリをクリーン**オプション。</span><span class="sxs-lookup"><span data-stu-id="5e71a-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="5e71a-218">ライブラリ以外のファイルの意図しない削除を防ぐためには、クリーンの操作は、ディレクトリ全体を削除されません。</span><span class="sxs-lookup"><span data-stu-id="5e71a-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="5e71a-219">前の復元に含まれていたファイルを削除するだけです。</span><span class="sxs-lookup"><span data-stu-id="5e71a-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="5e71a-220">クリーン操作が実行されている場合: 中</span><span class="sxs-lookup"><span data-stu-id="5e71a-220">While the clean operation is running:</span></span>

* <span data-ttu-id="5e71a-221">Visual Studio のステータス バーに TSC アイコンは、アニメーション化して、読み取りは*クライアント ライブラリの操作を開始*します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="5e71a-222">アイコンをクリックすると、既知のバック グラウンド タスクを一覧表示するツールヒントが開きます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="5e71a-223">ステータス バーに送信されるメッセージと**ライブラリ マネージャー**のフィード、**出力**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="5e71a-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="5e71a-224">例えば:</span><span class="sxs-lookup"><span data-stu-id="5e71a-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="5e71a-225">クリーン操作では、ファイルがプロジェクトからのみ削除します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="5e71a-226">ライブラリ ファイルは、将来の復元操作に高速な検索のキャッシュに保存します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="5e71a-227">ローカル コンピューターのキャッシュに格納されているライブラリ ファイルを管理するには、使用、 [LibMan CLI](xref:client-side/libman/libman-cli)します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="5e71a-228">ライブラリ ファイルをアンインストールします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-228">Uninstall library files</span></span>

<span data-ttu-id="5e71a-229">ライブラリ ファイルをアンインストールするには。</span><span class="sxs-lookup"><span data-stu-id="5e71a-229">To uninstall library files:</span></span>

* <span data-ttu-id="5e71a-230">開いている*libman.json*します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-230">Open *libman.json*.</span></span>
* <span data-ttu-id="5e71a-231">対応の内側にキャレットを配置`libraries`オブジェクト リテラル。</span><span class="sxs-lookup"><span data-stu-id="5e71a-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="5e71a-232">左の余白に表示される電球アイコンをクリックし、選択**アンインストール\<library_name > @\<library_version >** :</span><span class="sxs-lookup"><span data-stu-id="5e71a-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![ライブラリのコンテキスト メニュー オプションをアンインストールします。](_static/uninstall-menu-option.png)

<span data-ttu-id="5e71a-234">または、手動で編集して保存できます LibMan マニフェスト (*libman.json*)。</span><span class="sxs-lookup"><span data-stu-id="5e71a-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="5e71a-235">[復元操作に](#restore-library-files)ファイルを保存するときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="5e71a-236">ライブラリ ファイルで定義されている不要になった*libman.json*プロジェクトから削除されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="5e71a-237">ライブラリのバージョンを更新します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-237">Update library version</span></span>

<span data-ttu-id="5e71a-238">ライブラリの更新バージョンを確認します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-238">To check for an updated library version:</span></span>

* <span data-ttu-id="5e71a-239">開いている*libman.json*します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-239">Open *libman.json*.</span></span>
* <span data-ttu-id="5e71a-240">対応の内側にキャレットを配置`libraries`オブジェクト リテラル。</span><span class="sxs-lookup"><span data-stu-id="5e71a-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="5e71a-241">左余白に表示される電球アイコンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="5e71a-242">ポインターを合わせる**更新プログラムの確認**します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="5e71a-243">LibMan は、インストールされているバージョンより新しいバージョンのライブラリをチェックします。</span><span class="sxs-lookup"><span data-stu-id="5e71a-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="5e71a-244">結果は次が発生することができます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="5e71a-245">A**なしの更新が見つかりました**最新バージョンが既にインストールされている場合、メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="5e71a-246">最新の安定バージョンが表示されていない場合は。</span><span class="sxs-lookup"><span data-stu-id="5e71a-246">The latest stable version is displayed if not already installed.</span></span>

  ![コンテキスト メニュー オプションの更新プログラムを確認します。](_static/update-menu-option.png)

* <span data-ttu-id="5e71a-248">インストールされているバージョンより新しいのプレリリース版を使用できる場合は、プレリリース版が表示されます。</span><span class="sxs-lookup"><span data-stu-id="5e71a-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="5e71a-249">ライブラリの以前のバージョンにダウン グレードする手動で編集、 *libman.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="5e71a-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="5e71a-250">保存すると、ファイルは、LibMan[復元操作に](#restore-library-files):</span><span class="sxs-lookup"><span data-stu-id="5e71a-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="5e71a-251">以前のバージョンからの余分なファイルを削除します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="5e71a-252">新しいバージョンから、新規および更新されたファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="5e71a-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e71a-253">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="5e71a-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="5e71a-254">LibMan の GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="5e71a-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
