# <a name="additional-claims-sample-app"></a><span data-ttu-id="9e404-101">Дополнительный пример приложения утверждений</span><span class="sxs-lookup"><span data-stu-id="9e404-101">Additional Claims Sample App</span></span>

<span data-ttu-id="9e404-102">В примере приложения показано, как:</span><span class="sxs-lookup"><span data-stu-id="9e404-102">The sample app demonstrates how to:</span></span>

* <span data-ttu-id="9e404-103">Получите заданные пользователем имя и фамилию из Google и храните утверждения о имени в значениях, предоставленных Google.</span><span class="sxs-lookup"><span data-stu-id="9e404-103">Obtain the user's given name and surname from Google and store the name claims with the values provided by Google.</span></span>
* <span data-ttu-id="9e404-104">Храните маркер доступа Google в @no__t пользователя (0).</span><span class="sxs-lookup"><span data-stu-id="9e404-104">Store the Google access token in the user's `AuthenticationProperties`.</span></span>

<span data-ttu-id="9e404-105">Чтобы использовать пример приложения, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="9e404-105">To use the sample app:</span></span>

1. <span data-ttu-id="9e404-106">Зарегистрируйте приложение и получите допустимый идентификатор клиента и секрет клиента для проверки подлинности Google.</span><span class="sxs-lookup"><span data-stu-id="9e404-106">Register the app and obtain a valid client ID and client secret for Google authentication.</span></span> <span data-ttu-id="9e404-107">Дополнительные сведения см. в статье [Настройка внешнего входа Google](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).</span><span class="sxs-lookup"><span data-stu-id="9e404-107">For more information, see [Google external login setup](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins).</span></span>
1. <span data-ttu-id="9e404-108">Укажите идентификатор клиента и секрет клиента для приложения в [гуглеоптионс](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9e404-108">Provide the client ID and client secret to the app in the [GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) of `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="9e404-109">Запустите приложение и запросите страницу Мои заявки.</span><span class="sxs-lookup"><span data-stu-id="9e404-109">Run the app and request the My Claims page.</span></span> <span data-ttu-id="9e404-110">Когда пользователь не вошел в, приложение перенаправляется в Google.</span><span class="sxs-lookup"><span data-stu-id="9e404-110">When the user isn't signed in, the app redirects to Google.</span></span> <span data-ttu-id="9e404-111">Войдите в Google.</span><span class="sxs-lookup"><span data-stu-id="9e404-111">Sign in with Google.</span></span> <span data-ttu-id="9e404-112">Google перенаправляет пользователя обратно в приложение (`/MyClaims`).</span><span class="sxs-lookup"><span data-stu-id="9e404-112">Google redirects the user back to the app (`/MyClaims`).</span></span> <span data-ttu-id="9e404-113">Пользователь прошел проверку подлинности, и загружается страница Мои заявки.</span><span class="sxs-lookup"><span data-stu-id="9e404-113">The user is authenticated, and the My Claims page is loaded.</span></span> <span data-ttu-id="9e404-114">Заданные имена и заявления о фамилии находятся в **заявке User Claims** со значениями, предоставленными Google.</span><span class="sxs-lookup"><span data-stu-id="9e404-114">The given name and surname claims are present under **User Claims** with the values provided by Google.</span></span> <span data-ttu-id="9e404-115">Маркер доступа отображается в разделе **свойства проверки подлинности**.</span><span class="sxs-lookup"><span data-stu-id="9e404-115">The access token is displayed under **Authentication Properties**.</span></span>