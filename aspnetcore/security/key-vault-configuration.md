---
title: ASP.NET Core の構成プロバイダーの Azure Key Vault
author: guardrex
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/01/2019
uid: security/key-vault-configuration
ms.openlocfilehash: 0d0b6e20a1901d4a2630ce263b5fd0cd7bcca8fe
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776651"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core の構成プロバイダーの Azure Key Vault

作成者 [Luke Latham](https://github.com/guardrex)および [Andrew Stanton-Nurse](https://github.com/anurse)

このドキュメントでは、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを使用して Azure Key Vault シークレットからアプリ構成値を読み込む方法について説明します。 Azure Key Vault は、アプリとサービスで使用される暗号化キーとシークレットを保護するのに役立つクラウドベースのサービスです。 ASP.NET Core アプリで Azure Key Vault を使用する一般的なシナリオは次のとおりです。

* 機密性の高い構成データへのアクセスを制御する。
* 構成データを格納するときに、FIPS 140-2 Level 2 で検証されたハードウェアセキュリティモジュール (HSM) の要件を満たしている。

このシナリオは ASP.NET Core 2.1 以降を対象とするアプリで使用できます。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

Azure Key Vault 構成プロバイダーを使用するには、パッケージへの参照を[Microsoft. extension. AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)パッケージに追加します。

[Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)のシナリオを採用するには、[Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) にパッケージ参照を追加します。

> [!NOTE]
> このドキュメントの作成時点では、の最新の`Microsoft.Azure.Services.AppAuthentication`安定し`1.0.3`たリリースであるバージョンで、[システムによって割り当てられたマネージド id](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)がサポートされています。 *ユーザー割り当てのマネージド id*のサポートは、 `1.2.0-preview2`パッケージで利用できます。 このトピックでは、システム管理の id の使用方法を示します。提供さ`1.0.3`れるサンプル`Microsoft.Azure.Services.AppAuthentication`アプリでは、パッケージのバージョンを使用します。

## <a name="sample-app"></a>サンプル アプリ

サンプルアプリは、 `#define` *Program.cs*ファイルの先頭にあるステートメントによって決定される2つのモードのいずれかで実行されます。

* `Certificate`&ndash; Azure Key Vault に格納されているシークレットにアクセスするために Azure Key Vault クライアント ID と x.509 証明書を使用する方法を示します。 このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。
* `Managed`アプリのコードまたは構成に資格情報が格納されていない Azure AD 認証を使用して Azure Key Vault するために、 [Azure リソースのマネージド id](/azure/active-directory/managed-identities-azure-resources/overview)を使用してアプリを認証する方法について説明します。 &ndash; マネージ id を使用して認証を行う場合、Azure AD アプリケーション ID とパスワード (クライアントシークレット) は必要ありません。 サンプル`Managed`のバージョンは、Azure にデプロイする必要があります。 「 [Azure リソースの管理対象 id の使用](#use-managed-identities-for-azure-resources)」セクションのガイダンスに従ってください。

プリプロセッサディレクティブ (`#define`) を使用してサンプルアプリケーションを構成する方法の詳細については、「」を参照<xref:index#preprocessor-directives-in-sample-code>してください。

## <a name="secret-storage-in-the-development-environment"></a>開発環境でのシークレットストレージ

[シークレットマネージャーツール](xref:security/app-secrets)を使用してローカルにシークレットを設定します。 開発環境のローカルコンピューターでサンプルアプリを実行すると、シークレットはローカルシークレットマネージャーストアから読み込まれます。

Secret Manager ツールでは、 `<UserSecretsId>`アプリのプロジェクトファイルにプロパティが必要です。 プロパティ値 (`{GUID}`) を任意の一意の GUID に設定します。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層値 (構成セクション) では`:` 、 [ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名の区切り記号として (コロン) を使用します。

シークレットマネージャーは、プロジェクトのコンテンツルートに開かれたコマンドシェルから使用され`{SECRET NAME}`ます。ここで`{SECRET VALUE}` 、は名前、は値です。

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

プロジェクトのコンテンツルートからコマンドシェルで次のコマンドを実行して、サンプルアプリのシークレットを設定します。

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットが Azure Key Vault セクションを[含む運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)の Azure Key Vault に格納されて`_dev`いる場合、サフィックス`_prod`はに変更されます。 サフィックスは、構成値のソースを示すビジュアルキューをアプリの出力に提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault を使用した運用環境のシークレットストレージ

[クイックスタートで説明されている手順は次のとおりです。Azure CLI](/azure/key-vault/quick-create-cli)トピックを使用して Azure Key Vault からシークレットを設定および取得する方法については、「Azure Key Vault を作成する」および「サンプルアプリで使用するシークレットの保存」を参照してください。 詳細については、「」を参照してください。

1. [Azure portal](https://portal.azure.com/)の次のいずれかの方法を使用して、Azure Cloud shell を開きます。

   * コードブロックの右上隅にある **[試してみる]** を選択します。 テキストボックス内の検索文字列 "Azure CLI" を使用します。
   * **[Cloud Shell の起動]** ボタンを使用して、ブラウザーで Cloud Shell を開きます。
   * Azure portal の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。

   詳細については、「 [Azure コマンドラインインターフェイス (CLI)](/cli/azure/) 」および「 [Azure Cloud Shell の概要](/azure/cloud-shell/overview)」を参照してください。

1. まだ認証されていない場合は、 `az login`コマンドを使用してサインインします。

1. 次のコマンド`{RESOURCE GROUP NAME}`を使用してリソースグループを作成し`{LOCATION}`ます。は、新しいリソースグループのリソースグループ名で、は Azure リージョン (datacenter) です。

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、リソースグループにキーコンテナーを`{KEY VAULT NAME}`作成します。ここで、は新しい`{LOCATION}` key vault の名前、は Azure リージョン (datacenter) です。

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 名前と値のペアとして、キーコンテナーにシークレットを作成します。

   Azure Key Vault のシークレット名は、英数字とダッシュに限定されます。 階層値 (構成セクション) は`--` 、区切り記号として (2 つのダッシュ) を使用します。 [ASP.NET Core 構成](xref:fundamentals/configuration/index)でサブキーからセクションを区切るために通常使用されるコロンは、key vault のシークレット名では許可されていません。 そのため、シークレットがアプリの構成に読み込まれるときに、2つのダッシュが使用され、コロンにスワップされます。

   サンプルアプリで使用するシークレットは次のとおりです。 値には、 `_prod`開発環境で読み込まれた`_dev`サフィックス値とユーザーシークレットから区別するためのサフィックスが含まれます。 は`{KEY VAULT NAME}` 、前の手順で作成したキーコンテナーの名前に置き換えます。

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Azure でホストされていないアプリにアプリケーション ID と x.509 証明書を使用する

**アプリが Azure の外部でホストされている場合**に、AZURE ACTIVE DIRECTORY アプリケーション ID と x.509 証明書を使用して Key Vault に対する認証を行うように Azure AD、Azure Key Vault、アプリを構成します。 詳細については、「[キー、シークレット、および証明書について](/azure/key-vault/about-keys-secrets-and-certificates)」を参照してください。

> [!NOTE]
> Azure でホストされているアプリでは、アプリケーション ID と x.509 証明書を使用することができますが、azure でアプリをホストする場合は、 [azure リソースの管理対象 id](#use-managed-identities-for-azure-resources)を使用することをお勧めします。 マネージド id では、アプリまたは開発環境に証明書を保存する必要はありません。

このサンプルアプリでは、 `#define` *Program.cs*ファイルの先頭にあるステートメントがに`Certificate`設定されている場合に、アプリケーション ID と x.509 証明書を使用します。

1. PKCS # 12 アーカイブ ( *.pfx*) 証明書を作成します。 証明書を作成するためのオプションとして、Windows と[OpenSSL](https://www.openssl.org/)[の MakeCert](/windows/desktop/seccrypto/makecert)があります。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをエクスポート可能としてマークすることはオプションです。 このプロセスの後半で使用される証明書の拇印をメモしておきます。
1. PKCS # 12 アーカイブ ( *.pfx*) 証明書を DER でエンコードされた証明書 ( *.cer*) としてエクスポートします。
1. アプリを Azure AD (**アプリの登録**) に登録します。
1. DER でエンコードされた証明書 ( *.cer*) を Azure AD にアップロードします。
   1. Azure AD でアプリを選択します。
   1. **[証明書 & シークレット]** に移動します。
   1. 公開キーを含む証明書をアップロードするには、 **[証明書のアップロード]** を選択します。 *.Cer*、 *pem*、または *.crt*証明書を使用できます。
1. Key vault 名、アプリケーション ID、および証明書の拇印をアプリの*appsettings*ファイルに格納します。
1. Azure portal の**キーコンテナー**に移動します。
1. Azure Key Vault セクションで、[運用環境のシークレットストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)に作成したキーコンテナーを選択します。
1. **[アクセスポリシー]** を選択します。
1. **[新規追加]** を選択します。
1. **[プリンシパルの選択]** を選択し、名前で登録済みのアプリを選択します。 **[選択]** ボタンを選択します。
1. **[シークレットのアクセス許可]** を開き、アプリに**Get**および**List**アクセス許可を提供します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリをデプロイします。

サンプル`Certificate`アプリでは、シークレット名と`IConfigurationRoot`同じ名前を使用してから構成値を取得します。

* 非階層値:の`SecretName`値は、を使用`config["SecretName"]`して取得されます。
* 階層値 (セクション):( `:`コロン) 表記`GetSection`または拡張メソッドを使用します。 次のいずれかの方法を使用して、構成値を取得します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は OS によって管理されます。 アプリは、 `AddAzureKeyVault` *appsettings*ファイルによって指定された値を使用してを呼び出します。

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

値の例:

* Key vault 名:`contosovault`
* アプリケーション ID:`627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値は`_dev`サフィックス付きで読み込まれます。 運用環境では、値はという`_prod`サフィックスで読み込まれます。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースの管理対象 id を使用する

**Azure にデプロイされたアプリ**は、 [Azure リソースの管理対象 id](/azure/active-directory/managed-identities-azure-resources/overview)を利用できます。これにより、資格情報のない AZURE AD 認証 (アプリケーション ID とパスワード/クライアントシークレット) を使用して、アプリが Azure Key Vault で認証できるようになります。アプリに保存されます。

このサンプルアプリでは、 *Program.cs*ファイルの先頭に`#define`あるステートメントがに`Managed`設定されている場合に、Azure リソースの管理対象 id を使用します。

アプリの*appsettings*ファイルにコンテナー名を入力します。 このサンプルアプリでは、 `Managed`バージョンに設定するときにアプリケーション ID とパスワード (クライアントシークレット) は必要ありません。そのため、これらの構成エントリは無視してかまいません。 アプリが Azure にデプロイされ、Azure は、 *appsettings*ファイルに格納されているコンテナー名を使用してのみ Azure Key Vault にアクセスするようにアプリを認証します。

サンプルアプリを Azure App Service にデプロイします。

Azure App Service にデプロイされたアプリは、サービスの作成時に Azure AD に自動的に登録されます。 次のコマンドで使用するために、デプロイからオブジェクト ID を取得します。 オブジェクト ID は、App Service の **[id]** パネルの Azure portal に表示されます。

Azure CLI とアプリのオブジェクト ID を使用して、キーコンテナー `list`に`get`アクセスするためのアクセス許可とアクセス許可をアプリに付与します。

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

Azure CLI、PowerShell、または Azure portal を使用して**アプリを再起動**します。

サンプルアプリ:

* 接続文字列を指定せ`AzureServiceTokenProvider`ずに、クラスのインスタンスを作成します。 接続文字列が指定されていない場合、プロバイダーは Azure リソースの管理対象 id からアクセストークンを取得しようとします。
* インスタンストークン`KeyVaultClient`のコールバックを使用して、新しいが作成されます。 `AzureServiceTokenProvider`
* インスタンスは、の既定の実装で使用`IKeyVaultSecretManager`されます。この実装では、すべての`--`シークレット値が読み`:`込まれ、二重ダッシュ () がキー名のコロン () に置き換えられます。 `KeyVaultClient`

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

アプリを実行すると、web ページに読み込まれたシークレット値が表示されます。 開発環境では、シークレット値はユーザー `_dev`シークレットによって提供されるため、サフィックスが付いています。 運用環境では、値は Azure Key Vault によっ`_prod`て提供されるため、サフィックス付きで読み込まれます。

`Access denied`エラーが発生した場合は、アプリが Azure AD に登録されていること、およびキーコンテナーへのアクセスが提供されていることを確認します。 Azure でサービスを再起動したことを確認します。

## <a name="use-a-key-name-prefix"></a>キー名のプレフィックスを使用する

`AddAzureKeyVault`の実装を受け入れるオーバーロードを提供し`IKeyVaultSecretManager`ます。これにより、キーコンテナーのシークレットを構成キーに変換する方法を制御できます。 たとえば、アプリの起動時に指定されたプレフィックス値に基づいてシークレット値を読み込むようにインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づいてシークレットを読み込むことができます。

> [!WARNING]
> Key vault シークレットのプレフィックスを使用して、複数のアプリのシークレットを同じ key vault に配置したり、環境の機密情報 (*開発*、*運用*シークレットなど) を同じコンテナーに配置したりしないでください。 さまざまなアプリと開発/運用環境では、セキュリティレベルが最も高いアプリケーション環境を分離するために個別のキーコンテナーを使用することをお勧めします。

次の例では、キーコンテナー (および開発環境のシークレットマネージャーツールを使用) `5000-AppSecret`でシークレットが確立されています (キーコンテナーのシークレット名ではピリオドは使用できません)。 このシークレットは、アプリのバージョン5.0.0.0 のアプリシークレットを表します。 アプリの別のバージョンでは、5.1.0.0 は、キーコンテナーに (およびシークレットマネージャーツールを使用して) `5100-AppSecret`シークレットを追加します。 各アプリバージョンは、バージョン管理されたシークレット値`AppSecret`をとして構成に読み込みます。これにより、シークレットが読み込まれるときにバージョンが除去されます。

`AddAzureKeyVault`は、カスタム`IKeyVaultSecretManager`のを使用して呼び出されます。

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

実装`IKeyVaultSecretManager`によって、シークレットのバージョンプレフィックスに反応し、適切なシークレットが構成に読み込まれます。

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

この`Load`メソッドは、コンテナーシークレットを反復処理してバージョンプレフィックスを持つものを検索するプロバイダーアルゴリズムによって呼び出されます。 バージョンプレフィックスがで`Load`見つかった場合、アルゴリズムは、 `GetKey`メソッドを使用してシークレット名の構成名を返します。 シークレットの名前からバージョンプレフィックスを取り除き、アプリの構成名と値のペアに読み込むための残りのシークレット名を返します。

このアプローチが実装されている場合:

1. アプリのプロジェクトファイルで指定されているアプリのバージョン。 次の例では、アプリのバージョンがに`5.0.0.0`設定されています。

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. アプリケーションのプロジェクト`<UserSecretsId>`ファイルにプロパティが存在することを確認します`{GUID}` 。ここで、はユーザー指定の GUID です。

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   [シークレットマネージャーツール](xref:security/app-secrets)を使用して、次のシークレットをローカルに保存します。

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリを実行すると、key vault シークレットが読み込まれます。 の`5000-AppSecret`文字列シークレットは、アプリのプロジェクトファイル (`5.0.0.0`) で指定されているアプリのバージョンと一致します。

1. バージョン`5000` (ダッシュ付き) は、キー名から削除されます。 アプリ全体で、キー `AppSecret`を使用して構成を読み取ると、シークレットの値が読み込まれます。

1. アプリのバージョンがプロジェクトファイルでに`5.1.0.0`変更され、アプリが再度実行された場合、返されるシークレット値は`5.1.0.0_secret_value_dev`開発環境および`5.1.0.0_secret_value_prod`運用環境にあります。

> [!NOTE]
> また、に`KeyVaultClient` `AddAzureKeyVault`独自の実装を提供することもできます。 カスタムクライアントは、アプリ全体でクライアントの1つのインスタンスを共有することを許可します。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、POCO 配列にバインドするために、配列に構成値を読み取ることができます。

キーにコロン (`:`) 区切り文字を含めることができる構成ソースから読み取る場合、配列を構成するキーを区別するために数値キーセグメント (`:0:`、 `:1:`、... `:{n}:`) 詳細については[、「構成:配列をクラス](xref:fundamentals/configuration/index#bind-an-array-to-a-class)にバインドします。

Azure Key Vault キーでは、区切り記号としてコロンを使用することはできません。 このトピックで説明する方法では、階層`--`値 (セクション) の区切り記号として二重ダッシュ () を使用します。 配列キーは、2つのダッシュと数値のキーセグメント (`--0--`、 `--1--`、 &hellip; `--{n}--`) を使用して Azure Key Vault に格納されます。

JSON ファイルによって提供される次の[Serilog](https://serilog.net/) logging プロバイダーの構成を確認します。 次の2つのオブジェクトリテラルが`WriteTo`配列に定義されています。この*シンク*には、ログ出力の宛先が記述されています。

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

前の JSON ファイルに示されている構成は、二重ダッシュ (`--`) 表記と数値セグメントを使用して Azure Key Vault に格納されます。

| キー | [値] |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットの再読み込み

シークレットは、が`IConfigurationRoot.Reload()`呼び出されるまでキャッシュされます。 が実行されるまで`Reload` 、key vault 内の有効期限切れ、無効、および更新されたシークレットは、アプリで尊重されません。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効なシークレットと期限切れのシークレット

無効で有効期限が切れ`KeyVaultClientException`たシークレットは、実行時にをスローします。 アプリがスローされないようにするには、別の構成プロバイダーを使用して構成を提供するか、無効または期限切れのシークレットを更新してください。

## <a name="troubleshoot"></a>トラブルシューティング

アプリがプロバイダーを使用した構成の読み込みに失敗すると、エラーメッセージが[ASP.NET Core ログ記録インフラストラクチャ](xref:fundamentals/logging/index)に書き込まれます。 次の状況では、構成を読み込むことができません。

* Azure Active Directory で、アプリまたは証明書が正しく構成されていません。
* キーコンテナーは Azure Key Vault に存在しません。
* アプリは、key vault にアクセスする権限がありません。
* アクセスポリシーには、 `Get`と`List`のアクセス許可は含まれません。
* Key vault で、構成データ (名前と値のペア) の名前が正しくないか、存在しないか、無効であるか、または期限が切れています。
* アプリの key vault 名 (`KeyVaultName`)、Azure AD アプリケーション Id (`AzureADApplicationId`)、または Azure AD 証明書の拇印 (`AzureADCertThumbprint`) が正しくありません。
* 読み込みようとしている値の構成キー (名前) が正しくありません。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/configuration/index>
* [Microsoft Azure:Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure:Key Vault のドキュメント](/azure/key-vault/)
* [Azure Key Vault 用に HSM で保護されたキーを生成して転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイック スタート:.NET web アプリを使用して Azure Key Vault からシークレットを設定および取得する](/azure/key-vault/quick-create-net)
* [チュートリアル: .NET で Azure Windows 仮想マシンで Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)
