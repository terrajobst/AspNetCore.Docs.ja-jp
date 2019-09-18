---
title: App Service - ASP.NET Core および Azure を使用した DevOps へアプリをデプロイします。
author: CamSoper
description: ASP.NET Core アプリのデプロイを Azure App Service に ASP.NET Core および Azure を使用した DevOps の最初の手順。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080436"
---
# <a name="deploy-an-app-to-app-service"></a>App Service にアプリをデプロイします。

[Azure App Service](/azure/app-service/)は Azure の web ホスティング プラットフォーム。 手動または自動のプロセスによって、web アプリを Azure App Service にデプロイを実行できます。 このガイドのこのセクションでは、手動か、コマンドラインを使用してスクリプトをトリガーできるまたは Visual Studio を使用して手動でトリガーされた配置方法について説明します。

このセクションでは、次のタスクを実行します。

* ダウンロードして、サンプル アプリをビルドします。
* Azure Cloud Shell を使用して Azure App Service Web アプリを作成します。
* Git を使用して Azure にサンプル アプリをデプロイします。
* Visual Studio を使用してアプリに変更をデプロイします。
* Web アプリには、ステージング スロットを追加します。
* ステージング スロットに更新プログラムを展開します。
* ステージングと運用スロットをスワップします。

## <a name="download-and-test-the-app"></a>ダウンロードして、アプリをテストします。

このガイドで使用されるアプリは、構築済みの ASP.NET Core アプリ[単純なフィード リーダー](https://github.com/Azure-Samples/simple-feed-reader/)します。 使用する Razor ページ アプリは、 `Microsoft.SyndicationFeed.ReaderWriter` API を RSS フィードおよび Atom フィードを取得し、ニュース項目を一覧表示します。

自由に、コードのレビューがこのアプリに関する特別なものを理解することが重要です。 説明の目的で、簡単な ASP.NET Core アプリだけです。

コマンド シェルからコードをダウンロードしてプロジェクトをビルドして、次のように実行します。

> *注:Linux/macOS ユーザーは、パスに対して適切な変更を行う必要があり`/`ます。たとえば、スラッシュ (`\`) ではなくスラッシュ () を使用します。*

1. ローカル コンピューター上のフォルダーにコードを複製します。

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. 作業フォルダーの変更、*単純なフィード-リーダー*が作成されたフォルダーです。

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. パッケージを復元し、ソリューションをビルドします。

    ```dotnetcli
    dotnet build
    ```

4. アプリを実行します。

    ```dotnetcli
    dotnet run
    ```

    ![Dotnet run コマンドが正常に完了](./media/deploying-to-app-service/dotnet-run.png)

5. ブラウザーを開きに移動します`http://localhost:5000`します。 アプリでは、入力するか、配信フィードの URL を貼り付けることができ、ニュース アイテムの一覧を表示します。

     ![RSS フィードの内容を表示するアプリ](./media/deploying-to-app-service/app-in-browser.png)

6. アプリが正常に動作がなければ、キーを押してシャット ダウン**Ctrl**+**C**コマンド シェルでします。

## <a name="create-the-azure-app-service-web-app"></a>Azure App Service Web アプリを作成します。

アプリを展開するには、アプリ サービスを作成する必要があります[Web アプリ](/azure/app-service/app-service-web-overview)します。 Web アプリの作成後は、Git を使用して、ローカル コンピューターからをデプロイします。

1. サインイン、 [Azure Cloud Shell](https://shell.azure.com/bash)します。 メモ:初めてサインインするときに、構成ファイル用のストレージアカウントを作成するように Cloud Shell プロンプトが表示されます。 既定値を受け入れるか、一意の名前を指定します。

2. 次の手順については、Cloud Shell を使用します。

    a. Web アプリの名前を格納する変数を宣言します。 名前は、既定の URL で使用される一意である必要があります。 使用して、`$RANDOM`名前を作成する Bash 関数は、一意性を保証しの形式で結果`webappname99999`します。

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. リソース グループを作成します。 リソース グループは、グループとして管理する Azure リソースを集計するための手段を提供します。

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    `az`コマンドを呼び出す、 [Azure CLI](/cli/azure/)します。 CLI をローカルで実行することができますが、Cloud Shell で使用する時間と構成を保存します。

    c. S1 レベルでは、App Service プランを作成します。 App Service プランは、同じ価格レベルを共有する web アプリのグループです。 S1 レベルが無料ではありませんが、ステージング スロットの機能は必要です。

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. 同じリソース グループで、App Service プランを使用して web アプリのリソースを作成します。

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. デプロイ資格情報を設定します。 これらのデプロイ資格情報は、サブスクリプション内のすべての web アプリに適用されます。 ユーザー名に特殊文字を使用しないでください。

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. ローカルの Git および表示から展開を受け入れるように web アプリの構成、 *Git デプロイメント URL*します。 **後で参照に対して、この URL に注意してください**します。

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. 表示、 *web アプリの URL*します。 空の web アプリを確認するには、この URL に移動します。 **後で参照に対して、この URL に注意してください**します。

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. コマンド シェルを使用して、ローカル コンピューターで web アプリのプロジェクト フォルダーに移動します (たとえば、 `.\simple-feed-reader\SimpleFeedReader`)。 デプロイの URL にプッシュする Git のセットアップには、次のコマンドを実行します。

    a. ローカル リポジトリには、リモートの URL を追加します。

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. ローカルをプッシュ*マスター*に分岐、 *azure prod*リモートの*マスター*分岐します。

    ```console
    git push azure-prod master
    ```

    先ほど作成したデプロイ資格情報を求め。 コマンド シェルに出力を確認します。 Azure では、ASP.NET Core アプリをリモートでビルドします。

4. ブラウザーに移動します。、 *Web アプリの URL*と、アプリがビルドおよびデプロイされたに注意してください。 追加の変更がローカル Git リポジトリにコミットできる`git commit`します。 これらの変更は、上記の Azure にプッシュされる`git push`コマンド。

## <a name="deployment-with-visual-studio"></a>Visual Studio でのデプロイ

> *注:このセクションは、Windows のみに適用されます。Linux および macOS ユーザーには、次のステップ 2 で説明されている変更を加える必要があります。ファイルを保存しをローカル リポジトリに変更をコミット`git commit`します。最後に、変更をプッシュ`git push`最初のセクションのようにします。*

コマンド シェルから、アプリは既に展開されています。 Visual Studio の統合ツールを使用して、アプリに更新プログラムをデプロイしましょう。 バック グラウンドでは、Visual Studio には、Visual Studio の使い慣れた UI 内でコマンド ライン ツールと同じことが実現されます。

1. 開いている*SimpleFeedReader.sln* Visual Studio でします。
2. ソリューション エクスプ ローラーで開く*Pages\Index.cshtml*します。 変更`<h2>Simple Feed Reader</h2>`に`<h2>Simple Feed Reader - V2</h2>`します。
3. キーを押して**Ctrl**+**Shift**+**B**アプリをビルドします。
4. ソリューション エクスプ ローラーでプロジェクトを右クリックし、をクリックして**発行**します。

    ![右クリックし、発行を示すスクリーン ショット](./media/deploying-to-app-service/publish.png)
5. Visual Studio は、新しい App Service リソースを作成できますが、この更新プログラムは、既存のデプロイで公開されます。 **発行先を選択**ダイアログ ボックスで、 **App Service** 、左側のリストから選び**既存の**。 **[発行]** をクリックします。
6. **App Service**ダイアログ ボックスで、Microsoft または組織アカウントを Azure サブスクリプションを作成するために使用が右上に表示されることを確認します。 ない場合、ドロップダウン リストをクリックし、追加します。
7. いることを確認、適切な Azure**サブスクリプション**が選択されています。 **ビュー**、**リソース グループ**します。 展開、 **azure チュートリアル**リソース グループと、既存の web アプリを選択します。 **[OK]** をクリックします。

    ![App Service の発行 ダイアログのスクリーン ショット](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio がビルドされ、アプリを Azure にデプロイします。 Web アプリの URL に移動します。 いることを確認、`<h2>`要素の変更がライブです。

![変更されたタイトルとアプリ](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>デプロイ スロット

デプロイ スロットでは、実稼働環境で実行されているアプリに影響を与えることがなく変更のステージングをサポートします。 ステージングされたバージョンのアプリの品質保証チームによって検証されると、運用スロットとステージング スロットをスワップすることができます。 ステージング環境でのアプリは、この方法で運用環境に昇格されます。 次の手順は、ステージング スロットを作成、いくつかの変更に配置し、検証後の本番環境とステージング スロットをスワップします。

1. サインイン、 [Azure Cloud Shell](https://shell.azure.com/bash)署名されていない場合は、します。
2. ステージング スロットを作成します。

    a. デプロイ スロットを作成、名前を持つ*ステージング*します。

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. ローカルの Git および get からのデプロイを使用するステージング スロットの構成、**ステージング**デプロイの URL。 **後で参照に対して、この URL に注意してください**します。

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. ステージング スロットの URL を表示します。 空のステージング スロットを表示する URL に移動します。 **後で参照に対して、この URL に注意してください**します。

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. テキスト エディターまたは Visual Studio では、次のように変更します。 *Pages/Index.cshtml*もう一度ように、`<h2>`要素を読み取ります`<h2>Simple Feed Reader - V3</h2>`ファイルを保存します。

4. いずれかを使用して、ローカルの Git リポジトリにファイルをコミット、**変更**Visual studio のページ*チーム エクスプ ローラー*  タブで、または入力して、次を使用して、ローカル コンピューターのコマンド シェル。

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. ローカル コンピューターのコマンド シェルを使用してでは、Git リモートとしてステージング配置の URL を追加し、コミットされた変更をプッシュします。

    a. ローカルの Git リポジトリには、ステージング用のリモート URL を追加します。

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. ローカルをプッシュ*マスター*に分岐、 *azure ステージング*リモートの*マスター*分岐します。

    ```console
    git push azure-staging master
    ```

    Azure の構築し、アプリの配置中には、待機します。

6. V3 がステージング スロットにデプロイされていることを確認するには、2 つのブラウザー ウィンドウを開きます。 1 つのウィンドウでは、元の web アプリの URL に移動します。 その他のウィンドウでは、ステージング web アプリの URL に移動します。 運用環境の URL では、アプリの V2 は機能します。 ステージング URL では、アプリの V3 は機能します。

    ![ブラウザー ウィンドウを比較するスクリーン ショット](./media/deploying-to-app-service/ready-to-swap.png)

7. Cloud Shell では、運用環境に検証/ウォーム アップのステージング スロットをスワップします。

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. 2 つのブラウザー ウィンドウを更新することで、スワップが発生したことを確認します。

    ![スワップ後にブラウザー ウィンドウを比較します。](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>まとめ

このセクションで、次のタスクを完了しました。

* ダウンロードして、サンプル アプリをビルドします。
* Azure Cloud Shell を使用して Azure App Service Web アプリを作成します。
* サンプル アプリを Git を使用して Azure にデプロイします。
* Visual Studio を使用してアプリに変更をデプロイします。
* Web アプリには、ステージング スロットを追加します。
* ステージング スロットに更新プログラムを展開します。
* ステージングと運用スロットをスワップします。

次のセクションでは、Azure のパイプラインを使用した DevOps パイプラインを構築する方法を学習します。

## <a name="additional-reading"></a>その他の参考資料

* [Web Apps の概要](/azure/app-service/app-service-web-overview)
* [Azure App Service で .NET Core および SQL Database web アプリを構築します。](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Azure App Service のデプロイ資格情報を構成します。](/azure/app-service/app-service-deployment-credentials)
* [Azure App Service でステージング環境を設定します。](/azure/app-service/web-sites-staged-publishing)
