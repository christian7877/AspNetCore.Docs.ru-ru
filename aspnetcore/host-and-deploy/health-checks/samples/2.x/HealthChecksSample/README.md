# <a name="aspnet-core-health-check-sample"></a><span data-ttu-id="d4cbc-101">Пример проверки работоспособности ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d4cbc-101">ASP.NET Core Health Check Sample</span></span>

<span data-ttu-id="d4cbc-102">В этом примере описывается использование ПО промежуточного слоя и служб для проверки работоспособности.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-102">This sample illustrates the use of Health Check Middleware and services.</span></span> <span data-ttu-id="d4cbc-103">В нем демонстрируется сценарий, описанный в статье [Проверки работоспособности в ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks).</span><span class="sxs-lookup"><span data-stu-id="d4cbc-103">This sample demonstrates the scenario described in the [Health checks in ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) topic.</span></span>

<span data-ttu-id="d4cbc-104">Чтобы запустить образец приложения для сценария, описанного в разделе, используйте команду [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) из папки проекта в командной строке.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-104">To run the sample app for a scenario described in the topic, use the [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="d4cbc-105">Укажите параметр того сценария, который вы изучаете.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-105">Pass a switch for the scenario that you're exploring.</span></span> <span data-ttu-id="d4cbc-106">По умолчанию приложение использует конфигурацию `basic`, если параметр для `dotnet run` не указан.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-106">The app defaults to the `basic` configuration when a switch isn't provided to `dotnet run`.</span></span>

| <span data-ttu-id="d4cbc-107">Сценарий</span><span class="sxs-lookup"><span data-stu-id="d4cbc-107">Scenario</span></span>                                               | <span data-ttu-id="d4cbc-108">Пример команды для приложения</span><span class="sxs-lookup"><span data-stu-id="d4cbc-108">Sample app command</span></span>               | <span data-ttu-id="d4cbc-109">Описание</span><span class="sxs-lookup"><span data-stu-id="d4cbc-109">Description</span></span> |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| <span data-ttu-id="d4cbc-110">Базовая проверка работоспособности (по умолчанию)</span><span class="sxs-lookup"><span data-stu-id="d4cbc-110">Basic health probe (default)</span></span>                           | `dotnet run --scenario basic`    | <span data-ttu-id="d4cbc-111">Подтверждает, что приложение может обрабатывать HTTP-запросы.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-111">Confirms that the app can process HTTP requests.</span></span> |
| <span data-ttu-id="d4cbc-112">Проверка базы данных</span><span class="sxs-lookup"><span data-stu-id="d4cbc-112">Database probe</span></span>                                         | `dotnet run --scenario db`       | <span data-ttu-id="d4cbc-113">Проверяет подключение к базе данных SQL Server.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-113">Checks a SQL Server database connection.</span></span> <span data-ttu-id="d4cbc-114">См. инструкции в разделе [Проверка базы данных](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe).</span><span class="sxs-lookup"><span data-stu-id="d4cbc-114">See the [Database probe](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) section of the topic for instructions.</span></span> |
| <span data-ttu-id="d4cbc-115">Проверки готовности и активности</span><span class="sxs-lookup"><span data-stu-id="d4cbc-115">Readiness/liveness probes</span></span>                              | `dotnet run --scenario liveness` | <span data-ttu-id="d4cbc-116">Выполняет проверку состояния активной работы приложения (*жизнеспособность*) и подготовки приложения к работе (*готовность*).</span><span class="sxs-lookup"><span data-stu-id="d4cbc-116">Performs checks for a live app status (*liveness*) versus the app preparing to become live (*readiness*).</span></span> |
| <span data-ttu-id="d4cbc-117">Проверка на основе метрики (памяти) или</span><span class="sxs-lookup"><span data-stu-id="d4cbc-117">Metric-based probe (memory)/</span></span><br><span data-ttu-id="d4cbc-118">настраиваемого модуля записи ответов</span><span class="sxs-lookup"><span data-stu-id="d4cbc-118">custom response writer</span></span> | `dotnet run --scenario writer`   | <span data-ttu-id="d4cbc-119">Проверяет использование памяти и записывает настраиваемые данные JSON при проверке конечной точки работоспособности.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-119">Checks against memory use and writes out custom JSON when the health endpoint is checked.</span></span> |
| <span data-ttu-id="d4cbc-120">Фильтр по портам</span><span class="sxs-lookup"><span data-stu-id="d4cbc-120">Filter by port</span></span>                                         | `dotnet run --scenario port`     | <span data-ttu-id="d4cbc-121">Фильтрует проверки работоспособности для указанного порта.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-121">Filters health checks to a given port.</span></span> <span data-ttu-id="d4cbc-122">См. инструкции в разделе [Фильтр по портам](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="d4cbc-122">See the [Filter by port](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) section of the topic for instructions.</span></span> |

<span data-ttu-id="d4cbc-123">Сценарии проверки базы данных и фильтра по портам требуют дополнительной настройки.</span><span class="sxs-lookup"><span data-stu-id="d4cbc-123">The database probe and port filter scenarios require additional configuration.</span></span> <span data-ttu-id="d4cbc-124">См. сведения в разделе [Проверки работоспособности](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks).</span><span class="sxs-lookup"><span data-stu-id="d4cbc-124">See the [Health checks](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) topic for details.</span></span>