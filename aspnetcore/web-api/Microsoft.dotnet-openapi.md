---
title: Разработка приложений ASP.NET Core с использованием OpenAPI
author: ryanbrandenburg
description: Здесь демонстрируется, как добавить ссылки в файлы OpenAPI с использованием средства Microsoft.dotnet-openapi.
ms.author: rybrande
ms.date: 08/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: a9b38bb7e69744d72867bf69cecf1fa92d7c15b3
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187368"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Разработка приложений ASP.NET Core с использованием средств OpenAPI

Автор: Райан Бранденбург (Ryan Brandenburg)

`Microsoft.dotnet-openapi` — это глобальное средство .NET Core для управления ссылками [OpenAPI](https://github.com/OAI/OpenAPI-Specification) в рамках проекта.

## <a name="installation"></a>Установка

Чтобы установить `Microsoft.dotnet-openapi`, выполните следующую команду:

```console
dotnet tool install -g Microsoft.dotnet-openapi
```

`Microsoft.dotnet-openapi` — это [глобальное средство .NET Core](/dotnet/core/tools/global-tools).

## <a name="add"></a>Add

При добавлении ссылки OpenAPI с помощью любой из команд на этой странице в файл *CSPROJ* добавляется элемент `<OpenApiReference />`, аналогичный приведенному ниже:

```xml
<OpenApiReference Include="openapi.json" />
```

Для вызова созданного клиентского кода приложению требуется предыдущая ссылка.

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Добавление файла

#### <a name="options"></a>Параметры

| Короткий параметр| Длинный параметр| ОПИСАНИЕ | Пример |
|-------|------|-------|---------|
| -v|--verbose | Отображение подробных выходных данных. |dotnet openapi add file *-v* .\OpenAPI.json |
| -p|--updateProject | Проект для выполнения операции. |dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -c|--code-generator| Генератор кода, применяемый к ссылке. Возможные значения: `NSwagCSharp` и `NSwagTypeScript`. Если атрибут `--code-generator` не задан, по умолчанию для средств будет выбрано `NSwagCSharp`.|dotnet openapi add file .\OpenApi.json --code-generator
| -h|--help|Отображение справочных сведений.|dotnet openapi add file --help|

#### <a name="arguments"></a>Аргументы

|  Аргумент  | ОПИСАНИЕ | Пример |
|-------------|-------------|---------|
| source-file | Источник, из которого создается ссылка. Должен быть файлом OpenAPI. |dotnet openapi add file *.\OpenAPI.json* |

### <a name="add-url"></a>Добавление URL-адреса

#### <a name="options"></a>Параметры

| Короткий параметр| Длинный параметр| ОПИСАНИЕ | Пример |
|-------|------|-------------|---------|
| -v|--verbose | Отображение подробных выходных данных. |dotnet openapi add url *-v* `https://contoso.com/openapi.json` |
| -p|--updateProject | Проект для выполнения операции. |dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -o|--output-file | Место размещения локальной копии файла OpenAPI. |dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json* |
| -c|--code-generator| Генератор кода, применяемый к ссылке. Возможные значения: `NSwagCSharp` и `NSwagTypeScript`. |dotnet openapi add file .\OpenApi.json --code-generator
| -h|--help|Отображение справочных сведений.|dotnet openapi add url --help|

#### <a name="arguments"></a>Аргументы

|  Аргумент  | ОПИСАНИЕ | Пример |
|-------------|-------------|---------|
| source-URL | Источник, из которого создается ссылка. Должен быть URL-адресом. |dotnet openapi add url `https://contoso.com/openapi.json` |

## <a name="remove"></a>Удалить

Удаляет ссылку на OpenAPI, соответствующую заданному имени файла, из файла *CSPROJ*. При удалении ссылки OpenAPI клиенты не будут создаваться. Локальные файлы *JSON* и *YAML* удаляются.

### <a name="options"></a>Параметры

| Короткий параметр| Длинный параметр| ОПИСАНИЕ| Пример |
|-------|------|------------|---------|
| -v|--verbose | Отображение подробных выходных данных. |dotnet openapi remove *-v*|
| -p|--updateProject | Проект для выполнения операции. |dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -h|--help|Отображение справочных сведений.|dotnet openapi remove --help|

### <a name="arguments"></a>Аргументы

|  Аргумент  | ОПИСАНИЕ| Пример |
| ------------|------------|---------|
| source-file | Источник, ссылку на который необходимо удалить. |dotnet openapi remove *.\OpenAPI.json* |

## <a name="refresh"></a>Обновление

Обновляет локальную версию файла, скачанного с использованием последнего содержимого из URL-адреса для скачивания.

### <a name="options"></a>Параметры

| Короткий параметр| Длинный параметр| ОПИСАНИЕ | Пример |
|-------|------|-------------|---------|
| -v|--verbose | Отображение подробных выходных данных. | dotnet openapi refresh *-v* `https://contoso.com/openapi.json` |
| -p|--updateProject | Проект для выполнения операции. | dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -h|--help|Отображение справочных сведений.|dotnet openapi refresh --help|

### <a name="arguments"></a>Аргументы

|  Аргумент  | ОПИСАНИЕ | Пример |
| ------------|-------------|---------|
| source-URL | URL-адрес, ссылку из которого необходимо обновить. | dotnet openapi refresh `https://contoso.com/openapi.json` |