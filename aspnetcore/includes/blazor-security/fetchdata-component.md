В `FetchData` компоненте показано, как:

* Подготавливает маркер доступа.
* Используйте маркер доступа для вызова API защищенных ресурсов в *серверном* приложении.

`@attribute [Authorize]` Директива указывает системе авторизации веб блазор, что пользователь должен быть авторизован для посещения этого компонента. Наличие атрибута в *клиентском* приложении не мешает вызову API на сервере без соответствующих учетных данных. *Серверное* приложение также должно использовать `[Authorize]` на соответствующих конечных точках для правильной защиты.

`IAccessTokenProvider.RequestAccessToken();`позаботится о запросе маркера доступа, который можно добавить в запрос для вызова API. Если маркер кэшируется или служба может подготавливать новый маркер доступа без вмешательства пользователя, запрос маркера будет выполнен. В противном случае запрос маркера завершается `AccessTokenNotAvailableException` ошибкой, которая перехватывается `try-catch` инструкцией.

Чтобы получить фактический токен для включения в запрос, приложение должно проверить, что запрос прошел, вызвав метод `tokenResult.TryGetToken(out var token)`. 

Если запрос был успешным, переменная маркера заполняется маркером доступа. `Value` Свойство маркера предоставляет строку литерала для включения в заголовок `Authorization` запроса.

Если запрос завершился ошибкой, так как не удалось подготовить маркер без взаимодействия с пользователем, результат маркера содержит URL-адрес перенаправления. При переходе по этому URL-адресу пользователь переходит на страницу входа и возвращается к текущей странице после успешной проверки подлинности.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
