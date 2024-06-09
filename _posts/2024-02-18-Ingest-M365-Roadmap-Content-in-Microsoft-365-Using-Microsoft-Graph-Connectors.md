---
title: Ingest M365 Roadmap content in Microsoft 365 using Microsoft Graph connectors 
date: 2024-02-18 20:14 +0300
categories: [M365, Microsoft Graph connectors, Microsoft 365 copilot]
tags: [M365, Microsoft Graph connectors, Microsoft 365 copilot, MS Graph] 
---

## Ingest M365 Roadmap content in Microsoft 365 using Microsoft Graph connectors

### Overview
After going through the interesting Microsoft learn module [Integrate external content with Copilot for Microsoft 365 using Microsoft Graph connectors built with .NET](https://devblogs.microsoft.com/microsoft365dev/build-message-extensions-for-microsoft-teams-and-copilot/). After that I decided to build my first Microsoft Graph connector for Ingesting M365 Roadmap content in Microsoft 365 using Microsoft Graph connectors. I would really want to thank [Waldek Mastykarz](https://twitter.com/waldekm) who helped me during implement that.

### The idea 
Most of us are always going to [Microsoft 365 roadmap site](https://www.microsoft.com/en-us/microsoft-365/roadmap) to get latest updates about the roadmap features and I thought why not integrating the roadmap in Microsoft 365. After going through the Microsoft module, I found it easy and simple to implement. Check out my [GitHub Repo for Microsft365 Roadmap Connector](https://github.com/mohammadamer/GraphConnectorM365RoadMap)

![M365 Roadmap Graph Connector](/assets/img/posts/2024-02-18-Ingest-M365-Roadmap-Content-in-Microsoft-365-Using-Microsoft-Graph-Connectors/M365-Roadmap-Graph-Connector01.png)
![M365 Roadmap Graph Connector](/assets/img/posts/2024-02-18-Ingest-M365-Roadmap-Content-in-Microsoft-365-Using-Microsoft-Graph-Connectors/M365-Roadmap-Graph-Connector02.png)

### Copilot for Microsoft 365
By Using Microsoft Graph connectors, you will be able to centralize information in your organization on Microsoft 365. Importing external content to Microsoft 365 allows Copilot for Microsoft 365 to reason over all information in your organization and give you relevant and accurate answers.

### Conclusion
Using Microsoft Graph connectors, you can centralize information in your organization on Microsoft 365. Importing external content to Microsoft 365 allows you to find and discover relevant information and share it with your colleagues more easily. When you use Copilot for Microsoft 365, importing external content to Microsoft 365 lets Copilot reason over all information in your organization, which empowers it to give you more relevant responses.

### Tips
* Don't start your connection id with microsoft like what I did `microsoft365roadmap` because I got error `The request is malformed or incorrect` and the connection failed to be created.

![Connection Id](/assets/img/posts/2024-02-18-Ingest-M365-Roadmap-Content-in-Microsoft-365-Using-Microsoft-Graph-Connectors/The-request-is-malformed-or-incorrect.png)

* If adding nuget package `System.CommandLine` does not work with you or if you get error `"error: There are no versions available for the package 'System.CommandLine'." ` then add the package manually to your .csproj file with the prerelease version then It will work and the project will be able to restore the packages.

![There are no versions available for the package](/assets/img/posts/2024-02-18-Ingest-M365-Roadmap-Content-in-Microsoft-365-Using-Microsoft-Graph-Connectors/There-are-no-versions-available-for-the-package.png)

### References:
* [Integrate external content with Copilot for Microsoft 365 using Microsoft Graph connectors built with .NET](https://learn.microsoft.com/en-us/training/modules/copilot-graph-connectors/)
* [Microsoft Graph Connector Samples](https://adoption.microsoft.com/en-us/sample-solution-gallery/?keyword=&sort-by=updateDateTime-true&page=1&product=Microsoft+Graph+connectors)
* [Sample code in Microsoft Graph connectors samples](https://adoption.microsoft.com/en-us/sample-solution-gallery/sample/pnp-graph-connector-dotnet-csharp-m365-roadmap/)
* [Microsft365 Roadmap Connector in my GitHub Repo](https://github.com/mohammadamer/GraphConnectorM365RoadMap)