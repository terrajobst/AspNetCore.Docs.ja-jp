---
title: dotnet-grpc を使用して Protobuf 参照を管理する
author: juntaoluo
description: Dotnet-grpc グローバルツールを使用した Protobuf 参照の追加、更新、削除、および一覧表示について説明します。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519038"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="7543c-103">dotnet-grpc を使用して Protobuf 参照を管理する</span><span class="sxs-lookup"><span data-stu-id="7543c-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="7543c-104">作成者: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="7543c-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="7543c-105">`dotnet-grpc` は、.NET gRPC プロジェクト内の[Protobuf (*proto*)](xref:grpc/basics#proto-file)参照を管理するための .net Core グローバルツールです。</span><span class="sxs-lookup"><span data-stu-id="7543c-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="7543c-106">このツールを使用すると、Protobuf 参照の追加、更新、削除、および一覧表示を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7543c-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="7543c-107">インストール</span><span class="sxs-lookup"><span data-stu-id="7543c-107">Installation</span></span>

<span data-ttu-id="7543c-108">@No__t 0 [.Net Core グローバルツール](/dotnet/core/tools/global-tools)をインストールするには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7543c-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="7543c-109">参照の追加</span><span class="sxs-lookup"><span data-stu-id="7543c-109">Add references</span></span>

<span data-ttu-id="7543c-110">`dotnet-grpc` を使用すると、 *.csproj*ファイルに `<Protobuf />` 項目として Protobuf 参照を追加できます。</span><span class="sxs-lookup"><span data-stu-id="7543c-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="7543c-111">Protobuf 参照は、 C#クライアントまたはサーバーの資産を生成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="7543c-112">@No__t 0 のツールでは、次のことができます。</span><span class="sxs-lookup"><span data-stu-id="7543c-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="7543c-113">ディスク上のローカルファイルから Protobuf 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="7543c-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="7543c-114">URL で指定されたリモートファイルから Protobuf 参照を作成します。</span><span class="sxs-lookup"><span data-stu-id="7543c-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="7543c-115">正しい gRPC パッケージの依存関係がプロジェクトに追加されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="7543c-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="7543c-116">たとえば、@no__t 0 のパッケージが web アプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="7543c-117">`Grpc.AspNetCore` には、gRPC サーバーとクライアントライブラリ、およびツールのサポートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="7543c-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="7543c-118">また、gRPC クライアントライブラリとツールのサポートのみを含む、`Grpc.Net.Client`、`Grpc.Tools`、`Google.Protobuf` のパッケージがコンソールアプリに追加されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="7543c-119">ファイルの追加</span><span class="sxs-lookup"><span data-stu-id="7543c-119">Add file</span></span>

<span data-ttu-id="7543c-120">@No__t-0 コマンドを使用して、ディスク上のローカルファイルを Protobuf 参照として追加します。</span><span class="sxs-lookup"><span data-stu-id="7543c-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="7543c-121">指定されたファイルパス:</span><span class="sxs-lookup"><span data-stu-id="7543c-121">The file paths provided:</span></span>

* <span data-ttu-id="7543c-122">現在のディレクトリまたは絶対パスを基準とした相対パスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="7543c-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="7543c-123">パターンベースのファイル[グロビング](https://wikipedia.org/wiki/Glob_(programming))のワイルドカードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="7543c-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="7543c-124">ファイルがプロジェクトディレクトリの外部にある場合は、Visual Studio のフォルダー `Protos` の下にファイルを表示するために @no__t 0 要素が追加されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="7543c-125">使用方法</span><span class="sxs-lookup"><span data-stu-id="7543c-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="7543c-126">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-126">Arguments</span></span>

| <span data-ttu-id="7543c-127">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-127">Argument</span></span> | <span data-ttu-id="7543c-128">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-128">Description</span></span> |
|-|-|
| <span data-ttu-id="7543c-129">ファイル</span><span class="sxs-lookup"><span data-stu-id="7543c-129">files</span></span> | <span data-ttu-id="7543c-130">Protobuf ファイル参照。</span><span class="sxs-lookup"><span data-stu-id="7543c-130">The protobuf file references.</span></span> <span data-ttu-id="7543c-131">ローカル protobuf ファイルの場合は、glob のパスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="7543c-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="7543c-132">オプション</span><span class="sxs-lookup"><span data-stu-id="7543c-132">Options</span></span>

| <span data-ttu-id="7543c-133">短いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-133">Short option</span></span> | <span data-ttu-id="7543c-134">長いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-134">Long option</span></span> | <span data-ttu-id="7543c-135">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7543c-136">-p</span><span class="sxs-lookup"><span data-stu-id="7543c-136">-p</span></span> | <span data-ttu-id="7543c-137">--project</span><span class="sxs-lookup"><span data-stu-id="7543c-137">--project</span></span> | <span data-ttu-id="7543c-138">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="7543c-138">The path to the project file to operate on.</span></span> <span data-ttu-id="7543c-139">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="7543c-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="7543c-140">-s</span><span class="sxs-lookup"><span data-stu-id="7543c-140">-s</span></span> | <span data-ttu-id="7543c-141">--サービス</span><span class="sxs-lookup"><span data-stu-id="7543c-141">--services</span></span> | <span data-ttu-id="7543c-142">生成する必要がある gRPC サービスの種類。</span><span class="sxs-lookup"><span data-stu-id="7543c-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="7543c-143">@No__t-0 が指定されている場合は、Web プロジェクトに対して `Both` が使用され、非 Web プロジェクトには `Client` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="7543c-144">指定できる値は `Both`、`Client`、`Default`、`None`、`Server` です。</span><span class="sxs-lookup"><span data-stu-id="7543c-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="7543c-145">-i</span><span class="sxs-lookup"><span data-stu-id="7543c-145">-i</span></span> | <span data-ttu-id="7543c-146">--追加-インポート-ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="7543c-146">--additional-import-dirs</span></span> | <span data-ttu-id="7543c-147">Protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="7543c-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="7543c-148">これは、セミコロンで区切られたパスの一覧です。</span><span class="sxs-lookup"><span data-stu-id="7543c-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="7543c-149">--アクセス</span><span class="sxs-lookup"><span data-stu-id="7543c-149">--access</span></span> | <span data-ttu-id="7543c-150">生成されC#たクラスに使用するアクセス修飾子。</span><span class="sxs-lookup"><span data-stu-id="7543c-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="7543c-151">既定値は `Public`です。</span><span class="sxs-lookup"><span data-stu-id="7543c-151">The default value is `Public`.</span></span> <span data-ttu-id="7543c-152">許容される値は `Internal` および `Public` です。</span><span class="sxs-lookup"><span data-stu-id="7543c-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="7543c-153">URL の追加</span><span class="sxs-lookup"><span data-stu-id="7543c-153">Add URL</span></span>

<span data-ttu-id="7543c-154">@No__t-0 コマンドを使用して、ソース URL で指定されたリモートファイルを Protobuf reference として追加します。</span><span class="sxs-lookup"><span data-stu-id="7543c-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="7543c-155">リモートファイルをダウンロードする場所を指定するには、ファイルパスを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7543c-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="7543c-156">ファイルパスは、現在のディレクトリまたは絶対パスを基準とした相対パスにすることができます。</span><span class="sxs-lookup"><span data-stu-id="7543c-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="7543c-157">ファイルパスがプロジェクトディレクトリの外部にある場合は、Visual Studio の仮想フォルダー `Protos` の下にファイルを表示するために @no__t 0 要素が追加されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="7543c-158">使用方法</span><span class="sxs-lookup"><span data-stu-id="7543c-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="7543c-159">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-159">Arguments</span></span>

| <span data-ttu-id="7543c-160">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-160">Argument</span></span> | <span data-ttu-id="7543c-161">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-161">Description</span></span> |
|-|-|
| <span data-ttu-id="7543c-162">url</span><span class="sxs-lookup"><span data-stu-id="7543c-162">url</span></span> | <span data-ttu-id="7543c-163">リモート protobuf ファイルの URL。</span><span class="sxs-lookup"><span data-stu-id="7543c-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="7543c-164">オプション</span><span class="sxs-lookup"><span data-stu-id="7543c-164">Options</span></span>

| <span data-ttu-id="7543c-165">短いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-165">Short option</span></span> | <span data-ttu-id="7543c-166">長いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-166">Long option</span></span> | <span data-ttu-id="7543c-167">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7543c-168">-o</span><span class="sxs-lookup"><span data-stu-id="7543c-168">-o</span></span> | <span data-ttu-id="7543c-169">--出力</span><span class="sxs-lookup"><span data-stu-id="7543c-169">--output</span></span> | <span data-ttu-id="7543c-170">リモート protobuf ファイルのダウンロードパスを指定します。</span><span class="sxs-lookup"><span data-stu-id="7543c-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="7543c-171">これは必須オプションです。</span><span class="sxs-lookup"><span data-stu-id="7543c-171">This is a required option.</span></span>
| <span data-ttu-id="7543c-172">-p</span><span class="sxs-lookup"><span data-stu-id="7543c-172">-p</span></span> | <span data-ttu-id="7543c-173">--project</span><span class="sxs-lookup"><span data-stu-id="7543c-173">--project</span></span> | <span data-ttu-id="7543c-174">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="7543c-174">The path to the project file to operate on.</span></span> <span data-ttu-id="7543c-175">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="7543c-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="7543c-176">-s</span><span class="sxs-lookup"><span data-stu-id="7543c-176">-s</span></span> | <span data-ttu-id="7543c-177">--サービス</span><span class="sxs-lookup"><span data-stu-id="7543c-177">--services</span></span> | <span data-ttu-id="7543c-178">生成する必要がある gRPC サービスの種類。</span><span class="sxs-lookup"><span data-stu-id="7543c-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="7543c-179">@No__t-0 が指定されている場合は、Web プロジェクトに対して `Both` が使用され、非 Web プロジェクトには `Client` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="7543c-180">指定できる値は `Both`、`Client`、`Default`、`None`、`Server` です。</span><span class="sxs-lookup"><span data-stu-id="7543c-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="7543c-181">-i</span><span class="sxs-lookup"><span data-stu-id="7543c-181">-i</span></span> | <span data-ttu-id="7543c-182">--追加-インポート-ディレクトリ</span><span class="sxs-lookup"><span data-stu-id="7543c-182">--additional-import-dirs</span></span> | <span data-ttu-id="7543c-183">Protobuf ファイルのインポートを解決するときに使用する追加のディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="7543c-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="7543c-184">これは、セミコロンで区切られたパスの一覧です。</span><span class="sxs-lookup"><span data-stu-id="7543c-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="7543c-185">--アクセス</span><span class="sxs-lookup"><span data-stu-id="7543c-185">--access</span></span> | <span data-ttu-id="7543c-186">生成されC#たクラスに使用するアクセス修飾子。</span><span class="sxs-lookup"><span data-stu-id="7543c-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="7543c-187">既定値は `Public`にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7543c-187">Default value is `Public`.</span></span> <span data-ttu-id="7543c-188">許容される値は `Internal` および `Public` です。</span><span class="sxs-lookup"><span data-stu-id="7543c-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="7543c-189">削除</span><span class="sxs-lookup"><span data-stu-id="7543c-189">Remove</span></span>

<span data-ttu-id="7543c-190">@No__t-0 コマンドを使用して、 *.csproj*ファイルから Protobuf 参照を削除します。</span><span class="sxs-lookup"><span data-stu-id="7543c-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="7543c-191">コマンドは、引数としてパス引数とソース Url を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="7543c-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="7543c-192">ツール:</span><span class="sxs-lookup"><span data-stu-id="7543c-192">The tool:</span></span>

* <span data-ttu-id="7543c-193">Protobuf 参照のみを削除します。</span><span class="sxs-lookup"><span data-stu-id="7543c-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="7543c-194">は、最初にリモート URL からダウンロードされた場合でも、*プロトコル*ファイルを削除しません。</span><span class="sxs-lookup"><span data-stu-id="7543c-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="7543c-195">使用方法</span><span class="sxs-lookup"><span data-stu-id="7543c-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="7543c-196">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-196">Arguments</span></span>

| <span data-ttu-id="7543c-197">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-197">Argument</span></span> | <span data-ttu-id="7543c-198">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-198">Description</span></span> |
|-|-|
| <span data-ttu-id="7543c-199">参照</span><span class="sxs-lookup"><span data-stu-id="7543c-199">references</span></span> | <span data-ttu-id="7543c-200">削除する protobuf 参照の Url またはファイルパス。</span><span class="sxs-lookup"><span data-stu-id="7543c-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="7543c-201">オプション</span><span class="sxs-lookup"><span data-stu-id="7543c-201">Options</span></span>

| <span data-ttu-id="7543c-202">短いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-202">Short option</span></span> | <span data-ttu-id="7543c-203">長いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-203">Long option</span></span> | <span data-ttu-id="7543c-204">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7543c-205">-p</span><span class="sxs-lookup"><span data-stu-id="7543c-205">-p</span></span> | <span data-ttu-id="7543c-206">--project</span><span class="sxs-lookup"><span data-stu-id="7543c-206">--project</span></span> | <span data-ttu-id="7543c-207">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="7543c-207">The path to the project file to operate on.</span></span> <span data-ttu-id="7543c-208">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="7543c-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="7543c-209">最新の情報に更新</span><span class="sxs-lookup"><span data-stu-id="7543c-209">Refresh</span></span>

<span data-ttu-id="7543c-210">@No__t-0 コマンドは、ソース URL の最新のコンテンツを使用してリモート参照を更新するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="7543c-211">ダウンロードファイルパスとソース URL の両方を使用して、更新する参照を指定できます。</span><span class="sxs-lookup"><span data-stu-id="7543c-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="7543c-212">メモ:</span><span class="sxs-lookup"><span data-stu-id="7543c-212">Note:</span></span>

* <span data-ttu-id="7543c-213">ファイルの内容のハッシュは、ローカルファイルを更新する必要があるかどうかを判断するために比較されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="7543c-214">タイムスタンプ情報は比較されません。</span><span class="sxs-lookup"><span data-stu-id="7543c-214">No timestamp information is compared.</span></span>

<span data-ttu-id="7543c-215">更新が必要な場合、ツールは常にローカルファイルをリモートファイルに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7543c-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="7543c-216">使用方法</span><span class="sxs-lookup"><span data-stu-id="7543c-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="7543c-217">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-217">Arguments</span></span>

| <span data-ttu-id="7543c-218">引数</span><span class="sxs-lookup"><span data-stu-id="7543c-218">Argument</span></span> | <span data-ttu-id="7543c-219">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-219">Description</span></span> |
|-|-|
| <span data-ttu-id="7543c-220">参照</span><span class="sxs-lookup"><span data-stu-id="7543c-220">references</span></span> | <span data-ttu-id="7543c-221">更新する必要があるリモート protobuf 参照への Url またはファイルパス。</span><span class="sxs-lookup"><span data-stu-id="7543c-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="7543c-222">すべてのリモート参照を更新するには、この引数を空のままにします。</span><span class="sxs-lookup"><span data-stu-id="7543c-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="7543c-223">オプション</span><span class="sxs-lookup"><span data-stu-id="7543c-223">Options</span></span>

| <span data-ttu-id="7543c-224">短いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-224">Short option</span></span> | <span data-ttu-id="7543c-225">長いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-225">Long option</span></span> | <span data-ttu-id="7543c-226">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7543c-227">-p</span><span class="sxs-lookup"><span data-stu-id="7543c-227">-p</span></span> | <span data-ttu-id="7543c-228">--project</span><span class="sxs-lookup"><span data-stu-id="7543c-228">--project</span></span> | <span data-ttu-id="7543c-229">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="7543c-229">The path to the project file to operate on.</span></span> <span data-ttu-id="7543c-230">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="7543c-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="7543c-231">--ドライ-実行</span><span class="sxs-lookup"><span data-stu-id="7543c-231">--dry-run</span></span> | <span data-ttu-id="7543c-232">新しいコンテンツをダウンロードせずに更新されるファイルの一覧を出力します。</span><span class="sxs-lookup"><span data-stu-id="7543c-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="7543c-233">リスト</span><span class="sxs-lookup"><span data-stu-id="7543c-233">List</span></span>

<span data-ttu-id="7543c-234">@No__t-0 コマンドは、プロジェクトファイル内のすべての Protobuf 参照を表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="7543c-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="7543c-235">列のすべての値が既定値の場合、列は省略される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7543c-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="7543c-236">使用方法</span><span class="sxs-lookup"><span data-stu-id="7543c-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="7543c-237">オプション</span><span class="sxs-lookup"><span data-stu-id="7543c-237">Options</span></span>

| <span data-ttu-id="7543c-238">短いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-238">Short option</span></span> | <span data-ttu-id="7543c-239">長いオプション</span><span class="sxs-lookup"><span data-stu-id="7543c-239">Long option</span></span> | <span data-ttu-id="7543c-240">説明</span><span class="sxs-lookup"><span data-stu-id="7543c-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="7543c-241">-p</span><span class="sxs-lookup"><span data-stu-id="7543c-241">-p</span></span> | <span data-ttu-id="7543c-242">--project</span><span class="sxs-lookup"><span data-stu-id="7543c-242">--project</span></span> | <span data-ttu-id="7543c-243">操作するプロジェクトファイルのパス。</span><span class="sxs-lookup"><span data-stu-id="7543c-243">The path to the project file to operate on.</span></span> <span data-ttu-id="7543c-244">ファイルが指定されていない場合、コマンドは現在のディレクトリを検索します。</span><span class="sxs-lookup"><span data-stu-id="7543c-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7543c-245">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="7543c-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
