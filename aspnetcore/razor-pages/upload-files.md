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
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a>ASP.NET Core で Razor ページにファイルをアップロードする

作成者: [Luke Latham](https://github.com/guardrex)

このトピックでのビルド時に、[サンプル アプリ](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample)で<xref:tutorials/razor-pages/razor-pages-start>します。

このトピックでは、これは小さいファイルのアップロードも単純なモデル バインディングを使用して、ファイルをアップロードする方法を示します。 大きなファイルをストリーミングする方法については、「[Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming)」 (ストリーミングでの大きいファイルのアップロード) を参照してください。

以下の手順では、映画スケジュール ファイルのアップロード機能をサンプル アプリに追加します。 映画スケジュールを表すのが `Schedule` クラスです。 このクラスには、2 つのバージョンのスケジュールが含まれています。 1 つのバージョンは顧客に提供される `PublicSchedule` です。 もう 1 つのバージョンは社員が利用する `PrivateSchedule` です。 いずれも別個のファイルとしてアップロードされます。 このチュートリアルでは、POST が 1 つのページからサーバーに 2 つのファイルをアップロードします。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/upload-files/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="security-considerations"></a>セキュリティの考慮事項

サーバーにファイルをアップロードする機能をユーザーに提供するときには、十分に注意してください。 攻撃者が[サービス拒否](/windows-hardware/drivers/ifs/denial-of-service)などの攻撃をシステムに実行する場合があります。 攻撃の成功の可能性を少なくするセキュリティ手順には、次のようなものがあります。

* システムの専用のファイル アップロード領域にファイルをアップロードする。これによりアップロードしたコンテンツにセキュリティ対策を適用しやすくなります。 ファイルのアップロードを許可する際に、実行アクセス許可がアップロード先で無効になっていることを確認する。
* ユーザー入力やアップロードされたファイルのファイル名からではなく、アプリで決定された安全なファイル名を使用する。
* 承認されている特定のファイル拡張子のみを許可する。
* クライアント側のチェックがサーバーで実行されることを確認する。 クライアント側のチェックは簡単に回避できます。
* アップロードのサイズを確認し、アップロードのサイズが予想よりも大きくならないようにする。
* アップロードされたコンテンツに対してウイルス/マルウェア スキャナーを実行する。

> [!WARNING]
> システムへの悪意のあるコードのアップロードは、頻繁に次のような内容のコードの実行するための足がかりとなります。
> * システムを完全に乗っ取る。
> * システムが完全に失敗したという結果を、システムにオーバーロードする。
> * ユーザーまたはシステムのデータを破壊する。
> * パブリック インターフェイスに落書きする。

## <a name="add-a-fileupload-class"></a>FileUpload クラスを追加する

ファイル アップロードのペアを処理する Razor ページを作成します。 `FileUpload` クラスを追加して、スケジュール データを取得するページにバインドします。 *Models* フォルダーを右クリックします。 **[追加]** 、 **[クラス]** の順に選択します。 クラスに **FileUpload** という名前を付けて、次のプロパティを追加します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

このクラスには、スケジュールのタイトルのプロパティと 2 つあるスケジュールのバージョンのそれぞれのプロパティが含まれます。 3 つのプロパティはすべて必須であり、タイトルの長さは 3 ～ 60 文字にする必要があります。

## <a name="add-a-helper-method-to-upload-files"></a>ファイルをアップロードするヘルパー メソッドを追加する

アップロード済みのスケジュール ファイルを処理するコード重複を回避するために、静的なヘルパー メソッドを先に追加します。 アプリで *Utilities* フォルダーを作成し、次のコンテンツを含む *FileHelpers.cs* ファイルを追加します。 ヘルパー メソッドの `ProcessFormFile` は [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) と [ModelStateDictionary](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) を受け取り、ファイルのサイズとコンテンツが含まれる文字列を返します。 コンテンツの種類と長さが確認されます。 ファイルが検証チェックに合格しなければ、エラーが `ModelState` に追加されます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a>ファイルをディスクに保存する

このサンプル アプリでは、アップロードされたファイルはデータベース フィールドに保存されます。 ファイルをディスクに保存するには、[FileStream](/dotnet/api/system.io.filestream) を使用します。 次の例では、`FileUpload.UploadPublicSchedule` によって保持されているファイルは、`OnPostAsync` メソッドの `FileStream` にコピーされます。 `FileStream` は、指定されている `<PATH-AND-FILE-NAME>` にあるディスクにファイルを書き込みます。

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

ワーカー プロセスには、`filePath` によって指定された場所への書き込みアクセス許可が必要です。

> [!NOTE]
> `filePath` にはファイル名が*必要*です。 ファイル名が指定されていない場合、実行時に [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) がスローされます。

> [!WARNING]
> アプリと同じディレクトリ ツリーには、アップロードしたファイルを保持しないでください。
>
> このコード例には、悪意のあるファイルのアップロードに対する、サーバー側での保護はありません。 ユーザーからファイルを受け入れる際の外部アクセスによる攻撃を減らす方法については、次の資料を参照してください。
>
> * [Unrestricted File Upload (ファイルの無制限のアップロード)](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [Azure のセキュリティ:ユーザーからのファイルを受け入れるときに、適切なコントロールが配置には必ず](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a>Azure Blob ストレージにファイルを保存する

ファイルの内容を Azure Blob ストレージにアップロードする方法については、「[.NET を使用して Azure Blob Storage を使用する](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)」を参照してください。 このトピックでは [UploadFromStream](/dotnet/api/microsoft.azure.storage.file.cloudfile.uploadfromstreamasync) を使用して [FileStream](/dotnet/api/system.io.filestream) を Blob ストレージに保存する方法を説明しています。

## <a name="add-the-schedule-class"></a>Schedule クラスを追加する

*Models* フォルダーを右クリックします。 **[追加]** 、 **[クラス]** の順に選択します。 クラスに **Schedule** という名前を付けて、次のプロパティを追加します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

このクラスは `Display` 属性と `DisplayFormat` 属性を使用します。この 2 つの属性は、スケジュール データがレンダリングされるとき、わかりやすい名前を生成し、書式を設定します。

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a>RazorPagesMovieContext を更新する

スケジュールに対し、`RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) で `DbSet` を指定します。

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a>MovieContext を更新する

このスケジュールは、`MovieContext` (*Models/MovieContext.cs*) で `DbSet` を指定します。

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a>Schedule テーブルをデータベースに追加する

パッケージ マネージャー コンソール (PMC) を開きます。**ツール** > **NuGet パッケージ マネージャー** > **パッケージ マネージャー コンソール**します。

![PMC メニュー](upload-files/_static/pmc.png)

PMC で、次のコマンドを実行します。 コマンドによって `Schedule` テーブルがデータベースに追加されます。

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a>ファイル アップロード Razor ページを追加する

*Pages* フォルダーで、*Schedules* フォルダーを作成します。 *Schedules* フォルダーで、*Index.cshtml* という名前のページを作成します。次のコンテンツでスケジュールをアップロードするためのページです。

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

各フォーム グループには、 **\<label>** が含まれます。これは各クラス プロパティの名前を表示します。 `FileUpload` モデルの `Display` 属性は、ラベルの表示値を与えます。 たとえば、`UploadPublicSchedule` プロパティの表示名は `[Display(Name="Public Schedule")]` で設定されます。そのため、フォームがレンダリングされると、ラベルに "Public Schedule" と表示されます。

各フォーム グループには、検証 **\<span>** が含まれます。 ユーザーの入力が `FileUpload` クラスに設定されているプロパティ属性の要求を持たさない場合、あるいは `ProcessFormFile` メソッド ファイルの検証チェックで不合格になった場合、モデルは検証に失敗します。 モデルが検証に失敗すると、ヒントが表示されます。 たとえば、`Title` プロパティであれば、`[Required]` や `[StringLength(60, MinimumLength = 3)]` という注釈が表示されます。 ユーザーがタイトルを入力しないと、値が必須であるというメッセージが表示されます。 ユーザーが 3 文字より少ないか、60 文字より多い値を入力した場合、値の長さが正しくないというメッセージが表示されます。 コンテンツのないファイルが指定された場合、ファイルが空であるというメッセージが表示されます。

## <a name="add-the-page-model"></a>ページ モデルの追加

*Schedules* フォルダーにページ モデル (*Index.cshtml.cs*) を追加します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

ページ モデル (*Index.cshtml.cs* の `IndexModel`) は `FileUpload` クラスをバインドします。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

このモデルはまた、スケジュールの一覧 (`IList<Schedule>`) を利用し、ページのデータベースに保存されているスケジュールを表示します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

ページが `OnGetAsync` で読み込まれると、データベースから `Schedules` が入力され、読み込まれたスケジュールの HTML テーブルが生成されます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

フォームがサーバーに投稿されると、`ModelState` がチェックされます。 無効な場合、`Schedule` が再ビルドされます。ページがレンダリングされ、ページ検証に失敗した理由を伝える検証メッセージが表示されます。 有効な場合、`FileUpload` プロパティが *OnPostAsync* で使用され、バージョンが 2 つあるスケジュール ファイルがアップロードされ、データを保存するための新しい `Schedule` オブジェクトが作成されます。 スケジュールがデータベースに保存されます。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a>ファイル アップロード Razor ページをリンクする

*Pages/Shared/_Layout.cshtml* を開き、スケジュール ページにアクセスするためのリンクをナビゲーション バーに追加します。

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

## <a name="add-a-page-to-confirm-schedule-deletion"></a>スケジュール削除を確定するためのページを追加する

ユーザーがスケジュールを削除するボタンをクリックすると、削除を取り消すことができます。 削除確定ページ (*Delete.cshtml*) を *Schedules* フォルダーに追加します。

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

このページ モデル (*Delete.cshtml.cs*) は、要求のルート データの `id` によって識別される 1 つのスケジュールを読み込みます。 *Delete.cshtml.cs* ファイルを *Schedules* フォルダーに追加します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

`OnPostAsync` メソッドは、その `id` によるスケジュール削除を処理します。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

スケジュールが削除されると、`RedirectToPage` はユーザーをスケジュール *Index.cshtml* ページに戻します。

## <a name="the-working-schedules-razor-page"></a>Schedules Razor ページの実際の動作

ページが読み込まれると、スケジュール タイトルのラベルと情報、パブリック スケジュール、プライベート スケジュール、送信ボタンがレンダリングされます。

![初回読み込み時の Schedules Razor ページ。検証エラーや空のフィールドに関する表示はまだありません。](upload-files/_static/browser1.png)

フィールドに何も入力せずに **[アップロード]** ボタンを選択すると、モデルの `[Required]` 属性に違反することになります。 `ModelState` が無効です。 検証エラー メッセージが表示されます。

![各入力コントロールの隣に検証エラー メッセージが表示されます。](upload-files/_static/browser2.png)

**[タイトル]** フィールドに 2 つの文字を入力します。 タイトルは 3 ～ 60 文字にする必要があるという検証メッセージが表示されます。

![タイトルの検証メッセージが変わりました](upload-files/_static/browser3.png)

1 つまたは複数のスケジュールをアップロードすると、 **[Loaded Schedules]\(読み込まれたスケジュール\)** セクションに、読み込まれたスケジュールがレンダリングされます。

![読み込まれたスケジュールのテーブル。各スケジュールのタイトル、アップロード日付 (UTC)、パブリック バージョン ファイルのサイズ、プライベート バージョン ファイルのサイズ](upload-files/_static/browser4.png)

この **[削除]** リンクをクリックすると、削除確定ビューが表示されます。削除を確定するか、取り消す機会が与えられます。

## <a name="troubleshooting"></a>トラブルシューティング

トラブルシューティングの情報については`IFormFile`アップロードするを参照してください[ASP.NET Core でファイルをアップロードします。トラブルシューティング](xref:mvc/models/file-uploads#troubleshooting)します。
