---
title: ASP.NET Core の構成プロバイダーの Azure Key Vault
author: guardrex
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/key-vault-configuration
ms.openlocfilehash: e0e55d40734e0cb6e3e1afe1c708ec47c6f43054
ms.sourcegitcommit: f91d322f790123d41ec3271fa084ae20ed9f89a6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155160"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="0658d-103">ASP.NET Core の構成プロバイダーの Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="0658d-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="0658d-104">[Luke Latham](https://github.com/guardrex)および[Andrew Stanton-看護師](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="0658d-104">By [Luke Latham](https://github.com/guardrex) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="0658d-105">このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0658d-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="0658d-106">Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。</span><span class="sxs-lookup"><span data-stu-id="0658d-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="0658d-107">ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0658d-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="0658d-108">機密性の高い構成データへのアクセスを制御する。</span><span class="sxs-lookup"><span data-stu-id="0658d-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="0658d-109">構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。</span><span class="sxs-lookup"><span data-stu-id="0658d-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="0658d-110">[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="0658d-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="0658d-111">パッケージ</span><span class="sxs-lookup"><span data-stu-id="0658d-111">Packages</span></span>

<span data-ttu-id="0658d-112">パッケージ[への参照を、パッケージへ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)の参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="0658d-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="0658d-113">サンプル アプリ</span><span class="sxs-lookup"><span data-stu-id="0658d-113">Sample app</span></span>

<span data-ttu-id="0658d-114">サンプルアプリは、 *Program.cs*ファイルの先頭にある `#define` ステートメントによって決定される2つのモードのいずれかで実行されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="0658d-115">`Certificate` &ndash; は、Azure Key Vault クライアント ID と x.509 証明書を使用して Azure Key Vault に格納されているシークレットにアクセスする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="0658d-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="0658d-116">このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。</span><span class="sxs-lookup"><span data-stu-id="0658d-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="0658d-117">`Managed` &ndash; では、アプリのコードまたは構成に格納されている資格情報を使用せずに Azure AD 認証で Azure Key Vault するために、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を使用してアプリを認証する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0658d-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="0658d-118">マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="0658d-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="0658d-119">このサンプルの `Managed` バージョンは、Azure にデプロイする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0658d-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="0658d-120">「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources)」セクションのガイダンスに従ってください。</span><span class="sxs-lookup"><span data-stu-id="0658d-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="0658d-121">プリプロセッサディレクティブ (`#define`) を使用してサンプルアプリケーションを構成する方法の詳細については、「<xref:index#preprocessor-directives-in-sample-code>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="0658d-122">開発環境でのシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="0658d-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="0658d-123">[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="0658d-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="0658d-124">開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0658d-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="0658d-125">Secret Manager ツールでは、アプリのプロジェクトファイルに `<UserSecretsId>` プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="0658d-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="0658d-126">プロパティ値 (`{GUID}`) を任意の一意の GUID に設定します。</span><span class="sxs-lookup"><span data-stu-id="0658d-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="0658d-127">シークレットは、名前と値のペアとして作成されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="0658d-128">階層値 (構成セクション) では、 [ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名の区切り記号として `:` (コロン) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0658d-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="0658d-129">シークレットマネージャーは、プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)に開かれているコマンドシェルから使用されます。ここで `{SECRET NAME}` は名前で、`{SECRET VALUE}` はの値です。</span><span class="sxs-lookup"><span data-stu-id="0658d-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="0658d-130">プロジェクトの[コンテンツルート](xref:fundamentals/index#content-root)からコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。</span><span class="sxs-lookup"><span data-stu-id="0658d-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="0658d-131">これらのシークレットが[Azure Key Vault セクションを含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)の Azure Key Vault に格納されている場合、`_dev` サフィックスは `_prod`に変更されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="0658d-132">サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。</span><span class="sxs-lookup"><span data-stu-id="0658d-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="0658d-133">Azure Key Vault を使用した運用環境のシークレットストレージ</span><span class="sxs-lookup"><span data-stu-id="0658d-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="0658d-134">クイックスタートに記載されている手順[: Azure CLI トピックを使用して Azure Key Vault からシークレットを設定して取得](/azure/key-vault/quick-create-cli)する方法については、Azure Key Vault の作成とサンプルアプリで使用するシークレットの保存に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="0658d-135">詳細については、「」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="0658d-136">[Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="0658d-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="0658d-137">コードブロックの右上隅にある **[試してみる]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="0658d-138">テキストボックス内の検索文字列 "Azure CLI" を使用します。</span><span class="sxs-lookup"><span data-stu-id="0658d-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="0658d-139">**[Cloud Shell の起動]** ボタンを使用して、ブラウザーで Cloud Shell を開きます。</span><span class="sxs-lookup"><span data-stu-id="0658d-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="0658d-140">Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="0658d-141">詳細については、「 [Azure コマンドラインインターフェイス (CLI)](/cli/azure/) 」および「 [Azure Cloud Shell の概要](/azure/cloud-shell/overview)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-141">For more information, see [Azure Command-Line Interface (CLI)](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="0658d-142">まだ認証されていない場合は、`az login` コマンドを使用してサインインします。</span><span class="sxs-lookup"><span data-stu-id="0658d-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="0658d-143">次のコマンドを使用してリソースグループを作成します。ここで `{RESOURCE GROUP NAME}` は、新しいリソースグループのリソースグループ名で、`{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="0658d-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0658d-144">次のコマンドを使用して、リソースグループにキーコンテナーを作成します。ここで `{KEY VAULT NAME}` は新しいキーコンテナーの名前、`{LOCATION}` は Azure リージョン (datacenter) です。</span><span class="sxs-lookup"><span data-stu-id="0658d-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azure-cli
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="0658d-145">名前と値のペアとして、キーコンテナーにシークレットを作成します。</span><span class="sxs-lookup"><span data-stu-id="0658d-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="0658d-146">Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="0658d-147">階層値 (構成セクション) は、区切り記号として `--` (2 つのダッシュ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0658d-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="0658d-148">[ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。</span><span class="sxs-lookup"><span data-stu-id="0658d-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="0658d-149">そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。</span><span class="sxs-lookup"><span data-stu-id="0658d-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="0658d-150">サンプルアプリで使用するシークレットは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="0658d-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="0658d-151">値には、開発環境で読み込まれた `_dev` サフィックス値とユーザーシークレットから区別するための `_prod` サフィックスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0658d-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="0658d-152">`{KEY VAULT NAME}` は、前の手順で作成したキーコンテナーの名前に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0658d-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="0658d-153">Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する</span><span class="sxs-lookup"><span data-stu-id="0658d-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="0658d-154">**アプリが Azure の外部でホストされている場合**に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。</span><span class="sxs-lookup"><span data-stu-id="0658d-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="0658d-155">詳細については、「[キー、シークレット、および証明書について](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="0658d-156">Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0658d-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="0658d-157">マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0658d-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="0658d-158">このサンプルアプリでは、 *Program.cs*ファイルの先頭にある `#define` ステートメントが `Certificate`に設定されている場合に、アプリケーション ID と x.509 証明書を使用します。</span><span class="sxs-lookup"><span data-stu-id="0658d-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="0658d-159">PKCS # 12 アーカイブ ( *.pfx*) 証明書を作成します。</span><span class="sxs-lookup"><span data-stu-id="0658d-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="0658d-160">証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。</span><span class="sxs-lookup"><span data-stu-id="0658d-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="0658d-161">現在のユーザーの個人証明書ストアに証明書をインストールします。</span><span class="sxs-lookup"><span data-stu-id="0658d-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="0658d-162">キーをエクスポート可能としてマークすることはオプションです。</span><span class="sxs-lookup"><span data-stu-id="0658d-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="0658d-163">このプロセスの後半で使用される証明書の拇印をメモしておきます。</span><span class="sxs-lookup"><span data-stu-id="0658d-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="0658d-164">PKCS # 12 アーカイブ ( *.pfx*) 証明書を DER でエンコードされた証明書 ( *.cer*) としてエクスポートします。</span><span class="sxs-lookup"><span data-stu-id="0658d-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="0658d-165">アプリを Azure AD (**アプリの登録**) に登録します。</span><span class="sxs-lookup"><span data-stu-id="0658d-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="0658d-166">DER でエンコードされた証明書 ( *.cer*) を Azure AD にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="0658d-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="0658d-167">Azure AD でアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="0658d-168">**[証明書 & シークレット]** に移動します。</span><span class="sxs-lookup"><span data-stu-id="0658d-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="0658d-169">公開キーを含む証明書をアップロードするには、 **[証明書のアップロード]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="0658d-170">*.Cer*、 *pem*、または *.crt*証明書を使用できます。</span><span class="sxs-lookup"><span data-stu-id="0658d-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="0658d-171">Key vault 名、アプリケーション ID、および証明書の拇印をアプリの*appsettings*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="0658d-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="0658d-172">Azure portal の**キーコンテナー**に移動します。</span><span class="sxs-lookup"><span data-stu-id="0658d-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="0658d-173">Azure Key Vault セクションで、[運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)に作成したキーコンテナーを選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="0658d-174">**[アクセスポリシー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="0658d-175">**[アクセスポリシーの追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="0658d-176">**[シークレットのアクセス許可]** を開き、アプリに**Get**および**List**アクセス許可を提供します。</span><span class="sxs-lookup"><span data-stu-id="0658d-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="0658d-177">**[プリンシパルの選択]** を選択し、名前で登録済みのアプリを選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="0658d-178">**[選択]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="0658d-179">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-179">Select **OK**.</span></span>
1. <span data-ttu-id="0658d-180">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="0658d-180">Select **Save**.</span></span>
1. <span data-ttu-id="0658d-181">アプリをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="0658d-181">Deploy the app.</span></span>

<span data-ttu-id="0658d-182">`Certificate` サンプルアプリは、シークレット名と同じ名前の `IConfigurationRoot` から構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="0658d-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="0658d-183">非階層値: `SecretName` の値は `config["SecretName"]`で取得されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="0658d-184">階層値 (セクション): `:` (コロン) 表記または `GetSection` 拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="0658d-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="0658d-185">次のいずれかの方法を使用して、構成値を取得します。</span><span class="sxs-lookup"><span data-stu-id="0658d-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="0658d-186">X.509 証明書は OS によって管理されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="0658d-187">アプリは、appsettings ファイルによって指定さ*れ*た値を使用して <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0658d-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

::: moniker-end

<span data-ttu-id="0658d-188">値の例:</span><span class="sxs-lookup"><span data-stu-id="0658d-188">Example values:</span></span>

* <span data-ttu-id="0658d-189">Key vault 名: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0658d-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="0658d-190">アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="0658d-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="0658d-191">証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="0658d-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="0658d-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0658d-192">*appsettings.json*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

::: moniker-end

<span data-ttu-id="0658d-193">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0658d-194">開発環境では、シークレット値は `_dev` サフィックスで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0658d-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="0658d-195">運用環境では、値は `_prod` サフィックスで読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0658d-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="0658d-196">Azure リソースの管理対象 id を使用する</span><span class="sxs-lookup"><span data-stu-id="0658d-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="0658d-197">**Azure にデプロイされたアプリ**は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、アプリは、アプリに格納されている資格情報 (アプリケーション ID とパスワード/クライアントシークレット) を使用せずに Azure AD 認証を使用して Azure Key Vault で認証することができます。</span><span class="sxs-lookup"><span data-stu-id="0658d-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="0658d-198">このサンプルアプリでは、 *Program.cs*ファイルの先頭にある `#define` ステートメントが `Managed`に設定されている場合に、Azure リソースの管理対象 id を使用します。</span><span class="sxs-lookup"><span data-stu-id="0658d-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="0658d-199">アプリの*appsettings*ファイルにコンテナー名を入力します。</span><span class="sxs-lookup"><span data-stu-id="0658d-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="0658d-200">このサンプルアプリでは、`Managed` バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません。そのため、これらの構成エントリは無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="0658d-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="0658d-201">アプリが Azure にデプロイされ、Azure は、 *appsettings*ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証します。</span><span class="sxs-lookup"><span data-stu-id="0658d-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="0658d-202">サンプルアプリを Azure App Service にデプロイします。</span><span class="sxs-lookup"><span data-stu-id="0658d-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="0658d-203">Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="0658d-204">次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="0658d-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="0658d-205">オブジェクト ID は、App Service の **[id]** パネルの Azure portal に表示されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="0658d-206">Azure CLI とアプリのオブジェクト ID を使用して、キーコンテナーにアクセスするための `list` と `get` アクセス許可をアプリに提供します。</span><span class="sxs-lookup"><span data-stu-id="0658d-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azure-cli
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="0658d-207">Azure CLI、PowerShell、または Azure portal を使用して**アプリを再起動**します。</span><span class="sxs-lookup"><span data-stu-id="0658d-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="0658d-208">サンプルアプリ:</span><span class="sxs-lookup"><span data-stu-id="0658d-208">The sample app:</span></span>

* <span data-ttu-id="0658d-209">接続文字列を指定せずに `AzureServiceTokenProvider` クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="0658d-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="0658d-210">接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="0658d-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="0658d-211">`AzureServiceTokenProvider` インスタンストークンのコールバックを使用して、新しい <xref:Microsoft.Azure.KeyVault.KeyVaultClient> が作成されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="0658d-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> インスタンスは、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> の既定の実装で使用されます。この実装では、すべてのシークレット値が読み込まれ、二重ダッシュ (`--`) がキー名のコロン (`:`) で置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="0658d-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

::: moniker-end

<span data-ttu-id="0658d-213">Key vault 名の例の値: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="0658d-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="0658d-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0658d-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="0658d-215">アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="0658d-216">開発環境では、シークレット値はユーザーシークレットによって提供されるため、`_dev` のサフィックスが付いています。</span><span class="sxs-lookup"><span data-stu-id="0658d-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="0658d-217">運用環境では、値は Azure Key Vault によって提供されるため、`_prod` のサフィックスを使用して読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0658d-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="0658d-218">`Access denied` エラーが発生した場合は、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="0658d-219">Azure でサービスを再起動したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="0658d-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="0658d-220">マネージ id と Azure DevOps パイプラインでプロバイダーを使用する方法の詳細については、「[管理対象サービス id を使用して VM への Azure Resource Manager サービス接続を作成](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)する」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configuration-options"></a><span data-ttu-id="0658d-221">構成オプション</span><span class="sxs-lookup"><span data-stu-id="0658d-221">Configuration options</span></span>

<span data-ttu-id="0658d-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="0658d-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="0658d-223">property</span><span class="sxs-lookup"><span data-stu-id="0658d-223">Property</span></span>         | <span data-ttu-id="0658d-224">説明</span><span class="sxs-lookup"><span data-stu-id="0658d-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="0658d-225">値を取得するために使用する <xref:Microsoft.Azure.KeyVault.KeyVaultClient>。</span><span class="sxs-lookup"><span data-stu-id="0658d-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="0658d-226">シークレットの読み込みを制御するために使用される <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> インスタンス。</span><span class="sxs-lookup"><span data-stu-id="0658d-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="0658d-227">キーコンテナーのポーリングによって変更が試行されるまでの待機時間を `Timespan` します。</span><span class="sxs-lookup"><span data-stu-id="0658d-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="0658d-228">既定値は `null` です (構成は再読み込みされません)。</span><span class="sxs-lookup"><span data-stu-id="0658d-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="0658d-229">Key vault URI。</span><span class="sxs-lookup"><span data-stu-id="0658d-229">Key vault URI.</span></span> |

::: moniker-end

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="0658d-230">キー名のプレフィックスを使用する</span><span class="sxs-lookup"><span data-stu-id="0658d-230">Use a key name prefix</span></span>

<span data-ttu-id="0658d-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> には、<xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>の実装を受け入れるオーバーロードが用意されています。これを使用すると、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。</span><span class="sxs-lookup"><span data-stu-id="0658d-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="0658d-232">たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="0658d-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="0658d-233">これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="0658d-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="0658d-234">Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 (*開発*、*運用*シークレットなど) を同じコンテナーに配置したりしないでください。</span><span class="sxs-lookup"><span data-stu-id="0658d-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="0658d-235">さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0658d-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="0658d-236">次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) で `5000-AppSecret` (キーコンテナーのシークレット名にはピリオドは使用できません) にシークレットが設定されています。</span><span class="sxs-lookup"><span data-stu-id="0658d-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="0658d-237">このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。</span><span class="sxs-lookup"><span data-stu-id="0658d-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="0658d-238">5\.1.0.0 の別のバージョンのアプリでは、キーコンテナーにシークレット (およびシークレットマネージャーツールを使用) が `5100-AppSecret`に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="0658d-239">各アプリバージョンでは、バージョン管理されているシークレット値が `AppSecret`として構成に読み込まれ、シークレットが読み込まれるときにバージョンが除去されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="0658d-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> は、カスタム <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>を使用して呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="0658d-241"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> の実装は、構成に適切なシークレットを読み込むために、シークレットのバージョンプレフィックスに反応します。</span><span class="sxs-lookup"><span data-stu-id="0658d-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="0658d-242">`Load` は、名前がプレフィックスで始まるときにシークレットを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0658d-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="0658d-243">他のシークレットは読み込まれていません。</span><span class="sxs-lookup"><span data-stu-id="0658d-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="0658d-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="0658d-244">`GetKey`:</span></span>
  * <span data-ttu-id="0658d-245">シークレット名からプレフィックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="0658d-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="0658d-246">任意の名前の2つのダッシュを `KeyDelimiter`に置き換えます。これは、構成で使用される区切り記号 (通常はコロン) です。</span><span class="sxs-lookup"><span data-stu-id="0658d-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="0658d-247">Azure Key Vault では、シークレット名にコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="0658d-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="0658d-248">`Load` メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを見つけるプロバイダーアルゴリズムによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="0658d-249">`Load`でバージョンプレフィックスが見つかった場合、アルゴリズムは、`GetKey` メソッドを使用してシークレット名の構成名を返します。</span><span class="sxs-lookup"><span data-stu-id="0658d-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="0658d-250">シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。</span><span class="sxs-lookup"><span data-stu-id="0658d-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="0658d-251">このアプローチが実装されている場合:</span><span class="sxs-lookup"><span data-stu-id="0658d-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="0658d-252">アプリのプロジェクトファイルで指定されているアプリのバージョン。</span><span class="sxs-lookup"><span data-stu-id="0658d-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="0658d-253">次の例では、アプリのバージョンが `5.0.0.0`に設定されています。</span><span class="sxs-lookup"><span data-stu-id="0658d-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="0658d-254">アプリのプロジェクトファイルに `<UserSecretsId>` プロパティが存在することを確認します。 `{GUID}` はユーザー指定の GUID です。</span><span class="sxs-lookup"><span data-stu-id="0658d-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="0658d-255">[シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。</span><span class="sxs-lookup"><span data-stu-id="0658d-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="0658d-256">シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azure-cli
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="0658d-257">アプリを実行すると、key vault シークレットが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="0658d-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="0658d-258">`5000-AppSecret` の文字列シークレットは、アプリのプロジェクトファイル (`5.0.0.0`) で指定されているアプリのバージョンと一致します。</span><span class="sxs-lookup"><span data-stu-id="0658d-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="0658d-259">`5000` バージョン (ダッシュ付き) がキー名から削除されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="0658d-260">アプリ全体で、キー `AppSecret` 使用して構成を読み取り、シークレット値を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="0658d-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="0658d-261">アプリのバージョンがプロジェクトファイルで `5.1.0.0` に変更され、アプリが再度実行された場合、返されたシークレット値は開発環境で `5.1.0.0_secret_value_dev` され、運用環境で `5.1.0.0_secret_value_prod` ます。</span><span class="sxs-lookup"><span data-stu-id="0658d-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="0658d-262">独自の <xref:Microsoft.Azure.KeyVault.KeyVaultClient> の実装を提供して <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>することもできます。</span><span class="sxs-lookup"><span data-stu-id="0658d-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="0658d-263">カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。</span><span class="sxs-lookup"><span data-stu-id="0658d-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="0658d-264">配列をクラスにバインドする</span><span class="sxs-lookup"><span data-stu-id="0658d-264">Bind an array to a class</span></span>

<span data-ttu-id="0658d-265">プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="0658d-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="0658d-266">キーがコロン (`:`) 区切り記号を含むように構成ソースから読み取る場合、配列を構成するキー (`:0:`、`:1:`、... を区別するために、数値キーセグメントが使用されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, …</span></span> <span data-ttu-id="0658d-267">`:{n}:`)</span><span class="sxs-lookup"><span data-stu-id="0658d-267">`:{n}:`).</span></span> <span data-ttu-id="0658d-268">詳細については、「[構成: 配列をクラスにバインドする](xref:fundamentals/configuration/index#bind-an-array-to-a-class)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-268">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="0658d-269">Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="0658d-269">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="0658d-270">このトピックで説明する方法では、階層値 (セクション) の区切り記号として二重ダッシュ (`--`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="0658d-270">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="0658d-271">配列キーは、二重ダッシュと数値キーセグメント (`--0--`、`--1--`、&hellip; `--{n}--`) を使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-271">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="0658d-272">JSON ファイルによって提供される次の[Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。</span><span class="sxs-lookup"><span data-stu-id="0658d-272">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="0658d-273">`WriteTo` 配列に定義されている2つのオブジェクトリテラルは、ログ出力の宛先を示す2つの Serilog*シンク*を反映しています。</span><span class="sxs-lookup"><span data-stu-id="0658d-273">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="0658d-274">前の JSON ファイルに示されている構成は、二重ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。</span><span class="sxs-lookup"><span data-stu-id="0658d-274">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="0658d-275">キー</span><span class="sxs-lookup"><span data-stu-id="0658d-275">Key</span></span> | <span data-ttu-id="0658d-276">[値]</span><span class="sxs-lookup"><span data-stu-id="0658d-276">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="0658d-277">シークレットの再読み込み</span><span class="sxs-lookup"><span data-stu-id="0658d-277">Reload secrets</span></span>

<span data-ttu-id="0658d-278">シークレットは `IConfigurationRoot.Reload()` が呼び出されるまでキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="0658d-278">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="0658d-279">`Reload` が実行されるまで、key vault 内の有効期限切れ、無効、および更新されたシークレットはアプリによって尊重されません。</span><span class="sxs-lookup"><span data-stu-id="0658d-279">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="0658d-280">無効なシークレットと期限切れのシークレット</span><span class="sxs-lookup"><span data-stu-id="0658d-280">Disabled and expired secrets</span></span>

<span data-ttu-id="0658d-281">無効で期限切れのシークレットは、<xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>をスローします。</span><span class="sxs-lookup"><span data-stu-id="0658d-281">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="0658d-282">アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。</span><span class="sxs-lookup"><span data-stu-id="0658d-282">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="0658d-283">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="0658d-283">Troubleshoot</span></span>

<span data-ttu-id="0658d-284">アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが[ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="0658d-284">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0658d-285">次の状況では、構成を読み込むことができません。</span><span class="sxs-lookup"><span data-stu-id="0658d-285">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="0658d-286">Azure Active Directory で、アプリまたは証明書が正しく構成されていません。</span><span class="sxs-lookup"><span data-stu-id="0658d-286">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="0658d-287">キーコンテナーは Azure Key Vault に存在しません。</span><span class="sxs-lookup"><span data-stu-id="0658d-287">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="0658d-288">アプリは、key vault にアクセスする権限がありません。</span><span class="sxs-lookup"><span data-stu-id="0658d-288">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="0658d-289">アクセスポリシーには、`Get` および `List` のアクセス許可は含まれません。</span><span class="sxs-lookup"><span data-stu-id="0658d-289">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="0658d-290">Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。</span><span class="sxs-lookup"><span data-stu-id="0658d-290">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="0658d-291">アプリの key vault 名 (`KeyVaultName`)、Azure AD アプリケーション Id (`AzureADApplicationId`)、Azure AD 証明書の拇印 (`AzureADCertThumbprint`) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="0658d-291">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="0658d-292">読み込みようとしている値の構成キー (名前) が正しくありません。</span><span class="sxs-lookup"><span data-stu-id="0658d-292">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="0658d-293">アプリのアクセスポリシーを key vault に追加すると、ポリシーが作成されましたが、**アクセスポリシー** UI で **[保存]** ボタンが選択されていませんでした。</span><span class="sxs-lookup"><span data-stu-id="0658d-293">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0658d-294">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="0658d-294">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="0658d-295">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="0658d-295">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="0658d-296">Microsoft Azure: Key Vault のドキュメント</span><span class="sxs-lookup"><span data-stu-id="0658d-296">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="0658d-297">Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法</span><span class="sxs-lookup"><span data-stu-id="0658d-297">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="0658d-298">KeyVaultClient クラス</span><span class="sxs-lookup"><span data-stu-id="0658d-298">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="0658d-299">クイックスタート: .NET web アプリを使用して Azure Key Vault からシークレットを設定および取得する</span><span class="sxs-lookup"><span data-stu-id="0658d-299">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="0658d-300">チュートリアル: .NET で Azure Windows 仮想マシンと共に Azure Key Vault を使用する方法</span><span class="sxs-lookup"><span data-stu-id="0658d-300">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)
