Страница индекса (*wwwroot/index.HTML*) содержит скрипт, определяющий `AuthenticationService` в JavaScript. `AuthenticationService` обрабатывает сведения о низком уровне протокола OIDC. Приложение внутренне вызывает методы, определенные в скрипте для выполнения операций проверки подлинности.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
