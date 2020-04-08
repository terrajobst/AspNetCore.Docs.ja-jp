---
title: ASP.NET Core での LoggerMessage による高パフォーマンスのログ記録
author: rick-anderson
description: 高パフォーマンスのログ記録シナリオにおいて、必要とするオブジェクト割り当ての数が少ない、キャッシュ可能なデリゲートを LoggerMessage を使用して作成する方法について説明します。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 48ebba69b5c15a0f9a42f7f6b3d2c1fcb0a2211c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649022"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="fd5ba-103">ASP.NET Core での LoggerMessage による高パフォーマンスのログ記録</span><span class="sxs-lookup"><span data-stu-id="fd5ba-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fd5ba-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> 機能では、[ や ](xref:Microsoft.Extensions.Logging.LoggerExtensions) のような<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>ロガー拡張メソッド<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>と比較して、必要なオブジェクト割り当ての数が少なくコンピューティング オーバーヘッドが小さいキャッシュ可能なデリゲートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="fd5ba-105">高パフォーマンスのログ記録シナリオの場合は、<xref:Microsoft.Extensions.Logging.LoggerMessage> パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="fd5ba-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> には、ロガー拡張メソッドに比べて次のようなパフォーマンス上の利点があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="fd5ba-107">ロガー拡張メソッドでは、`int` などの値の型を `object` に "ボックス化" (変換) する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="fd5ba-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> パターンでは、静的な <xref:System.Action> フィールドと、厳密に型指定されたパラメーターを持つ拡張メソッドを使用してボックス化を回避します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="fd5ba-109">ロガー拡張メソッドでは、ログ メッセージが書き込まれるたびにメッセージ テンプレート (名前付きの書式文字列) を解析する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="fd5ba-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> では、メッセージを定義するときに、一度テンプレートを解析する必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="fd5ba-111">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fd5ba-112">サンプル アプリでは、基本的な見積もり追跡システムで <xref:Microsoft.Extensions.Logging.LoggerMessage> 機能を実演します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="fd5ba-113">アプリでは、メモリ内のデータベースを使用して見積もりの追加および削除を行います。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="fd5ba-114">これらの操作が実行されると、<xref:Microsoft.Extensions.Logging.LoggerMessage> パターンによってログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="fd5ba-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="fd5ba-115">LoggerMessage.Define</span></span>

<span data-ttu-id="fd5ba-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) では、メッセージをログ記録するための <xref:System.Action> デリゲートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="fd5ba-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> オーバー ロードでは、名前付きの書式文字列 (テンプレート) に対して最大 6 個の型パラメーターを渡すことを許可します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="fd5ba-118"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> メソッドに指定された文字列はテンプレートであり、補間文字列ではありません。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="fd5ba-119">プレースホルダーは、型が指定された順に入力されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="fd5ba-120">テンプレート内のプレースホルダー名はわかりやすく、テンプレート間で一貫している必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="fd5ba-121">それらは構造化されたログ データ内でプロパティ名としての役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="fd5ba-122">プレースホルダー名には [Pascal 形式](/dotnet/standard/design-guidelines/capitalization-conventions)を推奨します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="fd5ba-123">たとえば、`{Count}`、`{FirstName}` のようになります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="fd5ba-124">各ログ メッセージは、<xref:System.Action>LoggerMessage.Define[ によって作成された静的フィールドに保持される ](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) です。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="fd5ba-125">たとえば、サンプル アプリでは、インデックス ページに対する GET 要求に関するログ メッセージを記述するフィールドを作成します (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="fd5ba-126"><xref:System.Action> の場合は、次を指定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="fd5ba-127">ログ レベル。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-127">The log level.</span></span>
* <span data-ttu-id="fd5ba-128">静的な拡張メソッドの名前を持つ一意のイベント識別子です (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="fd5ba-129">メッセージ テンプレート (名前付き書式文字列)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-129">The message template (named format string).</span></span> 

<span data-ttu-id="fd5ba-130">サンプル アプリのインデックス ページに対する要求では、次の設定を行います。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="fd5ba-131">ログ レベルを `Information` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-131">Log level to `Information`.</span></span>
* <span data-ttu-id="fd5ba-132">イベント ID を `1` メソッドの名前を含む `IndexPageRequested` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="fd5ba-133">メッセージ テンプレート (名前付き書式文字列) を文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="fd5ba-134">構造化されたログ ストアは、イベント ID によって指定されているとき、ログ記録を強化するために、イベント名を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="fd5ba-135">たとえば、[Serilog](https://github.com/serilog/serilog-extensions-logging) はイベント名を使用します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="fd5ba-136"><xref:System.Action> は厳密に型指定された拡張メソッドによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="fd5ba-137">`IndexPageRequested` メソッドは、サンプル アプリにおけるインデックス ページの GET 要求に関するメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="fd5ba-138">`IndexPageRequested` は、`OnGetAsync`Pages/Index.cshtml.cs*に含まれる* メソッドのロガー上で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="fd5ba-139">アプリのコンソール出力を検査する:</span><span class="sxs-lookup"><span data-stu-id="fd5ba-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="fd5ba-140">ログ メッセージにパラメーターを渡すには、静的フィールドを作成するときに最大 6 個の型を定義します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="fd5ba-141">サンプル アプリでは、`string` フィールドに対して <xref:System.Action> 型を定義して見積もりを追加すると、文字列が記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="fd5ba-142">デリゲートのログ メッセージ テンプレートは、そのプレースホルダー値を指定された型で受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="fd5ba-143">サンプル アプリでは、見積もりを追加するためのデリゲートを定義します。ここで見積もりパラメーターは `string` となります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="fd5ba-144">見積もりを追加するための静的な拡張メソッドである `QuoteAdded` は、見積もり引数の値を受け取って、<xref:System.Action> デリゲートに渡します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="fd5ba-145">インデックス ページのページ モデル (*Pages/Index.cshtml.cs*) では、メッセージを記録するために `QuoteAdded` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="fd5ba-146">アプリのコンソール出力を検査する:</span><span class="sxs-lookup"><span data-stu-id="fd5ba-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="fd5ba-147">サンプル アプリでは、見積もり削除用の [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) パターンを実装します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-147">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="fd5ba-148">削除操作に成功した場合、情報メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="fd5ba-149">例外がスローされた場合は、削除操作に対してエラー メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="fd5ba-150">削除操作が失敗した場合のログ メッセージには、例外スタック トレースが含まれます (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="fd5ba-151">`QuoteDeleteFailed` 内のデリゲートに例外が渡される方法に注目してください。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="fd5ba-152">インデックス ページのページ モデルでは、見積もりの削除に成功すると、ロガー上で `QuoteDeleted` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="fd5ba-153">削除対象の見積もりが見つからない場合は、<xref:System.ArgumentNullException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="fd5ba-154">例外は [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントによってトラップされ、`QuoteDeleteFailed`catch[ ブロック (](/dotnet/csharp/language-reference/keywords/try-catch)Pages/Index.cshtml.cs *) 内のロガーで*  メソッドを呼び出すことにより記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-154">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="fd5ba-155">見積もりが正常に削除されたら、アプリのコンソール出力を検査します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="fd5ba-156">見積もりの削除が失敗したら、アプリのコンソール出力を検査します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="fd5ba-157">例外はログ メッセージに含められます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-157">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="fd5ba-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="fd5ba-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="fd5ba-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) は、<xref:System.Func%601>ログ スコープ[を定義するための ](xref:fundamentals/logging/index#log-scopes) デリゲートを作成します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="fd5ba-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> オーバー ロードでは、名前付きの書式文字列 (テンプレート) に対して最大 3 個の型パラメーターを渡すことを許可します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="fd5ba-161"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> メソッドの場合と同様に、<xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> メソッドに指定された文字列はテンプレートであり、補間文字列ではありません。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="fd5ba-162">プレースホルダーは、型が指定された順に入力されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="fd5ba-163">テンプレート内のプレースホルダー名はわかりやすく、テンプレート間で一貫している必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="fd5ba-164">それらは構造化されたログ データ内でプロパティ名としての役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="fd5ba-165">プレースホルダー名には [Pascal 形式](/dotnet/standard/design-guidelines/capitalization-conventions)を推奨します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="fd5ba-166">たとえば、`{Count}`、`{FirstName}` のようになります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="fd5ba-167">[ メソッドを使用して、一連のログ メッセージに適用する](xref:fundamentals/logging/index#log-scopes)ログ スコープ<xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>を定義します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="fd5ba-168">サンプル アプリには、データベース内のすべての見積もりを削除するための **[すべてクリア]** ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="fd5ba-169">見積もりを削除するには、一度に 1 つ削除します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="fd5ba-170">見積もりを削除するたびに、ロガーで `QuoteDeleted` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="fd5ba-171">ログ スコープは、これらのログ メッセージに追加されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="fd5ba-172">`IncludeScopes`appsettings.json *のコンソール ロガーのセクションで、* を有効にします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="fd5ba-173">ログ スコープを作成するには、スコープに対して <xref:System.Func%601> デリゲートを保持するフィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="fd5ba-174">サンプル アプリでは、`_allQuotesDeletedScope` と呼ばれるフィールドを作成します (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="fd5ba-175"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> を使用してデリゲートを作成します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="fd5ba-176">デリゲートを呼び出すとき、テンプレート引数として使用するように最大 3 つの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="fd5ba-177">サンプル アプリでは、削除された見積もりの数 (`int` 型) を含むメッセージ テンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="fd5ba-178">ログ メッセージ用の静的な拡張メソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="fd5ba-179">メッセージ テンプレートに表示される名前付きプロパティの任意の型パラメーターを含めます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="fd5ba-180">サンプル アプリでは、削除する見積もりの `count` を取得し、`_allQuotesDeletedScope` を返します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="fd5ba-181">スコープは、[using](/dotnet/csharp/language-reference/keywords/using-statement) ブロックでログ拡張呼び出しをラップします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="fd5ba-182">アプリのコンソール出力のログ メッセージを検査します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="fd5ba-183">次の結果には、削除された 3 つの見積もりに加えて、ログ スコープ メッセージが表示されています。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-183">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fd5ba-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> 機能では、[ や ](xref:Microsoft.Extensions.Logging.LoggerExtensions) のような<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>ロガー拡張メソッド<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>と比較して、必要なオブジェクト割り当ての数が少なくコンピューティング オーバーヘッドが小さいキャッシュ可能なデリゲートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="fd5ba-185">高パフォーマンスのログ記録シナリオの場合は、<xref:Microsoft.Extensions.Logging.LoggerMessage> パターンを使用します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="fd5ba-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> には、ロガー拡張メソッドに比べて次のようなパフォーマンス上の利点があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="fd5ba-187">ロガー拡張メソッドでは、`int` などの値の型を `object` に "ボックス化" (変換) する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="fd5ba-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> パターンでは、静的な <xref:System.Action> フィールドと、厳密に型指定されたパラメーターを持つ拡張メソッドを使用してボックス化を回避します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="fd5ba-189">ロガー拡張メソッドでは、ログ メッセージが書き込まれるたびにメッセージ テンプレート (名前付きの書式文字列) を解析する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="fd5ba-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> では、メッセージを定義するときに、一度テンプレートを解析する必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="fd5ba-191">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fd5ba-192">サンプル アプリでは、基本的な見積もり追跡システムで <xref:Microsoft.Extensions.Logging.LoggerMessage> 機能を実演します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="fd5ba-193">アプリでは、メモリ内のデータベースを使用して見積もりの追加および削除を行います。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="fd5ba-194">これらの操作が実行されると、<xref:Microsoft.Extensions.Logging.LoggerMessage> パターンによってログ メッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="fd5ba-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="fd5ba-195">LoggerMessage.Define</span></span>

<span data-ttu-id="fd5ba-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) では、メッセージをログ記録するための <xref:System.Action> デリゲートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="fd5ba-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> オーバー ロードでは、名前付きの書式文字列 (テンプレート) に対して最大 6 個の型パラメーターを渡すことを許可します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="fd5ba-198"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> メソッドに指定された文字列はテンプレートであり、補間文字列ではありません。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="fd5ba-199">プレースホルダーは、型が指定された順に入力されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="fd5ba-200">テンプレート内のプレースホルダー名はわかりやすく、テンプレート間で一貫している必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="fd5ba-201">それらは構造化されたログ データ内でプロパティ名としての役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="fd5ba-202">プレースホルダー名には [Pascal 形式](/dotnet/standard/design-guidelines/capitalization-conventions)を推奨します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="fd5ba-203">たとえば、`{Count}`、`{FirstName}` のようになります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="fd5ba-204">各ログ メッセージは、<xref:System.Action>LoggerMessage.Define[ によって作成された静的フィールドに保持される ](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) です。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="fd5ba-205">たとえば、サンプル アプリでは、インデックス ページに対する GET 要求に関するログ メッセージを記述するフィールドを作成します (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="fd5ba-206"><xref:System.Action> の場合は、次を指定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="fd5ba-207">ログ レベル。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-207">The log level.</span></span>
* <span data-ttu-id="fd5ba-208">静的な拡張メソッドの名前を持つ一意のイベント識別子です (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="fd5ba-209">メッセージ テンプレート (名前付き書式文字列)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-209">The message template (named format string).</span></span> 

<span data-ttu-id="fd5ba-210">サンプル アプリのインデックス ページに対する要求では、次の設定を行います。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="fd5ba-211">ログ レベルを `Information` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-211">Log level to `Information`.</span></span>
* <span data-ttu-id="fd5ba-212">イベント ID を `1` メソッドの名前を含む `IndexPageRequested` に設定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="fd5ba-213">メッセージ テンプレート (名前付き書式文字列) を文字列に設定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="fd5ba-214">構造化されたログ ストアは、イベント ID によって指定されているとき、ログ記録を強化するために、イベント名を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="fd5ba-215">たとえば、[Serilog](https://github.com/serilog/serilog-extensions-logging) はイベント名を使用します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="fd5ba-216"><xref:System.Action> は厳密に型指定された拡張メソッドによって呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="fd5ba-217">`IndexPageRequested` メソッドは、サンプル アプリにおけるインデックス ページの GET 要求に関するメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="fd5ba-218">`IndexPageRequested` は、`OnGetAsync`Pages/Index.cshtml.cs*に含まれる* メソッドのロガー上で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="fd5ba-219">アプリのコンソール出力を検査する:</span><span class="sxs-lookup"><span data-stu-id="fd5ba-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="fd5ba-220">ログ メッセージにパラメーターを渡すには、静的フィールドを作成するときに最大 6 個の型を定義します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="fd5ba-221">サンプル アプリでは、`string` フィールドに対して <xref:System.Action> 型を定義して見積もりを追加すると、文字列が記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="fd5ba-222">デリゲートのログ メッセージ テンプレートは、そのプレースホルダー値を指定された型で受け取ります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="fd5ba-223">サンプル アプリでは、見積もりを追加するためのデリゲートを定義します。ここで見積もりパラメーターは `string` となります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="fd5ba-224">見積もりを追加するための静的な拡張メソッドである `QuoteAdded` は、見積もり引数の値を受け取って、<xref:System.Action> デリゲートに渡します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="fd5ba-225">インデックス ページのページ モデル (*Pages/Index.cshtml.cs*) では、メッセージを記録するために `QuoteAdded` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="fd5ba-226">アプリのコンソール出力を検査する:</span><span class="sxs-lookup"><span data-stu-id="fd5ba-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="fd5ba-227">サンプル アプリでは、見積もり削除用の [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) パターンを実装します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-227">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="fd5ba-228">削除操作に成功した場合、情報メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="fd5ba-229">例外がスローされた場合は、削除操作に対してエラー メッセージが記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="fd5ba-230">削除操作が失敗した場合のログ メッセージには、例外スタック トレースが含まれます (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="fd5ba-231">`QuoteDeleteFailed` 内のデリゲートに例外が渡される方法に注目してください。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="fd5ba-232">インデックス ページのページ モデルでは、見積もりの削除に成功すると、ロガー上で `QuoteDeleted` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="fd5ba-233">削除対象の見積もりが見つからない場合は、<xref:System.ArgumentNullException> がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="fd5ba-234">例外は [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) ステートメントによってトラップされ、`QuoteDeleteFailed`catch[ ブロック (](/dotnet/csharp/language-reference/keywords/try-catch)Pages/Index.cshtml.cs *) 内のロガーで*  メソッドを呼び出すことにより記録されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-234">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="fd5ba-235">見積もりが正常に削除されたら、アプリのコンソール出力を検査します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="fd5ba-236">見積もりの削除が失敗したら、アプリのコンソール出力を検査します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="fd5ba-237">例外はログ メッセージに含められます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-237">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="fd5ba-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="fd5ba-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="fd5ba-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) は、<xref:System.Func%601>ログ スコープ[を定義するための ](xref:fundamentals/logging/index#log-scopes) デリゲートを作成します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="fd5ba-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> オーバー ロードでは、名前付きの書式文字列 (テンプレート) に対して最大 3 個の型パラメーターを渡すことを許可します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="fd5ba-241"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> メソッドの場合と同様に、<xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> メソッドに指定された文字列はテンプレートであり、補間文字列ではありません。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="fd5ba-242">プレースホルダーは、型が指定された順に入力されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="fd5ba-243">テンプレート内のプレースホルダー名はわかりやすく、テンプレート間で一貫している必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="fd5ba-244">それらは構造化されたログ データ内でプロパティ名としての役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="fd5ba-245">プレースホルダー名には [Pascal 形式](/dotnet/standard/design-guidelines/capitalization-conventions)を推奨します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="fd5ba-246">たとえば、`{Count}`、`{FirstName}` のようになります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="fd5ba-247">[ メソッドを使用して、一連のログ メッセージに適用する](xref:fundamentals/logging/index#log-scopes)ログ スコープ<xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>を定義します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="fd5ba-248">サンプル アプリには、データベース内のすべての見積もりを削除するための **[すべてクリア]** ボタンがあります。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="fd5ba-249">見積もりを削除するには、一度に 1 つ削除します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="fd5ba-250">見積もりを削除するたびに、ロガーで `QuoteDeleted` メソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="fd5ba-251">ログ スコープは、これらのログ メッセージに追加されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="fd5ba-252">`IncludeScopes`appsettings.json *のコンソール ロガーのセクションで、* を有効にします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="fd5ba-253">ログ スコープを作成するには、スコープに対して <xref:System.Func%601> デリゲートを保持するフィールドを追加します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="fd5ba-254">サンプル アプリでは、`_allQuotesDeletedScope` と呼ばれるフィールドを作成します (*Internal/LoggerExtensions.cs*)。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="fd5ba-255"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> を使用してデリゲートを作成します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="fd5ba-256">デリゲートを呼び出すとき、テンプレート引数として使用するように最大 3 つの型を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="fd5ba-257">サンプル アプリでは、削除された見積もりの数 (`int` 型) を含むメッセージ テンプレートが使用されます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="fd5ba-258">ログ メッセージ用の静的な拡張メソッドを指定します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="fd5ba-259">メッセージ テンプレートに表示される名前付きプロパティの任意の型パラメーターを含めます。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="fd5ba-260">サンプル アプリでは、削除する見積もりの `count` を取得し、`_allQuotesDeletedScope` を返します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="fd5ba-261">スコープは、[using](/dotnet/csharp/language-reference/keywords/using-statement) ブロックでログ拡張呼び出しをラップします。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="fd5ba-262">アプリのコンソール出力のログ メッセージを検査します。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="fd5ba-263">次の結果には、削除された 3 つの見積もりに加えて、ログ スコープ メッセージが表示されています。</span><span class="sxs-lookup"><span data-stu-id="fd5ba-263">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fd5ba-264">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="fd5ba-264">Additional resources</span></span>

* [<span data-ttu-id="fd5ba-265">ログ</span><span class="sxs-lookup"><span data-stu-id="fd5ba-265">Logging</span></span>](xref:fundamentals/logging/index)
