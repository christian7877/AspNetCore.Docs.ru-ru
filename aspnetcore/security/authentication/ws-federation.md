---
title: Проверка подлинности пользователей с помощью WS-Federation в ASP.NET Core
author: chlowell
description: В этом руководстве показано, как использовать WS-Federation в приложении ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: fede3887ad7dacd40cf3bb5d1b785392a9bc1480
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850465"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Проверка подлинности пользователей с помощью WS-Federation в ASP.NET Core

В этом руководстве показано, как разрешить пользователям выполнять вход с помощью поставщика проверки подлинности WS-Federation, например службы федерации Active Directory (AD FS) (ADFS) или [Azure Active Directory](/azure/active-directory/) (AAD). В нем используется ASP.NET Core пример приложения, описанный в статье [Аутентификация Facebook, Google и внешнего поставщика](xref:security/authentication/social/index).

Для ASP.NET Core приложений Поддержка WS-Federation предоставляется службой [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Этот компонент переносится из [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) и разделяет многие механики этого компонента. Однако эти компоненты отличаются друг от друга несколькими важными способами.

По умолчанию новое по промежуточного слоя:

* Не разрешает незапрошенные имена входа. Эта функция протокола WS-Federation уязвима для атак XSRF. Однако его можно включить с помощью `AllowUnsolicitedLogins` параметра.
* Не проверяет каждую форму POST для сообщений входа. Только запросы к компоненту проверяются `CallbackPath` на наличие входов. `CallbackPath` значение `/signin-wsfed` по умолчанию —, но его можно изменить с помощью унаследованного свойства [ремотеаусентикатионоптионс. каллбаккпас](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) класса [всфедератионоптионс](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Этот путь можно использовать совместно с другими поставщиками проверки подлинности, включив параметр [скипунрекогнизедрекуестс](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Регистрация приложения в Active Directory

### <a name="active-directory-federation-services"></a>Службы федерации Active Directory (AD FS)

* Откройте **Мастер добавления отношения доверия с проверяющей стороной** на сервере из консоли управления ADFS:

![Мастер добавления отношения доверия с проверяющей стороной: Добро пожаловать](ws-federation/_static/AdfsAddTrust.png)

* Выберите Ввод данных вручную:

![Мастер добавления отношения доверия с проверяющей стороной: Выбор источника данных](ws-federation/_static/AdfsSelectDataSource.png)

* Введите отображаемое имя проверяющей стороны. Это имя не имеет значения для приложения ASP.NET Core.

* В [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) отсутствует поддержка шифрования маркеров, поэтому не настраивайте сертификат шифрования маркеров:

![Мастер добавления отношения доверия с проверяющей стороной: Настройка сертификата](ws-federation/_static/AdfsConfigureCert.png)

* Включите поддержку пассивного протокола WS-Federation с помощью URL-адреса приложения. Проверьте правильность порта для приложения:

![Мастер добавления отношения доверия с проверяющей стороной: Настройка URL-адреса](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Это должен быть URL-адрес HTTPS. IIS Express может предоставить самозаверяющий сертификат при размещении приложения во время разработки. Для Kestrel требуется ручная настройка сертификата. Дополнительные сведения см. в [документации по Kestrel](xref:fundamentals/servers/kestrel) .

* Нажмите кнопку **Далее** в мастере и **закройте** в конце.

* ASP.NET Core Identity требуется утверждение **идентификатора имени** . Добавьте его в диалоговом окне **изменение правил утверждений** :

![Изменение правил утверждения](ws-federation/_static/EditClaimRules.png)

* В **мастере добавления правила преобразования утверждений**Оставьте выбранный по умолчанию шаблон **отправки атрибутов LDAP в качестве шаблона утверждений** и нажмите кнопку **Далее**. Добавьте правило, сопоставленное с атрибутом **SAM-Account-Name** LDAP к исходящему утверждению **ID** :

![Мастер добавления правила преобразования утверждений: Настройка правила для утверждений](ws-federation/_static/AddTransformClaimRule.png)

* В окне **изменение правил утверждений** нажмите кнопку **Готово** > **ОК** .

### <a name="azure-active-directory"></a>Azure Active Directory

* Перейдите в колонку регистрации приложений клиента AAD. Щелкните **Регистрация нового приложения**:

![Azure Active Directory: Регистрация приложений](ws-federation/_static/AadNewAppRegistration.png)

* Введите имя для регистрации приложения. Это не имеет значения для ASP.NET Core приложения.
* Введите URL-адрес, который прослушивает приложение в качестве **URL-адреса входа**:

![Azure Active Directory: создание регистрации приложения](ws-federation/_static/AadCreateAppRegistration.png)

* Щелкните **конечные точки** и запишите URL-адрес **документа метаданных федерации** . Это по промежуточного слоя WS-Federation `MetadataAddress`:

![Azure Active Directory: конечные точки](ws-federation/_static/AadFederationMetadataDocument.png)

* Перейдите к новой регистрации приложения. Щелкните **Параметры** > **Свойства** и запишите **URI идентификатора приложения**. Это по промежуточного слоя WS-Federation `Wtrealm`:

![Azure Active Directory: свойства регистрации приложения](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Использование WS-Federation без ASP.NET CoreIdentity

По промежуточного слоя WS-Federation можно использовать Identityбез. Пример:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Добавление WS-Federation в качестве внешнего поставщика входа для ASP.NET CoreIdentity

* Добавьте зависимость от [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) в проект.
* Добавить WS-Federation в `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Вход с помощью WS-Federation

Перейдите к приложению и щелкните ссылку **Вход** в заголовке навигации. Есть возможность войти с помощью WsFederation: Log On Page ![.](ws-federation/_static/WsFederationButton.png)

При использовании ADFS в качестве поставщика кнопка перенаправляется на страницу входа ADFS: ![страница входа ADFS](ws-federation/_static/AdfsLoginPage.png)

При Azure Active Directory в качестве поставщика кнопка перенаправляется на страницу входа AAD: страница входа AAD. ![](ws-federation/_static/AadSignIn.png)

При успешном входе нового пользователя выполняется перенаправление на страницу регистрации пользователя приложения: ![регистрация страницы](ws-federation/_static/Register.png)
