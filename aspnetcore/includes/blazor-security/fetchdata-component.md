<span data-ttu-id="83b81-101">`FetchData` コンポーネントでは、次の方法を示します。</span><span class="sxs-lookup"><span data-stu-id="83b81-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="83b81-102">アクセストークンをプロビジョニングします。</span><span class="sxs-lookup"><span data-stu-id="83b81-102">Provision an access token.</span></span>
* <span data-ttu-id="83b81-103">*サーバー*アプリで保護されたリソース API を呼び出すには、アクセストークンを使用します。</span><span class="sxs-lookup"><span data-stu-id="83b81-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="83b81-104">`@attribute [Authorize]` ディレクティブは、このコンポーネントにアクセスするためにユーザーを承認する必要があることを Blazor WebAssembly 承認システムに示します。</span><span class="sxs-lookup"><span data-stu-id="83b81-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="83b81-105">*クライアント*アプリに属性が存在しても、適切な資格情報を使用せずにサーバー上の API が呼び出されるのを防ぐことはできません。</span><span class="sxs-lookup"><span data-stu-id="83b81-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="83b81-106">また、*サーバー*アプリは、適切なエンドポイントで `[Authorize]` を使用して、適切に保護する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83b81-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="83b81-107">`AuthenticationService.RequestAccessToken();` は、API を呼び出すために要求に追加できるアクセストークンの要求を処理します。</span><span class="sxs-lookup"><span data-stu-id="83b81-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="83b81-108">トークンがキャッシュされている場合、またはサービスがユーザーの操作なしで新しいアクセストークンをプロビジョニングできる場合、トークン要求は成功します。</span><span class="sxs-lookup"><span data-stu-id="83b81-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="83b81-109">それ以外の場合、トークン要求は失敗します。</span><span class="sxs-lookup"><span data-stu-id="83b81-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="83b81-110">要求に含める実際のトークンを取得するには、アプリは `tokenResult.TryGetToken(out var token)`を呼び出すことによって、要求が成功したことを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="83b81-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="83b81-111">要求が成功すると、トークン変数にアクセストークンが設定されます。</span><span class="sxs-lookup"><span data-stu-id="83b81-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="83b81-112">トークンの `Value` プロパティは、`Authorization` 要求ヘッダーに含めるリテラル文字列を公開します。</span><span class="sxs-lookup"><span data-stu-id="83b81-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="83b81-113">ユーザーの操作なしでトークンをプロビジョニングできなかったために要求が失敗した場合、トークンの結果にはリダイレクト URL が含まれます。</span><span class="sxs-lookup"><span data-stu-id="83b81-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="83b81-114">この URL に移動すると、認証が成功した後、ユーザーがログインページに移動し、現在のページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="83b81-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
...
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="83b81-115">詳細については、「[認証操作の前にアプリの状態を保存する](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="83b81-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
