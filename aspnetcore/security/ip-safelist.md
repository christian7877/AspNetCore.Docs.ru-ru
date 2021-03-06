---
title: Адрес списка надежных IP-адресов клиента для ASP.NET Core
author: damienbod
description: Узнайте, как создавать по промежуточного слоя или фильтры действий для проверки удаленных IP-адресов по списку утвержденных IP-адресов.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 7923a81e72124cfb0e11e3c1ac327c1e32194b21
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776504"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Адрес списка надежных IP-адресов клиента для ASP.NET Core

[(Damien Бауден](https://twitter.com/damien_bod) и [Tom Dykstra)](https://github.com/tdykstra)
 
В этой статье показано три способа реализации списка надежных IP-адресов (также известного как список разрешений) в приложении ASP.NET Core. В прилагаемом примере приложения демонстрируются все три подхода. Вы можете использовать:

* По промежуточного слоя для проверки удаленного IP-адреса каждого запроса.
* Фильтры действий MVC для проверки удаленного IP-адреса запросов для конкретных контроллеров или методов действий.
* RazorСтраницы фильтруется для проверки удаленного IP-адреса запросов к Razor страницам.

В каждом случае строка, содержащая утвержденные IP-адреса клиентов, сохраняется в параметре приложения. По промежуточного слоя или фильтра:

* Выполняет синтаксический анализ строки в массиве. 
* Проверяет, существует ли удаленный IP-адрес в массиве.

Доступ разрешен, если массив содержит IP-адрес. В противном случае возвращается код состояния HTTP 403 запрещено.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Адрес списка надежных IP-адресов

В примере приложения адрес списка надежных IP-адресов:

* Определяется `AdminSafeList` свойством в файле *appSettings. JSON* .
* Строка с разделителями-точкой с запятой, которая может содержать адреса [протокола IP версии 4 (IPv4)](https://wikipedia.org/wiki/IPv4) и [протокола IP версии 6 (IPv6)](https://wikipedia.org/wiki/IPv6) .

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

В предыдущем примере разрешены адреса `127.0.0.1` IPv4 и `192.168.1.5` и IPv6-адрес замыкания на себя `::1` (сжатый формат для `0:0:0:0:0:0:0:1`).

## <a name="middleware"></a>ПО промежуточного слоя

`Startup.Configure` Метод добавляет пользовательский `AdminSafeListMiddleware` тип по промежуточного слоя в конвейер запросов приложения. Списка надежных отправителей извлекается с помощью поставщика конфигурации .NET Core и передается в качестве параметра конструктора.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

По промежуточного слоя анализирует строку в массив и ищет удаленный IP-адрес в массиве. Если удаленный IP-адрес не найден, по промежуточного слоя возвращает HTTP 403 запрещено. Этот процесс проверки обходится для HTTP-запросов GET.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Фильтр действий

Если требуется управление доступом к спискам надежных отправителей для конкретных контроллеров MVC или методов действий, используйте фильтр действий. Пример:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

В `Startup.ConfigureServices`добавьте фильтр действий в коллекцию фильтров MVC. В следующем примере добавляется фильтр `ClientIpCheckActionFilter` действий. Объект списка надежных отправителей и экземпляр средства ведения журнала консоли передаются как параметры конструктора.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Затем фильтр действий можно применить к контроллеру или методу действия с помощью атрибута [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

В примере приложения фильтр действий применяется к методу `Get` действия контроллера. При тестировании приложения путем отправки:

* Запрос HTTP GET, `[ServiceFilter]` атрибут проверяет IP-адрес клиента. Если доступ разрешен для метода `Get` действия, то в фильтре действий и методе действия создается разновидность следующих выходных данных консоли:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Команда HTTP-запроса, отличная от GET, `AdminSafeListMiddleware` по промежуточного слоя проверяет IP-адрес клиента.

## <a name="razor-pages-filter"></a>RazorФильтр страниц

Если требуется управлять доступом к спискам надежных отправителей Razor для приложения страниц, используйте Razor фильтр страниц. Пример:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

В `Startup.ConfigureServices`включите фильтр Razor страниц, добавив его в коллекцию фильтров MVC. В следующем примере `ClientIpCheckPageFilter` Razor добавляется фильтр страниц. Объект списка надежных отправителей и экземпляр средства ведения журнала консоли передаются как параметры конструктора.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Когда запрашивается страница *индекса* Razor примера приложения, фильтр Razor страниц проверяет IP-адрес клиента. Фильтр создает разновидность следующих выходных данных консоли:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/middleware/index>
* [Фильтры действий](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
