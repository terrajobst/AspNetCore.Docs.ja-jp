---
title: ASP.NET Core でのコントローラーへの依存関係の挿入
author: ardalis
description: ASP.NET Core の MVC コントローラーが、ASP.NET Core でそれらのコンストラクターと依存関係の挿入を使用して、明示的にそれらの依存関係を要求する方法について説明します。
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 202b62d4b30c5c61c407abdc8509a2a75e181cb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652154"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>ASP.NET Core でのコントローラーへの依存関係の挿入

<a name="dependency-injection-controllers"></a>

作成者: [Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://github.com/ardalis)

ASP.NET Core の MVC コントローラーは、コンストラクターを使用して明示的に依存関係を要求します。 ASP.NET Core には、[依存関係の挿入 (DI)](xref:fundamentals/dependency-injection) の組み込みのサポートがあります。 DI を利用すれば、アプリのテストと保守管理が簡単になります。

[サンプル コードを表示またはダウンロード](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample)します ([ダウンロード方法](xref:index#how-to-download-a-sample))。

## <a name="constructor-injection"></a>コンストラクターの挿入

サービスはコンストラクター パラメーターとして追加されます。ランタイムによって、サービス コンテナーからサービスが解決されます。 サービスは通常、インターフェイスを利用して定義されます。 たとえば、現在の時刻を必要とするアプリについて考えてください。 次のインターフェイスは `IDateTime` サービスを公開します。

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

次のコードは `IDateTime` インターフェイスを実装します。

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

サービスをサービス コンテナーに追加します。

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> の詳細については、[DI のサービス有効期間](xref:fundamentals/dependency-injection#service-lifetimes)に関するページを参照してください。

次のコードでは、一日の時刻に基づいてユーザーにあいさつが表示されます。

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

アプリを実行すると、時刻に基づいてメッセージが表示されます。

## <a name="action-injection-with-fromservices"></a>FromServices によるアクションの挿入

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> を利用すると、コンストラクター挿入を利用しなくても、アクション メソッドがサービスに直接挿入されます。

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>コントローラーから設定にアクセスする

コントローラー内からアプリまたは構成の設定にアクセスするのが一般的なパターンです。 *で説明されている*オプション パターン<xref:fundamentals/configuration/options>が設定の管理手法として推奨されています。 通常はコントローラーに <xref:Microsoft.Extensions.Configuration.IConfiguration> を直接挿入しないでください。

オプションを表すクラスを作成します。 例 :

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

サービス コレクションに構成クラスを追加します。

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

JSON 形式ファイルから設定を読み込むようにアプリを構成します。

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

次のコードはサービス コンテナーから `IOptions<SampleWebSettings>` 設定を要求し、それを `Index` メソッドで使用します。

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>その他のリソース

* コントローラーで依存関係を明示的に要求することによってコードをテストしやすくする方法については、「<xref:mvc/controllers/testing>」を参照してください。

* [既定の依存関係挿入コンテナーをサードパーティの実装に置換します](xref:fundamentals/dependency-injection#default-service-container-replacement)。
