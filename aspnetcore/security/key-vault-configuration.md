---
title: ASP.NET Core での azure Key Vault 構成プロバイダー
author: guardrex
description: Azure Key Vault 構成プロバイダーを使用して、実行時に読み込まれる名前と値のペアを使用してアプリを構成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: security/key-vault-configuration
ms.openlocfilehash: be176ed612be0773c4a5b52607c023da3856ac14
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815332"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>ASP.NET Core での azure Key Vault 構成プロバイダー

作成者 [Luke Latham](https://github.com/guardrex)および [Andrew Stanton-Nurse](https://github.com/anurse)

このドキュメントは、使用する方法を説明します、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーを Azure Key Vault シークレットからアプリの構成値を読み込めません。 Azure Key Vault とは、暗号化キーとシークレットをアプリやサービスで使用される保護に役立つクラウド ベース サービスです。 ASP.NET Core アプリを Azure Key Vault を使用するための一般的なシナリオは次のとおりです。

* 機密性の高い構成データへのアクセスを制御します。
* 構成データを格納するときに、ハードウェア セキュリティ モジュール (HSM) の検証を 140-2 レベル 2 FIPS の要件を満たします。

このシナリオでは、ASP.NET Core 2.1 を対象とするアプリの使用可能なまたはそれ以降です。

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="packages"></a>パッケージ

Azure Key Vault 構成プロバイダーを使用するへのパッケージ参照を追加、 [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)パッケージ。

採用する、[の Azure リソースの id を管理](/azure/active-directory/managed-identities-azure-resources/overview)シナリオでは、パッケージ参照を追加、 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)パッケージ。

> [!NOTE]
> 最新の安定版リリースの書き込み時に`Microsoft.Azure.Services.AppAuthentication`、バージョン`1.0.3`、サポートを提供します[の id を管理システムによって割り当てられた](/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)します。 サポート*の id を管理ユーザーによって割り当てられた*で使用できるは、`1.2.0-preview2`パッケージ。 このトピックでは、システムで管理される id の使用を示します、提供されているサンプル アプリは、バージョンを使用して`1.0.3`の`Microsoft.Azure.Services.AppAuthentication`パッケージ。

## <a name="sample-app"></a>サンプル アプリ

サンプル アプリの実行によって 2 つのモードのいずれかで、`#define`の上部にあるステートメント、 *Program.cs*ファイル。

* `Certificate` &ndash; Azure Key Vault に格納されているアクセス シークレットを Azure Key Vault のクライアント ID と X.509 証明書の使用を示します。 このバージョンのサンプルは、Azure App Service または ASP.NET Core アプリのサービスを提供できる任意のホストに展開されている任意の場所から実行できます。
* `Managed` &ndash; 使用する方法を示します[の Azure リソースの id を管理](/azure/active-directory/managed-identities-azure-resources/overview)アプリのコードまたは構成に格納されている資格情報のない Azure AD 認証を使用した Azure Key Vault にアプリを認証します。 管理対象 id 認証を使用する場合、Azure AD アプリケーション ID とパスワード (クライアント シークレット) は必要ありません。 `Managed`バージョンのサンプルを Azure にデプロイする必要があります。 ガイダンスに従って、 [Azure リソースの管理対象 id を使用して](#use-managed-identities-for-azure-resources)セクション。

プリプロセッサ ディレクティブを使用してサンプル アプリを構成する方法の詳細 (`#define`) を参照してください<xref:index#preprocessor-directives-in-sample-code>します。

## <a name="secret-storage-in-the-development-environment"></a>開発環境でのシークレットのストレージ

シークレットを使用してローカルの設定、 [Secret Manager ツール](xref:security/app-secrets)します。 機密情報が読み込まれているサンプル アプリを開発環境でローカル コンピューターで実行すると、ローカルの Secret Manager ストアから。

Secret Manager ツールが必要です、`<UserSecretsId>`アプリのプロジェクト ファイルのプロパティ。 プロパティ値を設定 (`{GUID}`) の一意の guid。

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

シークレットは、名前と値のペアとして作成されます。 階層型の値 (構成セクション) を使用して、 `:` (コロン) で区切り記号として[ASP.NET Core 構成](xref:fundamentals/configuration/index)キー名。

Secret Manager は、プロジェクトのコンテンツのルートに開かれているコマンド シェルから使用場所`{SECRET NAME}`名前と`{SECRET VALUE}`値です。

```console
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

サンプル アプリのシークレットを設定するプロジェクトのコンテンツのルートからコマンド シェルで次のコマンドを実行します。

```console
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

これらのシークレットがで Azure Key Vault に格納されている場合、 [Azure Key Vault の実稼働環境でのシークレット ストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault) セクションで、`_dev`にサフィックスが変更された`_prod`します。 サフィックスは、アプリの出力の構成値のソースを示す視覚的な合図を提供します。

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Azure Key Vault の実稼働環境でのシークレットのストレージ

手順に従って、[クイック スタート。設定して、Azure CLI を使用して Azure Key Vault からシークレットを取得](/azure/key-vault/quick-create-cli)トピックでは、Azure Key Vault を作成およびサンプル アプリで使用されるシークレットを格納するためここで説明します。 詳細については、トピックを参照してください。

1. Azure のクラウド シェルを開くには、次のメソッドのいずれかを使用して、 [Azure portal](https://portal.azure.com/):

   * 選択**試して**コード ブロックの右上隅にします。 テキスト ボックスに検索文字列の"Azure CLI"を使用します。
   * ブラウザーで Cloud Shell を開き、 **Cloud Shell の起動**ボタンをクリックします。
   * 選択、 **Cloud Shell** Azure portal の右上隅のメニュー ボタンをクリックします。

   詳細については、次を参照してください。 [Azure コマンド ライン インターフェイス (CLI)](/cli/azure/)と[Azure Cloud Shell の概要](/azure/cloud-shell/overview)します。

1. 既に認証されていない場合でサインイン、`az login`コマンド。

1. 次のコマンドでリソース グループを作成、`{RESOURCE GROUP NAME}`は新しいリソース グループのリソース グループ名と`{LOCATION}`Azure リージョン (データ センター) には。

   ```console
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 次のコマンドを使用して、リソース グループに key vault を作成場所`{KEY VAULT NAME}`、新しいキー コンテナーの名前を指定および`{LOCATION}`は Azure の地域 (データ センター)。

   ```console
   az keyvault create --name "{KEY VAULT NAME}" --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. 名前と値のペアとして、key vault にシークレットを作成します。

   Azure Key Vault シークレットの名前は、英数字とダッシュに制限されます。 階層型の値 (構成セクション) を使用して、 `--` (ダッシュを 2 つ) を区切り記号として。 サブキーのセクションを区切るために通常使用される、コロン[ASP.NET Core 構成](xref:fundamentals/configuration/index)、key vault のシークレット名では許可されません。 そのため、2 つのダッシュが使用され、シークレットは、アプリの構成に読み込まれるときに、コロンのスワップします。

   次のシークレットは、サンプル アプリで使用するためです。 値を`_prod`サフィックスから区別するために、`_dev`ユーザー シークレットから開発環境に読み込まれた値のサフィックスします。 置換`{KEY VAULT NAME}`前の手順で作成した key vault の名前に置き換えます。

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>アプリケーション ID と X.509 証明書を使用して、Azure でホストされているアプリ

Azure AD を構成、キー コンテナーへの認証に証明書を Azure Active Directory アプリケーション ID と X.509 を使用するには、Azure Key Vault とアプリ **、アプリが Azure の外部でホストされている**します。 詳細については、次を参照してください。[キー、シークレット、および証明書に関する](/azure/key-vault/about-keys-secrets-and-certificates)します。

> [!NOTE]
> 使用が推奨アプリを Azure でホストされているは、アプリケーション ID と X.509 証明書を使用がサポートされています、[の Azure リソースの id を管理](#use-managed-identities-for-azure-resources)Azure でのアプリをホストする場合。 管理対象 id は、アプリや開発環境では、証明書を格納する必要はありません。

アプリケーション ID と X.509 証明書の場合にサンプル アプリでは、`#define`の上部にあるステートメント、 *Program.cs*に設定されているファイル`Certificate`します。

1. PKCS #12 アーカイブを作成 ( *.pfx*) 証明書。 証明書を作成するためのオプションがあります。 [Windows で MakeCert](/windows/desktop/seccrypto/makecert)と[OpenSSL](https://www.openssl.org/)します。
1. 現在のユーザーの個人証明書ストアに証明書をインストールします。 キーをマークするエクスポート可能とは省略可能です。 このプロセスの後半で使用される証明書の拇印に注意してください。
1. PKCS #12 アーカイブのエクスポート ( *.pfx*) DER でエンコードされた証明書として証明書 ( *.cer*)。
1. Azure AD にアプリを登録 (**アプリの登録**)。
1. DER でエンコードされた証明書のアップロード ( *.cer*) Azure AD に。
   1. Azure AD でアプリを選択します。
   1. 移動します**証明書およびシークレット**します。
   1. 選択**証明書のアップロード**パブリック キーを含む証明書をアップロードします。 A *.cer*、 *.pem*、または *.crt*証明書は許容されます。
1. アプリの key vault 名、アプリケーション ID、および証明書の拇印を格納する*appsettings.json*ファイル。
1. 移動します**キー コンテナー** Azure portal でします。
1. 作成した key vault の選択、 [Azure Key Vault の実稼働環境でのシークレット ストレージ](#secret-storage-in-the-production-environment-with-azure-key-vault)セクション。
1. 選択**アクセス ポリシー**します。
1. 選択**新規追加**します。
1. 選択**プリンシパルの選択**名前で登録済みのアプリを選択します。 選択、**選択**ボタンをクリックします。
1. 開いている**シークレットのアクセス許可**を使用してアプリを提供し、**取得**と**一覧**アクセス許可。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
1. アプリをデプロイします。

`Certificate`サンプル アプリからその構成値を取得する`IConfigurationRoot`シークレットの名前と同じ名前で。

* 非階層型の値:値は、`SecretName`では、行わ`config["SecretName"]`します。
* 階層型の値 (セクション)。使用`:`(コロン) 表記または`GetSection`拡張メソッド。 構成値を取得するのにには、これらの方法のいずれかを使用します。
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

X.509 証明書は、OS によって管理されます。 アプリによる呼び出し`AddAzureKeyVault`によって提供される値を持つ、 *appsettings.json*ファイル。

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet1&highlight=20-23)]

値の例:

* Key vault 名: `contosovault`
* アプリケーション ID: `627e911e-43cc-61d4-992e-12db9c81b413`
* 証明書の拇印: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/sample/appsettings.json)]

アプリを実行すると、web ページには、読み込まれた、シークレットの値が表示されます。 開発環境でシークレット値を読み込むと、`_dev`サフィックス。 運用環境で、値を読み込むと、`_prod`サフィックス。

## <a name="use-managed-identities-for-azure-resources"></a>Azure リソースの管理対象 id を使用してください。

**Azure にデプロイされたアプリ**活用できる[の Azure リソースの id を管理](/azure/active-directory/managed-identities-azure-resources/overview)、資格情報なしの Azure AD 認証を使用して Azure Key Vault での認証にアプリを許可する (アプリケーション ID とPassword/Client シークレット)、アプリに格納されています。

サンプル アプリは Azure リソースの管理対象 id を使用時に、`#define`の上部にあるステートメント、 *Program.cs*に設定されているファイル`Managed`します。

アプリの資格情報コンテナー名を入力します。 *appsettings.json*ファイル。 アプリケーション ID とパスワード (クライアント シークレット) に設定すると、サンプル アプリが必要としない、`Managed`バージョンについては、これらの構成エントリは無視できます。 アプリが Azure にデプロイし、Azure に格納されている場合のみ、コンテナー名を使用して Azure Key Vault にアクセスするアプリで認証される、 *appsettings.json*ファイル。

サンプル アプリを Azure App Service にデプロイします。

Azure App Service にデプロイされたアプリは、自動的にサービスを作成するときに Azure AD に登録されます。 次のコマンドで使用するためのデプロイからのオブジェクト ID を取得します。 オブジェクト ID が Azure portal に表示されます、 **Identity** App Service のパネルです。

使用してアプリを提供するアプリのオブジェクト ID では、Azure CLI を使用して`list`と`get`key vault へのアクセス許可。

```console
az keyvault set-policy --name '{KEY VAULT NAME}' --object-id {OBJECT ID} --secret-permissions get list
```

**アプリの再起動**Azure CLI、PowerShell、または Azure portal を使用します。

サンプル アプリ:

* インスタンスを作成、`AzureServiceTokenProvider`接続文字列を使用せずクラス。 接続文字列が指定されない場合、プロバイダーは、Azure リソースの管理対象 id からアクセス トークンを取得しようとします。
* 新しい`KeyVaultClient`で作成されたが、`AzureServiceTokenProvider`インスタンス トークンのコールバック。
* `KeyVaultClient`の既定の実装のインスタンスを使用`IKeyVaultSecretManager`をすべてのシークレット値を読み込むし、二重ダッシュに置き換えられます (`--`) にコロン (`:`) キー名。

[!code-csharp[](key-vault-configuration/sample/Program.cs?name=snippet2&highlight=13-21)]

アプリを実行すると、web ページには、読み込まれた、シークレットの値が表示されます。 シークレットの値がある、開発環境で、`_dev`サフィックスをユーザーの機密情報で提供しているためです。 運用環境で、値を読み込むと、`_prod`サフィックスを Azure Key Vault で提供しているためです。

表示された場合、`Access denied`エラー、アプリが Azure AD に登録され、key vault へのアクセスを提供することを確認します。 Azure でサービスを再起動したことを確認します。

## <a name="use-a-key-name-prefix"></a>キー名のプレフィックスを使用します。

`AddAzureKeyVault` 実装を受け取るオーバー ロードを提供します。 `IKeyVaultSecretManager`、構成キーに変換されます主要 vault のシークレットを制御できます。 たとえば、アプリの起動時に指定したプレフィックス値に基づくシークレットの値を読み込むインターフェイスを実装できます。 これにより、たとえば、アプリのバージョンに基づくシークレットを読み込めませんできます。

> [!WARNING]
> 同じ key vault にシークレットを複数のアプリを配置する環境のシークレットを配置するか、key vault のシークレットでプレフィックスを使用しないでください (たとえば、*開発*と*運用*シークレット) を同じ資格情報コンテナー。 別のアプリと開発/運用環境で最高レベルのセキュリティのアプリの環境を分離する個別の key vault を使用することをお勧めします。

キーに次の例では、シークレットが確立されている資格情報コンテナー (と Secret Manager ツールを使用して、開発環境用) の`5000-AppSecret`(key vault のシークレット名で許可されていない期間)。 このシークレットは、アプリのバージョン 5.0.0.0 用アプリのシークレットを表します。 5\.1.0.0、アプリの別のバージョンのシークレットが、キーに追加コンテナー (と Secret Manager ツールを使用して) の`5100-AppSecret`します。 各アプリのバージョンでは、としては、その構成にそのバージョンのシークレット値を読み込みます`AppSecret`シークレットが読み込まれるバージョンを削除します。

`AddAzureKeyVault` カスタムを使用して呼び出した`IKeyVaultSecretManager`:

[!code-csharp[](key-vault-configuration/sample_snapshot/Program.cs?highlight=30-34)]

`IKeyVaultSecretManager`実装が構成に適切なシークレットを読み込むのシークレットのバージョンのプレフィックスに反応します。

[!code-csharp[](key-vault-configuration/sample_snapshot/Startup.cs?name=snippet1)]

`Load`メソッドが付いているバージョンのプレフィックスを検索する資格情報コンテナーのシークレットを反復処理するプロバイダーのアルゴリズムによって呼び出されます。 バージョンのプレフィックスがで検出されたときに`Load`、アルゴリズムを使用して、`GetKey`シークレット名の構成名を返すメソッド。 シークレットの名前からのバージョンのプレフィックスを削除し、アプリの構成に名前/値ペアの読み込みのシークレット名の残りの部分を返します。

このアプローチが実装されている場合。

1. アプリのプロジェクト ファイルで指定されたアプリのバージョン。 次の例では、アプリのバージョンに設定されて`5.0.0.0`:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. いることを確認、`<UserSecretsId>`プロパティは、アプリのプロジェクト ファイルに存在する場所`{GUID}`はユーザーが指定した GUID です。

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   ローカルで次のシークレットを保存、 [Secret Manager ツール](xref:security/app-secrets):

   ```console
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. シークレットは、次の Azure CLI コマンドを使用して Azure Key Vault に保存されます。

   ```console
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name "{KEY VAULT NAME}" --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. アプリを実行すると、key vault のシークレットが読み込まれます。 文字列のシークレットを`5000-AppSecret`がアプリのプロジェクト ファイルで指定されたアプリのバージョンに一致 (`5.0.0.0`)。

1. バージョン、 `5000` (dash) には、キー名から取り除かれます。 キーを使用して構成を読み取って、アプリ全体にわたって`AppSecret`シークレット値を読み込みます。

1. プロジェクト ファイルで、アプリのバージョンが変更されるかどうか`5.1.0.0`シークレットに返される値は、アプリをもう一度実行`5.1.0.0_secret_value_dev`開発環境でと`5.1.0.0_secret_value_prod`実稼働環境でします。

> [!NOTE]
> 独自に提供することも`KeyVaultClient`実装`AddAzureKeyVault`します。 カスタムのクライアントでは、アプリ間でのクライアントの 1 つのインスタンスの共有を許可します。

## <a name="bind-an-array-to-a-class"></a>配列をクラスにバインドする

プロバイダーは、配列、POCO 配列にバインドするために構成値を読み取ることができます。

キーをコロンを含めることができる構成ソースから読み取るときに (`:`) 配列を構成するキーを区別するために、数値キー セグメントの区切り記号が使用される (`:0:`、 `:1:`,… `:{n}:`) 詳細については、次を参照してください。[構成。クラスに配列をバインド](xref:fundamentals/configuration/index#bind-an-array-to-a-class)します。

Azure Key Vault のキーは、区切り記号としてコロンを使用することはできません。 このトピックで説明されているアプローチは、二重ハイフンを使用 (`--`) (セクション) の階層値の区切り記号として。 ダッシュと数字キー セグメント倍精度浮動小数点で配列のキーが Azure Key Vault に格納されている (`--0--`、 `--1--`、 &hellip; `--{n}--`)。

次の確認[Serilog](https://serilog.net/)ログ プロバイダーの構成 JSON ファイルに含まれます。 2 つのオブジェクトで定義されているリテラルは、`WriteTo`配列 2 つの Serilog を反映する*シンク*、ログ出力の変換先を記述します。

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

上記の JSON ファイルに示すように構成が二重ダッシュを使用して Azure Key Vault に格納されている (`--`) 表記と数値のセグメント。

| キー | [値] |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>シークレットを再読み込み

シークレットはまでキャッシュ`IConfigurationRoot.Reload()`が呼び出されます。 期限切れ、無効にし、まで、アプリで、key vault にシークレットが更新されたが守られていない`Reload`を実行します。

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>無効になっており、期限切れのシークレット

無効になっており、期限切れのシークレットのスロー、`KeyVaultClientException`します。 アプリがスローされることを防ぐために、アプリの代わりにまたは、無効/有効期限切れのシークレットを更新します。

## <a name="troubleshoot"></a>トラブルシューティング

アプリは、プロバイダーを使用して構成の読み込みに失敗した場合、エラー メッセージが書き込む、 [ASP.NET Core のログ記録インフラストラクチャ](xref:fundamentals/logging/index)します。 次の条件は、読み込みを構成できないようにします。

* アプリまたは証明書は Azure Active Directory で正しく構成されていません。
* Key vault は、Azure Key Vault に存在しません。
* アプリは、キー コンテナーにアクセスする権限はありません。
* アクセス ポリシーが含まれていない`Get`と`List`アクセス許可。
* Key vault で構成データ (名前と値のペア) が正しくという名前のないがない、有効期限が切れたか、無効にします。
* アプリが正しくない key vault の名前 (`KeyVaultName`)、Azure AD アプリケーション Id (`AzureADApplicationId`)、または Azure AD 証明書の拇印 (`AzureADCertThumbprint`)。
* 構成キー (名) は、ロードしようとしている値用のアプリで正しくないです。

## <a name="additional-resources"></a>その他の技術情報

* <xref:fundamentals/configuration/index>
* [Microsoft Azure:Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure:Key Vault のドキュメント](/azure/key-vault/)
* [Azure Key Vault のキーを生成し、HSM で保護された転送する方法](/azure/key-vault/key-vault-hsm-protected-keys)
* [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [クイック スタート:設定して、.NET web アプリを使用して Azure Key Vault からシークレットを取得](/azure/key-vault/quick-create-net)
* [チュートリアル: Azure Windows 仮想マシンを使用した .NET で Azure Key Vault を使用する方法](/azure/key-vault/tutorial-net-windows-virtual-machine)
