---
title: ASP.NET Core と Entity Framework 6 の概要
author: rick-anderson
description: この記事では、ASP.NET Core アプリケーションで Entity Framework 6 を使用する方法を示します。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78645428"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a>ASP.NET Core と Entity Framework 6 の概要

作成者: [Paweł Grudzień](https://github.com/pgrudzien12)、[Damien Pontifex](https://github.com/DamienPontifex)、[Tom Dykstra](https://github.com/tdykstra)

この記事では、ASP.NET Core アプリケーションで Entity Framework 6 を使用する方法を示します。

## <a name="overview"></a>概要

Entity Framework 6 は .NET Core をサポートしていないので、Entity Framework 6 を使用するには、プロジェクトが .NET Framework に対してコンパイルする必要があります。 クロスプラットフォーム機能が必要な場合は、[Entity Framework Core](/ef/) にアップグレードする必要があります。

ASP.NET Core アプリケーションで Entity Framework 6 を使用するための推奨方法は、EF6 コンテキストとモデル クラスを、.NET Framework を対象とするクラス ライブラリ プロジェクト内に配置することです。 ASP.NET Core プロジェクトから、クラス ライブラリに参照を追加します。 [EF6 と ASP.NET Core プロジェクトを使用した Visual Studio ソリューション](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)のサンプルを参照してください。

.NET Core プロジェクトは、*Enable-Migrations* などの EF6 コマンドが必要とするすべての機能をサポートしていないため、EF6 コンテキストを ASP.NET Core プロジェクトに配置することはできません。

EF6 コンテキストを検索するプロジェクトの種類に関係なく、EF6 コマンドライン ツールのみが EF6 コンテキストを使用します。 たとえば、`Scaffold-DbContext` は、Entity Framework Core でのみ使用できます。 データベースを EF6 モデルにリバース エンジニアリングする必要がある場合は、「[Entity Framework Code First to an Existing Database](https://msdn.microsoft.com/jj200620)」 (既存のデータベースに対する Entity Framework Code First) を参照してください。

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>ASP.NET Core プロジェクトで完全なフレームワークと EF6 を参照する

ASP.NET Core プロジェクトは、.NET Framework を対象とし、EF6 を参照する必要があります。 たとえば、ASP.NET Core プロジェクトの *.csproj* ファイルは次の例のようになります (ファイルの関連する部分のみが表示されています)。

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

新しいプロジェクトを作成する場合は、**ASP.NET Core Web アプリケーション (.NET Framework)** テンプレートを使用します。

## <a name="handle-connection-strings"></a>接続文字列を処理する

EF6 クラス ライブラリ プロジェクトで使用する EF6 コマンドライン ツールには、コンテキストをインスタンス化できるように、既定のコンストラクターが必要です。 しかし、ASP.NET Core プロジェクトで使用するために接続文字列を指定する場合は、コンテキスト コンストラクターは接続文字列で渡すことができるパラメーターを持つ必要があります。 次に例を示します。

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

EF6 コンテキストにはパラメーターなしのコンストラクターがないため、EF6 プロジェクトが [IDbContextFactory](https://msdn.microsoft.com/library/hh506876) の実装を提供する必要があります。 EF6 コマンドライン ツールはその実装を見つけて使用するため、コンテキストをインスタンス化することができます。 次に例を示します。

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

このサンプル コードでは、`IDbContextFactory` の実装がハード コーディングされた接続文字列で渡されます。 これがコマンドライン ツールで使用される接続文字列です。 クラス ライブラリで呼び出し元のアプリケーションが使用するのと同じ接続文字列が確実に使用されるようにする方法を実装したい場合があります。 たとえば、両方のプロジェクトで環境変数から値を取得する場合です。

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>ASP.NET Core プロジェクトに依存性の注入を設定する

Core プロジェクトの *Startup.cs* ファイルで、`ConfigureServices` に依存性を注入 (DI) するために EF6 コンテキストを設定します。 EF コンテキスト オブジェクトは、要求ごとの有効期間に限定する必要があります。

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

その後、DI を使用して、コントローラーでコンテキストのインスタンスを取得できます。 コードは、EF Core コンテキスト用に記述したものと似ています。

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a>サンプル アプリケーション

実際に動作するサンプル アプリケーションについては、この記事に付属している[Visual Studio のサンプル ソリューション](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)を参照してください。

このサンプルは、次の手順に従って、Visual Studio でゼロから作成することができます。

* ソリューションを作成します。

* **[追加]**  >  **[新しいプロジェクト]**  >  **[Web]**  >  **[ASP.NET Core Web アプリケーション]**
  * プロジェクト テンプレートの選択ダイアログで、ドロップダウンの [API] と [.NET Framework] を選択します。

* **[追加]**  >  **[新しいプロジェクト]**  >  **[Windows デスクトップ]**  >  **[クラス ライブラリ (.NET Framework)]**

* 両方のプロジェクトの**パッケージ マネージャー コンソール** (PMC) ウィンドウで、`Install-Package Entityframework` コマンドを実行します。

* クラス ライブラリ プロジェクトで、データ モデル クラスとコンテキスト クラス、および `IDbContextFactory` の実装を作成します。

* クラス ライブラリ プロジェクトの PMC で、コマンド `Enable-Migrations` と `Add-Migration Initial` を実行します。 ASP.NET Core プロジェクトをスタートアップ プロジェクトとして設定している場合は、`-StartupProjectName EF6` をこれらのコマンドに追加します。

* Core プロジェクトで、プロジェクト参照をクラス ライブラリ プロジェクトに追加します。

* Core プロジェクト内の *Startup.cs* で、DI のコンテキストを登録します。

* Core プロジェクト内の *appsettings.json* で、接続文字列を追加します。

* Core プロジェクトで、コントローラーとビューを追加してデータの読み書きができることを確認します (ASP.NET Core MVC のスキャフォールディングは、クラス ライブラリから参照される EF6 コンテキストでは機能しないことに注意してください)。

## <a name="summary"></a>まとめ

この記事では、ASP.NET Core アプリケーションで Entity Framework 6 を使用するための基本的なガイダンスを提供しました。

## <a name="additional-resources"></a>その他の技術情報

* [Entity Framework - コード ベースの構成](https://msdn.microsoft.com/data/jj680699.aspx)
