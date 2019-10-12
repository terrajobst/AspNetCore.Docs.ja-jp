---
title: ASP.NET Core Id のカスタムストレージプロバイダー
author: ardalis
description: ASP.NET Core Id のカスタム記憶域プロバイダーを構成する方法について説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 500824807307840a9279dd00c2fe632835737c2d
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080790"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>ASP.NET Core Id のカスタムストレージプロバイダー

作成者: [Steve Smith](https://ardalis.com/)

ASP.NET Core Id は拡張可能なシステムであり、カスタム記憶域プロバイダーを作成してアプリに接続することができます。 このトピックでは、ASP.NET Core Id 用にカスタマイズされた記憶域プロバイダーを作成する方法について説明します。 この記事では、独自の記憶域プロバイダーを作成するための重要な概念について説明しますが、詳細な手順については説明しません。

[GitHub のサンプルを表示またはダウンロードしてください](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)。

## <a name="introduction"></a>はじめに

既定では、ASP.NET Core Id システムは Entity Framework Core を使用して SQL Server データベースにユーザー情報を格納します。 多くのアプリでは、この方法が適しています。 ただし、別の永続化メカニズムまたはデータスキーマを使用することをお勧めします。 例えば:

* [Azure Table Storage](/azure/storage/)または別のデータストアを使用します。
* データベーステーブルの構造が異なります。 
* [Dapper](https://github.com/StackExchange/Dapper)など、別のデータアクセス方法を使用することもできます。 

これらの各ケースでは、ストレージメカニズム用にカスタマイズされたプロバイダーを作成し、そのプロバイダーをアプリに接続できます。

ASP.NET Core Id は、Visual Studio の [個別のユーザーアカウント] オプションを使用してプロジェクトテンプレートに含まれています。

.NET Core CLI を使用する場合は`-au Individual`、次のように追加します。

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>ASP.NET Core Id アーキテクチャ

ASP.NET Core Id は、マネージャーとストアと呼ばれるクラスで構成されます。 *マネージャー*は、アプリ開発者が id ユーザーの作成などの操作を実行するために使用する高レベルのクラスです。 *ストア*は、ユーザーやロールなどのエンティティがどのように永続化されるかを指定する下位レベルのクラスです。 ストアはリポジトリパターンに従い、永続化メカニズムと密接に結び付いています。 マネージャーはストアから切り離されています。つまり、アプリケーションコードを変更することなく永続化メカニズムを置き換えることができます (構成を除く)。

次の図は、web アプリがマネージャーと対話する方法を示しています。また、ストアはデータアクセス層と対話します。

![ASP.NET Core アプリは、マネージャー (たとえば、' UserManager ', ' RoleManager ') と連携します。 マネージャーは、Entity Framework Core などのライブラリを使用してデータソースと通信するストア (たとえば、' UserStore ') を操作します。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

カスタム記憶域プロバイダーを作成するには、データソース、データアクセス層、およびこのデータアクセス層と対話するストアクラスを作成します (上の図の緑と灰色のボックス)。 マネージャーや、それらと対話するアプリコード (上の青いボックス) をカスタマイズする必要はありません。

の`UserManager`新しいインスタンスを作成する場合`RoleManager` 、またはユーザークラスの型を指定し、ストアクラスのインスタンスを引数として渡します。 この方法を使用すると、カスタマイズしたクラスを ASP.NET Core に組み込むことができます。 

[新しいストレージプロバイダーを使用するようにアプリを再構成](#reconfigure-app-to-use-a-new-storage-provider)するカスタムストアでと`UserManager` `RoleManager`をインスタンス化する方法について説明します。

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Id ストアのデータ型

[ASP.NET Core id](https://github.com/aspnet/identity)データ型の詳細については、次のセクションで説明します。

### <a name="users"></a>Users

Web サイトの登録済みユーザー。 [ユーザー](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)の種類は、独自のカスタム型の例として拡張または使用できます。 独自のカスタム id ストレージソリューションを実装するために、特定の型から継承する必要はありません。

### <a name="user-claims"></a>ユーザー要求

ユーザーの id を表す、ユーザーに関する一連のステートメント (または[要求](/dotnet/api/system.security.claims.claim))。 では、ロールを使用した場合よりも多くのユーザー id を有効にすることができます。

### <a name="user-logins"></a>ユーザーログイン

ユーザーのログイン時に使用する外部認証プロバイダー (Facebook や Microsoft アカウントなど) に関する情報。 [例](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>ロール

サイトの承認グループ。 ロール Id とロール名 ("Admin" や "Employee" など) が含まれます。 [例](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>データアクセス層

このトピックでは、使用する永続化メカニズムについて理解していること、およびそのメカニズムのエンティティを作成する方法について理解していることを前提としています。 このトピックでは、リポジトリまたはデータアクセスクラスの作成方法の詳細については説明しません。ASP.NET Core Id を使用する場合の設計上の決定について、いくつかの提案を提供します。

カスタマイズされたストアプロバイダーのデータアクセス層を設計する際には、自由度が高くなります。 アプリで使用する予定の機能に対してのみ、永続化メカニズムを作成する必要があります。 たとえば、アプリでロールを使用していない場合は、ロールまたはユーザーロールの関連付け用のストレージを作成する必要はありません。 テクノロジと既存のインフラストラクチャでは、ASP.NET Core Id の既定の実装とは大きく異なる構造が必要になる場合があります。 データアクセス層では、ストレージ実装の構造を操作するロジックを提供します。

データアクセス層は、ASP.NET Core Id からデータソースにデータを保存するためのロジックを提供します。 カスタマイズした記憶域プロバイダーのデータアクセス層には、ユーザーとロールの情報を格納するための次のクラスが含まれている場合があります。

### <a name="context-class"></a>Context クラス

永続化メカニズムに接続してクエリを実行するための情報をカプセル化します。 いくつかのデータクラスには、通常、依存関係の挿入によって提供されるこのクラスのインスタンスが必要です。 [例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。

### <a name="user-storage"></a>ユーザーストレージ

ユーザー情報 (ユーザー名やパスワードハッシュなど) を格納および取得します。 [例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>ロールストレージ

ロール名などのロール情報を格納および取得します。 [例](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>UserClaims ストレージ

ユーザー要求情報 (要求の種類や値など) を格納および取得します。 [例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>UserLogins ストレージ

ユーザーのログイン情報 (外部認証プロバイダーなど) を格納および取得します。 [例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>UserRole ストレージ

どのロールがどのユーザーに割り当てられているかを格納および取得します。 [例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**形**アプリで使用する予定のクラスのみを実装します。

データアクセスクラスで、永続化メカニズムのデータ操作を実行するコードを指定します。 たとえば、カスタムプロバイダー内で、*ストア*クラスに新しいユーザーを作成するには、次のコードが必要になることがあります。

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

ユーザーを作成するための実装ロジックは、 `_usersTable.CreateAsync`次に示すメソッドに含まれています。

## <a name="customize-the-user-class"></a>ユーザークラスをカスタマイズする

ストレージプロバイダーを実装する場合は、ユーザークラスを作成し[ます。この](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)クラスは、ユーザークラスに相当します。

少なくとも、ユーザークラスには`Id` `UserName`プロパティとプロパティが含まれている必要があります。

クラス`IdentityUser`は、要求された`UserManager`操作の実行時にが呼び出すプロパティを定義します。 `Id`プロパティの既定の型は文字列ですが、から`IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>`継承して別の型を指定することもできます。 フレームワークでは、ストレージの実装がデータ型の変換を処理することを想定しています。

## <a name="customize-the-user-store"></a>ユーザーストアをカスタマイズする

ユーザーの`UserStore`すべてのデータ操作のメソッドを提供するクラスを作成します。 このクラスは、 [userstore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)クラスに相当します。 クラスで、および必要`IUserStore<TUser>`に応じて省略可能なインターフェイスを実装します。 `UserStore` 実装するオプションのインターフェイスは、アプリで提供される機能に基づいて選択します。

### <a name="optional-interfaces"></a>省略可能なインターフェイス

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

オプションのインターフェイスは、 `IUserStore<TUser>`から継承されます。 [サンプルアプリ](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)には、部分的に実装されたサンプルユーザーストアが表示されます。

`UserStore`クラス内では、操作を実行するために作成したデータアクセスクラスを使用します。 これらは、依存関係の挿入を使用して渡されます。 たとえば、Dapper 実装`UserStore`の SQL Server では、クラスに`CreateAsync`は、の`DapperUsersTable`インスタンスを使用して新しいレコードを挿入するメソッドがあります。

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>ユーザーストアをカスタマイズするときに実装するインターフェイス

* **IUserStore**  
 [IUserStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)インターフェイスは、ユーザーストアに実装する必要がある唯一のインターフェイスです。 ユーザーの作成、更新、削除、および取得を行うためのメソッドを定義します。
* **IUserClaimStore**  
 [IUserClaimStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)インターフェイスでは、ユーザーの信頼性情報を有効にするために実装するメソッドを定義します。 これには、ユーザー要求を追加、削除、および取得するためのメソッドが含まれています。
* **IUserLoginStore**  
 [IUserLoginStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)は、外部認証プロバイダーを有効にするために実装するメソッドを定義します。 これには、ユーザーログインを追加、削除、取得するためのメソッド、およびログイン情報に基づいてユーザーを取得するためのメソッドが含まれています。
* **IUserRoleStore**  
 [IUserRoleStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)インターフェイスでは、ユーザーをロールにマップするために実装するメソッドを定義します。 これには、ユーザーのロールを追加、削除、取得するメソッド、およびユーザーがロールに割り当てられているかどうかを確認するメソッドが含まれています。
* **IUserPasswordStore**  
 [IUserPasswordStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)インターフェイスは、ハッシュされたパスワードを保持するために実装するメソッドを定義します。 ハッシュされたパスワードを取得および設定するためのメソッドと、ユーザーがパスワードを設定したかどうかを示すメソッドが含まれています。
* **IUserSecurityStampStore**  
 [IUserSecurityStampStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)インターフェイスでは、ユーザーのアカウント情報が変更されたかどうかを示すセキュリティスタンプを使用するために実装するメソッドを定義します。 このスタンプは、ユーザーがパスワードを変更したとき、またはログインを追加または削除したときに更新されます。 セキュリティスタンプを取得および設定するためのメソッドが含まれています。
* **IUserTwoFactorStore**  
 [IUserTwoFactorStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)インターフェイスでは、2要素認証をサポートするために実装するメソッドを定義します。 これには、ユーザーに対して2要素認証を有効にするかどうかを取得および設定するためのメソッドが含まれています。
* **IUserPhoneNumberStore**  
 [IUserPhoneNumberStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)インターフェイスでは、ユーザーの電話番号を格納するために実装するメソッドを定義します。 電話番号を取得して設定するためのメソッド、および電話番号を確認する方法が含まれています。
* **IUserEmailStore**  
 [IUserEmailStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)インターフェイスでは、ユーザーの電子メールアドレスを格納するために実装するメソッドを定義します。 このファイルには、電子メールアドレスを取得および設定するためのメソッドと、電子メールを確認するかどうかが含まれています。
* **IUserLockoutStore**  
 [IUserLockoutStore&lt;tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)インターフェイスは、アカウントのロックに関する情報を格納するために実装するメソッドを定義します。 失敗したアクセス試行とロックアウトを追跡するメソッドが含まれています。
* **IQueryableUserStore**  
 [&lt;Iqueryableuserstore tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)インターフェイスは、クエリ可能なユーザーストアを提供するために実装するメンバーを定義します。

アプリケーションで必要なインターフェイスだけを実装します。 例えば:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>ユーザー Id、ユーザー名、およびユーザー名

名前`Microsoft.AspNet.Identity.EntityFramework`空間には、ユーザー [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1) [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)[IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)クラスの実装が含まれています。 これらの機能を使用している場合は、これらのクラスの独自のバージョンを作成し、アプリのプロパティを定義することができます。 ただし、基本操作 (ユーザーの要求の追加や削除など) を実行するときに、これらのエンティティをメモリに読み込まない方が効率的な場合もあります。 代わりに、バックエンドストアクラスは、データソースでこれらの操作を直接実行できます。 たとえば、 `UserStore.GetClaimsAsync`メソッドは、 `userClaimTable.FindByUserId(user.Id)`メソッドを呼び出して、そのテーブルに対してクエリを直接実行し、クレームの一覧を返すことができます。

## <a name="customize-the-role-class"></a>ロールクラスをカスタマイズする

ロールストレージプロバイダーを実装する場合は、カスタムロールの種類を作成できます。 特定のインターフェイスを実装する必要はありませんが、 `Id`が必要であり、通常`Name`はプロパティを持つ必要があります。

ロールクラスの例を次に示します。

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>ロールストアをカスタマイズする

ロールに対するすべて`RoleStore`のデータ操作のメソッドを提供するクラスを作成できます。 このクラスは、 [rolestore&lt;trole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)クラスに相当します。 クラスでは、 `IRoleStore<TRole>`および必要に応じ`IQueryableRoleStore<TRole>`てインターフェイスを実装します。 `RoleStore`

* **IRoleStore&lt;TRole&gt;**  
 [Irolestore&lt;&gt; trole](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)インターフェイスは、ロールストアクラスに実装するメソッドを定義します。 これには、ロールの作成、更新、削除、および取得を行うためのメソッドが含まれています。
* **RoleStore&lt;TRole&gt;**  
 カスタマイズ`RoleStore`するには、 `IRoleStore<TRole>`インターフェイスを実装するクラスを作成します。 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>新しい記憶域プロバイダーを使用するようにアプリを再構成する

ストレージプロバイダーを実装したら、それを使用するようにアプリを構成します。 アプリで既定のプロバイダーが使用されている場合は、カスタムプロバイダーに置き換えます。

1. NuGet パッケージ`Microsoft.AspNetCore.EntityFramework.Identity`を削除します。
1. ストレージプロバイダーが別のプロジェクトまたはパッケージに存在する場合は、その記憶域プロバイダーへの参照を追加します。
1. へのすべての`Microsoft.AspNetCore.EntityFramework.Identity`参照を、ストレージプロバイダーの名前空間の using ステートメントに置き換えます。
1. メソッドで、カスタム型を`AddIdentity`使用するようにメソッドを変更します。 `ConfigureServices` この目的のために独自の拡張メソッドを作成できます。 例については、「 [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 」を参照してください。
1. ロールを使用している場合は`RoleManager` 、 `RoleStore`クラスを使用するようにを更新します。
1. 接続文字列と資格情報をアプリの構成に更新します。

例:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>リファレンス

* [ASP.NET 4.x Id 用のカスタムストレージプロバイダー](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core id](https://github.com/aspnet/identity)&ndash;このリポジトリには、コミュニティで管理されたストアプロバイダーへのリンクが含まれています。
