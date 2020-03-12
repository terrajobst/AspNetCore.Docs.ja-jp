`FetchData` コンポーネントでは、次の方法を示します。

* アクセストークンをプロビジョニングします。
* *サーバー*アプリで保護されたリソース API を呼び出すには、アクセストークンを使用します。

`@attribute [Authorize]` ディレクティブは、このコンポーネントにアクセスするためにユーザーを承認する必要があることを Blazor WebAssembly 承認システムに示します。 *クライアント*アプリに属性が存在しても、適切な資格情報を使用せずにサーバー上の API が呼び出されるのを防ぐことはできません。 また、*サーバー*アプリは、適切なエンドポイントで `[Authorize]` を使用して、適切に保護する必要があります。

`AuthenticationService.RequestAccessToken();` は、API を呼び出すために要求に追加できるアクセストークンの要求を処理します。 トークンがキャッシュされている場合、またはサービスがユーザーの操作なしで新しいアクセストークンをプロビジョニングできる場合、トークン要求は成功します。 それ以外の場合、トークン要求は失敗します。

要求に含める実際のトークンを取得するには、アプリは `tokenResult.TryGetToken(out var token)`を呼び出すことによって、要求が成功したことを確認する必要があります。 

要求が成功すると、トークン変数にアクセストークンが設定されます。 トークンの `Value` プロパティは、`Authorization` 要求ヘッダーに含めるリテラル文字列を公開します。

ユーザーの操作なしでトークンをプロビジョニングできなかったために要求が失敗した場合、トークンの結果にはリダイレクト URL が含まれます。 この URL に移動すると、認証が成功した後、ユーザーがログインページに移動し、現在のページに戻ります。

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

詳細については、「[認証操作の前にアプリの状態を保存する](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)」を参照してください。
