---
title: 'チュートリアル: ASP.NET MVC Web アプリでの EF Core の概要'
description: これは、Contoso University のサンプル アプリケーションを一から作成する方法を説明するチュートリアル シリーズの 1 回目です。
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 02/06/2019
ms.topic: tutorial
uid: data/ef-mvc/intro
ms.openlocfilehash: 3450ac5b46e2a03b5d58c8760b78a52065343992
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985371"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="aae52-103">チュートリアル: ASP.NET MVC Web アプリでの EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="aae52-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aae52-104">このチュートリアルは ASP.NET Core 3.0 に更新されて**いません**。</span><span class="sxs-lookup"><span data-stu-id="aae52-104">This tutorial has **not** been updated to ASP.NET Core 3.0.</span></span> <span data-ttu-id="aae52-105">[Razor Pages バージョン](xref:data/ef-rp/intro)は更新されています。</span><span class="sxs-lookup"><span data-stu-id="aae52-105">The [Razor Pages version](xref:data/ef-rp/intro) has been updated.</span></span> <span data-ttu-id="aae52-106">この情報がいつ更新されるかについては、[こちらの GitHub の問題](https://github.com/aspnet/AspNetCore.Docs/issues/13920)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aae52-106">For information on when this might be updated, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/13920).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="aae52-107">Contoso University のサンプル Web アプリケーションでは、Entity Framework (EF) Core 2.2 と Visual Studio 2017 または 2019 を使用して ASP.NET Core 2.2 MVC Web アプリケーションを作成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="aae52-107">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="aae52-108">サンプル アプリケーションは架空の Contoso University の Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="aae52-108">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="aae52-109">学生の受け付け、講座の作成、講師の割り当てなどの機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="aae52-109">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="aae52-110">これは、Contoso University のサンプル アプリケーションを一から作成する方法を説明するチュートリアル シリーズの 1 回目です。</span><span class="sxs-lookup"><span data-stu-id="aae52-110">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

<span data-ttu-id="aae52-111">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="aae52-111">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aae52-112">ASP.NET Core MVC Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="aae52-112">Create an ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="aae52-113">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="aae52-113">Set up the site style</span></span>
> * <span data-ttu-id="aae52-114">EF Core の NuGet パッケージについて学習する</span><span class="sxs-lookup"><span data-stu-id="aae52-114">Learn about EF Core NuGet packages</span></span>
> * <span data-ttu-id="aae52-115">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="aae52-115">Create the data model</span></span>
> * <span data-ttu-id="aae52-116">データベース コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="aae52-116">Create the database context</span></span>
> * <span data-ttu-id="aae52-117">依存関係挿入にコンテキストを登録する</span><span class="sxs-lookup"><span data-stu-id="aae52-117">Register the context for dependency injection</span></span>
> * <span data-ttu-id="aae52-118">テスト データでデータベースを初期化する</span><span class="sxs-lookup"><span data-stu-id="aae52-118">Initialize the database with test data</span></span>
> * <span data-ttu-id="aae52-119">コントローラーとビューの作成</span><span class="sxs-lookup"><span data-stu-id="aae52-119">Create a controller and views</span></span>
> * <span data-ttu-id="aae52-120">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="aae52-120">View the database</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aae52-121">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="aae52-121">Prerequisites</span></span>

* [<span data-ttu-id="aae52-122">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="aae52-122">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download)
* <span data-ttu-id="aae52-123">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) と次のワークロード:</span><span class="sxs-lookup"><span data-stu-id="aae52-123">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="aae52-124">**ASP.NET および Web の開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="aae52-124">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="aae52-125">**.NET Core クロスプラットフォームの開発**ワークロード</span><span class="sxs-lookup"><span data-stu-id="aae52-125">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="aae52-126">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="aae52-126">Troubleshooting</span></span>

<span data-ttu-id="aae52-127">解決できない問題に遭遇した場合、通常、[完成済みのプロジェクト](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)と自分のコードを比較することで解決策がわかります。</span><span class="sxs-lookup"><span data-stu-id="aae52-127">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final).</span></span> <span data-ttu-id="aae52-128">一般的なエラーとその解決方法の一覧については、[チュートリアル シリーズの後半に登場するトラブルシューティング セクション](advanced.md#common-errors)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="aae52-128">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="aae52-129">そこで必要な答えが見つからない場合、StackOverflow.com で [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) または [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) に関する質問を投稿できます。</span><span class="sxs-lookup"><span data-stu-id="aae52-129">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="aae52-130">これは 10 回のチュートリアルからなるシリーズであり、いずれの回も前のチュートリアルを基盤にしています。</span><span class="sxs-lookup"><span data-stu-id="aae52-130">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="aae52-131">チュートリアルが完了したら、毎回、プロジェクトのコピーを保存するようお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aae52-131">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="aae52-132">問題に遭遇したとき、前のチュートリアルから始めることができます。シリーズ全体の始めまで戻る必要がありません。</span><span class="sxs-lookup"><span data-stu-id="aae52-132">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="aae52-133">Contoso University Web アプリ</span><span class="sxs-lookup"><span data-stu-id="aae52-133">Contoso University web app</span></span>

<span data-ttu-id="aae52-134">一連のチュートリアルで作成するアプリケーションは、簡単な大学向け Web サイトです。</span><span class="sxs-lookup"><span data-stu-id="aae52-134">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="aae52-135">ユーザーは学生、講座、講師の情報を見たり、更新したりできます。</span><span class="sxs-lookup"><span data-stu-id="aae52-135">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="aae52-136">次のような画面をこれから作成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-136">Here are a few of the screens you'll create.</span></span>

![Students インデックス ページ](intro/_static/students-index.png)

![Students 編集ページ](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="aae52-139">Web アプリを作成する</span><span class="sxs-lookup"><span data-stu-id="aae52-139">Create web app</span></span>

* <span data-ttu-id="aae52-140">Visual Studio を開きます。</span><span class="sxs-lookup"><span data-stu-id="aae52-140">Open Visual Studio.</span></span>

* <span data-ttu-id="aae52-141">**[ファイル]** メニューで **[新規作成]、[プロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-141">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="aae52-142">左側のウィンドウで、 **[インストール済み]、[Visual C#]、[Web]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-142">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="aae52-143">**[ASP.NET Core Web アプリケーション]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-143">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="aae52-144">名前に「**ContosoUniversity**」と入力し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aae52-144">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![[新しいプロジェクト] ダイアログ](intro/_static/new-project2.png)

* <span data-ttu-id="aae52-146">**[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されるのを待ちます。</span><span class="sxs-lookup"><span data-stu-id="aae52-146">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="aae52-147">**[.NET Core]** 、 **[ASP.NET Core 2.2]** 、および **[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-147">Select **.NET Core**, **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="aae52-148">**[認証]** に **[認証なし]** が設定されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="aae52-148">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="aae52-149">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-149">Select **OK**</span></span>

  ![[新しい ASP.NET Core プロジェクト] ダイアログ](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="aae52-151">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="aae52-151">Set up the site style</span></span>

<span data-ttu-id="aae52-152">簡単な変更をいくつか行い、サイトのメニュー、レイアウト、ホーム ページを決めます。</span><span class="sxs-lookup"><span data-stu-id="aae52-152">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="aae52-153">*Views/Shared/_Layout.cshtml* を開き、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-153">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="aae52-154">"ContosoUniversity" をすべて "Contoso University" に変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-154">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="aae52-155">これは 3 回出てきます。</span><span class="sxs-lookup"><span data-stu-id="aae52-155">There are three occurrences.</span></span>

* <span data-ttu-id="aae52-156">メニュー エントリとして「**About**」、「**Students**」、「**Courses**」、「**Instructors**」、「**Departments**」を追加し、「**Privacy**」メニュー エントリを削除します。</span><span class="sxs-lookup"><span data-stu-id="aae52-156">Add menu entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="aae52-157">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-157">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="aae52-158">*Views/Home/Index.cshtml* で、ファイルの中身を次のコードに変更し、ASP.NET と MVC に関するテキストをこのアプリケーションに関するテキストに変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-158">In *Views/Home/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="aae52-159">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-159">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="aae52-160">一連のチュートリアルで作成するページのホーム ページとタブが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-160">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso University のホーム ページ](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="aae52-162">EF Core の NuGet パッケージについて</span><span class="sxs-lookup"><span data-stu-id="aae52-162">About EF Core NuGet packages</span></span>

<span data-ttu-id="aae52-163">プロジェクトに EF Core サポートを追加するには、対象とするデータベース プロバイダーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="aae52-163">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="aae52-164">このチュートリアルでは SQL Server を使用します。プロバイダー パッケージは [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) です。</span><span class="sxs-lookup"><span data-stu-id="aae52-164">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="aae52-165">このパッケージは [Microsoft.AspNetCore.App メタパッケージ](xref:fundamentals/metapackage-app)に含まれているので、パッケージを参照する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="aae52-165">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="aae52-166">EF SQL Server パッケージとその依存関係 (`Microsoft.EntityFrameworkCore` と `Microsoft.EntityFrameworkCore.Relational`) により、EF のランタイム サポートが提供されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-166">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="aae52-167">後の[移行](migrations.md)チュートリアルでツール パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="aae52-167">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="aae52-168">Entity Framework Core で利用できるその他のデータベース プロバイダーに関しては、「[データベース プロバイダー](/ef/core/providers/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aae52-168">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="aae52-169">データ モデルの作成</span><span class="sxs-lookup"><span data-stu-id="aae52-169">Create the data model</span></span>

<span data-ttu-id="aae52-170">次に、Contoso University アプリケーションのエンティティ クラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-170">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="aae52-171">次の 3 つのエンティティから始めます。</span><span class="sxs-lookup"><span data-stu-id="aae52-171">You'll start with the following three entities.</span></span>

![講座、登録、学生からなるデータ モデルの図](intro/_static/data-model-diagram.png)

<span data-ttu-id="aae52-173">`Student` エンティティと `Enrollment` エンティティの間に一対多の関係があり、`Course` エンティティと `Enrollment` エンティティの間に一対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="aae52-173">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="aae52-174">言い換えると、1 人の学生をさまざまな講座に登録し、1 つの講座にたくさんの学生を登録できます。</span><span class="sxs-lookup"><span data-stu-id="aae52-174">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="aae52-175">次のセクションでは、エンティティごとにクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-175">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="aae52-176">Student エンティティ</span><span class="sxs-lookup"><span data-stu-id="aae52-176">The Student entity</span></span>

![Student エンティティの図](intro/_static/student-entity.png)

<span data-ttu-id="aae52-178">*[Models]* フォルダーで、*Student.cs* という名前のクラス ファイルを作成し、テンプレート コードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-178">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="aae52-179">`ID` プロパティは、このクラスに相当するデータベース テーブルの主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="aae52-179">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="aae52-180">既定では、Entity Framework は、`ID` または `classnameID` という名前のプロパティを主キーとして解釈します。</span><span class="sxs-lookup"><span data-stu-id="aae52-180">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="aae52-181">`Enrollments` プロパティは[ナビゲーション プロパティ](/ef/core/modeling/relationships)です。</span><span class="sxs-lookup"><span data-stu-id="aae52-181">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="aae52-182">ナビゲーション プロパティには、このエンティティに関連する他のエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="aae52-182">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="aae52-183">この例では、`Student entity` の `Enrollments` プロパティで、その `Student` エンティティに関連するすべての `Enrollment` エンティティが保持されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-183">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="aae52-184">つまり、データベースの Student 行にある 2 つの Enrollment 行が関連している場合 (外部キー列 StudentID にその学生の主キー値が含まれる行)、その `Student` エンティティの `Enrollments` ナビゲーション プロパティにその 2 つの `Enrollment` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="aae52-184">In other words, if a given Student row in the database has two related Enrollment rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="aae52-185">ナビゲーション プロパティに複数のエンティティが含まれる場合 (多対多または一対多の関係で)、その型はリストにする必要があります。`ICollection<T>` のように、エンティティを追加、削除、更新できるリストです。</span><span class="sxs-lookup"><span data-stu-id="aae52-185">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="aae52-186">`ICollection<T>`、または `List<T>` や `HashSet<T>` などの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="aae52-186">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="aae52-187">`ICollection<T>` を指定した場合、EF では既定で `HashSet<T>` コレクションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-187">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="aae52-188">Enrollment エンティティ</span><span class="sxs-lookup"><span data-stu-id="aae52-188">The Enrollment entity</span></span>

![Enrollment エンティティの図](intro/_static/enrollment-entity.png)

<span data-ttu-id="aae52-190">*[Models]* フォルダーで、*Enrollment.cs* を作成し、既存のコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-190">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="aae52-191">`EnrollmentID` プロパティは主キーになります。このエンティティは、`Student` エンティティと同様に、`ID` ではなく `classnameID` パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="aae52-191">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="aae52-192">通常、パターンを 1 つ選択し、データ モデル全体でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="aae52-192">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="aae52-193">ここのバリエーションから、いずれのパターンも利用できることがわかります。</span><span class="sxs-lookup"><span data-stu-id="aae52-193">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="aae52-194">[後のチュートリアル](inheritance.md)では、クラス名なしの ID を利用し、データ モデルに継承を簡単に実装する方法を学習します。</span><span class="sxs-lookup"><span data-stu-id="aae52-194">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="aae52-195">`Grade` プロパティは `enum` です。</span><span class="sxs-lookup"><span data-stu-id="aae52-195">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="aae52-196">`Grade` の型宣言の後の疑問符は、`Grade` プロパティが null 許容であることを示します。</span><span class="sxs-lookup"><span data-stu-id="aae52-196">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="aae52-197">null という成績は、0 の成績とは異なります。null は成績がわからないことか、まだ評価されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="aae52-197">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="aae52-198">`StudentID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Student` です。</span><span class="sxs-lookup"><span data-stu-id="aae52-198">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="aae52-199">`Enrollment` エンティティは 1 つの `Student` エンティティに関連付けられており、1 つの `Student` エンティティだけを保持できます (先に見た、複数の `Enrollment` エンティティを保持できる `Student.Enrollments` ナビゲーション プロパティとは異なります)。</span><span class="sxs-lookup"><span data-stu-id="aae52-199">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="aae52-200">`CourseID` プロパティは外部キーです。それに対応するナビゲーション プロパティは `Course` です。</span><span class="sxs-lookup"><span data-stu-id="aae52-200">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="aae52-201">`Enrollment` エンティティは 1 つの `Course` エンティティに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="aae52-201">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="aae52-202">Entity Framework は `<navigation property name><primary key property name>` という名前が付いている場合、プロパティを外部キー プロパティとして解釈します。たとえば、`Student` ナビゲーション プロパティの `StudentID` です。`Student` エンティティの主キーが `ID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="aae52-202">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="aae52-203">外部キーにも `<primary key property name>` という単純な名前を付けることができます。たとえば、`CourseID` です。`Course` エンティティの主キーが `CourseID` であるためです。</span><span class="sxs-lookup"><span data-stu-id="aae52-203">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="aae52-204">Course エンティティ</span><span class="sxs-lookup"><span data-stu-id="aae52-204">The Course entity</span></span>

![Course エンティティの図](intro/_static/course-entity.png)

<span data-ttu-id="aae52-206">*[Models]* フォルダーで、*Course.cs* を作成し、既存のコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-206">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="aae52-207">`Enrollments` プロパティはナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="aae52-207">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="aae52-208">1 つの `Course` エンティティにたくさんの `Enrollment` エンティティを関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="aae52-208">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="aae52-209">`DatabaseGenerated` 属性については、このシリーズの[後のチュートリアル](complex-data-model.md)で詳しく学習します。</span><span class="sxs-lookup"><span data-stu-id="aae52-209">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="aae52-210">基本的に、この属性によって、講座の主キーをデータベースに生成させず、自分で入力できるようになります。</span><span class="sxs-lookup"><span data-stu-id="aae52-210">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="aae52-211">データベース コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="aae52-211">Create the database context</span></span>

<span data-ttu-id="aae52-212">所与のデータ モデルの Entity Framework 機能を調整するメイン クラスは、データベース コンテキスト クラスです。</span><span class="sxs-lookup"><span data-stu-id="aae52-212">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="aae52-213">このクラスは、`Microsoft.EntityFrameworkCore.DbContext` クラスから派生させて作成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-213">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="aae52-214">自分のコードでは、データ モデルに含めるエンティティを自分で指定します。</span><span class="sxs-lookup"><span data-stu-id="aae52-214">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="aae52-215">Entity Framework の特定の動作をカスタマイズすることもできます。</span><span class="sxs-lookup"><span data-stu-id="aae52-215">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="aae52-216">このプロジェクトでは、クラスに `SchoolContext` という名前が付けられています。</span><span class="sxs-lookup"><span data-stu-id="aae52-216">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="aae52-217">プロジェクト フォルダーで、*Data* という名前のフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-217">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="aae52-218">*[Data]* フォルダーで、*SchoolContext.cs* という名前の新しいクラス ファイルを作成し、テンプレート コードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-218">In the *Data* folder create a new class file named *SchoolContext.cs*, and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="aae52-219">このコードによって、エンティティ セットごとに `DbSet` プロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-219">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="aae52-220">Entity Framework の用語では、エンティティ セットは通常はデータベース テーブルに対応し、エンティティはテーブルの行に対応します。</span><span class="sxs-lookup"><span data-stu-id="aae52-220">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aae52-221">`DbSet<Enrollment>` ステートメントと `DbSet<Course>` ステートメントは省略しても同じ動作をします。</span><span class="sxs-lookup"><span data-stu-id="aae52-221">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="aae52-222">Entity Framework にはそれらが暗黙的に含まれることがあります。`Student` エンティティが `Enrollment` エンティティを参照し、`Enrollment` エンティティが `Course` エンティティを参照するためです。</span><span class="sxs-lookup"><span data-stu-id="aae52-222">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="aae52-223">データベースが作成されると、EF によって、`DbSet` プロパティと同じ名前を持つテーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-223">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="aae52-224">一般的にコレクションのプロパティ名は複数形 (Student ではなく、Students) ですが、テーブル名を複数にするかどうかについては、開発者の間で意見が分かれています。</span><span class="sxs-lookup"><span data-stu-id="aae52-224">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="aae52-225">このチュートリアル シリーズでは、DbContext に単数のテーブル名を指定して既定の動作をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="aae52-225">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="aae52-226">そのために、最後の DbSet プロパティの後に、次の強調表示されているコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="aae52-226">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="aae52-227">SchoolContext を登録する</span><span class="sxs-lookup"><span data-stu-id="aae52-227">Register the SchoolContext</span></span>

<span data-ttu-id="aae52-228">ASP.NET Core は既定で[依存関係の挿入](../../fundamentals/dependency-injection.md)を実装します。</span><span class="sxs-lookup"><span data-stu-id="aae52-228">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="aae52-229">サービス (EF データベース コンテキストなど) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-229">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="aae52-230">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンストラクターのパラメーターを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-230">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aae52-231">このチュートリアルの後半で、コンテキスト インスタンスを取得するコントローラー コンストラクター コードが登場します。</span><span class="sxs-lookup"><span data-stu-id="aae52-231">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="aae52-232">`SchoolContext` をサービスとして登録するには、*Startup.cs* を開き、強調表示されている行を `ConfigureServices` メソッドに追加します。</span><span class="sxs-lookup"><span data-stu-id="aae52-232">To register `SchoolContext` as a service, open *Startup.cs*, and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="aae52-233">`DbContextOptionsBuilder` オブジェクトでメソッドが呼び出され、接続文字列の名前がコンテキストに渡されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-233">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="aae52-234">ローカル開発の場合、[ASP.NET Core 構成システム](xref:fundamentals/configuration/index)が *appsettings.json* ファイルから接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="aae52-234">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="aae52-235">名前空間の `ContosoUniversity.Data` と `Microsoft.EntityFrameworkCore` に対して `using` ステートメントを追加し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="aae52-235">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="aae52-236">*appsettings.json* ファイルを開き、次のサンプルのように接続文字列を追加します。</span><span class="sxs-lookup"><span data-stu-id="aae52-236">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="aae52-237">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="aae52-237">SQL Server Express LocalDB</span></span>

<span data-ttu-id="aae52-238">この接続文字列によって SQL Server LocalDB データベースが指定されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-238">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="aae52-239">LocalDB は SQL Server Express データベース エンジンの軽量版であり、実稼働ではなく、アプリケーションの開発を意図して設計されています。</span><span class="sxs-lookup"><span data-stu-id="aae52-239">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="aae52-240">LocalDB は要求時に開始され、ユーザー モードで実行されるため、複雑な構成はありません。</span><span class="sxs-lookup"><span data-stu-id="aae52-240">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="aae52-241">既定では、LocalDB は `C:/Users/<user>` ディレクトリに *.mdf* データベース ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-241">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="aae52-242">テスト データで DB を初期化する</span><span class="sxs-lookup"><span data-stu-id="aae52-242">Initialize DB with test data</span></span>

<span data-ttu-id="aae52-243">Entity Framework によって空のデータベースが自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-243">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="aae52-244">このセクションでは、テスト データを入力する目的で、データベースの作成後に呼び出されるメソッドを記述します。</span><span class="sxs-lookup"><span data-stu-id="aae52-244">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="aae52-245">ここでは、データベースを自動的に作成する `EnsureCreated` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="aae52-245">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="aae52-246">[後のチュートリアル](migrations.md)では、モデル変更の処理方法について学習します。データベースを削除し、再作成するのではなく、Code First Migrations を利用してデータベース スキーマを変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-246">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="aae52-247">*[データ]* フォルダーで *DbInitializer.cs* という名前の新しいクラス ファイルを作成し、テンプレート コードを次のコードに変更します。このコードにより、必要なときにデータベースが作成され、新しいデータベースにテスト データが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="aae52-247">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="aae52-248">このコードはデータベースに学生が存在するかどうかを確認し、存在しない場合、そのデータベースは新しく、テスト データを入力する必要があると見なします。</span><span class="sxs-lookup"><span data-stu-id="aae52-248">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="aae52-249">`List<T>` コレクションではなく配列にテスト データを読み込み、パフォーマンスを最適化します。</span><span class="sxs-lookup"><span data-stu-id="aae52-249">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="aae52-250">*Program.cs* で、アプリケーションの起動時に次を実行するように `Main` メソッドを変更します。</span><span class="sxs-lookup"><span data-stu-id="aae52-250">In *Program.cs*, modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="aae52-251">依存関係挿入コンテナーからデータベース コンテキスト インスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="aae52-251">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="aae52-252">seed メソッドを呼び出し、コンテキストを渡します。</span><span class="sxs-lookup"><span data-stu-id="aae52-252">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="aae52-253">seed メソッドが完了したら、コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="aae52-253">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=3-20)]

<span data-ttu-id="aae52-254">`using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="aae52-254">Add `using` statements:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Usings)]

<span data-ttu-id="aae52-255">以前のチュートリアルでは、*Startup.cs* で `Configure` メソッドと同様のコードを確認できるかもしれません。</span><span class="sxs-lookup"><span data-stu-id="aae52-255">In older tutorials, you may see similar code in the `Configure` method in *Startup.cs*.</span></span> <span data-ttu-id="aae52-256">要求パイプラインを設定する目的でのみ `Configure` メソッドを利用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="aae52-256">We recommend that you use the `Configure` method only to set up the request pipeline.</span></span> <span data-ttu-id="aae52-257">アプリケーションの起動コードは、`Main` メソッドに属します。</span><span class="sxs-lookup"><span data-stu-id="aae52-257">Application startup code belongs in the `Main` method.</span></span>

<span data-ttu-id="aae52-258">これからアプリケーションを初めて実行します。データベースが作成され、テスト データが入力されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-258">Now the first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="aae52-259">データ モデルを変更するたびに、データベースを削除し、seed メソッドを更新し、新しいデータベースで同様にやり直すことができます。</span><span class="sxs-lookup"><span data-stu-id="aae52-259">Whenever you change your data model, you can delete the database, update your seed method, and start afresh with a new database the same way.</span></span> <span data-ttu-id="aae52-260">後のチュートリアルでは、データ モデルが変わったとき、データベースを削除して作り直すのではなく、修正する方法について学習します。</span><span class="sxs-lookup"><span data-stu-id="aae52-260">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="aae52-261">コントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="aae52-261">Create controller and views</span></span>

<span data-ttu-id="aae52-262">次に、Visual Studio のスキャフォールディング エンジンを利用し、MVC のコントローラーとビューを追加します。このコントローラーとビューでは、EF を利用してクエリを実行し、データを保存します。</span><span class="sxs-lookup"><span data-stu-id="aae52-262">Next, you'll use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="aae52-263">CRUD アクションのメソッドとビューの自動作成は、スキャフォールディングと言います。</span><span class="sxs-lookup"><span data-stu-id="aae52-263">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="aae52-264">一般的には生成されたコードは修正しないのに対し、スキャフォールディングされたコードを開始点として独自の要件に合うように変更できるという点で、スキャフォールディングはコード生成と異なります。</span><span class="sxs-lookup"><span data-stu-id="aae52-264">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="aae52-265">生成されたコードをカスタマイズする必要があるとき、部分クラスを利用するか、状況が変わったときにコードを再生成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-265">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="aae52-266">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、 **[追加]、[スキャフォールディングされた新しい項目]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-266">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>

* <span data-ttu-id="aae52-267">**[スキャフォールディングを追加]** ダイアログ ボックスで:</span><span class="sxs-lookup"><span data-stu-id="aae52-267">In the **Add Scaffold** dialog box:</span></span>

  * <span data-ttu-id="aae52-268">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-268">Select **MVC controller with views, using Entity Framework**.</span></span>

  * <span data-ttu-id="aae52-269">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aae52-269">Click **Add**.</span></span> <span data-ttu-id="aae52-270">**[Add MVC Controller with views, using Entity Framework]\(Entity Framework を使用したビューがある MVC コントローラーを追加する\)** ダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-270">The **Add MVC Controller with views, using Entity Framework** dialog box appears.</span></span>

    ![Student のスキャフォールディング](intro/_static/scaffold-student2.png)

  * <span data-ttu-id="aae52-272">**[モデル クラス]** で **[Student]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-272">In **Model class** select **Student**.</span></span>

  * <span data-ttu-id="aae52-273">**[データ コンテキスト クラス]** で **[SchoolContext]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-273">In **Data context class** select **SchoolContext**.</span></span>

  * <span data-ttu-id="aae52-274">名前は **StudentsController** をそのまま選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-274">Accept the default **StudentsController** as the name.</span></span>

  * <span data-ttu-id="aae52-275">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aae52-275">Click **Add**.</span></span>

  <span data-ttu-id="aae52-276">**[追加]** をクリックすると、Visual Studio スキャフォールディング エンジンは *StudentsController.cs* ファイルと、コントローラーと連動する一連のビュー ( *.cshtml* ファイル) を作成します。</span><span class="sxs-lookup"><span data-stu-id="aae52-276">When you click **Add**, the Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views (*.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="aae52-277">(スキャフォールディング エンジンは、このチュートリアルで先に行ったように手動で最初に作成しない場合、データベース コンテキストを自動作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="aae52-277">(The scaffolding engine can also create the database context for you if you don't create it manually first as you did earlier for this tutorial.</span></span> <span data-ttu-id="aae52-278">**[コントローラーの追加]** ボックスで新しいコンテキスト クラスを指定できます。 **[データ コンテキスト クラス]** の右にあるプラス記号をクリックします。</span><span class="sxs-lookup"><span data-stu-id="aae52-278">You can specify a new context class in the **Add Controller** box by clicking the plus sign to the right of **Data context class**.</span></span>  <span data-ttu-id="aae52-279">Visual Studio はコントローラーやビューと共に `DbContext` クラスを作成します。)</span><span class="sxs-lookup"><span data-stu-id="aae52-279">Visual Studio will then create your `DbContext` class as well as the controller and views.)</span></span>

<span data-ttu-id="aae52-280">コントローラーがコンストラクター パラメーターとして `SchoolContext` を受け取ることがわかります。</span><span class="sxs-lookup"><span data-stu-id="aae52-280">You'll notice that the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="aae52-281">ASP.NET Core 依存関係挿入では、`SchoolContext` のインスタンスがコントローラーに渡されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-281">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="aae52-282">それは先に *Startup.cs* ファイルで構成しました。</span><span class="sxs-lookup"><span data-stu-id="aae52-282">You configured that in the *Startup.cs* file earlier.</span></span>

<span data-ttu-id="aae52-283">コントローラーには `Index` アクション メソッドが含まれます。これはデータベースにあるすべての学生を表示します。</span><span class="sxs-lookup"><span data-stu-id="aae52-283">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="aae52-284">このメソッドはデータベース コンテキスト インスタンスの `Students` プロパティを読み取り、Students エンティティ セットから学生の一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="aae52-284">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="aae52-285">チュートリアルの後半で、このコードの非同期プログラミング要素について学習します。</span><span class="sxs-lookup"><span data-stu-id="aae52-285">You'll learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="aae52-286">*Views/Students/Index.cshtml* ビューには、この一覧で表形式で表示されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-286">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="aae52-287">CTRL を押しながら F5 を押してプロジェクトを実行するか、メニューで **[デバッグ]、[デバッグなしで開始]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-287">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="aae52-288">[Students] タブをクリックすると、`DbInitializer.Initialize` メソッドによって挿入されたテスト データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-288">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="aae52-289">ブラウザーのウィンドウ幅によって決まることですが、`Students` タブ リンクはページの一番上に表示されるか、右上隅のナビゲーション アイコンをクリックしないと表示されません。</span><span class="sxs-lookup"><span data-stu-id="aae52-289">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso University のホーム ページ (ウィンドウ幅が狭いとき)](intro/_static/home-page-narrow.png)

![Students インデックス ページ](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="aae52-292">データベースを表示する</span><span class="sxs-lookup"><span data-stu-id="aae52-292">View the database</span></span>

<span data-ttu-id="aae52-293">アプリケーションを起動する葉、`DbInitializer.Initialize` メソッドが `EnsureCreated` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="aae52-293">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="aae52-294">EF はデータベースがないことを認識し、作成します。`Initialize` メソッド コードの残りの部分により、データベースにデータが入力されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-294">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="aae52-295">**SQL Server Object Explorer** (SSOX) を利用し、Visual Studio でデータベースを表示できます。</span><span class="sxs-lookup"><span data-stu-id="aae52-295">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="aae52-296">ブラウザーを閉じます。</span><span class="sxs-lookup"><span data-stu-id="aae52-296">Close the browser.</span></span>

<span data-ttu-id="aae52-297">SSOX ウィンドウがまだ開いていない場合、Visual Studio の **[表示]** メニューから選択します。</span><span class="sxs-lookup"><span data-stu-id="aae52-297">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="aae52-298">SSOX で **(localdb)\MSSQLLocalDB > Databases** をクリックし、*appsettings.json* ファイルの接続文字列にあるデータベース名のエントリをクリックします。</span><span class="sxs-lookup"><span data-stu-id="aae52-298">In SSOX, click **(localdb)\MSSQLLocalDB > Databases**, and then click the entry for the database name that's in the connection string in your *appsettings.json* file.</span></span>

<span data-ttu-id="aae52-299">**[テーブル]** ノードを展開し、データベースのテーブルを表示します。</span><span class="sxs-lookup"><span data-stu-id="aae52-299">Expand the **Tables** node to see the tables in your database.</span></span>

![SSOX のテーブル](intro/_static/ssox-tables.png)

<span data-ttu-id="aae52-301">**[Student]** テーブルを右クリックし、 **[データの表示]** をクリックすると、作成された列とテーブルに挿入された行が表示されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-301">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX の Student テーブル](intro/_static/ssox-student-table.png)

<span data-ttu-id="aae52-303">データベース ファイルの *.mdf* と *.ldf* は *C:\Users\\\<ユーザー名>* フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="aae52-303">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<yourusername>* folder.</span></span>

<span data-ttu-id="aae52-304">アプリの起動時に実行される初期化子メソッドで `EnsureCreated` を呼び出すため、`Student` クラスを変更し、データベースを削除し、アプリケーションを再実行できます。変更に合わせてデータベースが自動的に再作成されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-304">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="aae52-305">たとえば、`Student` クラスに `EmailAddress` プロパティを追加する場合、再作成されたテーブルに新しい `EmailAddress` 列が表示されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-305">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="aae52-306">規約</span><span class="sxs-lookup"><span data-stu-id="aae52-306">Conventions</span></span>

<span data-ttu-id="aae52-307">規約を利用することや Entity Framework が想定を行うことにより、Entity Framework が完全なデータベースを自動作成するために記述しなければならないコードの量が最小限に抑えられます。</span><span class="sxs-lookup"><span data-stu-id="aae52-307">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="aae52-308">`DbSet` プロパティの名前がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-308">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="aae52-309">`DbSet` プロパティによって参照されないエンティティについては、エンティティ クラス名がテーブル名として使用されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-309">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>

* <span data-ttu-id="aae52-310">列名には、エンティティ プロパティ名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-310">Entity property names are used for column names.</span></span>

* <span data-ttu-id="aae52-311">ID または classnameID という名前が付けられているエンティティ プロパティは主キーのプロパティとして認識されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-311">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>

* <span data-ttu-id="aae52-312">*\<ナビゲーション プロパティ名>\<主キー プロパティ名>* という名前 (たとえば、`Student` ナビゲーション プロパティの場合、`Student` エンティティの主キーが `ID` なので、`StudentID` となります) が付いている場合、プロパティは外部キー プロパティとして解釈されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-312">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="aae52-313">外部キー プロパティにも *\<主キー プロパティ名>* という単純な名前を付けることができます (たとえば、`Enrollment` エンティティの主キーが `EnrollmentID` なので `EnrollmentID`)。</span><span class="sxs-lookup"><span data-stu-id="aae52-313">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="aae52-314">規約の動作はオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="aae52-314">Conventional behavior can be overridden.</span></span> <span data-ttu-id="aae52-315">たとえば、このチュートリアルで先に見たように、テーブル名を明示的に指定できます。</span><span class="sxs-lookup"><span data-stu-id="aae52-315">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="aae52-316">また、列名を設定し、任意のプロパティを主キーまたは外部キーとして設定できます。これについては、このシリーズの[後のチュートリアル](complex-data-model.md)で学習します。</span><span class="sxs-lookup"><span data-stu-id="aae52-316">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="aae52-317">非同期コード</span><span class="sxs-lookup"><span data-stu-id="aae52-317">Asynchronous code</span></span>

<span data-ttu-id="aae52-318">ASP.NET Core と EF Core では、非同期プログラミングが既定のモードです。</span><span class="sxs-lookup"><span data-stu-id="aae52-318">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="aae52-319">Web サーバーでは、利用できるスレッド数に限りがあります。負荷が高い状況では、利用できるスレッドが全部使われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="aae52-319">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="aae52-320">その場合、スレッドが解放されるまでサーバーは新しい要求を処理できません。</span><span class="sxs-lookup"><span data-stu-id="aae52-320">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="aae52-321">同期コードの場合、たくさんのスレッドが関連付けられていても、I/O の完了を待っているため、実際には何の作業も行っていないということがあります。</span><span class="sxs-lookup"><span data-stu-id="aae52-321">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="aae52-322">非同期コードの場合、あるプロセスが I/O の完了を待っているとき、そのスレッドは解放され、サーバーによって他の要求の処理に利用できます。</span><span class="sxs-lookup"><span data-stu-id="aae52-322">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="aae52-323">結果として、非同期コードの場合、サーバー リソースをより効率的に利用できます。サーバーは、より多くのトラフィックを遅延なく処理できます。</span><span class="sxs-lookup"><span data-stu-id="aae52-323">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="aae52-324">非同期コードは実行時に少量のオーバーヘッドを発生させるが、トラフィックが少ない場合、パフォーマンスに与える影響は無視して構わない程度です。トラフィックが多い場合、相当なパフォーマンス改善が見込まれます。</span><span class="sxs-lookup"><span data-stu-id="aae52-324">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="aae52-325">次のコードでは、キーワード `async`、戻り値 `Task<T>`、キーワード `await`、メソッド `ToListAsync` によりコードの実行が非同期になります。</span><span class="sxs-lookup"><span data-stu-id="aae52-325">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="aae52-326">キーワード `async` は、メソッド本文の一部にコールバックを生成し、返された `Task<IActionResult>` オブジェクトを自動作成するようにコンパイラに伝えます。</span><span class="sxs-lookup"><span data-stu-id="aae52-326">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>

* <span data-ttu-id="aae52-327">戻り値の型 `Task<IActionResult>` は、進行中の作業と型 `IActionResult` の結果を表します。</span><span class="sxs-lookup"><span data-stu-id="aae52-327">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>

* <span data-ttu-id="aae52-328">キーワード `await` により、コンパイラはメソッドを 2 つに分割します。</span><span class="sxs-lookup"><span data-stu-id="aae52-328">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="aae52-329">最初の部分は、非同期で開始される操作で終わります。</span><span class="sxs-lookup"><span data-stu-id="aae52-329">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="aae52-330">2 つ目の部分は、操作の完了時に呼び出されるコールバック メソッドに入ります。</span><span class="sxs-lookup"><span data-stu-id="aae52-330">The second part is put into a callback method that's called when the operation completes.</span></span>

* <span data-ttu-id="aae52-331">`ToListAsync` は、`ToList` 拡張メソッドの非同期バージョンです。</span><span class="sxs-lookup"><span data-stu-id="aae52-331">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="aae52-332">Entity Framework を利用する非同期コードの記述で注意すべき点:</span><span class="sxs-lookup"><span data-stu-id="aae52-332">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="aae52-333">クエリやコマンドをデータベースに送信するステートメントのみが非同期で実行されます。</span><span class="sxs-lookup"><span data-stu-id="aae52-333">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="aae52-334">たとえば、`ToListAsync`、`SingleOrDefaultAsync`、`SaveChangesAsync` などです。</span><span class="sxs-lookup"><span data-stu-id="aae52-334">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="aae52-335">`var students = context.Students.Where(s => s.LastName == "Davolio")` など、`IQueryable` を変更するだけのステートメントは含まれません。</span><span class="sxs-lookup"><span data-stu-id="aae52-335">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>

* <span data-ttu-id="aae52-336">EF コンテキストはスレッド セーフではありません。複数の操作を並列実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="aae52-336">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="aae52-337">非同期 EF メソッドを呼び出すとき、`await` キーワードを常に使用します。</span><span class="sxs-lookup"><span data-stu-id="aae52-337">When you call any async EF method, always use the `await` keyword.</span></span>

* <span data-ttu-id="aae52-338">非同期コードのパフォーマンス上の利点を最大限に活用する場合、(ページングなどのために) ライブラリ パッケージを利用しているのであれば、それがクエリをデータベースに送信させる Entity Framework メソッドを呼び出す場合、非同期を利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aae52-338">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="aae52-339">.NET の非同期プログラミングについては、「[非同期の概要](/dotnet/articles/standard/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="aae52-339">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="aae52-340">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="aae52-340">Get the code</span></span>

[<span data-ttu-id="aae52-341">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="aae52-341">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="aae52-342">次の手順</span><span class="sxs-lookup"><span data-stu-id="aae52-342">Next steps</span></span>

<span data-ttu-id="aae52-343">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="aae52-343">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aae52-344">ASP.NET Core MVC Web アプリを作成した</span><span class="sxs-lookup"><span data-stu-id="aae52-344">Created ASP.NET Core MVC web app</span></span>
> * <span data-ttu-id="aae52-345">サイトのスタイルを設定する</span><span class="sxs-lookup"><span data-stu-id="aae52-345">Set up the site style</span></span>
> * <span data-ttu-id="aae52-346">EF Core の NuGet パッケージについて学習した</span><span class="sxs-lookup"><span data-stu-id="aae52-346">Learned about EF Core NuGet packages</span></span>
> * <span data-ttu-id="aae52-347">データ モデルを作成した</span><span class="sxs-lookup"><span data-stu-id="aae52-347">Created the data model</span></span>
> * <span data-ttu-id="aae52-348">データベース コンテキストを作成した</span><span class="sxs-lookup"><span data-stu-id="aae52-348">Created the database context</span></span>
> * <span data-ttu-id="aae52-349">SchoolContext を登録した</span><span class="sxs-lookup"><span data-stu-id="aae52-349">Registered the SchoolContext</span></span>
> * <span data-ttu-id="aae52-350">テスト データで DB を初期化した</span><span class="sxs-lookup"><span data-stu-id="aae52-350">Initialized DB with test data</span></span>
> * <span data-ttu-id="aae52-351">コントローラーとビューを作成した</span><span class="sxs-lookup"><span data-stu-id="aae52-351">Created controller and views</span></span>
> * <span data-ttu-id="aae52-352">データベースを表示した</span><span class="sxs-lookup"><span data-stu-id="aae52-352">Viewed the database</span></span>

<span data-ttu-id="aae52-353">次のチュートリアルでは、基本的な CRUD (作成、読み取り、更新、削除) 操作を実行する方法について学習します。</span><span class="sxs-lookup"><span data-stu-id="aae52-353">In the following tutorial, you'll learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

<span data-ttu-id="aae52-354">基本的な CRUD (作成、読み取り、更新、削除) 操作の実行方法を学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="aae52-354">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="aae52-355">基本 CRUD 機能を実装する</span><span class="sxs-lookup"><span data-stu-id="aae52-355">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
