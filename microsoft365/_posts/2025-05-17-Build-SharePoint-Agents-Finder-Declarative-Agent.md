---
layout: post
date: 2025-05-17 21:14 +0300
#categories: [Microsoft 365 Copilot, Microsoft Graph Connectors]

#categories: [M365, Microsoft Graph connectors, Microsoft 365 copilot, m365 development]
# tags: [M365, Microsoft Graph connectors, Microsoft 365 copilot, MS Graph]



title: Build SharePoint Agents Finder declarative agent
image: /assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/sharepoint-agents-finder-declarative-agent.jpg

description: >
The SharePoint Agents Finder leverages the Microsoft Graph API as a Copilot plugin within a declarative agent. It utilizes the Microsoft Graph Search API's `/search/query` endpoint to efficiently retrieve information about SharePoint Agents and any file within Microsoft 365. This empowers end-users and IT administrators to quickly access details on SharePoint Agents or locate specific files in Microsoft 365 directly through the M365 Copilot chat interface.
---

# Build SharePoint Agents Finder declarative agent

* toc
{:toc}


### Overview

The SharePoint Agents Finder leverages the Microsoft Graph API as a Copilot plugin within a declarative agent. It utilizes the Microsoft Graph Search API's `/search/query` endpoint to efficiently retrieve information about SharePoint Agents and any file within Microsoft 365. This empowers end-users and IT administrators to quickly access details on SharePoint Agents or locate specific files in Microsoft 365 directly through the M365 Copilot chat interface.

### The idea

Extensive attempts to craft prompts for listing all SharePoint Agents within a SharePoint site proved challenging, as Copilot struggled to directly retrieve this information. However, SharePoint Agent files are essentially standard files with `.agent` or `.copilot` extensions, typically stored in SharePoint libraries or the Site Assets library. This realization sparked the idea of using a declarative agent with the Microsoft Graph API as a Copilot plugin. The Microsoft Graph Search API, in this context, not only facilitates the discovery of SharePoint Agents but also provides broader capabilities for finding files across SharePoint.

### Microsoft Graph Open API spec file

For plugins to function, OpenAPI specifications are essential. Given the vastness of the full Microsoft Graph API specification, a performance-optimized approach was adopted by initially focusing on the Search.yml file.

You can download Search.yml from [Search.yml](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/openApiDocs/v1.0/Search.yml). Save this file within your agent solution, for example, in a folder named apis/.

Subsequently, the necessary operations were extracted from the Search.yml specification. Even after this initial selection, the API specification remained comprehensive. For our specific use case, only the /search/query endpoint is relevant.

The extracted and prepared operations are available in the graph_search.yaml file, located in the GitHub repository's apis/ folder.


### Generate the Copilot plugin
We need to generate the plugin information that we will be used by the Copilot agent, The Kotia tool will help us achieve that. If not installed already, install the [the Kiota extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-graph.kiota). 

>Why using Kiota instead of the built-in Teams ToolKit plugin generation? The default Teams Toolkit plugin generation can struggle to generate plugins for complex property types like arrays or nested objects. Using Kiota will ensure the plugin information is generated correctly.

To generate the plugin:

1. In the Kiota options, add a new API description. Select Browse path and choose the graph_search.yml file.

2. Kiota will load all available endpoints from the file. Add the desired endpoint (by clicking the '+' sign on the POST line), then click Generate.

3. Select Copilot Plugin and assign a name, such as graphsearch.

![Generate the Copilot plugin](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot01.png)<br><br>
![Generate the Copilot plugin](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot02.png)<br><br>
![Generate the Copilot plugin](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot03.png)<br><br>
![Generate the Copilot plugin](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot04.png)<br><br>
![Generate the Copilot plugin](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot05.png)<br><br>
![Generate the Copilot plugin](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot06.png)<br><br>
![Generate the Copilot plugin](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot07.png)<br><br>

### Import the plugin with Microsoft 365 Agents Toolkit

Within Visual Studio code, navigate to the Microsoft 365 Agents Toolkit options. Use the `Add actions` option and select `Import an existing plugin`. Choose the graphsearch-openapi.json manifest file (.json) and the OpenAPI specification (.yml) from the plugin information generated by Kiota. Finally, select the declarative agent manifest file to integrate the plugin.

Upon completion, you should observe the ai-plugin.json file and a new folder named apiSpecificationFile containing two files: openapi.yaml and openapi.yaml.original. These files will be located under the appPackage folder.

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot08.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot09.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot10.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot11.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot12.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot13.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot14.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot15.png)<br><br>

![Import the plugin with Microsoft 365 Agents Toolkit](/assets/img/posts/2025-05-17-Build-SharePoint-Agents-Finder-Declarative-Agent/Screenshot16.png)<br><br>

### Update openapi.yaml:
   - Ensure you open this file `appPackage\apiSpecificationFile\openapi.yaml` and update the `tenantid` with your tenant Id.
   ```
     securitySchemes:
    azureaadv2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://login.microsoftonline.com/tenantid/oauth2/v2.0/authorize
          tokenUrl: https://login.microsoftonline.com/tenantid/oauth2/v2.0/token
   ```

### Prerequisites

1. **Entra ID App Registration**:
   - Register an Entra ID application.
   - Assign the Graph API permissions: `Files.Read.All` as delegated permissions.
   - Retrieve the `ClientId`, `ClientSecret`, and `TenantID`.
   - Add the https://teams.microsoft.com/api/platform/v1.0/oAuthRedirect as a redirect URL for web platform in the Authentication settings.

2. **Teams developer portal**:
   - Open [Teams developer portal](https://dev.teams.microsoft.com/)
   - Add a new OAuth client registration 
   - Register a new client with the following information 
        - Registration name: da-sharepoint-agents-finder
        - Base URL: https://graph.microsoft.com/v1.0
        - Restrict usage by org: My organization only
        - Restrict usage by app: Any Teams app (when agent is deployed, use the Teams app ID).

   - OAuth settings
        - Client ID: <the entra ID application ID>
        - Client secret: <the Entra ID application secret>
        - Authorization endpoint `(replace tenantid by your own value)`: https://login.microsoftonline.com/tenantid/oauth2/v2.0/authorize
        - Token endpoint `(replace tenantid by your own value)`: https://login.microsoftonline.com/tenantid/oauth2/v2.0/token
        - Refresh endpoint `(replace tenantid by your own value)`: https://login.microsoftonline.com/tenantid/oauth2/v2.0/refresh
        - Scope: Files.Read.All
        - Save the information. 
        - A new OAuth registration key will be generated. Save it in secure place to be added in `.env.dev`

### Minimal path to awesome
1. **Clone the Repository**:
   - Clone this repository to your local machine.

2. **Navigate to Solution Folder**:
   - Ensure you are in the solution folder of the cloned repository.

3. **Update openapi.yml**:
   - Ensure you open this file `appPackage\apiSpecificationFile\openapi.yaml` and update the `tenantid` with your tenant Id.
   ```
     securitySchemes:
    azureaadv2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://login.microsoftonline.com/tenantid/oauth2/v2.0/authorize
          tokenUrl: https://login.microsoftonline.com/tenantid/oauth2/v2.0/token
   ```
3. **Update .env.dev file**:
   - Ensure you open this file and update this `AZUREAADV2_REGISTRATION_ID` key with OAuth registration key generated in the prerequisites

### Update prompt instructions to call the plugin
Update the instructions.txt file with the following prompt

```
The agent should search for agent-related files or files that have extensions [.agent] or [.copilot] under the "SiteAssets/Copilots" folder within the given SharePoint site.
It should list agent files based on the query from SharePoint, ensuring that results are relevant to the user's request.
It should return agent files categorized by approval status, taking into consideration that agent files under "SiteAssets/Copilots" are not approved but the ones under "SiteAssets/Copilots/Approved" are the approved ones.

Ensure that the results returned are the results that the user has access to otherwise, respond to the user frindly that he/she doesn't have access.
The agent should provide clear and concise responses in a friendly and professional tone.

The agent should respond with a table containing the following details:
File Name
Created Date
Modified Date
Link to the Agent File

- For all queries, complement your answer by calling the 'action_1' plugin with the following API body format and by replacing the {query} token by the user query transformed as search keywords and translated to English.
Use no more than 3 keywords enclosed by double quotes and separated by an 'OR' condition, for example "maternity leave" OR "benefits" OR "vacation".
{
  "requests": [
    {
      "entityTypes": [
        "driveItem"
      ],
      "query": {
        "queryString": "{query}"
      }
    }
  ]
}
```

### Key Features
- **Interactive Chat experience**: The SharePoint Agents Finder declarative agent offers a seamless and interactive chat experience within the Microsoft 365 Copilot chat interface.
- **Graph API Integration**: Utilizes the Microsoft Graph API as a Copilot plugin in a declarative agent.
- **Querying Microsoft 365 and SharePoint content**: Querying Microsoft 365 and SharePoint content delivering structured responses in table format with links to files.
- **SharePoint Agents Finder or SharePoint Agents doc finder**: As long as the agent utilizes the Microsoft Graph API search then it could find not only SharePoint Agents but also any document in Microsoft 365.

### Conclusion 🎯
This SharePoint Agents Finder agent is step forward for using SharePoint content with Microsoft 365 Copilot. By using the Microsoft Graph Search API, we've made it much easier for find SharePoint Agents and other important documents. The guide covers everything from setting up the plugin to configuring Entra ID, giving you a solid plan to get it working. This not only makes finding info quicker but also shows off what's possible with declarative agents and the Microsoft Graph API, helping you get more out of Copilot and access crucial company knowledge.


## Potential Improvements
- **Table of Responses**: Table of Responses is a potential improvement that could be improved through the instructions
- **SharePoint Agent Link**: SharePoint Agent Link should be improved to either direct you to the agent or the link of the agent should appear in the SiteAssets library.

### Addition information and references
- [Declarative agents for Microsoft 365](https://learn.microsoft.com/en-us/microsoft-365-copilot/extensibility/overview-declarative-agent?wt.mc_id=MVP_433449)
- [Copilot developer Camp](https://aka.ms/copilotdevcamp?wt.mc_id=MVP_433449)
- [Microsoft 365 Copilot pro-developer samples](https://github.com/pnp/copilot-pro-dev-samples/)
- [Microsoft 365 Copilot extensibility](https://learn.microsoft.com/en-us/collections/p11pb18wmq6xqq?wt.mc_id=MVP_433449)
- [Use the Microsoft Graph API as a Copilot plugin for a declarative agent](https://blog.franckcornu.com/post/copilot-graph-api-qna-plugin/)

### GitHub Repos
* [SharePoint Agents Finder declarative agent - GitHub Repo](https://github.com/mohammadamer/da-sharepoint-agents-finder)











