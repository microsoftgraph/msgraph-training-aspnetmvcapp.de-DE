<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="27f97-101">In diesem Lernprogramm erfahren Sie, wie Sie eine ASP.NET MVC-Web-App erstellen, die die Microsoft Graph-API zum Abrufen von Kalenderinformationen für einen Benutzer verwendet.</span><span class="sxs-lookup"><span data-stu-id="27f97-101">This tutorial teaches you how to build an ASP.NET MVC web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="27f97-102">Wenn Sie das fertige Tutorial lieber herunterladen möchten, können Sie es auf zweierlei Weise herunterladen.</span><span class="sxs-lookup"><span data-stu-id="27f97-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="27f97-103">Laden Sie den [ASP.NET-Schnellstart](https://developer.microsoft.com/graph/quick-start?platform=option-dotnet) herunter, um in wenigen Minuten Arbeitscode zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="27f97-103">Download the [ASP.NET quick start](https://developer.microsoft.com/graph/quick-start?platform=option-dotnet) to get working code in minutes.</span></span>
> - <span data-ttu-id="27f97-104">Laden oder Klonen Sie das [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span><span class="sxs-lookup"><span data-stu-id="27f97-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="27f97-105">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="27f97-105">Prerequisites</span></span>

<span data-ttu-id="27f97-106">Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie [Visual Studio](https://visualstudio.microsoft.com/vs/) auf dem Entwicklungscomputer installiert haben.</span><span class="sxs-lookup"><span data-stu-id="27f97-106">Before you start this tutorial, you should have [Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="27f97-107">Wenn Sie nicht über Visual Studio verfügen, besuchen Sie den vorherigen Link für Downloadoptionen.</span><span class="sxs-lookup"><span data-stu-id="27f97-107">If you do not have Visual Studio, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="27f97-108">Dieses Lernprogramm wurde mit der Version 15,81 von Visual Studio 2017 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="27f97-108">This tutorial was written with Visual Studio 2017 version 15.81.</span></span> <span data-ttu-id="27f97-109">Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, jedoch nicht getestet.</span><span class="sxs-lookup"><span data-stu-id="27f97-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="27f97-110">Feedback</span><span class="sxs-lookup"><span data-stu-id="27f97-110">Feedback</span></span>

<span data-ttu-id="27f97-111">Feedback zu diesem Tutorial finden Sie im GitHub- [Repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span><span class="sxs-lookup"><span data-stu-id="27f97-111">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span></span>