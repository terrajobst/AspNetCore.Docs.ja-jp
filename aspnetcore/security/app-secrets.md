---
title: ASP.NET Core での開発におけるアプリシークレットの安全な保存
author: rick-anderson
description: ASP.NET Core アプリの開発中に、機密情報をアプリシークレットとして格納および取得する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 03/13/2019
uid: security/app-secrets
ms.openlocfilehash: 0203a5737caf1af809b739d9e266a6971cd1523b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080710"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="c53e8-103">ASP.NET Core での開発におけるアプリシークレットの安全な保存</span><span class="sxs-lookup"><span data-stu-id="c53e8-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="c53e8-104">[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Scott addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c53e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="c53e8-105">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="c53e8-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c53e8-106">このドキュメントでは、ASP.NET Core アプリの開発時に機密データを格納および取得する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-106">This document explains techniques for storing and retrieving sensitive data during the development of an ASP.NET Core app.</span></span> <span data-ttu-id="c53e8-107">パスワードやその他の機密データをソースコードに格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="c53e8-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="c53e8-108">運用環境のシークレットは、開発またはテストには使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c53e8-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="c53e8-109">[Azure Key Vault 構成プロバイダー](xref:security/key-vault-configuration)により、Azure テストと運用のシークレットを格納し、保護することが可能です。</span><span class="sxs-lookup"><span data-stu-id="c53e8-109">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="c53e8-110">環境変数</span><span class="sxs-lookup"><span data-stu-id="c53e8-110">Environment variables</span></span>

<span data-ttu-id="c53e8-111">環境変数は、コードまたはローカル構成ファイルにアプリシークレットを格納しないようにするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-111">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="c53e8-112">環境変数は、以前に指定したすべての構成ソースの構成値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="c53e8-112">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="c53e8-113">コンストラクターでを呼び出し<xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>て、環境変数の値の読み取りを構成します。 `Startup`</span><span class="sxs-lookup"><span data-stu-id="c53e8-113">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="c53e8-114">**個々のユーザーアカウント**のセキュリティが有効になっている ASP.NET Core web アプリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="c53e8-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="c53e8-115">既定のデータベース接続文字列は、というキー `DefaultConnection`を持つプロジェクトの*appsettings*ファイルに含まれています。</span><span class="sxs-lookup"><span data-stu-id="c53e8-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="c53e8-116">既定の接続文字列は LocalDB 用であり、ユーザーモードで実行され、パスワードは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="c53e8-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="c53e8-117">アプリの展開時に`DefaultConnection` 、キー値は環境変数の値でオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="c53e8-118">環境変数には、機密性の高い資格情報を持つ完全な接続文字列が格納される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c53e8-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="c53e8-119">環境変数は通常、暗号化されていないプレーンテキストで格納されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="c53e8-120">コンピューターまたはプロセスが侵害された場合、信頼されていない相手から環境変数にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="c53e8-121">ユーザーシークレットが漏えいしないようにするための追加の手段が必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="c53e8-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="c53e8-122">シークレットマネージャー</span><span class="sxs-lookup"><span data-stu-id="c53e8-122">Secret Manager</span></span>

<span data-ttu-id="c53e8-123">Secret Manager ツールは、ASP.NET Core プロジェクトの開発中に機微なデータを保存します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="c53e8-124">このコンテキストでは、機密データの一部がアプリシークレットになります。</span><span class="sxs-lookup"><span data-stu-id="c53e8-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="c53e8-125">アプリシークレットは、プロジェクトツリーとは別の場所に格納されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="c53e8-126">アプリシークレットは、特定のプロジェクトに関連付けられているか、複数のプロジェクト間で共有されています。</span><span class="sxs-lookup"><span data-stu-id="c53e8-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="c53e8-127">アプリシークレットがソース管理にチェックインされていません。</span><span class="sxs-lookup"><span data-stu-id="c53e8-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="c53e8-128">シークレットマネージャーツールは、保存されているシークレットを暗号化せず、信頼されたストアとして扱わないでください。</span><span class="sxs-lookup"><span data-stu-id="c53e8-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="c53e8-129">開発目的でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-129">It's for development purposes only.</span></span> <span data-ttu-id="c53e8-130">キーと値は、ユーザープロファイルディレクトリの JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="c53e8-131">Secret Manager ツールの動作</span><span class="sxs-lookup"><span data-stu-id="c53e8-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="c53e8-132">Secret Manager ツールは、値の格納場所や方法などの実装の詳細を抽象化します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="c53e8-133">このツールは、実装の詳細を把握していなくても使用できます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="c53e8-134">値は、ローカルコンピューター上のシステムで保護されたユーザープロファイルフォルダー内の JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="c53e8-135">Windows</span><span class="sxs-lookup"><span data-stu-id="c53e8-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="c53e8-136">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="c53e8-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="c53e8-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c53e8-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c53e8-138">ファイルシステムのパス:</span><span class="sxs-lookup"><span data-stu-id="c53e8-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="c53e8-139">上記のファイルパスで、を`<user_secrets_id>` *.csproj*ファイル`UserSecretsId`で指定された値に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="c53e8-140">シークレットマネージャーツールで保存したデータの場所または形式に依存するコードは記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="c53e8-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="c53e8-141">これらの実装の詳細は変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c53e8-141">These implementation details may change.</span></span> <span data-ttu-id="c53e8-142">たとえば、シークレット値は暗号化されませんが、将来の可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c53e8-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="c53e8-143">シークレットマネージャーツールをインストールする</span><span class="sxs-lookup"><span data-stu-id="c53e8-143">Install the Secret Manager tool</span></span>

<span data-ttu-id="c53e8-144">Secret Manager ツールは、.NET Core SDK 2.1.300 以降の .NET Core CLI にバンドルされています。</span><span class="sxs-lookup"><span data-stu-id="c53e8-144">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="c53e8-145">2\.1.300 の前にバージョンを .NET Core SDK には、ツールのインストールが必要です。</span><span class="sxs-lookup"><span data-stu-id="c53e8-145">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="c53e8-146">コマンド`dotnet --version`シェルからを実行して、インストールされている .NET Core SDK のバージョン番号を確認します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-146">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="c53e8-147">使用されている .NET Core SDK にツールが含まれている場合は、次のような警告が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-147">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="c53e8-148">ASP.NET Core プロジェクトに[SecretManager](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c53e8-148">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="c53e8-149">例:</span><span class="sxs-lookup"><span data-stu-id="c53e8-149">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="c53e8-150">コマンドシェルで次のコマンドを実行して、ツールのインストールを検証します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-150">Execute the following command in a command shell to validate the tool installation:</span></span>

```dotnetcli
dotnet user-secrets -h
```

<span data-ttu-id="c53e8-151">Secret Manager ツールには、使用法、オプション、およびコマンドヘルプのサンプルが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-151">The Secret Manager tool displays sample usage, options, and command help:</span></span>

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> <span data-ttu-id="c53e8-152">*.Csproj*ファイルの`DotNetCliToolReference`要素で定義されているツールを実行するには、 *.csproj*ファイルと同じディレクトリに存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c53e8-152">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="c53e8-153">シークレットストレージを有効にする</span><span class="sxs-lookup"><span data-stu-id="c53e8-153">Enable secret storage</span></span>

<span data-ttu-id="c53e8-154">Secret Manager ツールは、ユーザープロファイルに格納されているプロジェクト固有の構成設定を操作します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-154">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c53e8-155">Secret Manager ツールには .NET Core SDK `init` 3.0.100 以降のコマンドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="c53e8-155">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="c53e8-156">ユーザーシークレットを使用するには、プロジェクトディレクトリで次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-156">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="c53e8-157">上のコマンドは、 `UserSecretsId` *.csproj*ファイルの`PropertyGroup`内に要素を追加します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-157">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="c53e8-158">既定では、の`UserSecretsId`内部テキストは GUID です。</span><span class="sxs-lookup"><span data-stu-id="c53e8-158">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="c53e8-159">内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="c53e8-159">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c53e8-160">ユーザーシークレットを使用するには`UserSecretsId` 、 *.csproj*ファイル`PropertyGroup`の内に要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-160">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="c53e8-161">の`UserSecretsId`内部テキストは任意ですが、プロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="c53e8-161">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="c53e8-162">通常、 `UserSecretsId`開発者はの GUID を生成します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-162">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="c53e8-163">Visual Studio で、ソリューションエクスプローラーでプロジェクトを右クリックし、コンテキストメニューから **[ユーザーシークレットの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-163">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="c53e8-164">このジェスチャは、 `UserSecretsId` GUID が設定された要素を .csproj ファイルに追加し*ます*。</span><span class="sxs-lookup"><span data-stu-id="c53e8-164">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="c53e8-165">シークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="c53e8-165">Set a secret</span></span>

<span data-ttu-id="c53e8-166">キーとその値で構成されるアプリシークレットを定義します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-166">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="c53e8-167">シークレットは、プロジェクトの`UserSecretsId`値に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-167">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="c53e8-168">たとえば、 *.csproj*ファイルが存在するディレクトリから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-168">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="c53e8-169">前の例では、コロンは、 `Movies`が`ServiceApiKey`プロパティを持つオブジェクトリテラルであることを示しています。</span><span class="sxs-lookup"><span data-stu-id="c53e8-169">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="c53e8-170">シークレットマネージャーツールは、他のディレクトリからも使用できます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-170">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="c53e8-171">.Csproj ファイル`--project`が存在するファイルシステムパスを指定するには、オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-171">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="c53e8-172">例:</span><span class="sxs-lookup"><span data-stu-id="c53e8-172">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="c53e8-173">Visual Studio での JSON 構造のフラット化</span><span class="sxs-lookup"><span data-stu-id="c53e8-173">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="c53e8-174">Visual Studio の **[ユーザーシークレットの管理]** ジェスチャは、テキストエディターで*シークレットの json*ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-174">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="c53e8-175">*シークレット*の内容を、格納されるキーと値のペアで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-175">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="c53e8-176">例:</span><span class="sxs-lookup"><span data-stu-id="c53e8-176">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c53e8-177">JSON 構造体は、または`dotnet user-secrets remove` `dotnet user-secrets set`を使用した変更後にフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-177">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="c53e8-178">たとえば、を実行`dotnet user-secrets remove "Movies:ConnectionString"`すると`Movies` 、オブジェクトリテラルが折りたたまれます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-178">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="c53e8-179">変更後のファイルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c53e8-179">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="c53e8-180">複数のシークレットを設定する</span><span class="sxs-lookup"><span data-stu-id="c53e8-180">Set multiple secrets</span></span>

<span data-ttu-id="c53e8-181">シークレットのバッチは、JSON を`set`コマンドにパイプすることによって設定できます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-181">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="c53e8-182">次の例では、*入力した json*ファイルの内容を`set`コマンドにパイプ処理します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-182">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="c53e8-183">Windows</span><span class="sxs-lookup"><span data-stu-id="c53e8-183">Windows</span></span>](#tab/windows)

<span data-ttu-id="c53e8-184">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-184">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="c53e8-185">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="c53e8-185">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="c53e8-186">コマンドシェルを開き、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-186">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="c53e8-187">シークレットにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c53e8-187">Access a secret</span></span>

<span data-ttu-id="c53e8-188">[ASP.NET Core 構成 API](xref:fundamentals/configuration/index)は、シークレットマネージャーのシークレットへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-188">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="c53e8-189">プロジェクトが .NET Framework 対象である場合は、 [Microsoft. Extensions. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c53e8-189">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c53e8-190">ASP.NET Core 2.0 以降では、プロジェクトがを呼び出し<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>て、事前に構成された既定値でホストの新しいインスタンスを初期化すると、開発モードでユーザーシークレットの構成ソースが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-190">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="c53e8-191">`CreateDefaultBuilder`が<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*>の場合<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 、 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-191">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="c53e8-192">が`CreateDefaultBuilder`呼び出されない場合は、 `Startup`コンストラクターでを呼び出す<xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*>ことによって、ユーザーシークレット構成ソースを明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-192">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor.</span></span> <span data-ttu-id="c53e8-193">次`AddUserSecrets`の例に示すように、アプリが開発環境で実行されている場合にのみ、を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-193">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="c53e8-194">[Microsoft. extension. Configuration. UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="c53e8-194">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="c53e8-195"><xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*>コンストラクターで、の呼び出しを使用してユーザーシークレットの構成ソースを追加します。 `Startup`</span><span class="sxs-lookup"><span data-stu-id="c53e8-195">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="c53e8-196">ユーザーシークレットは API を使用し`Configuration`て取得できます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-196">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="c53e8-197">シークレットを POCO にマップする</span><span class="sxs-lookup"><span data-stu-id="c53e8-197">Map secrets to a POCO</span></span>

<span data-ttu-id="c53e8-198">オブジェクトリテラル全体を POCO (プロパティを持つ単純な .NET クラス) にマップすると、関連するプロパティを集計するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-198">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c53e8-199">前のシークレットを POCO にマップするには、 `Configuration` API の[オブジェクトグラフバインド](xref:fundamentals/configuration/index#bind-to-an-object-graph)機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-199">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="c53e8-200">次のコードは、カスタム`MovieSettings` POCO にバインドし、 `ServiceApiKey`プロパティ値にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="c53e8-200">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="c53e8-201">`MovieSettings`と`Movies:ConnectionString` シークレットは、のそれぞれのプロパティにマップされます。`Movies:ServiceApiKey`</span><span class="sxs-lookup"><span data-stu-id="c53e8-201">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="c53e8-202">シークレットを使用した文字列の置換</span><span class="sxs-lookup"><span data-stu-id="c53e8-202">String replacement with secrets</span></span>

<span data-ttu-id="c53e8-203">プレーンテキストでのパスワードの保存は安全ではありません。</span><span class="sxs-lookup"><span data-stu-id="c53e8-203">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="c53e8-204">たとえば、 *appsettings*に格納されているデータベース接続文字列には、指定されたユーザーのパスワードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-204">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="c53e8-205">より安全な方法は、パスワードをシークレットとして保存することです。</span><span class="sxs-lookup"><span data-stu-id="c53e8-205">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="c53e8-206">例えば:</span><span class="sxs-lookup"><span data-stu-id="c53e8-206">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="c53e8-207">Appsettings の`Password`接続文字列からキーと値のペアを削除します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-207">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="c53e8-208">例:</span><span class="sxs-lookup"><span data-stu-id="c53e8-208">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="c53e8-209">シークレットの値は、 <xref:System.Data.SqlClient.SqlConnectionStringBuilder>オブジェクトの<xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*>プロパティに設定して、接続文字列を完成させることができます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-209">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="c53e8-210">シークレットを一覧表示する</span><span class="sxs-lookup"><span data-stu-id="c53e8-210">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c53e8-211">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-211">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="c53e8-212">次の出力が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-212">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="c53e8-213">前の例では、キー名のコロンは、 *json*内のオブジェクト階層を表しています。</span><span class="sxs-lookup"><span data-stu-id="c53e8-213">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="c53e8-214">1つのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="c53e8-214">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c53e8-215">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-215">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="c53e8-216">アプリの*シークレットの json*ファイルは、 `MoviesConnectionString`キーに関連付けられているキーと値のペアを削除するように変更されました。</span><span class="sxs-lookup"><span data-stu-id="c53e8-216">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="c53e8-217">を`dotnet user-secrets list`実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-217">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="c53e8-218">すべてのシークレットを削除する</span><span class="sxs-lookup"><span data-stu-id="c53e8-218">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="c53e8-219">*.Csproj*ファイルが存在するディレクトリから、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="c53e8-219">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="c53e8-220">アプリのすべてのユーザーシークレットが、*シークレットの json*ファイルから削除されています。</span><span class="sxs-lookup"><span data-stu-id="c53e8-220">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="c53e8-221">を`dotnet user-secrets list`実行すると、次のメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c53e8-221">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="c53e8-222">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c53e8-222">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
