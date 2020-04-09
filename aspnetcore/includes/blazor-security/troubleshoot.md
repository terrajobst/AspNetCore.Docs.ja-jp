## <a name="troubleshoot"></a><span data-ttu-id="f3c38-101">トラブルシューティング</span><span class="sxs-lookup"><span data-stu-id="f3c38-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="f3c38-102">クッキーとサイトデータ</span><span class="sxs-lookup"><span data-stu-id="f3c38-102">Cookies and site data</span></span>

<span data-ttu-id="f3c38-103">Cookie とサイト データは、アプリの更新後も保持され、テストやトラブルシューティングに支障をきたします。</span><span class="sxs-lookup"><span data-stu-id="f3c38-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="f3c38-104">アプリ コードの変更、プロバイダーによるユーザー アカウントの変更、プロバイダー アプリの構成の変更を行うときは、次の項目をオフにします。</span><span class="sxs-lookup"><span data-stu-id="f3c38-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="f3c38-105">ユーザー サインイン Cookie</span><span class="sxs-lookup"><span data-stu-id="f3c38-105">User sign-in cookies</span></span>
* <span data-ttu-id="f3c38-106">アプリのクッキー</span><span class="sxs-lookup"><span data-stu-id="f3c38-106">App cookies</span></span>
* <span data-ttu-id="f3c38-107">キャッシュおよび保存されたサイト データ</span><span class="sxs-lookup"><span data-stu-id="f3c38-107">Cached and stored site data</span></span>

<span data-ttu-id="f3c38-108">残留 Cookie とサイト データがテストとトラブルシューティングに干渉しないようにする方法の 1 つは、次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="f3c38-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="f3c38-109">ブラウザを閉じるたびにすべての Cookie とサイト データを削除するように構成できるテスト用に、ブラウザを使用します。</span><span class="sxs-lookup"><span data-stu-id="f3c38-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="f3c38-110">アプリ、テスト ユーザー、またはプロバイダーの構成に対する変更の間にブラウザーを閉じます。</span><span class="sxs-lookup"><span data-stu-id="f3c38-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="f3c38-111">サーバー アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="f3c38-111">Run the Server app</span></span>

<span data-ttu-id="f3c38-112">ホストされている Blazor アプリをテストおよびトラブルシューティングする場合は、**サーバー**プロジェクトからアプリを実行していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3c38-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="f3c38-113">たとえば、Visual Studio で、次のいずれかの方法でアプリを起動する前に、**ソリューション エクスプローラー**でサーバー プロジェクトが強調表示されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f3c38-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="f3c38-114">**[実行]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="f3c38-114">Select the **Run** button.</span></span>
* <span data-ttu-id="f3c38-115">メニュー**Debug** > から**デバッグ開始デバッグ**を使用します。</span><span class="sxs-lookup"><span data-stu-id="f3c38-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="f3c38-116"><kbd>F5</kbd>キーを押します。</span><span class="sxs-lookup"><span data-stu-id="f3c38-116">Press <kbd>F5</kbd>.</span></span>
