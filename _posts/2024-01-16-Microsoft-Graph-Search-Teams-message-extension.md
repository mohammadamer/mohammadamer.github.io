---
title: Microsoft Graph Search Teams message extension
date: 2024-01-16 20:14 +0300
categories: [M365, Teams message Extension, Microsoft Graph Search API, Microsoft Teams Developmen]
tags: [M365, Teams message Extension, Microsoft Graph Search API, MS Graph, MS Teams] 
---

## Microsoft Graph Search Teams message extension

### Overview
Microsoft Teams message extensions are a powerful tool that can enhance the messaging functionality of Teams. With message extensions, users can easily search for and share information through rich cards, capture data, and preview app content right within Teams. One way to take advantage of this functionality is by building a custom Teams message extension app that leverages the Graph Search API.

The Graph Search API allows to access data from across the Microsoft 365 ecosystem, including data from SharePoint, OneDrive, and Exchange. By using the Graph Search API in a Teams message extension app, you can create powerful search experiences that allow users to quickly find and share information from across their organization.

![Microsoft Graph Search Teams message extension - Screenshot](/assets/img/posts/2024-01-16-Microsoft-Graph-Search-Teams-message-extension/Microsoft-Graph-Search-Teams-message-extension01.png)

### Why Teams message extensions?
Teams message extensions play a pivotal role in enhancing collaboration within Microsoft Teams. It empowers users to seamlessly search for and share information, capture data, and preview app content directly within the Teams interface. By leveraging the Graph Search API, these extensions enable users to access data across the Microsoft 365 ecosystem, including SharePoint, OneDrive, and Exchange.

The significance lies in the streamlined communication and information retrieval process. Users can quickly find and share relevant data without leaving the Teams environment, fostering a more efficient and collaborative workflow. Whether it's searching for documents, files, news, list items, messages, or events, Teams message extensions provide a unified and real-time search experience.

In essence, Teams message extensions contribute to a more cohesive and productive collaborative environment by simplifying information access and sharing, ultimately improving the overall teamwork and efficiency of organizations using Microsoft Teams.

### What is Microsoft Graph Search message extension custom app?
Imagine a Teams message extension app that allows users to search for documents and files stored in SharePoint and OneDrive. With the Graph Search API, the app could provide users with real-time search results, allowing them to quickly find and share the information they need. Additionally, the app could use rich cards to display previews of the documents and files, making it easy for users to see the content before sharing it with others.

### How to build message extensions?
The Prerequisites that you'll only need are Teams Toolkit and a developer tenant to develop an app. Rabia Williams explained how to build message extensions in details in this article [Build message extensions for Microsoft Teams and Copilot](https://devblogs.microsoft.com/microsoft365dev/build-message-extensions-for-microsoft-teams-and-copilot/)

### Explaining msgext-graph-search
After our message extensions app has been scaffolded `msgext-graph-search`

#### How I want the message extension to look like?
I wanted the message extension to have five tabs which meaning that there should be five respective commands with five unique Ids.
1. SearchFiles
2. SearchNews
3. SearchListItems
4. SearchMessages
5. SearchEvents

```
  "composeExtensions": [
    {
      "botId": "${{BOT_ID}}",
      "commands": [
        {
          "id": "SearchFiles",
          "type": "query",
          "title": "Files",
          "description": "Search Files",
          "initialRun": false,
          "fetchTask": false,
          "context": ["commandBox", "compose", "message"],
          "parameters": [
            {
              "name": "queryString",
              "title": "Query",
              "description": "Query string text",
              "inputType": "text",
              "choices": []
            }
          ]
        },
        {
          "id": "SearchNews",
          "type": "query",
          "title": "News",
          "description": "Search News",
          "initialRun": false,
          "fetchTask": false,
          "context": ["commandBox", "compose", "message"],
          "parameters": [
            {
              "name": "queryString",
              "title": "Query",
              "description": "Query string text",
              "inputType": "text",
              "choices": []
            }
          ]
        },
        {
          "id": "SearchListItems",
          "type": "query",
          "title": "List items",
          "description": "Search SharePoint list items",
          "initialRun": false,
          "fetchTask": false,
          "context": ["commandBox", "compose", "message"],
          "parameters": [
            {
              "name": "queryString",
              "title": "Query",
              "description": "Query string text",
              "inputType": "text",
              "choices": []
            }
          ]
        },
        {
          "id": "SearchMessages",
          "type": "query",
          "title": "Messages",
          "description": "Search Outlook messages",
          "initialRun": false,
          "fetchTask": false,
          "context": ["commandBox", "compose", "message"],
          "parameters": [
            {
              "name": "queryString",
              "title": "Query",
              "description": "Query string text",
              "inputType": "text",
              "choices": []
            }
          ]
        },
        {
          "id": "SearchEvents",
          "type": "query",
          "title": "Events",
          "description": "Search Events",
          "initialRun": false,
          "fetchTask": false,
          "context": ["commandBox", "compose", "message"],
          "parameters": [
            {
              "name": "queryString",
              "title": "Query",
              "description": "Query string text",
              "inputType": "text",
              "choices": []
            }
          ]
        }
      ]
    }
  ],
```

#### Mapping message extension commands and Graph Search API entities
I have created enums to map the message extension commands and Graph Search API entities in two different enums
```
export enum CommandIds {
  SearchEvents = 'SearchEvents',
  SearchFiles = 'SearchFiles',
  SearchListItems = 'SearchListItems',
  SearchMessages = 'SearchMessages',
  SearchNews = 'SearchNews',
}
```

```
export enum EntityType {
  Event = 'event',
  Message = 'message',
  DriveItem = 'driveItem',
  ExternalItem = 'externalItem',
  Site = 'site',
  List = 'list',
  ListItem = 'listItem',
  Drive = 'drive',
  UnknownFutureValue = 'UnknownFutureValue',
}
```

#### Handling message extension query
If it's the first time for the end user to open the extension, then the user must authenticate thus obtain a token.
After that the user will be able to query Microsoft 356 data by leveraging Graph client using the authentication provider with the required Graph permission scops. By Choosing the message extension specific command, Mapping will occur to specify the proper entity type that should be passed to Graph API.
```
 public async handleTeamsMessagingExtensionQuery(context: TurnContext, query: MessagingExtensionQuery): Promise<any> {

    return await handleMessageExtensionQueryWithSSO(context, oboAuthConfig, initialLoginEndpoint, ["User.Read.All", "User.Read"],
     async (token: MessageExtensionTokenResponse) => {
        const credential = new OnBehalfOfUserCredential(token.ssoToken, oboAuthConfig);
        const attachments = [];
        if (query.parameters[0] && query.parameters[0].name === "initialRun") 
        {
          // Return empty preview Items on initial run
          return this.GetPreviewItems(attachments);
        } 
        else 
        {
          // Create an instance of the TokenCredentialAuthenticationProvider by passing the tokenCredential instance and options to the constructor
          const authProvider = new TokenCredentialAuthenticationProvider(credential, 
            {scopes: ["User.Read.All", "Files.Read.All", "Calendars.Read", "People.Read", "Sites.Read.All", "Mail.Read"]});

          // Initialize Graph client instance with authProvider
          const graphClient = Client.initWithMiddleware({authProvider: authProvider});
          
          //const searchContext = query.parameters[0].value;
          let searchContext: string = (query.parameters[0]?.value as string) ?? '';

          //Get the entity type from the commandId
          const entityType = this.getEntityType(query.commandId);
          
          //Add the PromotedState:2 filter in order to get only news posts in Search API
          if (query.commandId === CommandIds.SearchNews) {
            searchContext = `${searchContext} PromotedState:2`;
          }

          const searchResponse = {requests: [{ entityTypes: [entityType],
            query: {queryString: searchContext},
            fields: ['Id','title','name','subject','webURL','start','createdDateTime','start','end']
           }]};
          const results = await graphClient.api('/search/query').post(searchResponse);

          if (results != null && results.value.length > 0)
          {
              const hitsContainer = results.value[0].hitsContainers[0];
              const total = hitsContainer.total;
              const moreResultsAvailable = hitsContainer.moreResultsAvailable;
              const hits = hitsContainer.hits;

              for (const item of hits) {
                const title = this.GetThumbnailCardTitle(item, entityType);
                const text = this.GetThumbnailCardText(item, entityType);

                //messages and events have a different card format than files and list items, so we need to handle them differently
                const thumbnailCard = (entityType === EntityType.Message || entityType === EntityType.Event) ? 
                CardFactory.thumbnailCard(title, text) :
                CardFactory.thumbnailCard(title, text, undefined,
                  [{type: ActionTypes.OpenUrl, title: "View Item", value: item.resource.webUrl}]);

                attachments.push(thumbnailCard);
              }
          }
        }
        return {
          composeExtension: {
            type: "result",
            attachmentLayout: "list",
            attachments: attachments,
          },
        };
      }
    );
  }
```
#### Mapping a commandId to Graph Entitytype
```
  private getEntityType(commandId: string): EntityType {
    switch (commandId) {
      case CommandIds.SearchEvents:
        return EntityType.Event;
      case CommandIds.SearchFiles:
        return EntityType.DriveItem;
      case CommandIds.SearchListItems:
        return EntityType.ListItem;
      case CommandIds.SearchMessages:
        return EntityType.Message;
      case CommandIds.SearchNews:
        return EntityType.ListItem;
      default:
        return EntityType.UnknownFutureValue;
    }
  }
```
#### Adjusting thumbnail card's title based on the EntityType
```
  private GetThumbnailCardTitle(item: any, entityType: EntityType): string {
    if (entityType === EntityType.Event) {
      return item.resource.subject || "Unknown";
    } else if (entityType === EntityType.ListItem) {
        return item.resource.fields.title || "Unknown";
    } else if (entityType === EntityType.Message) {
        return item.resource.subject || "Unknown";
    } else if (entityType === EntityType.DriveItem) {
        return item.resource.name || "Unknown";
    } else {
        return "Unknown";
    }
  }
```
#### Adjusting thumbnail card's text based on the EntityType
```
  private GetThumbnailCardText(item: any, entityType: EntityType): string {
    if (entityType === EntityType.Event) {
        return `Start: ${item.resource.start.dateTime}` || "Unknown";
    } else if (entityType === EntityType.ListItem) {
        return `Created: ${item.resource.createdDateTime}` || "Unknown";
    } else if (entityType === EntityType.Message) {
        return `Created: ${item.resource.createdDateTime}` || "Unknown";
    } else if (entityType === EntityType.DriveItem) {
        return `Created: ${item.resource.createdDateTime}` || "Unknown";
    } else {
        return "Unknown";
    }
  }
```

### Conclusion
Overall, Microsoft Teams message extensions and the Graph Search API provide developers with a powerful set of tools for creating custom search experiences within Teams. By leveraging these tools, developers can create apps that help users find and share information more easily, improving collaboration and productivity within their organization.

### Challenges
* Have you ever thought about searching for BI Dashboards that are shared with you using Microsoft Graph API?
* Is it possible to search for Microsoft learn modules using Microsoft Graph API? 

### References:
* [Build message extensions for Microsoft Teams and Copilot](https://devblogs.microsoft.com/microsoft365dev/build-message-extensions-for-microsoft-teams-and-copilot/)
* [Teams Toolkit Overview](https://learn.microsoft.com/en-us/microsoftteams/platform/toolkit/teams-toolkit-fundamentals?pivots=visual-studio-code-v5)
* [Build message extensions](https://learn.microsoft.com/en-us/microsoftteams/platform/messaging-extensions/what-are-messaging-extensions)
* [Github Repo](https://github.com/mohammadamer/msgext-graph-search)