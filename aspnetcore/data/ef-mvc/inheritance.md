---
title: 'チュートリアル: 継承を実装する - ASP.NET MVC と EF Core'
description: このチュートリアルでは、ASP.NET Core アプリケーションで Entity Framework Core を使用して、データ モデル内の継承を実装する方法を説明します。
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 60d97137fbc6a08152b7b8a631b7a32940875b38
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975102"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="2d46e-103">チュートリアル: 継承を実装する - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="2d46e-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="2d46e-104">前のチュートリアルでは、コンカレンシー制御の例外を処理しました。</span><span class="sxs-lookup"><span data-stu-id="2d46e-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="2d46e-105">このチュートリアルでは、データ モデルで継承を実装する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="2d46e-106">オブジェクト指向プログラミングでは、継承を使用してコードの再利用を容易にします。</span><span class="sxs-lookup"><span data-stu-id="2d46e-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="2d46e-107">このチュートリアルでは、`Instructor` と `Student` クラスを `Person` 基底クラスから派生するように変更します。この基底クラスはインストラクターと受講者の両方に共通な `LastName` などのプロパティを含んでいます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="2d46e-108">どの Web ページも追加または変更しませんが、コードの一部を変更し、それらの変更はデータベースに自動的に反映されます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="2d46e-109">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="2d46e-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2d46e-110">継承をデータベースにマップする</span><span class="sxs-lookup"><span data-stu-id="2d46e-110">Map inheritance to database</span></span>
> * <span data-ttu-id="2d46e-111">Person クラスの作成</span><span class="sxs-lookup"><span data-stu-id="2d46e-111">Create the Person class</span></span>
> * <span data-ttu-id="2d46e-112">Instructor と Student を更新する</span><span class="sxs-lookup"><span data-stu-id="2d46e-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="2d46e-113">モデルに Person を追加する</span><span class="sxs-lookup"><span data-stu-id="2d46e-113">Add Person to the model</span></span>
> * <span data-ttu-id="2d46e-114">移行を作成および更新する</span><span class="sxs-lookup"><span data-stu-id="2d46e-114">Create and update migrations</span></span>
> * <span data-ttu-id="2d46e-115">実装をテストする</span><span class="sxs-lookup"><span data-stu-id="2d46e-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2d46e-116">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2d46e-116">Prerequisites</span></span>

* [<span data-ttu-id="2d46e-117">コンカレンシーの処理</span><span class="sxs-lookup"><span data-stu-id="2d46e-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="2d46e-118">継承をデータベースにマップする</span><span class="sxs-lookup"><span data-stu-id="2d46e-118">Map inheritance to database</span></span>

<span data-ttu-id="2d46e-119">School データ モデル内の `Instructor` および `Student` クラスにはいくつかの同じプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Student クラスと Instructor クラス](inheritance/_static/no-inheritance.png)

<span data-ttu-id="2d46e-121">`Instructor` エンティティと `Student` エンティティで共有されるプロパティの冗長なコードを削除すると仮定します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="2d46e-122">または、インストラクターと学生のどちらから名前を取得したかに関係なく、名前をフォーマットできるサービスを記述するとします。</span><span class="sxs-lookup"><span data-stu-id="2d46e-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="2d46e-123">次の図に示すように、それらの共有プロパティのみが含まれる `Person` 基底クラスを作成し、`Instructor` クラスと `Student`クラスがその基底クラスから継承するようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Person クラスから派生する Student クラスと Instructor クラス](inheritance/_static/inheritance.png)

<span data-ttu-id="2d46e-125">データベースでこの継承構造を表すことができるいくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="2d46e-126">受講者とインストラクターの両方に関する情報を 1 つのテーブル内に含む Person テーブルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="2d46e-127">一部の列 (HireDate) はインストラクターのみに適用され、一部 (EnrollmentDate) は受講者のみに適用され、一部 (LastName、FirstName) は両方に適用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="2d46e-128">通常、各行がどの種類を表すかを示す識別子の列があります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="2d46e-129">たとえば、識別子列にインストラクターを示す "Instructor" と受講者を示す "Student" がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Table-per-Hierarchy の例](inheritance/_static/tph.png)

<span data-ttu-id="2d46e-131">1 つのデータベース テーブルからエンティティの継承構造を生成するこのパターンは、Table-per-Hierarchy (TPH) 継承と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="2d46e-132">代わりに、継承構造と同じように見えるデータベースを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="2d46e-133">たとえば、Person テーブルに名前フィールドのみを含め、データ フィールドが含まれる別の Instructor テーブルと Student テーブルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

![Table-Per-Type 継承](inheritance/_static/tpt.png)

<span data-ttu-id="2d46e-135">このエンティティ クラスごとにデータベース テーブルを作成するパターンは、Table-Per-Type (TPT) 継承と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-135">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="2d46e-136">他のオプションとして、個々のテーブルにすべての非抽象型をマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-136">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="2d46e-137">継承されたプロパティを含むクラスのすべてのプロパティは、対応するテーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-137">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="2d46e-138">このパターンは、Table-per-Concrete Class (TPC) 継承と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-138">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="2d46e-139">前に示したように、Person、Student、および Instructor クラスの TPC 継承を実装した場合、Student テーブルと Instructor テーブルは、継承を実装した後がその前とまったく同じに見えます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-139">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="2d46e-140">TPC および TPH 継承パターンは、一般的に TPT 継承パターンよりも高いパフォーマンスを実現します。これは、TPT パターンの結果として複雑な結合クエリになる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="2d46e-140">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="2d46e-141">このチュートリアルでは、TPH 継承の実装方法を示します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-141">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="2d46e-142">TPH は、Entity Framework Core がサポートする唯一の継承パターンです。</span><span class="sxs-lookup"><span data-stu-id="2d46e-142">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="2d46e-143">実行する作業として、`Person` クラスを作成し、`Instructor` および `Student` クラスを `Person` から派生するように変更し、新しいクラスを `DbContext` に追加して、移行を作成します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-143">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="2d46e-144">次の変更を加える前に、プロジェクトのコピーを保存することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="2d46e-144">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="2d46e-145">問題が発生して最初からやり直す必要がある場合、このチュートリアルで実行した手順を逆に実行したり、すべてのシリーズの最初に戻ったりするよりも、保存したプロジェクトから開始する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="2d46e-145">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="2d46e-146">Person クラスの作成</span><span class="sxs-lookup"><span data-stu-id="2d46e-146">Create the Person class</span></span>

<span data-ttu-id="2d46e-147">[モデル] フォルダーで、Person.cs を作成し、テンプレートのコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-147">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="2d46e-148">Instructor と Student を更新する</span><span class="sxs-lookup"><span data-stu-id="2d46e-148">Update Instructor and Student</span></span>

<span data-ttu-id="2d46e-149">*Instructor.cs* で、Person クラスから Instructor を派生させ、キーと名前のフィールドを削除します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-149">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="2d46e-150">コードは次の例のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-150">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="2d46e-151">*Student.cs* に同じ変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-151">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="2d46e-152">モデルに Person を追加する</span><span class="sxs-lookup"><span data-stu-id="2d46e-152">Add Person to the model</span></span>

<span data-ttu-id="2d46e-153">Person エンティティ型を *SchoolContext.cs* に追加します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-153">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="2d46e-154">新しい行が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-154">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="2d46e-155">Table-per-Hierarchy 継承を構成するために Entity Framework に必要なのことはこれですべてです。</span><span class="sxs-lookup"><span data-stu-id="2d46e-155">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="2d46e-156">ご覧のように、データベースが更新されたときに、Student テーブルと Instructor テーブルの代わりに、Person テーブルがあります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-156">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="2d46e-157">移行を作成および更新する</span><span class="sxs-lookup"><span data-stu-id="2d46e-157">Create and update migrations</span></span>

<span data-ttu-id="2d46e-158">変更を保存し、プロジェクトをビルドします。</span><span class="sxs-lookup"><span data-stu-id="2d46e-158">Save your changes and build the project.</span></span> <span data-ttu-id="2d46e-159">次に、プロジェクト フォルダーでコマンド ウィンドウを開き、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-159">Then open the command window in the project folder and enter the following command:</span></span>

```console
dotnet ef migrations add Inheritance
```

<span data-ttu-id="2d46e-160">`database update` コマンドはまだ実行しないでください。</span><span class="sxs-lookup"><span data-stu-id="2d46e-160">Don't run the `database update` command yet.</span></span> <span data-ttu-id="2d46e-161">このコマンドは、Instructor テーブルを削除し、Student テーブルの名前を Person に変更するので、コマンドの結果としてデータが失われます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-161">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="2d46e-162">既存のデータを保持するためにカスタム コードを提供する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-162">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="2d46e-163">*Migrations/\<timestamp>_Inheritance.cs* を開き、`Up` メソッドを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-163">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="2d46e-164">このコードは、次のデータベースの更新タスクを処理します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-164">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="2d46e-165">外部キー制約と Student テーブルをポイントするインデックスを削除します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-165">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="2d46e-166">Instructor テーブルの名前の Person に変更し、Student データを格納するために必要な変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-166">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="2d46e-167">受講者の null 許容 EnrollmentDate を追加します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-167">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="2d46e-168">行が、受講者かインストラクターかを示すために識別子列を追加します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-168">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="2d46e-169">受講者行には雇用日がないので HireDate を nul 許容にします。</span><span class="sxs-lookup"><span data-stu-id="2d46e-169">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="2d46e-170">受講者をポイントする外部キーの更新に使用する一時的なフィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-170">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="2d46e-171">Person テーブルに受講者をコピーするときに新しい主キー値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-171">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="2d46e-172">Student テーブルから Person テーブルにデータをコピーします。</span><span class="sxs-lookup"><span data-stu-id="2d46e-172">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="2d46e-173">これにより、受講者に新しい主キー値が割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-173">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="2d46e-174">受講者をポイントする外部キー値を修正します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-174">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="2d46e-175">今は Person テーブルをポイントしている外部キー制約とインデックスを再作成します</span><span class="sxs-lookup"><span data-stu-id="2d46e-175">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="2d46e-176">(主キーの型として整数の代わりに GUID を使用した場合は、受講者の主キー値を変更する必要はなく、これらの手順のいくつかを省略できます)。</span><span class="sxs-lookup"><span data-stu-id="2d46e-176">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="2d46e-177">`database update` コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-177">Run the `database update` command:</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="2d46e-178">(実稼働システムでは、以前のデータベースバージョンに戻すために `Down` メソッドを使用する必要があった場合、このメソッドに対応する変更を行います。</span><span class="sxs-lookup"><span data-stu-id="2d46e-178">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="2d46e-179">このチュートリアルでは、`Down` メソッドは使用しません)</span><span class="sxs-lookup"><span data-stu-id="2d46e-179">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="2d46e-180">データが存在するデータベースでスキーマの変更を行うと、他のエラーが発生する場合があります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-180">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="2d46e-181">解決できない移行エラーが発生した場合は、接続文字列のデータベース名を変更するか、データベースを削除できます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-181">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="2d46e-182">新しいデータベースには移行するデータが存在しないため、update-database コマンドがエラーなしで完了する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="2d46e-182">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="2d46e-183">データベースを削除するには、SSOX を使用するか `database drop` CLI コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-183">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="2d46e-184">実装をテストする</span><span class="sxs-lookup"><span data-stu-id="2d46e-184">Test the implementation</span></span>

<span data-ttu-id="2d46e-185">アプリを実行して、さまざまなページを試してください。</span><span class="sxs-lookup"><span data-stu-id="2d46e-185">Run the app and try various pages.</span></span> <span data-ttu-id="2d46e-186">すべてが前と同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-186">Everything works the same as it did before.</span></span>

<span data-ttu-id="2d46e-187">**SQL Server オブジェクト エクスプローラー**で、 **[データ接続/SchoolContext]** を展開し、 **[テーブル]** を展開すると、Student テーブルと Instructor テーブルが Person テーブルに置き換えられていることを確認できます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-187">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="2d46e-188">Person テーブル デザイナーを開くと、Student テーブルと Student テーブルに存在していたすべての列が表示されます。</span><span class="sxs-lookup"><span data-stu-id="2d46e-188">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![SSOX の Person テーブル](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="2d46e-190">Person テーブルを右クリックし、 **[テーブル データの表示]** をクリックして識別子列を表示します。</span><span class="sxs-lookup"><span data-stu-id="2d46e-190">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![SSOX の Person テーブル - テーブル データ](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="2d46e-192">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="2d46e-192">Get the code</span></span>

[<span data-ttu-id="2d46e-193">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="2d46e-193">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="2d46e-194">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="2d46e-194">Additional resources</span></span>

<span data-ttu-id="2d46e-195">Entity Framework Core での継承の詳細については、「[継承](/ef/core/modeling/inheritance)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2d46e-195">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2d46e-196">次の手順</span><span class="sxs-lookup"><span data-stu-id="2d46e-196">Next steps</span></span>

<span data-ttu-id="2d46e-197">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="2d46e-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2d46e-198">継承をデータベースにマップした</span><span class="sxs-lookup"><span data-stu-id="2d46e-198">Mapped inheritance to database</span></span>
> * <span data-ttu-id="2d46e-199">Person クラスを作成した</span><span class="sxs-lookup"><span data-stu-id="2d46e-199">Created the Person class</span></span>
> * <span data-ttu-id="2d46e-200">Instructor と Student を更新した</span><span class="sxs-lookup"><span data-stu-id="2d46e-200">Updated Instructor and Student</span></span>
> * <span data-ttu-id="2d46e-201">モデルに Person を追加した</span><span class="sxs-lookup"><span data-stu-id="2d46e-201">Added Person to the model</span></span>
> * <span data-ttu-id="2d46e-202">移行を作成および更新した</span><span class="sxs-lookup"><span data-stu-id="2d46e-202">Created and update migrations</span></span>
> * <span data-ttu-id="2d46e-203">実装をテストした</span><span class="sxs-lookup"><span data-stu-id="2d46e-203">Tested the implementation</span></span>

<span data-ttu-id="2d46e-204">比較的高度な Entity Framework のさまざまなシナリオを処理する方法について学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="2d46e-204">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="2d46e-205">次へ: 詳細トピック</span><span class="sxs-lookup"><span data-stu-id="2d46e-205">Next: Advanced topics</span></span>](advanced.md)
