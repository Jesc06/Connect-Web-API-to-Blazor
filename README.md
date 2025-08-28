# *Connect-Web-API-to-Blazor*
Configuration how to connect Web API to Blazor server | Blazor Wasm

### 1. *Setup Web API base URL in blazor program.cs*

```csharp
builder.Services.AddHttpClient("API", option =>
{ 
    option.BaseAddress = new Uri("https://localhost:7253");//Web API URL
} );

```

<br>

### 2. *Use Web API Base URL from blazor program.cs using IHttpClientFactory & HttpClient*

```csharp

        private readonly HttpClient _httpClient;
        private readonly IHttpClientFactory _httpClientFactory;

        public AuthService(HttpClient httpClient, IHttpClientFactory httpClientFactory)
        {
            _httpClient = httpClient;
            _httpClientFactory = httpClientFactory;
        }


        public async Task<string> login(LoginDTO loginDto)
        {
            var httpFactory = _httpClientFactory.CreateClient("API");
            var response = await httpFactory.PostAsJsonAsync("api/Account/Login", loginDto);

            if (response.IsSuccessStatusCode)
            {
                var result = await response.Content.ReadFromJsonAsync<LoginResult>();

                await _jsRuntime.InvokeVoidAsync("localStorage.setItem", "authToken", result.Token);
                _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
                return result.Token;
            }
            return null;
        }

```
