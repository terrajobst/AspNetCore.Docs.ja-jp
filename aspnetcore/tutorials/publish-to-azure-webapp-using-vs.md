---
title: Visual Studio を使用して Azure に ASP.NET Core アプリを発行する
author: rick-anderson
description: Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する方法を説明します。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "78648494"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Visual Studio を使用して Azure に ASP.NET Core アプリを発行する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


macOS で作業している場合は、「[Visual Studio for Mac を使用して Azure App Service に Web アプリを発行する](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019)」を参照してください。

App Service の配置に関する問題を解決するには、「<xref:test/troubleshoot-azure-iis>」を参照してください。

## <a name="set-up"></a>設定

* Azure アカウントをお持ちでない場合は、[Azure 無料アカウント](https://azure.microsoft.com/free/dotnet/)を作成します。 

## <a name="create-a-web-app"></a>Web アプリの作成

Visual Studio のスタート ページで、 **[ファイル]、[新規作成]、[プロジェクト]** の順に選択します。

![[ファイル] メニュー](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

**[新しいプロジェクト]** ダイアログで次のように設定します。

* 左側のウィンドウで、 **[.NET Core]** を選択します。
* 中央のウィンドウで、 **[ASP.NET Core Web Application]** を選択します。
* **[OK]** を選択します。

![[新しいプロジェクト] ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj.png)

**[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。

* **[Web アプリケーション]** を選択します。
* **[認証の変更]** を選択します。

![[新しいプロジェクト] ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**[認証の変更]** ダイアログが表示されます。 

* **[個人のユーザー アカウント]** を選択します。
* **[OK]** を選択して **[新しい ASP.NET Core Web アプリケーション]** に戻り、もう一度 **[OK]** を選択します。

![新しい ASP.NET Core Web 認証ダイアログ](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio によってソリューションが作成されます。

## <a name="run-the-app"></a>アプリを実行する

* Ctrl キーを押しながら F5 キーを押してプロジェクトを実行します。
* **[About]** リンクと **[Contact]** リンクをテストします。

![Microsoft Edge で開いているローカルホストの Web アプリケーション](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>ユーザーを登録する

* **[登録]** を選択して、新しいユーザーを登録します。 架空の電子メール アドレスを使用できます。 送信すると、ページに次のエラーが表示されます。

    *"内部サーバー エラー: 要求の処理中にデータベースの操作に失敗しました。SQL 例外: データベースを開けません。Applying existing migrations for Application DB context may resolve this issue. /(アプリケーション DB コンテキストの既存の移行を適用すると問題が解決する場合があります。/)*
* **[Apply Migrations]/(移行を適用する/)** を選択し、移行が完了したら、ページを更新します。

![内部サーバー エラー: 要求の処理中にデータベースの操作に失敗しました。 SQL 例外: データベースを開けません。 Applying existing migrations for Application DB context may resolve this issue. /(アプリケーション DB コンテキスト用の既存の以降を適用すると問題が解決する場合があります。/)](publish-to-azure-webapp-using-vs/_static/mig.png)

アプリには、新しいユーザーの登録に使用した電子メールと **[ログアウト]** リンクが表示されます。

![Microsoft Edge で開いている Web アプリケーション。 [Register]/(登録/) リンクは Hello email@domain.com! というテキストで置き換えられます。](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Azure にアプリを配置する

ソリューション エクスプローラーでプロジェクトを右クリックし、 **[発行]** を選択します。

![[発行] リンクが選択された状態でコンテキスト メニューが開きます](publish-to-azure-webapp-using-vs/_static/pub.png)

**[発行]** ダイアログで、次の操作を行います。

* **[Microsoft Azure App Service]** を選択します。
* 歯車アイコンを選択してから **[プロファイルの作成]** を選択します。
* **[プロファイルの作成]** を選択します。

![[発行] ダイアログ](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Azure リソースを作成する

**[App Service の作成]** ダイアログが表示されます。

* ご自分のサブスクリプションを入力します。
* **[アプリ名]** 、 **[リソース グループ]** 、 **[App Service プラン]** の各入力フィールドに値が設定されます。 これらの名前を保持することも、変更することもできます。

![[App Service] ダイアログ](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* **[サービス]** タブを選択して、新しいデータベースを作成します。

* 緑色の **+** アイコンを選択して新しい SQL Database を作成します。

![新しい SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* **[SQL Database の構成]** ダイアログの **[新規作成]** を選択して新しいデータベースを作成します。

![新しい SQL Database とサーバー](publish-to-azure-webapp-using-vs/_static/conf.png)

**[SQL Server の構成]** ダイアログが表示されます。

* 管理者のユーザー名とパスワードを入力し、 **[OK]** を選択します。 既定の **[サーバー名]** をそのまま使用できます。 

> [!NOTE]
> 管理者のユーザー名に "admin" は使用できません。

![[SQL Server の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* **[OK]** を選択します。

Visual Studio が **[App Service の作成]** ダイアログに戻ります。

* **[App Service の作成]** ダイアログで **[作成]** を選択します。

![[SQL Database の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio は、Azure で Web アプリと SQL Server を作成します。 このステップには数分かかる場合があります。 作成されるリソースについては、「[追加のリソース](#additional-resources)」を参照してください。

配置が完了したら、 **[設定]** を選択します。

![[SQL Server の構成] ダイアログ](publish-to-azure-webapp-using-vs/_static/set.png)

**[発行]** ダイアログの **[設定]** ページで次の手順を実行します。

* **[データベース]** を展開し、 **[この接続文字列を実行時に使用する]** をオンにします。
* **[Entity Framework の移行]** を展開し、 **[発行時にこの移行を適用する]** をオンにします。

* **[保存]** を選択します。 Visual Studio が **[発行]** ダイアログに戻ります。 

![[発行] ダイアログ:[設定] パネル](publish-to-azure-webapp-using-vs/_static/pubs.png)

**[発行]** をクリックします。 Visual Studio が Azure にアプリを発行します。 デプロイが完了すると、ブラウザーでアプリが開きます。

### <a name="test-your-app-in-azure"></a>Azure でアプリをテストする

* **[About]** リンクと **[Contact]** リンクをテストします

* 新しいユーザーを登録します

![Microsoft Edge で開かれている Azure App Service の Web アプリケーション](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>アプリを更新する

* *Pages/About.cshtml* Razor ページを編集して、その内容を変更します。 たとえば、"Hello ASP.NET Core!" と表示されるように段落を修正できます。

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* プロジェクトを右クリックし、 **[発行]** をもう一度選択します。

![[発行] リンクが選択された状態でコンテキスト メニューが開きます](publish-to-azure-webapp-using-vs/_static/pub.png)

* アプリが発行されたら、Azure に変更内容が反映されていることを確認します。

![タスクの完了を確認します。](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>クリーンアップ

アプリのテストが完了したら、[Azure Portal](https://portal.azure.com/) に移動し、アプリを削除します。

* **[リソース グループ]** を選択し、作成したリソース グループを選択します。

![Azure Portal: サイドバー メニューの [リソース グループ]](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* **[リソース グループ]** ページで、 **[削除]** を選択します。

![Azure Portal:[リソース グループ] ページ](publish-to-azure-webapp-using-vs/_static/rgd.png)

* リソース グループ名を入力し、 **[削除]** を選択します。 このチュートリアルで作成されたアプリとその他すべてのリソースが Azure から削除されます。

### <a name="next-steps"></a>次の手順

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>その他の技術情報

* Visual Studio Code については、「[発行プロファイル](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)」をご覧ください。
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Azure リソース グループ](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
