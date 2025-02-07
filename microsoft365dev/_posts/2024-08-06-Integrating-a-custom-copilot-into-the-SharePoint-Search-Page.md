---
layout: post
date: 2024-08-06 21:14 +0300
#categories: [M365, Copilot Studio, Copilot, SharePoint FrameWork]

#categories: [M365, Microsoft Graph connectors, Microsoft 365 copilot, m365 development]
# tags: [M365, Microsoft Graph connectors, Microsoft 365 copilot, MS Graph]

title: Integrate a custom Copilot into the SharePoint Search Page
image: /assets/img/posts/2024-08-06-Integrating-a-custom-copilot-into-the-SharePoint-Search-Page/screenshot-solution-in-action03.png
description: >
 This extension would incorporate a custom Copilot, drawing on SharePoint Site Content as its knowledge base. Have you considered the possibility of adding a custom copilot to your SharePoint site's search page? In this article, we will explore the how to integrate a custom copilot into the SharePoint Site Search Page via the SharePoint Framework (SPFx), with the goal of improving your SharePoint Search experience.
---

# Integrate a custom Copilot into the SharePoint Search Page

* toc
{:toc}


### Overview
Many of us utilize SharePoint Search, yet based on my observations, several non-developer end-users struggle to effectively navigate SharePoint Search to locate their desired content. This observation prompted me to consider enhancing SharePoint Search with a Copilot feature through an SPFx extension. This extension would incorporate a custom Copilot, drawing on SharePoint Site Content as its knowledge base. Have you considered the possibility of adding a custom copilot to your SharePoint site's search page? In this article, we will explore the how to integrate a custom copilot into the SharePoint Site Search Page via the SharePoint Framework (SPFx), with the goal of improving your SharePoint Search experience.


### The idea 
Many of us have developed custom copilots using Copilot Studio, yet integrating these custom copilots into SharePoint is less common. It's likely that many are eagerly awaiting the "Building your custom Copilot in SharePoint" release. For those interested in similar capabilities sooner, this sample project can serve as a temporary solution until the official SharePoint feature is available.

Here's how it operates: Initially, we need to build a custom copilot designed to source answers from a SharePoint Site. The Generative Answers feature streamlines the creation of an AI-powered, conversational interface for users.
Subsequently, users will interact with the custom copilot through a SPFx component on the SharePoint Search Page.

Integration with Azure App Registrations enables us to obtain and exchange tokens with the copilot, granting it access to SharePoint data. This allows for the posing of questions and receiving relevant answers, utilizing the capabilities of Generative AI in Copilot Studio.

![Integrate a custom Copilot into the SharePoint Search Page](/assets/img/posts/2024-08-06-Integrating-a-custom-copilot-into-the-SharePoint-Search-Page/screenshot-solution-in-action03.png)
![Integrate a custom Copilot into the SharePoint Search Page](/assets/img/posts/2024-08-06-Integrating-a-custom-copilot-into-the-SharePoint-Search-Page/screenshot-solution-in-action04.png)



### Prerequisites

- Copilot Studio:
    - Build a custom copilot
- Azure level:
    - [Configure user authentication with Microsoft Entra ID](https://learn.microsoft.com/en-us/microsoft-copilot-studio/configuration-authentication-azure-ad)
    -  [Configure single sign-on with Microsoft Entra ID](https://learn.microsoft.com/en-us/microsoft-copilot-studio/configure-sso?tabs=webApp#create-app-registrations-for-your-custom-website)
- SharePoint Level:
    - Build SPFx Extension
    - SharePoint Configuration list

Before moving forward, it's crucial to acknowledge that there are specific prerequisites across various sections. In terms of Copilot Studio, I'll skip the details on creating a Custom copilot with Copilot Studio since our esteemed colleague [Paolo Pialorsi has already covered it extensively in a community call](https://www.youtube.com/watch?v=yFCYwIFj3Jg). For the custom Copilot, two Azure App registrations are essential: one for setting up Microsoft Entra ID authentication and another for managing the SSO experience in SharePoint effectively. Regarding SharePoint, We have to build an SPFx Extension and creating a SharePoint Configuration list to manage our extension variables.

For the SharePoint Configuration list columns. Here are the required columns:
- BotUrl: The token endpoint for Microsoft Copilot studio. This can be found in the Microsoft Copilot studio designer, under Settings -> Channels -> Mobile App
- CustomScope: The scope defined for the custom API in the copilot app registration. In the first Pre-requisite.
- ClientId: The Azure App registration created for handling SSO. In the second Pre-requisite.
- Authority: The login URL for your tenant. For example: mytenant.onmicrosoft.com
- Greet: Should the copilot greet users at the beginning of the conversation. "true/false"
- BotName: The title for the copilot bot.
- PanelLabel: The Label for copilot dialog chat.


### Code Overview
The SPFx extension code is now available in my [GitHub](https://github.com/mohammadamer/copilot-in-search-page). Feel free to review it and contribute by submitting PRs for any potential updates or enhancements you identify.

Inversify is being used for dependency injection and Mutation observer is being used to monitoring DOM changes and once the root element where we want to inject our component is rendered then the Copilot component button is rendered.

The SPFx extension code is well structured. There are three main components in the [components folder](https://github.com/mohammadamer/copilot-in-search-page/tree/main/src/components/CopilotComponents). There are also [Handlers](https://github.com/mohammadamer/copilot-in-search-page/tree/main/src/extensions/copilotInSearch/Handlers) for for handling DOM operations, MSAL Operations, PnPjs Operations, Mutation operations and SharePoint page operations.

### Conclusion
Integrating a custom Copilot into the SharePoint Search Page through the SharePoint Framework (SPFx) can significantly enhance the search experience for end-users who struggle to navigate SharePoint effectively. This approach leverages Generative AI to provide a conversational interface that sources answers from SharePoint Site Content, making it easier for users to find the information they need. By utilizing Azure App Registrations for user authentication and single sign-on (SSO), this integration ensures secure and seamless access to SharePoint data.

### References:
* [Creating custom copilot with Copilot Studio based on your files in SharePoint](https://www.youtube.com/watch?v=yFCYwIFj3Jg)
* [Configure user authentication with Microsoft Entra ID](https://learn.microsoft.com/en-us/microsoft-copilot-studio/configuration-authentication-azure-ad)
* [Configure single sign-on with Microsoft Entra ID](https://learn.microsoft.com/en-us/microsoft-copilot-studio/configure-sso?tabs=webApp#create-app-registrations-for-your-custom-website)
* [GitHub Repo - Copilot in Search Page](https://github.com/mohammadamer/copilot-in-search-page)

*[SERP]: Search Engine Results Page