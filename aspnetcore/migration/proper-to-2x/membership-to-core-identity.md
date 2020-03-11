---
title: ASP.NET メンバーシップ認証から ASP.NET Core 2.0 Id に移行する
author: isaac2004
description: メンバーシップ認証を使用して既存の ASP.NET アプリを ASP.NET Core 2.0 Id に移行する方法について説明します。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651836"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="729b2-103">ASP.NET メンバーシップ認証から ASP.NET Core 2.0 Id に移行する</span><span class="sxs-lookup"><span data-stu-id="729b2-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="729b2-104">著者: [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="729b2-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="729b2-105">この記事では、メンバーシップ認証を使用して ASP.NET アプリのデータベーススキーマを ASP.NET Core 2.0 Id に移行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="729b2-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="729b2-106">このドキュメントでは、ASP.NET メンバーシップベースのアプリ用のデータベーススキーマを ASP.NET Core Id に使用されるデータベーススキーマに移行するために必要な手順について説明します。</span><span class="sxs-lookup"><span data-stu-id="729b2-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="729b2-107">ASP.NET メンバーシップベースの認証から ASP.NET Identity への移行の詳細については、「 [SQL メンバーシップから ASP.NET Identity への既存のアプリの移行](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="729b2-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="729b2-108">ASP.NET Core Id の詳細については、「 [ASP.NET Core の id の概要](xref:security/authentication/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="729b2-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="729b2-109">メンバーシップスキーマのレビュー</span><span class="sxs-lookup"><span data-stu-id="729b2-109">Review of Membership schema</span></span>

<span data-ttu-id="729b2-110">ASP.NET 2.0 より前の開発者は、アプリの認証と承認のプロセス全体を作成しようとしていました。</span><span class="sxs-lookup"><span data-stu-id="729b2-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="729b2-111">ASP.NET 2.0 では、メンバーシップが導入され、ASP.NET アプリ内のセキュリティを処理する定型的なソリューションが提供されています。</span><span class="sxs-lookup"><span data-stu-id="729b2-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="729b2-112">開発者は、 [aspnet_regsql の .exe](https://msdn.microsoft.com/library/ms229862.aspx)コマンドを使用して、スキーマを SQL Server データベースにブートストラップできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="729b2-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="729b2-113">このコマンドを実行すると、次のテーブルがデータベースに作成されます。</span><span class="sxs-lookup"><span data-stu-id="729b2-113">After running this command, the following tables were created in the database.</span></span>

  ![メンバーシップテーブル](identity/_static/membership-tables.png)

<span data-ttu-id="729b2-115">既存のアプリを ASP.NET Core 2.0 Id に移行するには、これらのテーブルのデータを新しい Id スキーマによって使用されるテーブルに移行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="729b2-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="729b2-116">ASP.NET Core Id 2.0 スキーマ</span><span class="sxs-lookup"><span data-stu-id="729b2-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="729b2-117">ASP.NET Core 2.0 は、ASP.NET 4.5 で導入された[id](/aspnet/identity/index)原則に従います。</span><span class="sxs-lookup"><span data-stu-id="729b2-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="729b2-118">原則は共有されていますが、ASP.NET Core のバージョン間でもフレームワーク間の実装は異なります ( [ASP.NET Core 2.0 への認証と id の移行に関する情報を](xref:migration/1x-to-2x/index)参照してください)。</span><span class="sxs-lookup"><span data-stu-id="729b2-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="729b2-119">ASP.NET Core 2.0 Id のスキーマを表示する最も簡単な方法は、新しい ASP.NET Core 2.0 アプリを作成することです。</span><span class="sxs-lookup"><span data-stu-id="729b2-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="729b2-120">Visual Studio 2017 で、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="729b2-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="729b2-121">**[File]**  >  **[New]**  >  **[Project]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="729b2-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="729b2-122">*CoreIdentitySample*という名前の新しい**ASP.NET Core Web アプリケーション**プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="729b2-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="729b2-123">ドロップダウンで**ASP.NET Core 2.0**を選択し、 **[Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="729b2-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="729b2-124">このテンプレートは、 [Razor Pages](xref:razor-pages/index)アプリを生成します。</span><span class="sxs-lookup"><span data-stu-id="729b2-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="729b2-125">[ **OK]** をクリックしてから、 **[認証の変更]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="729b2-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="729b2-126">Id テンプレートに**個別のユーザーアカウント**を選択します。</span><span class="sxs-lookup"><span data-stu-id="729b2-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="729b2-127">最後に、 **[ok]** をクリックし、[ **ok]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="729b2-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="729b2-128">Visual Studio によって、ASP.NET Core Id テンプレートを使用してプロジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="729b2-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="729b2-129">[**ツール** > **NuGet パッケージマネージャー** > **パッケージマネージャーコンソール**] を選択して、**パッケージマネージャーコンソール**(PMC) ウィンドウを開きます。</span><span class="sxs-lookup"><span data-stu-id="729b2-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="729b2-130">PMC のプロジェクトルートに移動し、 [Entity Framework (EF) Core](/ef/core) `Update-Database` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="729b2-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="729b2-131">ASP.NET Core 2.0 Id では、EF Core を使用して、認証データを格納するデータベースと対話します。</span><span class="sxs-lookup"><span data-stu-id="729b2-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="729b2-132">新しく作成したアプリを機能させるには、このデータを格納するデータベースが必要です。</span><span class="sxs-lookup"><span data-stu-id="729b2-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="729b2-133">新しいアプリを作成した後、データベース環境でスキーマを検査する最も簡単な方法は、EF Core の[移行](/ef/core/managing-schemas/migrations/)を使用してデータベースを作成することです。</span><span class="sxs-lookup"><span data-stu-id="729b2-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="729b2-134">このプロセスでは、ローカルまたはその他の場所にあるデータベースを作成し、そのスキーマを模倣します。</span><span class="sxs-lookup"><span data-stu-id="729b2-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="729b2-135">詳細については、前のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="729b2-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="729b2-136">EF Core コマンドは、 *appsettings*で指定されたデータベースの接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="729b2-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="729b2-137">次の接続文字列は、 *localhost*という名前のデータベースを*対象とし*ています。</span><span class="sxs-lookup"><span data-stu-id="729b2-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="729b2-138">この設定では、`DefaultConnection` 接続文字列を使用するように EF Core が構成されています。</span><span class="sxs-lookup"><span data-stu-id="729b2-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="729b2-139">**ビュー** > **SQL Server オブジェクトエクスプローラー**を選択します。</span><span class="sxs-lookup"><span data-stu-id="729b2-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="729b2-140">*Appsettings*の [`ConnectionStrings:DefaultConnection`] プロパティで指定したデータベース名に対応するノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="729b2-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="729b2-141">`Update-Database` コマンドを実行すると、スキーマで指定されたデータベースと、アプリの初期化に必要なデータが作成されます。</span><span class="sxs-lookup"><span data-stu-id="729b2-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="729b2-142">次の図は、前の手順で作成されたテーブル構造を示しています。</span><span class="sxs-lookup"><span data-stu-id="729b2-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Id テーブル](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="729b2-144">スキーマを移行する</span><span class="sxs-lookup"><span data-stu-id="729b2-144">Migrate the schema</span></span>

<span data-ttu-id="729b2-145">テーブルの構造とフィールドには、メンバーシップと ASP.NET Core Id の両方について微妙な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="729b2-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="729b2-146">このパターンは、ASP.NET および ASP.NET Core アプリでの認証/承認のために大幅に変更されています。</span><span class="sxs-lookup"><span data-stu-id="729b2-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="729b2-147">Id で引き続き使用されるキーオブジェクトは、*ユーザー*と*ロール*です。</span><span class="sxs-lookup"><span data-stu-id="729b2-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="729b2-148">ここでは、*ユーザー*、*ロール*、および*userroles*のマッピングテーブルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="729b2-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="729b2-149">Users</span><span class="sxs-lookup"><span data-stu-id="729b2-149">Users</span></span>

|<span data-ttu-id="729b2-150">*Id<br>(dbo です。AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="729b2-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="729b2-151">*メンバーシップ<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="729b2-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="729b2-152">**フィールド名**</span><span class="sxs-lookup"><span data-stu-id="729b2-152">**Field Name**</span></span>                 |<span data-ttu-id="729b2-153">**Type**</span><span class="sxs-lookup"><span data-stu-id="729b2-153">**Type**</span></span>|<span data-ttu-id="729b2-154">**フィールド名**</span><span class="sxs-lookup"><span data-stu-id="729b2-154">**Field Name**</span></span>                                    |<span data-ttu-id="729b2-155">**Type**</span><span class="sxs-lookup"><span data-stu-id="729b2-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="729b2-156">string</span><span class="sxs-lookup"><span data-stu-id="729b2-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="729b2-157">string</span><span class="sxs-lookup"><span data-stu-id="729b2-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="729b2-158">string</span><span class="sxs-lookup"><span data-stu-id="729b2-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="729b2-159">string</span><span class="sxs-lookup"><span data-stu-id="729b2-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="729b2-160">string</span><span class="sxs-lookup"><span data-stu-id="729b2-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="729b2-161">string</span><span class="sxs-lookup"><span data-stu-id="729b2-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="729b2-162">string</span><span class="sxs-lookup"><span data-stu-id="729b2-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="729b2-163">string</span><span class="sxs-lookup"><span data-stu-id="729b2-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="729b2-164">string</span><span class="sxs-lookup"><span data-stu-id="729b2-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="729b2-165">string</span><span class="sxs-lookup"><span data-stu-id="729b2-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="729b2-166">string</span><span class="sxs-lookup"><span data-stu-id="729b2-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="729b2-167">string</span><span class="sxs-lookup"><span data-stu-id="729b2-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="729b2-168">bit</span><span class="sxs-lookup"><span data-stu-id="729b2-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="729b2-169">bit</span><span class="sxs-lookup"><span data-stu-id="729b2-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="729b2-170">すべてのフィールドマッピングは、メンバーシップから ASP.NET Core Id への一対一のリレーションシップに似ているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="729b2-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="729b2-171">上の表は、既定のメンバーシップユーザースキーマを取得し、それを ASP.NET Core Id スキーマにマップします。</span><span class="sxs-lookup"><span data-stu-id="729b2-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="729b2-172">メンバーシップに使用されていたその他のカスタムフィールドは、手動でマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="729b2-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="729b2-173">このマッピングでは、パスワードの条件とパスワード salts の両方が2つの間で移行されないため、パスワードのマップはありません。</span><span class="sxs-lookup"><span data-stu-id="729b2-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="729b2-174">**パスワードを null として残し、ユーザーにパスワードのリセットを依頼することをお勧めします。**</span><span class="sxs-lookup"><span data-stu-id="729b2-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="729b2-175">ASP.NET Core Id では、ユーザーがロックアウトされている場合、`LockoutEnd` は将来の日付に設定する必要があります。これは移行スクリプトに示されています。</span><span class="sxs-lookup"><span data-stu-id="729b2-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="729b2-176">役割</span><span class="sxs-lookup"><span data-stu-id="729b2-176">Roles</span></span>

|<span data-ttu-id="729b2-177">*Id<br>(dbo です。AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="729b2-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="729b2-178">*メンバーシップ<br>(dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="729b2-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="729b2-179">**フィールド名**</span><span class="sxs-lookup"><span data-stu-id="729b2-179">**Field Name**</span></span>                 |<span data-ttu-id="729b2-180">**Type**</span><span class="sxs-lookup"><span data-stu-id="729b2-180">**Type**</span></span>|<span data-ttu-id="729b2-181">**フィールド名**</span><span class="sxs-lookup"><span data-stu-id="729b2-181">**Field Name**</span></span>   |<span data-ttu-id="729b2-182">**Type**</span><span class="sxs-lookup"><span data-stu-id="729b2-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="729b2-183">string</span><span class="sxs-lookup"><span data-stu-id="729b2-183">string</span></span>  |`RoleId`         | <span data-ttu-id="729b2-184">string</span><span class="sxs-lookup"><span data-stu-id="729b2-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="729b2-185">string</span><span class="sxs-lookup"><span data-stu-id="729b2-185">string</span></span>  |`RoleName`       | <span data-ttu-id="729b2-186">string</span><span class="sxs-lookup"><span data-stu-id="729b2-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="729b2-187">string</span><span class="sxs-lookup"><span data-stu-id="729b2-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="729b2-188">string</span><span class="sxs-lookup"><span data-stu-id="729b2-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="729b2-189">ユーザー ロール</span><span class="sxs-lookup"><span data-stu-id="729b2-189">User Roles</span></span>

|<span data-ttu-id="729b2-190">*Id<br>(dbo です。AspNetUserRoles*</span><span class="sxs-lookup"><span data-stu-id="729b2-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="729b2-191">*メンバーシップ<br>(dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="729b2-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="729b2-192">**フィールド名**</span><span class="sxs-lookup"><span data-stu-id="729b2-192">**Field Name**</span></span>           |<span data-ttu-id="729b2-193">**Type**</span><span class="sxs-lookup"><span data-stu-id="729b2-193">**Type**</span></span>  |<span data-ttu-id="729b2-194">**フィールド名**</span><span class="sxs-lookup"><span data-stu-id="729b2-194">**Field Name**</span></span>|<span data-ttu-id="729b2-195">**Type**</span><span class="sxs-lookup"><span data-stu-id="729b2-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="729b2-196">string</span><span class="sxs-lookup"><span data-stu-id="729b2-196">string</span></span>    |`RoleId`      |<span data-ttu-id="729b2-197">string</span><span class="sxs-lookup"><span data-stu-id="729b2-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="729b2-198">string</span><span class="sxs-lookup"><span data-stu-id="729b2-198">string</span></span>    |`UserId`      |<span data-ttu-id="729b2-199">string</span><span class="sxs-lookup"><span data-stu-id="729b2-199">string</span></span>                     |

<span data-ttu-id="729b2-200">*ユーザー*および*ロール*の移行スクリプトを作成する場合は、上記のマッピングテーブルを参照してください。</span><span class="sxs-lookup"><span data-stu-id="729b2-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="729b2-201">次の例では、データベースサーバーに2つのデータベースがあることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="729b2-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="729b2-202">1つのデータベースには、既存の ASP.NET メンバーシップスキーマとデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="729b2-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="729b2-203">他の*CoreIdentitySample*データベースは、前に説明した手順を使用して作成されました。</span><span class="sxs-lookup"><span data-stu-id="729b2-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="729b2-204">詳細については、コメントがインラインで含まれています。</span><span class="sxs-lookup"><span data-stu-id="729b2-204">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="729b2-205">前のスクリプトが完了すると、前に作成した ASP.NET Core Id アプリにメンバーシップユーザーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="729b2-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="729b2-206">ユーザーはログインする前にパスワードを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="729b2-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="729b2-207">メンバーシップシステムにユーザー名が電子メールアドレスと一致しないユーザーがいる場合は、これに対応するために以前に作成したアプリに変更を加える必要があります。</span><span class="sxs-lookup"><span data-stu-id="729b2-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="729b2-208">既定のテンプレートでは `UserName` と `Email` は同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="729b2-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="729b2-209">これらが異なる場合は、`Email`ではなく `UserName` を使用するようにログインプロセスを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="729b2-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="729b2-210">*Pages\Account\Login.cshtml.cs*にあるログインページの `PageModel` で、" *Email* " プロパティから `[EmailAddress]` 属性を削除します。</span><span class="sxs-lookup"><span data-stu-id="729b2-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="729b2-211">名前を*UserName*に変更します。</span><span class="sxs-lookup"><span data-stu-id="729b2-211">Rename it to *UserName*.</span></span> <span data-ttu-id="729b2-212">これには `EmailAddress` が示されている場所で、*ビュー*と*PageModel*で変更が必要になります。</span><span class="sxs-lookup"><span data-stu-id="729b2-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="729b2-213">結果は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="729b2-213">The result looks like the following:</span></span>

 ![固定ログイン](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="729b2-215">次のステップ:</span><span class="sxs-lookup"><span data-stu-id="729b2-215">Next steps</span></span>

<span data-ttu-id="729b2-216">このチュートリアルでは、SQL メンバーシップからユーザーを ASP.NET Core 2.0 Id に移植する方法を学習しました。</span><span class="sxs-lookup"><span data-stu-id="729b2-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="729b2-217">ASP.NET Core Id の詳細については、「 [id の概要](xref:security/authentication/identity)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="729b2-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
