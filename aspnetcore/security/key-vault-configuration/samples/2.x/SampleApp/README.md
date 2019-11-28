# <a name="key-vault-configuration-provider-sample-app"></a>Key Vault 構成プロバイダーのサンプルアプリ

このサンプルでは、Azure Key Vault 構成プロバイダーの使用方法を示します。

このサンプルは、 *Program.cs*ファイルの先頭にある `#define` ステートメントによって決定される2つのモードのいずれかで実行されます。 手順については、[サンプルコードの「プリプロセッサディレクティブ](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code)」を参照してください。

* `Certificate` &ndash; は、Azure Key Vault クライアント ID と x.509 証明書を使用して Azure Key Vault に格納されているシークレットにアクセスする方法を示しています。 このバージョンのサンプルは、Azure App Service、または ASP.NET Core アプリを提供できる任意のホストにデプロイされている任意の場所から実行できます。
* `Managed` &ndash; では、Azure の[マネージドサービス ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)を使用してアプリを認証し、アプリのコードまたは構成で資格情報を使用せずに Azure AD 認証を使用して Azure Key Vault する方法を示します。 アプリケーションが Azure Key Vault で認証するには、Azure AD のクライアント ID とシークレットは必要ありません。 マネージド Id scearnio を調べるには、このサンプルを Azure App Service にデプロイする必要があります。

詳細については、「 [Azure Key Vault 構成プロバイダー](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration)」を参照してください。
