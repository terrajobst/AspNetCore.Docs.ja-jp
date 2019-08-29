---
title: 'チュートリアル: コンカレンシーの処理 - ASP.NET MVC と EF Core'
description: このチュートリアルでは、複数のユーザーが同じエンティティを同時に更新するときの競合の処理方法について説明します。
author: tdykstra
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 0a42a8adbe485f47853fa05fb500f4480f06e3f5
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975109"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="6180f-103">チュートリアル: コンカレンシーの処理 - ASP.NET MVC と EF Core</span><span class="sxs-lookup"><span data-stu-id="6180f-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="6180f-104">先のチュートリアルでは、データを更新する方法について学習しました。</span><span class="sxs-lookup"><span data-stu-id="6180f-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="6180f-105">このチュートリアルでは、複数のユーザーが同じエンティティを同時に更新するときの競合の処理方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="6180f-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="6180f-106">Department エンティティを使用する Web ページを作成し、コンカレンシー エラーを処理します。</span><span class="sxs-lookup"><span data-stu-id="6180f-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="6180f-107">次の図は Edit ページと Delete ページのものです。コンカレンシーで競合が発生すると、メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Department Edit ページ](concurrency/_static/edit-error.png)

![Department Delete ページ](concurrency/_static/delete-error.png)

<span data-ttu-id="6180f-110">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="6180f-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6180f-111">コンカレンシーの競合について学習する</span><span class="sxs-lookup"><span data-stu-id="6180f-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="6180f-112">トラッキング プロパティを追加する</span><span class="sxs-lookup"><span data-stu-id="6180f-112">Add a tracking property</span></span>
> * <span data-ttu-id="6180f-113">Departments のコントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="6180f-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="6180f-114">Index ビューを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-114">Update Index view</span></span>
> * <span data-ttu-id="6180f-115">Edit メソッドを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-115">Update Edit methods</span></span>
> * <span data-ttu-id="6180f-116">Edit ビューを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-116">Update Edit view</span></span>
> * <span data-ttu-id="6180f-117">コンカレンシーの競合をテストする</span><span class="sxs-lookup"><span data-stu-id="6180f-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="6180f-118">[削除] ページを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-118">Update the Delete page</span></span>
> * <span data-ttu-id="6180f-119">Details ビューと Create ビューの更新</span><span class="sxs-lookup"><span data-stu-id="6180f-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6180f-120">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="6180f-120">Prerequisites</span></span>

* [<span data-ttu-id="6180f-121">関連データの更新</span><span class="sxs-lookup"><span data-stu-id="6180f-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="6180f-122">コンカレンシーの競合</span><span class="sxs-lookup"><span data-stu-id="6180f-122">Concurrency conflicts</span></span>

<span data-ttu-id="6180f-123">あるユーザーがあるエンティティのデータを編集目的で表示したとき、別のユーザーが同じエンティティのデータを最初のユーザーの変更がデータベースに書き込まれる前に更新すると、コンカレンシーの競合が発生します。</span><span class="sxs-lookup"><span data-stu-id="6180f-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="6180f-124">このような競合の検出を有効にしないと、最後にデータベースを更新したユーザーが他のユーザーの変更を上書きすることになります。</span><span class="sxs-lookup"><span data-stu-id="6180f-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="6180f-125">多くのアプリケーションでは、このリスクが許容されています。ユーザーや更新がわずかであれば、あるいは変更が一部上書きされても大きな問題なければ、コンカレンシーのプログラミングにかかるコストが利点よりも重視されることがあります。</span><span class="sxs-lookup"><span data-stu-id="6180f-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="6180f-126">その場合、コンカレンシーの競合を処理するようにアプリケーションを構成する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6180f-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="6180f-127">ペシミスティック コンカレンシー (ロック)</span><span class="sxs-lookup"><span data-stu-id="6180f-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="6180f-128">コンカレンシーで偶発的にデータが失われる事態をアプリケーションで回避する必要があれば、その方法としてデータベース ロックがあります。</span><span class="sxs-lookup"><span data-stu-id="6180f-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="6180f-129">これはペシミスティック コンカレンシーと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="6180f-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="6180f-130">たとえば、データベースから行を読む前に、読み取り専用か更新アクセスでロックを要求します。</span><span class="sxs-lookup"><span data-stu-id="6180f-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="6180f-131">更新アクセスで行をロックすると、他のユーザーはその行を読み取り専用または更新アクセスでロックできなくなります。変更中のデータのコピーが与えられるためです。</span><span class="sxs-lookup"><span data-stu-id="6180f-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="6180f-132">読み取り専用で行をロックすると、他のユーザーはその行を読み取り専用でロックできますが、更新アクセスではロックできません。</span><span class="sxs-lookup"><span data-stu-id="6180f-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="6180f-133">ロックの利用には短所があります。</span><span class="sxs-lookup"><span data-stu-id="6180f-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="6180f-134">プログラムが複雑になります。</span><span class="sxs-lookup"><span data-stu-id="6180f-134">It can be complex to program.</span></span> <span data-ttu-id="6180f-135">相当なデータベース管理リソースが必要になります。アプリケーションの利用者数が増えると、パフォーマンス上の問題を引き起こすことがあります。</span><span class="sxs-lookup"><span data-stu-id="6180f-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="6180f-136">そのような理由から、一部のデータベース管理システムはペシミスティック コンカレンシーに対応していません。</span><span class="sxs-lookup"><span data-stu-id="6180f-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="6180f-137">Entity Framework Core にも組み込まれておらず、このチュートリアルでは実装方法を説明しません。</span><span class="sxs-lookup"><span data-stu-id="6180f-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="6180f-138">オプティミスティック コンカレンシー</span><span class="sxs-lookup"><span data-stu-id="6180f-138">Optimistic Concurrency</span></span>

<span data-ttu-id="6180f-139">ペシミスティック コンカレンシーの代わりとなるのがオプティミスティック コンカレンシーです。</span><span class="sxs-lookup"><span data-stu-id="6180f-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="6180f-140">オプティミスティック コンカレンシーでは、コンカレンシーの競合の発生を許し、発生したら適切に対処します。</span><span class="sxs-lookup"><span data-stu-id="6180f-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="6180f-141">たとえば、Jane が Department Edit ページにアクセスし、English 部署の予算を $350,000.00 から $0.00 に変更するとします。</span><span class="sxs-lookup"><span data-stu-id="6180f-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![予算を 0 に変更する](concurrency/_static/change-budget.png)

<span data-ttu-id="6180f-143">Jane が **[保存]** をクリックする前に John が同じページにアクセスし、[開始日] フィールドを 9/1/2007 から 9/1/2013 に変更します。</span><span class="sxs-lookup"><span data-stu-id="6180f-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![開始日を 2013 に変更する](concurrency/_static/change-date.png)

<span data-ttu-id="6180f-145">Jane が **[保存]** を先にクリックすると、ブラウザーが Index ページに戻ったとき、Jane の変更が反映されています。</span><span class="sxs-lookup"><span data-stu-id="6180f-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![予算が 0 に変更された](concurrency/_static/budget-zero.png)

<span data-ttu-id="6180f-147">次に John が Edit ページの **[保存]** をクリックします。このとき、予算は $350,000.00 と表示されています。</span><span class="sxs-lookup"><span data-stu-id="6180f-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="6180f-148">この後の動作は、コンカレンシーの競合の処理方法によって決定します。</span><span class="sxs-lookup"><span data-stu-id="6180f-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="6180f-149">次のようなオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="6180f-149">Some of the options include the following:</span></span>

* <span data-ttu-id="6180f-150">ユーザーが変更したプロパティを追跡記録し、それに該当する列だけをデータベースで更新できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="6180f-151">例のシナリオでは、2 人のユーザーが異なるプロパティを更新したため、データは失われません。</span><span class="sxs-lookup"><span data-stu-id="6180f-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="6180f-152">今度誰かが English 部署を閲覧すると、Jane の変更と John の変更が両方とも表示されます。開始日が 9/1/2013 で予算が 0 ドルです。</span><span class="sxs-lookup"><span data-stu-id="6180f-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="6180f-153">この更新方法では、データの損失につながる可能性がある競合の数を減らすことができますが、あるエンティティの同じプロパティに対して行われた変更が競合する場合、データの損失は避けられません。</span><span class="sxs-lookup"><span data-stu-id="6180f-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="6180f-154">Entity Framework がこのように動作するかどうかは、更新コードの実装方法に依存します。</span><span class="sxs-lookup"><span data-stu-id="6180f-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="6180f-155">これは Web アプリケーションの場合、実用的ではない場合が多いです。あるエンティティの新しい値に加え、元のプロパティ値もすべて追跡記録するため、大量のステータスを更新することになるからです。</span><span class="sxs-lookup"><span data-stu-id="6180f-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="6180f-156">大量のステータスを更新するとなると、サーバー リソースが必要になるか、Web ページ自体 (非表示フィールドなど) や Cookie に含める必要があるため、アプリケーションのパフォーマンスに影響が出ます。</span><span class="sxs-lookup"><span data-stu-id="6180f-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="6180f-157">John の変更で Jane の変更を上書きするように指定できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="6180f-158">今度誰かが English 部署を閲覧すると、日付は 9/1/2013 ですが、金額が $350,000.00 に戻っています。</span><span class="sxs-lookup"><span data-stu-id="6180f-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="6180f-159">これは *Client Wins* (クライアント側に合わせる) シナリオまたは *Last in Wins* (最終書き込み者優先) シナリオと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="6180f-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="6180f-160">(クライアントからの値がすべて、データ ストアの値より優先されます。)このセクションの冒頭でお伝えしたように、コンカレンシー処理について何のコーディングもしない場合、これが自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="6180f-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="6180f-161">データベースで John の変更が更新されないようにできます。</span><span class="sxs-lookup"><span data-stu-id="6180f-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="6180f-162">通常、エラー メッセージが表示され、John にデータの現在の状態が伝えられます。John は望むなら変更を再適用できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="6180f-163">これは *Store Wins* (ストア側に合わせる) シナリオと呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="6180f-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="6180f-164">(クライアントが送信した値よりデータストアの値が優先されます。)このチュートリアルでは、Store Wins シナリオを実装します。</span><span class="sxs-lookup"><span data-stu-id="6180f-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="6180f-165">この手法では、変更が上書きされるとき、それが必ずユーザーに警告されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="6180f-166">コンカレンシーの競合の検出</span><span class="sxs-lookup"><span data-stu-id="6180f-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="6180f-167">Entity Framework がスローする `DbConcurrencyException` 例外を処理することで競合を解決できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="6180f-168">このような例外がスローされるタイミングを認識する目的で、Entity Framework は競合を検出できなければなりません。</span><span class="sxs-lookup"><span data-stu-id="6180f-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="6180f-169">そのため、データベースとデータ モデルを適宜構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6180f-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="6180f-170">競合検出を有効にするためのオプションには次のようなものがあります。</span><span class="sxs-lookup"><span data-stu-id="6180f-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="6180f-171">行が変更されたタイミングを判断するトラッキング列をデータベース テーブルに追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="6180f-172">その後、Entity Framework を構成し、SQL の Update または Delete コマンドの Where 句にその列を追加できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="6180f-173">トラッキング列のデータ型は通常、`rowversion` です。</span><span class="sxs-lookup"><span data-stu-id="6180f-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="6180f-174">`rowversion` 値は連続番号であり、行が更新されるたびに増えます。</span><span class="sxs-lookup"><span data-stu-id="6180f-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="6180f-175">Update または Delete コマンドでは、Where 句にトラッキング列の元の値が含まれます (元の行バージョン)。</span><span class="sxs-lookup"><span data-stu-id="6180f-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="6180f-176">更新中の行が別のユーザーによって変更された場合、`rowversion` 列の値は元の値とは異なります。Where 句に起因し、Update または Delete ステートメントは更新する行を見つけられません。</span><span class="sxs-lookup"><span data-stu-id="6180f-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="6180f-177">Update または Delete コマンドによって行が更新されていないことを Entity Framework が確認すると (影響を受けた行の数が 0 のとき)、それをコンカレンシーの競合として解釈します。</span><span class="sxs-lookup"><span data-stu-id="6180f-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="6180f-178">Entity Framework を構成し、テーブルで Update コマンドと Delete コマンドの Where 句にすべての列の元の値を追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="6180f-179">最初のオプションと同様に、行が最初に読み取られてから行に変更があった場合、Where 句は更新する行を返さず、Entity Framework はそれをコンカレンシーの競合として解釈します。</span><span class="sxs-lookup"><span data-stu-id="6180f-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="6180f-180">データベース テーブルに列がたくさんある場合、この手法では結果的に大量の Where 句が出現し、大量のステータスを保守管理しなければならなくなります。</span><span class="sxs-lookup"><span data-stu-id="6180f-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="6180f-181">先に述べたように、大量のステータスを保守管理することになると、アプリケーションのパフォーマンスに影響が出ます。</span><span class="sxs-lookup"><span data-stu-id="6180f-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="6180f-182">そのため、この手法は一般的には推奨されません。このチュートリアルでも利用しません。</span><span class="sxs-lookup"><span data-stu-id="6180f-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="6180f-183">コンカレンシーにこの手法を導入する場合、コンカレンシーを追跡記録するエンティティのすべての主キーではないプロパティに `ConcurrencyCheck` 属性を追加し、印を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="6180f-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="6180f-184">これで、Entity Framework では、Update ステートメントと Delete ステートメントの SQL Where 句にすべての列を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="6180f-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="6180f-185">このチュートリアルの残りの部分では、Department エンティティに `rowversion` トラッキング プロパティを追加し、コントローラーとビューを作成し、すべてが適切に動作することをテストで確認します。</span><span class="sxs-lookup"><span data-stu-id="6180f-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="6180f-186">トラッキング プロパティを追加する</span><span class="sxs-lookup"><span data-stu-id="6180f-186">Add a tracking property</span></span>

<span data-ttu-id="6180f-187">*Models/Department.cs* で、RowVersion という名前のトラッキング プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="6180f-188">`Timestamp` 属性によって、データベースに送信された Update コマンドと Delete コマンドの Where 句にこの列が追加されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="6180f-189">前のバージョンの SQL Server では、SQL `rowversion` に取って代わられる前、SQL `timestamp` というデータ型が使用されていたため、この属性は `Timestamp` と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="6180f-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="6180f-190">`rowversion` の .NET 型はバイト配列です。</span><span class="sxs-lookup"><span data-stu-id="6180f-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="6180f-191">fluent API を利用する場合、次の例のように、`IsConcurrencyToken` メソッドを利用し (*Data/SchoolContext.cs* で)、トラッキング プロパティを指定できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="6180f-192">プロパティを追加し、データベース モデルを変更したので、別の移行を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="6180f-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="6180f-193">変更内容を保存し、プロジェクトをビルドしてください。その後、コマンド ウィンドウに次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="6180f-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```console
dotnet ef migrations add RowVersion
```

```console
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="6180f-194">Departments のコントローラーとビューを作成する</span><span class="sxs-lookup"><span data-stu-id="6180f-194">Create Departments controller and views</span></span>

<span data-ttu-id="6180f-195">先に Students、Courses、Instructors に行ったように、Departments のコントローラーとビューをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="6180f-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Department のスキャフォールディング](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="6180f-197">*DepartmentsController.cs* ファイルに 4 回登場する "FirstMidName" をすべて "FullName" に変更します。それにより、部署の管理者のドロップダウン リストに講師の姓だけでなく、姓名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="6180f-198">Index ビューを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-198">Update Index view</span></span>

<span data-ttu-id="6180f-199">スキャフォールディング エンジンによりインデックス ビューに RowVersion 列が作成されましたが、このフィールドは表示すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="6180f-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="6180f-200">*Views/Departments/Index.cshtml* のコードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="6180f-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="6180f-201">これで見出しが "Departments" に変更され、RowVersion 列が削除され、管理者の名ではなく姓名が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="6180f-202">Edit メソッドを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-202">Update Edit methods</span></span>

<span data-ttu-id="6180f-203">HttpGet `Edit` メソッドと `Details` メソッドの両方に `AsNoTracking` を追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="6180f-204">HttpGet `Edit` メソッドに Administrator の一括読み込みを追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="6180f-205">HttpPost `Edit` メソッドの既存コードを次のコードに変更します。</span><span class="sxs-lookup"><span data-stu-id="6180f-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="6180f-206">このコードはまず、更新する部署を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="6180f-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="6180f-207">`FirstOrDefaultAsync` が null を返した場合、部署は別のユーザーが削除しています。</span><span class="sxs-lookup"><span data-stu-id="6180f-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="6180f-208">その場合、このコードは送信されたフォーム値を利用して部署エンティティを作成します。編集ページはエラー メッセージと共に再表示できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="6180f-209">あるいは、部署フィールドを再表示せず、エラー メッセージのみを表示するのであれば、部署エンティティを再作成する必要はないでしょう。</span><span class="sxs-lookup"><span data-stu-id="6180f-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="6180f-210">ビューの非表示フィールドに元の `RowVersion` 値が保管されます。このメソッドは `rowVersion` パラメーターでその値を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6180f-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="6180f-211">`SaveChanges` を呼び出す前に、エンティティの `OriginalValues` コレクションにその元の `RowVersion` プロパティ値を置く必要があります。</span><span class="sxs-lookup"><span data-stu-id="6180f-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="6180f-212">次に、Entity Framework で SQL UPDATE コマンドが作成されるとき、元の `RowVersion` 値が含まれる行を探す WHERE 句がそのコマンドに含まれます。</span><span class="sxs-lookup"><span data-stu-id="6180f-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="6180f-213">UPDATE コマンドの影響を受ける行がない場合 (元の `RowVersion` 値が含まれる行がない)、Entity Framework は `DbUpdateConcurrencyException` 例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="6180f-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="6180f-214">その例外の catch ブロックのコードによって、影響を受けた、例外オブジェクトの `Entries` プロパティから更新後の値が与えられた Department エンティティが取得されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="6180f-215">`Entries` コレクションには `EntityEntry` オブジェクトが 1 つだけ与えられます。</span><span class="sxs-lookup"><span data-stu-id="6180f-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="6180f-216">そのオブジェクトを利用し、ユーザーが入力した新しい値と現在のデータベース値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="6180f-217">このコードにより、編集ページでユーザーが入力したものとデータベース値が異なる列ごとにカスタムのエラー メッセージが追加されます (簡潔にするため、ここでは 1 つだけフィールドを示しています)。</span><span class="sxs-lookup"><span data-stu-id="6180f-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="6180f-218">最後になりますが、このコードで `departmentToUpdate` の `RowVersion` 値がデータベースから取得された新しい値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="6180f-219">Edit ページが再表示されるとき、この新しい `RowVersion` 値が非表示フィールドに保存されます。今度ユーザーが **[保存]** をクリックすると、Edit ページの再表示後に発生したコンカレンシー エラーのみが取得されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="6180f-220">`ModelState` の `RowVersion` 値が古いため、`ModelState.Remove` ステートメントが必要になります。</span><span class="sxs-lookup"><span data-stu-id="6180f-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="6180f-221">このビューでは、フィールドの `ModelState` 値がモデル プロパティ値より優先されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="6180f-222">Edit ビューを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-222">Update Edit view</span></span>

<span data-ttu-id="6180f-223">*Views/Departments/Edit.cshtml* で、次の変更を行います。</span><span class="sxs-lookup"><span data-stu-id="6180f-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="6180f-224">`DepartmentID` プロパティの非表示フィールドのすぐ後ろに `RowVersion` プロパティ値を保存する非表示フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="6180f-225">ドロップダウン リストに "Select Administrator" オプションを追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="6180f-226">コンカレンシーの競合をテストする</span><span class="sxs-lookup"><span data-stu-id="6180f-226">Test concurrency conflicts</span></span>

<span data-ttu-id="6180f-227">アプリを実行し、Departments Index ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6180f-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="6180f-228">English 部署の **[編集]** ハイパーリンクを右クリックし、 **[新しいタブで開く]** を選択し、English 部署の **[編集]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="6180f-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="6180f-229">2 つのブラウザー タブに同じ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="6180f-230">最初のブラウザー タブでフィールドを変更し、 **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6180f-230">Change a field in the first browser tab and click **Save**.</span></span>

![変更後の Department Edit ページ 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="6180f-232">値が変更された Index ページがブラウザーに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="6180f-233">2 番目のブラウザー タブでフィールドを変更します。</span><span class="sxs-lookup"><span data-stu-id="6180f-233">Change a field in the second browser tab.</span></span>

![変更後の Department Edit ページ 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="6180f-235">**[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6180f-235">Click **Save**.</span></span> <span data-ttu-id="6180f-236">エラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-236">You see an error message:</span></span>

![Department Edit ページのエラー メッセージ](concurrency/_static/edit-error.png)

<span data-ttu-id="6180f-238">**[保存]** をもう一度クリックします。</span><span class="sxs-lookup"><span data-stu-id="6180f-238">Click **Save** again.</span></span> <span data-ttu-id="6180f-239">2 番目のブラウザー タブで入力した値が保存されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="6180f-240">Index ページが表示されると、保存した値を確認できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="6180f-241">Delete ページを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-241">Update the Delete page</span></span>

<span data-ttu-id="6180f-242">Delete ページの場合、Entity Framework は、同様の方法で部署を編集している他のユーザーが起こしたコンカレンシーの競合を検出します。</span><span class="sxs-lookup"><span data-stu-id="6180f-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="6180f-243">HttpGet `Delete` 値に確定ビューが表示されると、そのビューに非表示フィールドの元の `RowVersion` 値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6180f-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="6180f-244">その値は、ユーザーが削除を確定したときに呼び出される HttpPost `Delete` メソッドで利用されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="6180f-245">Entity Framework で SQL DELETE コマンドが作成されるとき、WHERE 句と元の `RowVersion` 値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6180f-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="6180f-246">コマンドの結果、影響を受けた行が 0 であれば (削除確認ページの表示後に行が変更された)、コンカレンシー例外がスローされます。確定ページをエラー メッセージと共に再表示するために、エラー フラグが true に設定された状態で HttpGet `Delete` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="6180f-247">別のユーザーが行を削除したため、影響を受けた行が 0 になることもあります。その場合、エラー メッセージは表示されません。</span><span class="sxs-lookup"><span data-stu-id="6180f-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="6180f-248">Departments コントローラーの Delete メソッドの更新</span><span class="sxs-lookup"><span data-stu-id="6180f-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="6180f-249">*DepartmentsController.cs* で、HttpGet `Delete` メソッドを次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6180f-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="6180f-250">このメソッドは、コンカレンシー エラー後にページが再表示されたかどうかを示すオプション パラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="6180f-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="6180f-251">このフラグが true のとき、指定の部署が現存していなければ、別のユーザーによって削除されています。</span><span class="sxs-lookup"><span data-stu-id="6180f-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="6180f-252">その場合、このコードは Index ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="6180f-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="6180f-253">このフラグが true のとき、Department が存在すれば、別のユーザーが変更しています。</span><span class="sxs-lookup"><span data-stu-id="6180f-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="6180f-254">その場合、このコードは `ViewData` を利用してビューにエラー メッセージを送信します。</span><span class="sxs-lookup"><span data-stu-id="6180f-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="6180f-255">HttpPost `Delete` メソッドのコード (名前は `DeleteConfirmed`) を次のコードで置き換えます。</span><span class="sxs-lookup"><span data-stu-id="6180f-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="6180f-256">置き換えたスキャフォールディングされたコードで、このメソッドがレコード ID を 1 つだけ受け取りました。</span><span class="sxs-lookup"><span data-stu-id="6180f-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="6180f-257">このパラメーターをモデル バインダーによって作成された Department エンティティに変更しています。</span><span class="sxs-lookup"><span data-stu-id="6180f-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="6180f-258">それにより、レコード キーに加え、RowVersion プロパティ値に EF はアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6180f-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="6180f-259">また、アクション メソッドの名前を `DeleteConfirmed` から `Delete` に変更しています。</span><span class="sxs-lookup"><span data-stu-id="6180f-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="6180f-260">スキャフォールディングされたコードは `DeleteConfirmed` という名前を利用し、HttpPost メソッドに一意のシグネチャを与えました。</span><span class="sxs-lookup"><span data-stu-id="6180f-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="6180f-261">(CLR では、さまざまなメソッド パラメーターを持つために、オーバーロードされたメソッドを必要とします。)シグネチャが一意になっているので、MVC 規則に準拠し、削除メソッドの HttpPost と HttpGet に同じ名前を利用できます。</span><span class="sxs-lookup"><span data-stu-id="6180f-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="6180f-262">部署が既に削除されている場合、`AnyAsync` メソッドは false を返します。アプリケーションは Index メソッドに戻ります。</span><span class="sxs-lookup"><span data-stu-id="6180f-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="6180f-263">コンカレンシー エラーがキャッチされた場合、このコードは削除確認ページを再表示し、コンカレンシー エラー メッセージを表示するかどうかを示すフラグを提供します。</span><span class="sxs-lookup"><span data-stu-id="6180f-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="6180f-264">Delete ビューを更新する</span><span class="sxs-lookup"><span data-stu-id="6180f-264">Update the Delete view</span></span>

<span data-ttu-id="6180f-265">*Views/Departments/Delete.cshtml* で、スキャフォールディングされたコードを次のコードで置き換えます。このコードは、DepartmentID プロパティと RowVersion プロパティのエラー メッセージ フィールドと非表示フィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="6180f-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="6180f-266">変更が強調表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-266">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="6180f-267">これにより、次の変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="6180f-267">This makes the following changes:</span></span>

* <span data-ttu-id="6180f-268">見出しの `h2` と `h3` の間にエラー メッセージが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="6180f-269">**[管理者]** フィールドで FirstMidName が FullName に変更されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="6180f-270">RowVersion フィールドが削除されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="6180f-271">`RowVersion` プロパティの非表示フィールドが追加されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="6180f-272">アプリを実行し、Departments Index ページに移動します。</span><span class="sxs-lookup"><span data-stu-id="6180f-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="6180f-273">English 部署の **[削除]** ハイパーリンクを右クリックし、 **[新しいタブで開く]** を選択します。次に最初のタブで English 部署の **[編集]** ハイパーリンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="6180f-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="6180f-274">最初のウィンドウで、いずれかの値を変更し、 **[保存]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6180f-274">In the first window, change one of the values, and click **Save**:</span></span>

![変更後/削除前の Department Edit ページ](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="6180f-276">2 番目のタブで **[削除]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="6180f-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="6180f-277">コンカレンシー エラー メッセージが表示されます。Department 値がデータベースの現在の内容で更新されています。</span><span class="sxs-lookup"><span data-stu-id="6180f-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Department 削除確認ページとコンカレンシー エラー](concurrency/_static/delete-error.png)

<span data-ttu-id="6180f-279">**[削除]** をもう一度クリックすると、Index ページにリダイレクトされます。Index ページには、部署が削除されていることが表示されます。</span><span class="sxs-lookup"><span data-stu-id="6180f-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="6180f-280">Details ビューと Create ビューの更新</span><span class="sxs-lookup"><span data-stu-id="6180f-280">Update Details and Create views</span></span>

<span data-ttu-id="6180f-281">Details ビューと Create ビューで、スキャフォールディングされたコードを任意でクリーンアップできます。</span><span class="sxs-lookup"><span data-stu-id="6180f-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="6180f-282">RowVersion 列を削除し、管理者の姓名を表示するように *Views/Departments/Details.cshtml* のコードを置換します。</span><span class="sxs-lookup"><span data-stu-id="6180f-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="6180f-283">ドロップダウン リストに Select オプションを追加するように *Views/Departments/Create.cshtml* のコードを置換します。</span><span class="sxs-lookup"><span data-stu-id="6180f-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="6180f-284">コードを取得する</span><span class="sxs-lookup"><span data-stu-id="6180f-284">Get the code</span></span>

[<span data-ttu-id="6180f-285">完成したアプリケーションをダウンロードまたは表示する。</span><span class="sxs-lookup"><span data-stu-id="6180f-285">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="6180f-286">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="6180f-286">Additional resources</span></span>

 <span data-ttu-id="6180f-287">EF Core のコンカレンシー処理の詳細については、[コンカレンシーの競合](/ef/core/saving/concurrency)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6180f-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="6180f-288">次の手順</span><span class="sxs-lookup"><span data-stu-id="6180f-288">Next steps</span></span>

<span data-ttu-id="6180f-289">このチュートリアルでは、次の作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="6180f-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6180f-290">コンカレンシーの競合について学習した</span><span class="sxs-lookup"><span data-stu-id="6180f-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="6180f-291">トラッキング プロパティを追加した</span><span class="sxs-lookup"><span data-stu-id="6180f-291">Added a tracking property</span></span>
> * <span data-ttu-id="6180f-292">Departments のコントローラーとビューを作成した</span><span class="sxs-lookup"><span data-stu-id="6180f-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="6180f-293">Index ビューを更新した</span><span class="sxs-lookup"><span data-stu-id="6180f-293">Updated Index view</span></span>
> * <span data-ttu-id="6180f-294">Edit メソッドを更新した</span><span class="sxs-lookup"><span data-stu-id="6180f-294">Updated Edit methods</span></span>
> * <span data-ttu-id="6180f-295">Edit ビューを更新した</span><span class="sxs-lookup"><span data-stu-id="6180f-295">Updated Edit view</span></span>
> * <span data-ttu-id="6180f-296">コンカレンシーの競合をテストした</span><span class="sxs-lookup"><span data-stu-id="6180f-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="6180f-297">Delete ページを更新した</span><span class="sxs-lookup"><span data-stu-id="6180f-297">Updated the Delete page</span></span>
> * <span data-ttu-id="6180f-298">Details および Create ビューを更新した</span><span class="sxs-lookup"><span data-stu-id="6180f-298">Updated Details and Create views</span></span>

<span data-ttu-id="6180f-299">Instructor および Student エンティティの Table-Per-Hierarchy 継承を実装する方法について学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="6180f-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6180f-300">次へ: Table-Per-Hierarchy 継承を実装する</span><span class="sxs-lookup"><span data-stu-id="6180f-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
