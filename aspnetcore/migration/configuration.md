---
title: 構成を ASP.NET Core に移行する
author: ardalis
description: ASP.NET MVC プロジェクトから ASP.NET Core MVC プロジェクトに構成を移行する方法について説明します。
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: 2c50ea768a42aa38d14c55d8c403fea4176b3650
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651884"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="871bf-103">構成を ASP.NET Core に移行する</span><span class="sxs-lookup"><span data-stu-id="871bf-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="871bf-104">作成者: [Steve Smith](https://ardalis.com/)、[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="871bf-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="871bf-105">前の記事では、 [ASP.NET mvc プロジェクトの ASP.NET CORE mvc への移行](xref:migration/mvc)を開始しました。</span><span class="sxs-lookup"><span data-stu-id="871bf-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="871bf-106">この記事では、構成を移行します。</span><span class="sxs-lookup"><span data-stu-id="871bf-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="871bf-107">[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="871bf-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="871bf-108">セットアップの構成</span><span class="sxs-lookup"><span data-stu-id="871bf-108">Setup configuration</span></span>

<span data-ttu-id="871bf-109">ASP.NET Core は、以前のバージョンの ASP.NET が使用されていた*global.asax* *および web.config ファイルを使用*しなくなりました。</span><span class="sxs-lookup"><span data-stu-id="871bf-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="871bf-110">以前のバージョンの ASP.NET では、アプリケーションのスタートアップロジックは*global.asax*内の `Application_StartUp` メソッドに配置されました。</span><span class="sxs-lookup"><span data-stu-id="871bf-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="871bf-111">その後、ASP.NET MVC では、 *Startup.cs*ファイルがプロジェクトのルートに含まれていました。また、アプリケーションの起動時に呼び出されました。</span><span class="sxs-lookup"><span data-stu-id="871bf-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="871bf-112">ASP.NET Core は、すべてのスタートアップロジックを*Startup.cs*ファイルに配置することによって、この方法を完全に採用しています。</span><span class="sxs-lookup"><span data-stu-id="871bf-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="871bf-113">Web.config*ファイルも*ASP.NET Core で置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="871bf-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="871bf-114">*Startup.cs*で説明されているアプリケーションのスタートアップ手順の一部として、構成自体を構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="871bf-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="871bf-115">構成でも XML ファイルを利用できますが、通常は ASP.NET Core プロジェクトは、構成値を JSON 形式のファイル (たとえば、 *appsettings*) に配置します。</span><span class="sxs-lookup"><span data-stu-id="871bf-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="871bf-116">ASP.NET Core の構成システムは、環境変数に簡単にアクセスすることもできます。これにより、環境固有の値に対して[より安全かつ堅牢な場所](xref:security/app-secrets)を提供できます。</span><span class="sxs-lookup"><span data-stu-id="871bf-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="871bf-117">これは、ソース管理にチェックインしない接続文字列や API キーなどのシークレットに特に当てはまります。</span><span class="sxs-lookup"><span data-stu-id="871bf-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="871bf-118">ASP.NET Core の構成の詳細については、「[構成](xref:fundamentals/configuration/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="871bf-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="871bf-119">この記事では、[前の記事](xref:migration/mvc)の部分的に移行された ASP.NET Core プロジェクトから始めます。</span><span class="sxs-lookup"><span data-stu-id="871bf-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="871bf-120">構成を設定するには、次のコンストラクターとプロパティをプロジェクトのルートにある*Startup.cs*ファイルに追加します。</span><span class="sxs-lookup"><span data-stu-id="871bf-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="871bf-121">この時点で、 *Startup.cs*ファイルはコンパイルされません。ただし、次の `using` ステートメントを追加する必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="871bf-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="871bf-122">適切な項目テンプレートを使用して、プロジェクトのルートに*appsettings*ファイルを追加します。</span><span class="sxs-lookup"><span data-stu-id="871bf-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![AppSettings JSON の追加](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="871bf-124">Web.config から構成設定を移行する</span><span class="sxs-lookup"><span data-stu-id="871bf-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="871bf-125">ASP.NET MVC プロジェクトでは、必要なデータベース接続文字列が*web.config の `<connectionStrings>`* 要素に含まれていました。</span><span class="sxs-lookup"><span data-stu-id="871bf-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="871bf-126">この ASP.NET Core プロジェクトでは、この情報を*appsettings*ファイルに格納します。</span><span class="sxs-lookup"><span data-stu-id="871bf-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="871bf-127">*Appsettings*を開き、次のものが既に含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="871bf-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="871bf-128">上の強調表示された行で、データベースの名前を **_CHANGE_ME**からデータベースの名前に変更します。</span><span class="sxs-lookup"><span data-stu-id="871bf-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="871bf-129">要約</span><span class="sxs-lookup"><span data-stu-id="871bf-129">Summary</span></span>

<span data-ttu-id="871bf-130">ASP.NET Core によって、アプリケーションのすべてのスタートアップロジックが1つのファイルに配置されます。このファイルで、必要なサービスと依存関係を定義して構成することができます。</span><span class="sxs-lookup"><span data-stu-id="871bf-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="871bf-131">*Web.config ファイルは*、JSON などのさまざまなファイル形式や環境変数を利用できる柔軟な構成機能に置き換えられています。</span><span class="sxs-lookup"><span data-stu-id="871bf-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
