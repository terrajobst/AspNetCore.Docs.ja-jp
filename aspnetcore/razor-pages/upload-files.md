---
title: ASP.NET Core で Razor ページにファイルをアップロードする
author: guardrex
description: FileUpload クラスを使用して ASP.NET Core で Razor ページにファイルをアップロードする方法について説明します。
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 11/10/2018
ms.custom: mvc, seodec18
uid: razor-pages/upload-files
ms.openlocfilehash: 14d10424951e8ec3c7909d001c6f86e5fcb45d26
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815044"
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="fd226-103">ASP.NET Core で Razor ページにファイルをアップロードする</span><span class="sxs-lookup"><span data-stu-id="fd226-103">Upload files to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="fd226-104">作成者: [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fd226-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fd226-105">このトピックでのビルド時に、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample)で<xref:tutorials/razor-pages/razor-pages-start>します。</span><span class="sxs-lookup"><span data-stu-id="fd226-105">This topic builds upon the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) in <xref:tutorials/razor-pages/razor-pages-start>.</span></span>

<span data-ttu-id="fd226-106">このトピックでは、これは小さいファイルのアップロードも単純なモデル バインディングを使用して、ファイルをアップロードする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="fd226-106">This topic shows how to use simple model binding to upload files, which works well for uploading small files.</span></span> <span data-ttu-id="fd226-107">大きなファイルをストリーミングする方法については、「[Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming)」 (ストリーミングでの大きいファイルのアップロード) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd226-107">For information on streaming large files, see [Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span></span>

<span data-ttu-id="fd226-108">以下の手順では、映画スケジュール ファイルのアップロード機能をサンプル アプリに追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-108">In the following steps, a movie schedule file upload feature is added to the sample app.</span></span> <span data-ttu-id="fd226-109">映画スケジュールを表すのが `Schedule` クラスです。</span><span class="sxs-lookup"><span data-stu-id="fd226-109">A movie schedule is represented by a `Schedule` class.</span></span> <span data-ttu-id="fd226-110">このクラスには、2 つのバージョンのスケジュールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="fd226-110">The class includes two versions of the schedule.</span></span> <span data-ttu-id="fd226-111">1 つのバージョンは顧客に提供される `PublicSchedule` です。</span><span class="sxs-lookup"><span data-stu-id="fd226-111">One version is provided to customers, `PublicSchedule`.</span></span> <span data-ttu-id="fd226-112">もう 1 つのバージョンは社員が利用する `PrivateSchedule` です。</span><span class="sxs-lookup"><span data-stu-id="fd226-112">The other version is used for company employees, `PrivateSchedule`.</span></span> <span data-ttu-id="fd226-113">いずれも別個のファイルとしてアップロードされます。</span><span class="sxs-lookup"><span data-stu-id="fd226-113">Each version is uploaded as a separate file.</span></span> <span data-ttu-id="fd226-114">このチュートリアルでは、POST が 1 つのページからサーバーに 2 つのファイルをアップロードします。</span><span class="sxs-lookup"><span data-stu-id="fd226-114">The tutorial demonstrates how to perform two file uploads from a page with a single POST to the server.</span></span>

<span data-ttu-id="fd226-115">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fd226-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="fd226-116">セキュリティの考慮事項</span><span class="sxs-lookup"><span data-stu-id="fd226-116">Security considerations</span></span>

<span data-ttu-id="fd226-117">サーバーにファイルをアップロードする機能をユーザーに提供するときには、十分に注意してください。</span><span class="sxs-lookup"><span data-stu-id="fd226-117">Caution must be taken when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="fd226-118">攻撃者が[サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)などの攻撃をシステムに実行する場合があります。</span><span class="sxs-lookup"><span data-stu-id="fd226-118">Attackers may execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) and other attacks on a system.</span></span> <span data-ttu-id="fd226-119">攻撃の成功の可能性を少なくするセキュリティ手順には、次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="fd226-119">Some security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="fd226-120">システムの専用のファイル アップロード領域にファイルをアップロードする。これによりアップロードしたコンテンツにセキュリティ対策を適用しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="fd226-120">Upload files to a dedicated file upload area on the system, which makes it easier to impose security measures on uploaded content.</span></span> <span data-ttu-id="fd226-121">ファイルのアップロードを許可する際に、実行アクセス許可がアップロード先で無効になっていることを確認する。</span><span class="sxs-lookup"><span data-stu-id="fd226-121">When permitting file uploads, make sure that execute permissions are disabled on the upload location.</span></span>
* <span data-ttu-id="fd226-122">ユーザー入力やアップロードされたファイルのファイル名からではなく、アプリで決定された安全なファイル名を使用する。</span><span class="sxs-lookup"><span data-stu-id="fd226-122">Use a safe file name determined by the app, not from user input or the file name of the uploaded file.</span></span>
* <span data-ttu-id="fd226-123">承認されている特定のファイル拡張子のみを許可する。</span><span class="sxs-lookup"><span data-stu-id="fd226-123">Only allow a specific set of approved file extensions.</span></span>
* <span data-ttu-id="fd226-124">クライアント側のチェックがサーバーで実行されることを確認する。</span><span class="sxs-lookup"><span data-stu-id="fd226-124">Verify client-side checks are performed on the server.</span></span> <span data-ttu-id="fd226-125">クライアント側のチェックは簡単に回避できます。</span><span class="sxs-lookup"><span data-stu-id="fd226-125">Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="fd226-126">アップロードのサイズを確認し、アップロードのサイズが予想よりも大きくならないようにする。</span><span class="sxs-lookup"><span data-stu-id="fd226-126">Check the size of the upload and prevent larger uploads than expected.</span></span>
* <span data-ttu-id="fd226-127">アップロードされたコンテンツに対してウイルス/マルウェア スキャナーを実行する。</span><span class="sxs-lookup"><span data-stu-id="fd226-127">Run a virus/malware scanner on uploaded content.</span></span>

> [!WARNING]
> <span data-ttu-id="fd226-128">システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。</span><span class="sxs-lookup"><span data-stu-id="fd226-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
> * <span data-ttu-id="fd226-129">システムを完全に乗っ取る。</span><span class="sxs-lookup"><span data-stu-id="fd226-129">Completely takeover a system.</span></span>
> * <span data-ttu-id="fd226-130">システムが完全に失敗したという結果を、システムにオーバーロードする。</span><span class="sxs-lookup"><span data-stu-id="fd226-130">Overload a system with the result that the system completely fails.</span></span>
> * <span data-ttu-id="fd226-131">ユーザーまたはシステムのデータを破壊する。</span><span class="sxs-lookup"><span data-stu-id="fd226-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="fd226-132">パブリック インターフェイスに落書きする。</span><span class="sxs-lookup"><span data-stu-id="fd226-132">Apply graffiti to a public interface.</span></span>

## <a name="add-a-fileupload-class"></a><span data-ttu-id="fd226-133">FileUpload クラスを追加する</span><span class="sxs-lookup"><span data-stu-id="fd226-133">Add a FileUpload class</span></span>

<span data-ttu-id="fd226-134">ファイル アップロードのペアを処理する Razor ページを作成します。</span><span class="sxs-lookup"><span data-stu-id="fd226-134">Create a Razor Page to handle a pair of file uploads.</span></span> <span data-ttu-id="fd226-135">`FileUpload` クラスを追加して、スケジュール データを取得するページにバインドします。</span><span class="sxs-lookup"><span data-stu-id="fd226-135">Add a `FileUpload` class, which is bound to the page to obtain the schedule data.</span></span> <span data-ttu-id="fd226-136">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="fd226-136">Right click the *Models* folder.</span></span> <span data-ttu-id="fd226-137">**[追加]** 、 **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="fd226-137">Select **Add** > **Class**.</span></span> <span data-ttu-id="fd226-138">クラスに **FileUpload** という名前を付けて、次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-138">Name the class **FileUpload** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

<span data-ttu-id="fd226-139">このクラスには、スケジュールのタイトルのプロパティと 2 つあるスケジュールのバージョンのそれぞれのプロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="fd226-139">The class has a property for the schedule's title and a property for each of the two versions of the schedule.</span></span> <span data-ttu-id="fd226-140">3 つのプロパティはすべて必須であり、タイトルの長さは 3 ～ 60 文字にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd226-140">All three properties are required, and the title must be 3-60 characters long.</span></span>

## <a name="add-a-helper-method-to-upload-files"></a><span data-ttu-id="fd226-141">ファイルをアップロードするヘルパー メソッドを追加する</span><span class="sxs-lookup"><span data-stu-id="fd226-141">Add a helper method to upload files</span></span>

<span data-ttu-id="fd226-142">アップロード済みのスケジュール ファイルを処理するコード重複を回避するために、静的なヘルパー メソッドを先に追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-142">To avoid code duplication for processing uploaded schedule files, add a static helper method first.</span></span> <span data-ttu-id="fd226-143">アプリで *Utilities* フォルダーを作成し、次のコンテンツを含む *FileHelpers.cs* ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-143">Create a *Utilities* folder in the app and add a *FileHelpers.cs* file with the following content.</span></span> <span data-ttu-id="fd226-144">ヘルパー メソッドの `ProcessFormFile` は [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) と [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) を受け取り、ファイルのサイズとコンテンツが含まれる文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="fd226-144">The helper method, `ProcessFormFile`, takes an [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) and returns a string containing the file's size and content.</span></span> <span data-ttu-id="fd226-145">コンテンツの種類と長さが確認されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-145">The content type and length are checked.</span></span> <span data-ttu-id="fd226-146">ファイルが検証チェックに合格しなければ、エラーが `ModelState` に追加されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-146">If the file doesn't pass a validation check, an error is added to the `ModelState`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a><span data-ttu-id="fd226-147">ファイルをディスクに保存する</span><span class="sxs-lookup"><span data-stu-id="fd226-147">Save the file to disk</span></span>

<span data-ttu-id="fd226-148">このサンプル アプリでは、アップロードされたファイルはデータベース フィールドに保存されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-148">The sample app saves uploaded files into database fields.</span></span> <span data-ttu-id="fd226-149">ファイルをディスクに保存するには、[FileStream](/dotnet/api/system.io.filestream) を使用します。</span><span class="sxs-lookup"><span data-stu-id="fd226-149">To save a file to disk, use a [FileStream](/dotnet/api/system.io.filestream).</span></span> <span data-ttu-id="fd226-150">次の例では、`FileUpload.UploadPublicSchedule` によって保持されているファイルは、`OnPostAsync` メソッドの `FileStream` にコピーされます。</span><span class="sxs-lookup"><span data-stu-id="fd226-150">The following example copies a file held by `FileUpload.UploadPublicSchedule` to a `FileStream` in an `OnPostAsync` method.</span></span> <span data-ttu-id="fd226-151">`FileStream` は、指定されている `<PATH-AND-FILE-NAME>` にあるディスクにファイルを書き込みます。</span><span class="sxs-lookup"><span data-stu-id="fd226-151">The `FileStream` writes the file to disk at the `<PATH-AND-FILE-NAME>` provided:</span></span>

```csharp
public async Task<IActionResult> OnPostAsync()
{
    // Perform an initial check to catch FileUpload class attribute violations.
    if (!ModelState.IsValid)
    {
        return Page();
    }

    var filePath = "<PATH-AND-FILE-NAME>";

    using (var fileStream = new FileStream(filePath, FileMode.Create))
    {
        await FileUpload.UploadPublicSchedule.CopyToAsync(fileStream);
    }

    return RedirectToPage("./Index");
}
```

<span data-ttu-id="fd226-152">ワーカー プロセスには、`filePath` によって指定された場所への書き込みアクセス許可が必要です。</span><span class="sxs-lookup"><span data-stu-id="fd226-152">The worker process must have write permissions to the location specified by `filePath`.</span></span>

> [!NOTE]
> <span data-ttu-id="fd226-153">`filePath` にはファイル名が*必要*です。</span><span class="sxs-lookup"><span data-stu-id="fd226-153">The `filePath` *must* include the file name.</span></span> <span data-ttu-id="fd226-154">ファイル名が指定されていない場合、実行時に [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd226-154">If the file name isn't provided, an [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) is thrown at runtime.</span></span>

> [!WARNING]
> <span data-ttu-id="fd226-155">アプリと同じディレクトリ ツリーには、アップロードしたファイルを保持しないでください。</span><span class="sxs-lookup"><span data-stu-id="fd226-155">Never persist uploaded files in the same directory tree as the app.</span></span>
>
> <span data-ttu-id="fd226-156">このコード例には、悪意のあるファイルのアップロードに対する、サーバー側での保護はありません。</span><span class="sxs-lookup"><span data-stu-id="fd226-156">The code sample provides no server-side protection against malicious file uploads.</span></span> <span data-ttu-id="fd226-157">ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd226-157">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="fd226-158">Unrestricted File Upload (ファイルの無制限のアップロード)</span><span class="sxs-lookup"><span data-stu-id="fd226-158">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="fd226-159">Azure のセキュリティ:ユーザーからのファイルを受け入れるときに、適切なコントロールが配置には必ず</span><span class="sxs-lookup"><span data-stu-id="fd226-159">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a><span data-ttu-id="fd226-160">Azure Blob ストレージにファイルを保存する</span><span class="sxs-lookup"><span data-stu-id="fd226-160">Save the file to Azure Blob Storage</span></span>

<span data-ttu-id="fd226-161">ファイルの内容を Azure Blob ストレージにアップロードする方法については、「[.NET を使用して Azure Blob Storage を使用する](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd226-161">To upload file content to Azure Blob Storage, see [Get started with Azure Blob Storage using .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span></span> <span data-ttu-id="fd226-162">このトピックでは [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) を使用して [FileStream](/dotnet/api/system.io.filestream) を Blob ストレージに保存する方法を説明しています。</span><span class="sxs-lookup"><span data-stu-id="fd226-162">The topic demonstrates how to use [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) to save a [FileStream](/dotnet/api/system.io.filestream) to blob storage.</span></span>

## <a name="add-the-schedule-class"></a><span data-ttu-id="fd226-163">Schedule クラスを追加する</span><span class="sxs-lookup"><span data-stu-id="fd226-163">Add the Schedule class</span></span>

<span data-ttu-id="fd226-164">*Models* フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="fd226-164">Right click the *Models* folder.</span></span> <span data-ttu-id="fd226-165">**[追加]** 、 **[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="fd226-165">Select **Add** > **Class**.</span></span> <span data-ttu-id="fd226-166">クラスに **Schedule** という名前を付けて、次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-166">Name the class **Schedule** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

<span data-ttu-id="fd226-167">このクラスは `Display` 属性と `DisplayFormat` 属性を使用します。この 2 つの属性は、スケジュール データがレンダリングされるとき、わかりやすい名前を生成し、書式を設定します。</span><span class="sxs-lookup"><span data-stu-id="fd226-167">The class uses `Display` and `DisplayFormat` attributes, which produce friendly titles and formatting when the schedule data is rendered.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a><span data-ttu-id="fd226-168">RazorPagesMovieContext を更新する</span><span class="sxs-lookup"><span data-stu-id="fd226-168">Update the RazorPagesMovieContext</span></span>

<span data-ttu-id="fd226-169">スケジュールに対し、`RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) で `DbSet` を指定します。</span><span class="sxs-lookup"><span data-stu-id="fd226-169">Specify a `DbSet` in the `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a><span data-ttu-id="fd226-170">MovieContext を更新する</span><span class="sxs-lookup"><span data-stu-id="fd226-170">Update the MovieContext</span></span>

<span data-ttu-id="fd226-171">このスケジュールは、`MovieContext` (*Models/MovieContext.cs*) で `DbSet` を指定します。</span><span class="sxs-lookup"><span data-stu-id="fd226-171">Specify a `DbSet` in the `MovieContext` (*Models/MovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a><span data-ttu-id="fd226-172">Schedule テーブルをデータベースに追加する</span><span class="sxs-lookup"><span data-stu-id="fd226-172">Add the Schedule table to the database</span></span>

<span data-ttu-id="fd226-173">パッケージ マネージャー コンソール (PMC) を開きます。**ツール** > **NuGet パッケージ マネージャー** > **パッケージ マネージャー コンソール**します。</span><span class="sxs-lookup"><span data-stu-id="fd226-173">Open the Package Manger Console (PMC): **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

![PMC メニュー](upload-files/_static/pmc.png)

<span data-ttu-id="fd226-175">PMC で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="fd226-175">In the PMC, execute the following commands.</span></span> <span data-ttu-id="fd226-176">コマンドによって `Schedule` テーブルがデータベースに追加されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-176">These commands add a `Schedule` table to the database:</span></span>

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a><span data-ttu-id="fd226-177">ファイル アップロード Razor ページを追加する</span><span class="sxs-lookup"><span data-stu-id="fd226-177">Add a file upload Razor Page</span></span>

<span data-ttu-id="fd226-178">*Pages* フォルダーで、*Schedules* フォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="fd226-178">In the *Pages* folder, create a *Schedules* folder.</span></span> <span data-ttu-id="fd226-179">*Schedules* フォルダーで、*Index.cshtml* という名前のページを作成します。次のコンテンツでスケジュールをアップロードするためのページです。</span><span class="sxs-lookup"><span data-stu-id="fd226-179">In the *Schedules* folder, create a page named *Index.cshtml* for uploading a schedule with the following content:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

<span data-ttu-id="fd226-180">各フォーム グループには、 **\<label>** が含まれます。これは各クラス プロパティの名前を表示します。</span><span class="sxs-lookup"><span data-stu-id="fd226-180">Each form group includes a **\<label>** that displays the name of each class property.</span></span> <span data-ttu-id="fd226-181">`FileUpload` モデルの `Display` 属性は、ラベルの表示値を与えます。</span><span class="sxs-lookup"><span data-stu-id="fd226-181">The `Display` attributes in the `FileUpload` model provide the display values for the labels.</span></span> <span data-ttu-id="fd226-182">たとえば、`UploadPublicSchedule` プロパティの表示名は `[Display(Name="Public Schedule")]` で設定されます。そのため、フォームがレンダリングされると、ラベルに "Public Schedule" と表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-182">For example, the `UploadPublicSchedule` property's display name is set with `[Display(Name="Public Schedule")]` and thus displays "Public Schedule" in the label when the form renders.</span></span>

<span data-ttu-id="fd226-183">各フォーム グループには、検証 **\<span>** が含まれます。</span><span class="sxs-lookup"><span data-stu-id="fd226-183">Each form group includes a validation **\<span>**.</span></span> <span data-ttu-id="fd226-184">ユーザーの入力が `FileUpload` クラスに設定されているプロパティ属性の要求を持たさない場合、あるいは `ProcessFormFile` メソッド ファイルの検証チェックで不合格になった場合、モデルは検証に失敗します。</span><span class="sxs-lookup"><span data-stu-id="fd226-184">If the user's input fails to meet the property attributes set in the `FileUpload` class or if any of the `ProcessFormFile` method file validation checks fail, the model fails to validate.</span></span> <span data-ttu-id="fd226-185">モデルが検証に失敗すると、ヒントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-185">When model validation fails, a helpful validation message is rendered to the user.</span></span> <span data-ttu-id="fd226-186">たとえば、`Title` プロパティであれば、`[Required]` や `[StringLength(60, MinimumLength = 3)]` という注釈が表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-186">For example, the `Title` property is annotated with `[Required]` and `[StringLength(60, MinimumLength = 3)]`.</span></span> <span data-ttu-id="fd226-187">ユーザーがタイトルを入力しないと、値が必須であるというメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-187">If the user fails to supply a title, they receive a message indicating that a value is required.</span></span> <span data-ttu-id="fd226-188">ユーザーが 3 文字より少ないか、60 文字より多い値を入力した場合、値の長さが正しくないというメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-188">If the user enters a value less than three characters or more than sixty characters, they receive a message indicating that the value has an incorrect length.</span></span> <span data-ttu-id="fd226-189">コンテンツのないファイルが指定された場合、ファイルが空であるというメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-189">If a file is provided that has no content, a message appears indicating that the file is empty.</span></span>

## <a name="add-the-page-model"></a><span data-ttu-id="fd226-190">ページ モデルの追加</span><span class="sxs-lookup"><span data-stu-id="fd226-190">Add the page model</span></span>

<span data-ttu-id="fd226-191">*Schedules* フォルダーにページ モデル (*Index.cshtml.cs*) を追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-191">Add the page model (*Index.cshtml.cs*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

<span data-ttu-id="fd226-192">ページ モデル (*Index.cshtml.cs* の `IndexModel`) は `FileUpload` クラスをバインドします。</span><span class="sxs-lookup"><span data-stu-id="fd226-192">The page model (`IndexModel` in *Index.cshtml.cs*) binds the `FileUpload` class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="fd226-193">このモデルはまた、スケジュールの一覧 (`IList<Schedule>`) を利用し、ページのデータベースに保存されているスケジュールを表示します。</span><span class="sxs-lookup"><span data-stu-id="fd226-193">The model also uses a list of the schedules (`IList<Schedule>`) to display the schedules stored in the database on the page:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="fd226-194">ページが `OnGetAsync` で読み込まれると、データベースから `Schedules` が入力され、読み込まれたスケジュールの HTML テーブルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-194">When the page loads with `OnGetAsync`, `Schedules` is populated from the database and used to generate an HTML table of loaded schedules:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="fd226-195">フォームがサーバーに投稿されると、`ModelState` がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="fd226-195">When the form is posted to the server, the `ModelState` is checked.</span></span> <span data-ttu-id="fd226-196">無効な場合、`Schedule` が再ビルドされます。ページがレンダリングされ、ページ検証に失敗した理由を伝える検証メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-196">If invalid, `Schedule` is rebuilt, and the page renders with one or more validation messages stating why page validation failed.</span></span> <span data-ttu-id="fd226-197">有効な場合、`FileUpload` プロパティが *OnPostAsync* で使用され、バージョンが 2 つあるスケジュール ファイルがアップロードされ、データを保存するための新しい `Schedule` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-197">If valid, the `FileUpload` properties are used in *OnPostAsync* to complete the file upload for the two versions of the schedule and to create a new `Schedule` object to store the data.</span></span> <span data-ttu-id="fd226-198">スケジュールがデータベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-198">The schedule is then saved to the database:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a><span data-ttu-id="fd226-199">ファイル アップロード Razor ページをリンクする</span><span class="sxs-lookup"><span data-stu-id="fd226-199">Link the file upload Razor Page</span></span>

<span data-ttu-id="fd226-200">*Pages/Shared/_Layout.cshtml* を開き、スケジュール ページにアクセスするためのリンクをナビゲーション バーに追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-200">Open *Pages/Shared/_Layout.cshtml* and add a link to the navigation bar to reach the Schedules page:</span></span>

```cshtml
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li><a asp-page="/Index">Home</a></li>
        <li><a asp-page="/Schedules/Index">Schedules</a></li>
        <li><a asp-page="/About">About</a></li>
        <li><a asp-page="/Contact">Contact</a></li>
    </ul>
</div>
```

## <a name="add-a-page-to-confirm-schedule-deletion"></a><span data-ttu-id="fd226-201">スケジュール削除を確定するためのページを追加する</span><span class="sxs-lookup"><span data-stu-id="fd226-201">Add a page to confirm schedule deletion</span></span>

<span data-ttu-id="fd226-202">ユーザーがスケジュールを削除するボタンをクリックすると、削除を取り消すことができます。</span><span class="sxs-lookup"><span data-stu-id="fd226-202">When the user clicks to delete a schedule, a chance to cancel the operation is provided.</span></span> <span data-ttu-id="fd226-203">削除確定ページ (*Delete.cshtml*) を *Schedules* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-203">Add a delete confirmation page (*Delete.cshtml*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

<span data-ttu-id="fd226-204">このページ モデル (*Delete.cshtml.cs*) は、要求のルート データの `id` によって識別される 1 つのスケジュールを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="fd226-204">The page model (*Delete.cshtml.cs*) loads a single schedule identified by `id` in the request's route data.</span></span> <span data-ttu-id="fd226-205">*Delete.cshtml.cs* ファイルを *Schedules* フォルダーに追加します。</span><span class="sxs-lookup"><span data-stu-id="fd226-205">Add the *Delete.cshtml.cs* file to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

<span data-ttu-id="fd226-206">`OnPostAsync` メソッドは、その `id` によるスケジュール削除を処理します。</span><span class="sxs-lookup"><span data-stu-id="fd226-206">The `OnPostAsync` method handles deleting the schedule by its `id`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

<span data-ttu-id="fd226-207">スケジュールが削除されると、`RedirectToPage` はユーザーをスケジュール *Index.cshtml* ページに戻します。</span><span class="sxs-lookup"><span data-stu-id="fd226-207">After successfully deleting the schedule, the `RedirectToPage` sends the user back to the schedules *Index.cshtml* page.</span></span>

## <a name="the-working-schedules-razor-page"></a><span data-ttu-id="fd226-208">Schedules Razor ページの実際の動作</span><span class="sxs-lookup"><span data-stu-id="fd226-208">The working Schedules Razor Page</span></span>

<span data-ttu-id="fd226-209">ページが読み込まれると、スケジュール タイトルのラベルと情報、パブリック スケジュール、プライベート スケジュール、送信ボタンがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="fd226-209">When the page loads, labels and inputs for schedule title, public schedule, and private schedule are rendered with a submit button:</span></span>

![初回読み込み時の Schedules Razor ページ。検証エラーや空のフィールドに関する表示はまだありません。](upload-files/_static/browser1.png)

<span data-ttu-id="fd226-211">フィールドに何も入力せずに **[アップロード]** ボタンを選択すると、モデルの `[Required]` 属性に違反することになります。</span><span class="sxs-lookup"><span data-stu-id="fd226-211">Selecting the **Upload** button without populating any of the fields violates the `[Required]` attributes on the model.</span></span> <span data-ttu-id="fd226-212">`ModelState` が無効です。</span><span class="sxs-lookup"><span data-stu-id="fd226-212">The `ModelState` is invalid.</span></span> <span data-ttu-id="fd226-213">検証エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-213">The validation error messages are displayed to the user:</span></span>

![各入力コントロールの隣に検証エラー メッセージが表示されます。](upload-files/_static/browser2.png)

<span data-ttu-id="fd226-215">**[タイトル]** フィールドに 2 つの文字を入力します。</span><span class="sxs-lookup"><span data-stu-id="fd226-215">Type two letters into the **Title** field.</span></span> <span data-ttu-id="fd226-216">タイトルは 3 ～ 60 文字にする必要があるという検証メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fd226-216">The validation message changes to indicate that the title must be between 3-60 characters:</span></span>

![タイトルの検証メッセージが変わりました](upload-files/_static/browser3.png)

<span data-ttu-id="fd226-218">1 つまたは複数のスケジュールをアップロードすると、 **[Loaded Schedules]\(読み込まれたスケジュール\)** セクションに、読み込まれたスケジュールがレンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="fd226-218">When one or more schedules are uploaded, the **Loaded Schedules** section renders the loaded schedules:</span></span>

![読み込まれたスケジュールのテーブル。各スケジュールのタイトル、アップロード日付 (UTC)、パブリック バージョン ファイルのサイズ、プライベート バージョン ファイルのサイズ](upload-files/_static/browser4.png)

<span data-ttu-id="fd226-220">この **[削除]** リンクをクリックすると、削除確定ビューが表示されます。削除を確定するか、取り消す機会が与えられます。</span><span class="sxs-lookup"><span data-stu-id="fd226-220">The user can click the **Delete** link from there to reach the delete confirmation view, where they have an opportunity to confirm or cancel the delete operation.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="fd226-221">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="fd226-221">Troubleshooting</span></span>

<span data-ttu-id="fd226-222">トラブルシューティングの情報については`IFormFile`アップロードするを参照してください[ASP.NET Core でファイルをアップロードします。トラブルシューティング](xref:mvc/models/file-uploads#troubleshooting)します。</span><span class="sxs-lookup"><span data-stu-id="fd226-222">For troubleshooting information with `IFormFile` uploading, see [File uploads in ASP.NET Core: Troubleshooting](xref:mvc/models/file-uploads#troubleshooting).</span></span>
