---
title: ASP.NET Core での Identity モデルのカスタマイズ
author: ajcvickers
description: この記事では、ASP.NET Core Id の基になる Entity Framework Core データモデルをカスタマイズする方法について説明します。
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651458"
---
# <a name="identity-model-customization-in-aspnet-core"></a>ASP.NET Core での Identity モデルのカスタマイズ

[Arthur ヴィッカース](https://github.com/ajcvickers)

ASP.NET Core Id は、ASP.NET Core アプリでユーザーアカウントを管理および格納するためのフレームワークを提供します。 認証メカニズムとして個々の**ユーザーアカウント**を選択すると、id がプロジェクトに追加されます。 既定では、Id によって、Entity Framework (EF) のコアデータモデルが使用されます。 この記事では、Id モデルをカスタマイズする方法について説明します。

## <a name="identity-and-ef-core-migrations"></a>Identity と EF Core のマイグレーション

モデルを調べる前に、 [EF Core の移行](/ef/core/managing-schemas/migrations/)を使用して id がどのように動作し、データベースを作成および更新するかを理解しておくと便利です。 最上位レベルでは、プロセスは次のようになります。

1. [コードでデータモデル](/ef/core/modeling/)を定義または更新します。
1. このモデルをデータベースに適用できる変更に変換するには、移行を追加します。
1. 移行によって意図が正しく表現されていることを確認します。
1. モデルと同期するようにデータベースを更新するには、移行を適用します。
1. 手順 1. ~ 4. を繰り返して、モデルをさらに調整し、データベースを同期させます。

移行を追加して適用するには、次のいずれかの方法を使用します。

* Visual Studio を使用している場合は、[**パッケージマネージャーコンソール**(PMC)] ウィンドウ。 詳細については、「 [EF CORE PMC ツール](/ef/core/miscellaneous/cli/powershell)」を参照してください。
* コマンドラインを使用している場合は、.NET Core CLI ます。 詳細については、「 [EF Core .net コマンドラインツール](/ef/core/miscellaneous/cli/dotnet)」を参照してください。
* アプリの実行時に、エラーページの **[移行の適用]** ボタンをクリックします。

ASP.NET Core には、開発時エラーページハンドラーがあります。 ハンドラーは、アプリの実行時に移行を適用できます。 実稼働アプリでは、通常、移行から SQL スクリプトを生成し、制御されたアプリとデータベースの配置の一部としてデータベースの変更をデプロイします。

Id を使用する新しいアプリが作成されると、上記の手順 1. と 2. は既に完了しています。 つまり、初期データモデルが既に存在し、初期移行がプロジェクトに追加されています。 初期移行は、引き続きデータベースに適用する必要があります。 最初の移行は、次のいずれかの方法を使用して適用できます。

* PMC で `Update-Database` を実行します。
* コマンドシェルで `dotnet ef database update` を実行します。
* アプリの実行時に、エラーページの **[移行の適用]** ボタンをクリックします。

モデルが変更されたときに、上記の手順を繰り返します。

## <a name="the-identity-model"></a>Identity モデル

### <a name="entity-types"></a>エンティティの種類

Id モデルは、次のエンティティ型で構成されています。

|エンティティの種類|説明                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |ユーザーを表します。                                         |
|`Role`     |ロールを表します。                                           |
|`UserClaim`|ユーザーが所有するクレームを表します。                    |
|`UserToken`|ユーザーの認証トークンを表します。               |
|`UserLogin`|ユーザーをログインに関連付けます。                              |
|`RoleClaim`|ロール内のすべてのユーザーに付与されるクレームを表します。|
|`UserRole` |ユーザーとロールを関連付ける結合エンティティ。               |

### <a name="entity-type-relationships"></a>エンティティ型のリレーションシップ

[エンティティ型](#entity-types)は、次の方法で相互に関連付けられます。

* 各 `User` には、多くの `UserClaims`を含めることができます。
* 各 `User` には、多くの `UserLogins`を含めることができます。
* 各 `User` には、多くの `UserTokens`を含めることができます。
* 各 `Role` には、多数の `RoleClaims`を関連付けることができます。
* 各 `User` には多数の `Roles`を関連付けることができ、各 `Role` は多くの `Users`に関連付けることができます。 これは、データベース内の結合テーブルを必要とする多対多リレーションシップです。 結合テーブルは、`UserRole` エンティティによって表されます。

### <a name="default-model-configuration"></a>既定のモデル構成

Identity は、モデルを構成して使用するために[Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)から継承する多くの*コンテキストクラス*を定義します。 この構成は、コンテキストクラスの[Onmodelcreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)メソッドで[EF CORE Code First Fluent API](/ef/core/modeling/)を使用して行います。 既定の構成は次のとおりです。

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a>モデルのジェネリック型

Id は、上記の各エンティティ型に対して、既定の[共通言語ランタイム](/dotnet/standard/glossary#clr)(CLR) 型を定義します。 これらの型には、すべて*id*が付加されています。

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

これらの型を直接使用するのではなく、アプリケーション独自の型の基底クラスとして型を使用できます。 Id で定義される `DbContext` クラスはジェネリックであり、さまざまな CLR 型をモデルの1つ以上のエンティティ型に使用できます。 また、これらのジェネリック型を使用して、`User` 主キー (PK) のデータ型を変更することもできます。

ロールのサポートで Id を使用する場合は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> クラスを使用する必要があります。 例 :

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

ロールのない Id (要求のみ) を使用することもできます。その場合は、<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> クラスを使用する必要があります。

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a>モデルのカスタマイズ

モデルのカスタマイズの開始点は、適切なコンテキスト型から派生することです。 「[モデルのジェネリック型](#model-generic-types)」を参照してください。 このコンテキストの種類は `ApplicationDbContext` と呼ばれ、ASP.NET Core テンプレートによって作成されます。

このコンテキストは、次の2つの方法でモデルを構成するために使用されます。

* ジェネリック型パラメーターのエンティティ型とキー型を提供します。
* これらの型のマッピングを変更するために `OnModelCreating` をオーバーライドしています。

`OnModelCreating`をオーバーライドする場合は、最初に `base.OnModelCreating` を呼び出す必要があります。次に、オーバーライドする構成を呼び出す必要があります。 EF Core は、一般に、構成のための最後の1つの wins ポリシーを持っています。 たとえば、エンティティ型の `ToTable` メソッドが最初に1つのテーブル名で呼び出され、次に別のテーブル名を使用している場合は、2番目の呼び出しのテーブル名が使用されます。

### <a name="custom-user-data"></a>カスタムユーザーデータ

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[カスタムユーザーデータ](xref:security/authentication/add-user-data)は、`IdentityUser`から継承することによってサポートされます。 この型には、通常、`ApplicationUser`という名前を指定します。

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

`ApplicationUser` 型をコンテキストの汎用引数として使用します。

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

`ApplicationDbContext` クラスの `OnModelCreating` をオーバーライドする必要はありません。 EF Core は、`CustomTag` プロパティを慣例に従ってマップします。 ただし、新しい `CustomTag` 列を作成するには、データベースを更新する必要があります。 列を作成するには、移行を追加し、「 [id と EF Core の移行](#identity-and-ef-core-migrations)」の説明に従ってデータベースを更新します。

*Pages/Shared/_LoginPartial*を更新し、`IdentityUser` を `ApplicationUser`に置き換えます。

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

*区分/id/IdentityHostingStartup*または `Startup.ConfigureServices` を更新し、`IdentityUser` を `ApplicationUser`に置き換えます。

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

ASP.NET Core 2.1 以降では、Id は Razor クラスライブラリとして提供されます。 詳細については、<xref:security/authentication/scaffold-identity> を参照してください。 そのため、上記のコードでは <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>を呼び出す必要があります。 Id scaffolder を使用して Id ファイルをプロジェクトに追加した場合は、`AddDefaultUI`の呼び出しを削除します。 詳細については、次を参照してください。

* [Identity のスキャフォールディング](xref:security/authentication/scaffold-identity)
* [カスタムユーザーデータを Id に追加、ダウンロード、および削除する](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>主キーの型の変更

データベースを作成した後に PK 列のデータ型を変更すると、多くのデータベースシステムで問題が発生します。 PK を変更するには、通常、テーブルを削除してから再作成する必要があります。 そのため、データベースの作成時に、初期移行でキーの種類を指定する必要があります。

PK の種類を変更するには、次の手順に従います。

1. PK の変更前にデータベースを作成した場合は、`Drop-Database` (PMC) または `dotnet ef database drop` (.NET Core CLI) を実行して削除します。
2. データベースの削除を確認した後、`Remove-Migration` (PMC) または `dotnet ef migrations remove` (.NET Core CLI) を使用して最初の移行を削除します。
3. <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>から派生するように `ApplicationDbContext` クラスを更新します。 `TKey`の新しいキーの種類を指定します。 たとえば、`Guid` キーの種類を使用するには、次のように入力します。

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    前のコードでは、ジェネリッククラス <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> と <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> を指定して、新しいキーの型を使用する必要があります。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    前のコードでは、ジェネリッククラス <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> と <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> を指定して、新しいキーの型を使用する必要があります。

    ::: moniker-end

    汎用ユーザーを使用するには `Startup.ConfigureServices` を更新する必要があります。

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. カスタム `ApplicationUser` クラスが使用されている場合は、`IdentityUser`から継承するようにクラスを更新します。 例 :

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    カスタム `ApplicationUser` クラスを参照するように `ApplicationDbContext` を更新します。

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    `Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ASP.NET Core 2.1 以降では、Id は Razor クラスライブラリとして提供されます。 詳細については、<xref:security/authentication/scaffold-identity> を参照してください。 そのため、上記のコードでは <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>を呼び出す必要があります。 Id scaffolder を使用して Id ファイルをプロジェクトに追加した場合は、`AddDefaultUI`の呼び出しを削除します。

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> メソッドは、主キーのデータ型を示す `TKey` 型を受け取ります。

    ::: moniker-end

5. カスタム `ApplicationRole` クラスが使用されている場合は、`IdentityRole<TKey>`から継承するようにクラスを更新します。 例 :

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    カスタム `ApplicationRole` クラスを参照するように `ApplicationDbContext` を更新します。 たとえば、次のクラスは、カスタム `ApplicationUser` とカスタム `ApplicationRole`を参照します。

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    `Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ASP.NET Core 2.1 以降では、Id は Razor クラスライブラリとして提供されます。 詳細については、<xref:security/authentication/scaffold-identity> を参照してください。 そのため、上記のコードでは <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>を呼び出す必要があります。 Id scaffolder を使用して Id ファイルをプロジェクトに追加した場合は、`AddDefaultUI`の呼び出しを削除します。

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    `Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    主キーのデータ型は、 [Dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)オブジェクトを分析することによって推論されます。

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    `Startup.ConfigureServices`で Id サービスを追加するときに、カスタムデータベースコンテキストクラスを登録します。

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> メソッドは、主キーのデータ型を示す `TKey` 型を受け取ります。

    ::: moniker-end

### <a name="add-navigation-properties"></a>ナビゲーション プロパティの追加

リレーションシップのモデル構成の変更は、他の変更を行うよりも困難になることがあります。 新しい追加のリレーションシップを作成するのではなく、既存のリレーションシップを置き換える必要があります。 特に、変更されたリレーションシップでは、既存のリレーションシップと同じ外部キー (FK) プロパティを指定する必要があります。 たとえば、`Users` と `UserClaims` 間のリレーションシップは、既定では次のように指定されています。

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

このリレーションシップの FK は、`UserClaim.UserId` プロパティとして指定されます。 `HasMany` と `WithOne` は、ナビゲーションプロパティを使用せずにリレーションシップを作成するための引数なしで呼び出されます。

関連する `UserClaims` をユーザーから参照できるようにするナビゲーションプロパティを `ApplicationUser` に追加します。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

`IdentityUserClaim<TKey>` の `TKey` は、ユーザーの PK に対して指定された型です。 この場合、既定値が使用されているため、`TKey` が `string` ます。 `UserClaim` エンティティ型の PK 型では**ありません**。

ナビゲーションプロパティが存在するので、`OnModelCreating`で構成する必要があります。

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

リレーションシップが以前とまったく同じように構成されていることに注意してください。 `HasMany`の呼び出しで指定されたナビゲーションプロパティを使用した場合のみです。

ナビゲーションプロパティは、データベースではなく EF モデルにのみ存在します。 リレーションシップの FK は変更されていないため、この種のモデルの変更では、データベースを更新する必要はありません。 これは、変更を行った後に移行を追加することによって確認できます。 `Up` メソッドと `Down` メソッドは空です。

### <a name="add-all-user-navigation-properties"></a>すべてのユーザー ナビゲーション プロパティの追加

次の例では、上のセクションをガイダンスとして使用して、ユーザーのすべてのリレーションシップに対して一方向のナビゲーションプロパティを構成します。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a>ユーザーおよびロールのナビゲーション プロパティの追加

次の例では、上のセクションをガイダンスとして使用して、ユーザーとロールのすべてのリレーションシップに対してナビゲーションプロパティを構成します。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

注:

* この例には、ユーザーからロールへの多対多リレーションシップを移動するために必要な `UserRole` join エンティティも含まれています。
* `IdentityXxx` 型の代わりに `ApplicationXxx` 型が現在使用されていることを反映するように、ナビゲーションプロパティの型を変更することを忘れないでください。
* ジェネリック `ApplicationContext` 定義の `ApplicationXxx` を使用することを忘れないでください。

### <a name="add-all-navigation-properties"></a>すべてのナビゲーション プロパティの追加

次の例では、上のセクションをガイダンスとして使用して、すべてのエンティティ型のすべてのリレーションシップに対してナビゲーションプロパティを構成します。

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a>複合キーの使用

前のセクションでは、Id モデルで使用されるキーの種類の変更について説明しています。 複合キーを使用するように Id キーモデルを変更することはサポートされていないか、推奨されません。 Id で複合キーを使用するには、Identity manager コードがモデルとどのように連携するかを変更する必要があります。 このカスタマイズについては、このドキュメントでは説明しません。

### <a name="change-tablecolumn-names-and-facets"></a>テーブル名/列名とファセットの変更

テーブルと列の名前を変更するには、`base.OnModelCreating`を呼び出します。 次に、構成を追加して、既定値を上書きします。 たとえば、すべての Id テーブルの名前を変更するには、次のようにします。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

これらの例では、既定の Id の種類を使用します。 `ApplicationUser`などのアプリの種類を使用する場合は、既定の種類の代わりにその種類を構成します。

次の例では、いくつかの列名を変更します。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

一部の種類のデータベース列は、特定の*ファセット*を使用して構成できます (たとえば、許容される最大 `string` 長さ)。 次の例では、モデル内のいくつかの `string` プロパティの列の最大長を設定します。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a>別のスキーマへのマップ

スキーマは、データベースプロバイダーによって動作が異なります。 SQL Server の場合、既定では*dbo*スキーマのすべてのテーブルが作成されます。 テーブルは、別のスキーマで作成できます。 例 :

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>遅延読み込み

このセクションでは、Id モデルでの遅延読み込みプロキシのサポートを追加します。 遅延読み込みは、ナビゲーションプロパティが読み込まれていることを確認せずに使用できるため便利です。

エンティティ型は、 [EF Core のドキュメント](/ef/core/querying/related-data#lazy-loading)で説明されているように、いくつかの方法で遅延読み込みに適したものにすることができます。 わかりやすくするために、レイジー読み込みプロキシを使用します。これには次のものが必要です。

* [Microsoft EntityFrameworkCore プロキシ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)パッケージのインストール。
* [Adddbcontext\<tcontext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)内 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> の呼び出し。
* `public virtual` ナビゲーションプロパティを持つパブリックエンティティ型。

次の例は `Startup.ConfigureServices`で `UseLazyLoadingProxies` を呼び出す方法を示しています。

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

エンティティ型へのナビゲーションプロパティの追加に関するガイダンスについては、前の例を参照してください。

## <a name="additional-resources"></a>その他のリソース

* <xref:security/authentication/scaffold-identity>

::: moniker-end
