---
title: Exploring Microsoft Graph Search API to query SharePoint data
date: 2023-12-02 20:14 +0300
categories: [Microsoft Graph, Graph Search API, SharePoint Search, Keyword Query Language (KQL)]
tags: [Microsoft Graph, Microsoft Graph Explorer, Graph Search API, SharePoint Online, SharePoint Search, Keyword Query Language (KQL)]
---

## Exploring Microsoft Graph Search API to query SharePoint data

### Microsoft Search
Microsoft Search is an enterprise search engine that delivers productivity gains and relevant search results for your organization. Microsoft Search is available in various experiences including Office, SharePoint, Delve, Windows, and Bing. You can use the Microsoft Search API in Microsoft Graph to extend Microsoft Search to your apps.

### Why use the Microsoft Search API?
The Microsoft Search API provides one unified search endpoint that you can use to query data in the Microsoft cloud—messages and events in Outlook mailboxes and files on OneDrive and SharePoint that Microsoft Search already indexes.

The Microsoft Search API allows for sophisticated queries, enabling users to find information based on keywords, filters, and entity types. This richness in query capabilities ensures more accurate and relevant search results.

### What data can I add or access by using the Microsoft Search API?
The Microsoft Search API supports searching the following content in the Microsoft cloud:

1. >SharePoint and OneDrive files and folders (driveItem resources), list, listItem, site, and drive resources. `This will be our focus in this Article`
2. Outlook email message and calendar event resources.
3. Person resources in an organization who are most relevant to a user.
4. Content ingested through the Microsoft Graph connectors platform: externalItem resources.
5. Administrative search answer resources: acronyms, bookmarks, and QnA resources.

### Search API Endpoint
Try **Microsoft Graph Explorer**, your playground to try APIs on your tenant and explore capabilities [aka.ms/g-explorer](https://aka.ms/g-explorer)
```
https://graph.microsoft.com/v1.0/search/query
```

### Common use cases
The query method in the Microsoft Search API enables searching across your data in Microsoft Search. To perform a search, you provide a searchRequest in the request body, specifying the details of your search.

The query method is utilized in various scenarios depending on the properties and parameters configured in the searchRequest body. This flexibility allows for customization based on specific use cases.

It's important to note that search requests are executed on behalf of the user, and the search results are subject to the applicable access control policies. For instance, when dealing with files, the permissions associated with the files are taken into account during the search request. The scope of search results is constrained to align with the access permissions, ensuring that users cannot retrieve more items in a search than what they would be able to access through a corresponding GET operation with identical permissions and access controls.

#### Scope search based on entity types
Define the scope of the search request using the entityTypes property in the query request payload. The types available to query:

- **chatMessage** Teams messages.
- **message**	Email messages.
- **event**	Calendar events.
- **drive**	Document libraries.
- **driveItem**	SharePoint and OneDrive	Files, folders, pages, and news.
- **list**	SharePoint and OneDrive	Lists. Note that document libraries are also returned as lists.
- **listItem**	SharePoint and OneDrive	List items. Note that files and folders are also returned as list items; listItem is the super class of driveItem.
- **site**		Sites in SharePoint.
- **Bookmarks**		Microsoft Search bookmarks in your organization.
- **Acronyms** Microsoft Search acronyms in your organization.
```
{
    "requests": [
        {
            "entityTypes": [
                "chatMessage", // Permission scope required to access the items: Chat.Read, Chat.ReadWrite, ChannelMessage.Read.All
                "message",     // Permission scope required to access the items: Mail.Read, Mail.ReadWrite
                "event",       // Permission scope required to access the items: Calendars.Read, Calendars.ReadWrite
                "drive",       // Permission scope required to access the items: Files.Read.All, Files.ReadWrite.All, Sites.Read.All, Sites.ReadWrite.All
                "driveItem",   // Permission scope required to access the items: Files.Read.All, Files.ReadWrite.All, Sites.Read.All, Sites.ReadWrite.All
                "listItem",    // Permission scope required to access the items: Sites.Read.All, Sites.ReadWrite.All
                "list",        // Permission scope required to access the items: Sites.Read.All, Sites.ReadWrite.All
                "site",        // Permission scope required to access the items: Sites.Read.All, Sites.ReadWrite.All
                "Bookmarks",   // Permission scope required to access the items: Bookmark.Read.All
                "Acronyms"     // Permission scope required to access the items: Acronym.Read.All
            ],
            "query": {
                "queryString": "contoso"
            }
        }
    ]
}
```

#### Paging
Control pagination of the search results by specifying the following two properties in the query request body:
- **from** - An integer that indicates the 0-based starting point to list search results on the page. The default value is 0.
- **size** - An integer that indicates the number of results to be returned for a page. The default is 25 results. The maximum is 1000 results.

Note the following limits if you're searching the **event** or **message** entity:
- >**from** must start at zero in the first page request; otherwise, the request results in an HTTP 400 Bad request.
- >The maximum number of results per page (size) is 25 for **message** and **event**.
The upper limit for SharePoint or OneDrive items is 1000. A reasonable page size is 200. A larger page size generally incurs higher latency.

```
{
    "requests": [
        {
            "entityTypes": [
                "driveItem"
            ],
            "query": {
                "queryString": "contoso"
            },
            "from": 0, //starting point to list search results on the page. The default value is 0.
            "size": 15 //the number of results to be returned for a page. The default is 25 results. The maximum is 1000 results.
        }
    ]
}
```

#### Get selected properties
When searching an entity type, such as **message**, **event**, **drive**, **driveItem**, **list**, **listItem**, **site**, **externalItem**, you can include in the fields property specific entity properties to return in the search results. This is similar to using the OData system query option, $select in REST requests. The search API does not technically support these query options because the behavior is expressed in the POST body.

For all these entity types, specifying the fields property reduces the number of properties returned in the response, optimizing the payload over the wire.

The **listItem** and **externalItem** entities are the only supported entities that allow getting extended retrievable fields configured in the schema. You cannot retrieve extended properties from all the other entities by using the search API. For example, if you created a retrievable field for externalItem in the search schema, or if you have a retrievable custom column on a listItem, you can retrieve these properties from search. To retrieve an extended property on a file, specify the listItem type in the request.

If the fields specified in the request are either not present in the schema, or not marked as retrievable, they will not be returned in the response. Invalid fields in the request are silently ignored.

>If you do not specify any fields in the request, you will get the default set of properties for all types. For extended properties, listItem and externalItem behave differently when no fields are passed in the request:
- **listItem** will not return any custom field.
- **externalItem** will return all the fields marked with the retrievable attribute in the Microsoft Graph connector schema for that particular connection.

**Sample Request**
```
{
    "requests": [
        {
            "entityTypes": [
                "driveItem"
            ],
            "query": {
                "queryString": "Contoso"
            },
            "fields": [
                "title",
                "ListItemId",
                "ListID",
                "Filename",
                "WebUrl",
                "SiteID",
                "WebId",
                "Author",
                "LastModifiedTime"
            ],
            "from": 0,
            "size": 15
        }
    ]
}
```

**Sample response**
```
{
    "value": [
        {
            "searchTerms": [
                "contoso"
            ],
            "hitsContainers": [
                {
                    "hits": [
                        {
                            "hitId": "01A5XVIJYDETAIZxxxxxxxxxaaaaaaaaa",
                            "rank": 1,
                            "summary": "",
                            "resource": {
                                "@odata.type": "#microsoft.graph.driveItem",
                                "listItem": {
                                    "@odata.type": "#microsoft.graph.listItem",
                                    "fields": {
                                        "title": "contoso",
                                        "listItemId": "8",
                                        "listID": "a764353c-xxxx-xxxx-xxxx-dfc6c74ef2a2",
                                        "filename": "contoso",
                                        "siteID": "ab9a1905-xxxx-xxxx-xxxx-247d8c436072",
                                        "webId": "02b514ba-xxxx-xxxx-xxxx-10ab5420d192",
                                        "author": "Mohammad Amer",
                                        "lastModifiedTime": "2023-08-28T12:43:25Z"
                                    },
                                    "id": "8cc02403-xxxx-xxxx-xxxx-52d23e078433"
                                },
                                "webUrl": "https://abc.sharepoint.com/sites/xyz/SiteAssets/contoso"
                            }
                        }
                    ],
                    "total": 1000,
                    "moreResultsAvailable": true
                }
            ]
        }
    ],
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#Collection(microsoft.graph.searchResponse)"
}
```

#### Sorting
Search results in the response are sorted in the following default sort order:

- message and event are sorted by date.
- All SharePoint, OneDrive, person and connector types are sorted by relevance.
The query method lets you customize the search order by specifying the sortProperties on the requests parameter, which is a collection of sortProperty objects. This allows you to specify a list of one or more sortable properties and the sort order.

> Note that sorting results is currently only supported on the following SharePoint and OneDrive types: driveItem, listItem, list, site.

The properties on which the sort clause are applied need to be sortable in the SharePoint search schema. If the property specified in the request is not sortable or does not exist, the response will return an error, `HTTP 400 Bad Request`. Note that you cannot specify to sort documents by relevance using sortProperty.

When specifying the name of a sortProperty object, you can either use the property name from the Microsoft Graph type (for example, in driveItem), or the name of the managed property in the search index.

See [sort search results](https://learn.microsoft.com/en-us/graph/search-concept-sort) for examples that show how to sort results.

```
{
  "requests": [
    {
      "entityTypes": [
          "driveItem"
      ],
       "query": {
        "queryString": "contoso"
      },
      "sortProperties": [
          {
              "name": "CreatedDateTime",
              "isDescending": false
          }
      ]
    }
  ]
}
```

### Keyword Query Language (KQL) support
Specify free text keywords, operators (such as `AND`, `OR`), and property restrictions in KQL syntax in the actual search query string (query property of the query request body). The XRANK operator boosts the dynamic rank of items based on certain term occurrences within the match expression, without changing which items match the query. The syntax and command depend on the entity types (in the entityTypes property) you target in the same query request body.

Depending on the entity type, the searchable properties vary. For details, see:
- [Email properties](https://learn.microsoft.com/en-us/purview/ediscovery-keyword-queries-and-search-conditions#searchable-email-properties)
- [Site properties](https://learn.microsoft.com/en-us/purview/ediscovery-keyword-queries-and-search-conditions#searchable-site-properties)


### Known limitations
The search API has the following limitations:
The query method is defined to allow passing a collection of one or more searchRequest instances at once. However, the service currently supports only a single searchRequest at a time.
The searchRequest resource supports passing multiple types of entities at a time. The following table lists the combinations that are supported.
The contentSource property, which defines the connection to use, is only applicable when entityType is specified as externalItem.

- >The search API does not support custom sort for message, chatMessage, event, person, or externalItem.
- >The search API does not support aggregations for message, event, site or drive.
- >The search API does not support xrank for message, chatMessage, event, person, or externalItem.
- >Customizations in SharePoint search, such as a custom search schema or result sources, can interfere with Microsoft Search API operations.
- >The search API doesn't support the site-level search schema. Use the tenant-level or default search schema.

### Search tips and tricks
- The time zone for all searches is Coordinated Universal Time (UTC). Changing time zones for your organization isn't currently supported. Time zone display settings in the search view are only for applicable for values in Data column and don't affect time stamps on collected items.
- Using quotes stops wild cards and any operations inside the quotes.
- >Keyword searches aren't case-sensitive. For example, **cat** and **CAT** return the same results.
- >The Boolean operators **AND**, **OR**, **NOT**, and **NEAR** must be uppercase.
- >A space between two keywords or two `property:value` expressions is the same as using **OR**. For example, `from:"Sara Davis" subject:reorganization` returns all messages sent by Sara Davis or messages that contain the word reorganization in the subject line. However, using a mix of spaces and **OR** conditionals in a single query may lead to unexpected results. We recommend using either spaces or **OR** in a single query.
- >When searching sites, you have to add the trailing `/` to the end of the URL when using the path property to return only items in a specified site. If you don't include the trailing `/`, items from a site with a similar path name will also be returned. For example, if you use     `path:sites/HelloWorld` then items from sites named `sites/HelloWorld_East` or  `sites/HelloWorld_West` would also be returned. To return items only from the HelloWorld site, you have to use `path:sites/HelloWorld/`.
- Use syntax that matches the `property:value format`. Values aren't case-sensitive, and they can't have a space after the operator. If there's a space, your intended value is a full-text search. For example `to: pilarp` searches for "pilarp" as a keyword, rather than for messages sent to pilarp.
- When searching a recipient property, such as To, From, Cc, or Recipients, you can use an SMTP address, alias, or display name to denote a recipient. For example, you can use pilarp@contoso.com, pilarp, or "Pilar Pinilla".
- You can use only prefix searches; for example, **cat*** or **set***. Suffix searches (*cat), infix searches (c*t), and substring searches (*cat*) aren't supported.
- When searching a property, use double quotation marks (" ") if the search value consists of multiple words. For example, `subject:budget Q1` returns messages that contain budget in the subject line and that contain Q1 anywhere in the message or in any of the message properties. Using   `subject:"budget Q1"` returns all messages that contain budget Q1 anywhere in the subject line.
To exclude content marked with a certain property value from your search results, place a minus sign (-) before the name of the property. For example, `-from:"Sara Davis"` excludes any messages sent by Sara Davis.
- You can export items based on message type. For example, to export Skype conversations and chats in Microsoft Teams, use the syntax `kind:im`. To return only email messages, you would use `kind:email`. To return chats, meetings, and calls in Microsoft Teams, use `kind:microsoftteams`.
- The Query **language-country/region** must be defined in your search query prior to collecting content.
- When searching the **Sent** folders for emails, using the SMTP address for the sender isn't supported. Items in the **Sent** folder contain only display names.

#### Searching for results in a particular site or hub
As one of the known limitation is that  `search API doesn't support the site-level search schema` and it's really important in some cases to limit the returned results and get results only from specific site or hub by using `SiteID:site-id` or `DepartmentId:depatment-id` or   `path:sites/HelloWorld/` in the query string.

Get results from specific SharePoint site by specifing the `SiteID` in the query string:
```
{
    "requests": [
        {
            "entityTypes": [
                "listItem",
                "driveItem",
                "site",
                "list"
            ],
            "query": {
                "queryString": "contoso (SiteID:abcd893f-abcd-abcd-bbc6-abcd27826487)"
            }
        }
    ]
}
```

Get results from specific SharePoint HUB and the associated sites by specifing the `DepartmentId` in the query string:
```
{
    "requests": [
        {
            "entityTypes": [
                "listItem",
                "driveItem",
                "site",
                "list"
            ],
            "query": {
                "queryString": "contoso (DepartmentId:abcdabcd-abcd-abcd-bbc6-abcd2782abcd)"
            }
        }
    ]
}
```
#### Searching for news articles
```
{
    "requests": [
        {
            "entityTypes": [
                "listItem"
            ],
            "query": {
                "queryString": "contoso PromotedState:2"
            }
        }
    ]
}
```

#### Searching for video files
```
{
    "requests": [
        {
            "entityTypes": [
                "listItem",
                "driveItem"
            ],
            "query": {
                "queryString": "contoso filetype:mp4"
            }
        }
    ]
}
```

### References
* [Overview of Microsoft Search](https://learn.microsoft.com/en-us/microsoftsearch/overview-microsoft-search)
* [Overview of the Microsoft Search API in Microsoft Graph](https://learn.microsoft.com/en-us/graph/search-concept-overview)
* [Use the Microsoft Search API to query data](https://learn.microsoft.com/en-us/graph/api/resources/search-api-overview?view=graph-rest-1.0)
* [Github Practical Example: Teams Message Extension that leverages Microsoft Graph Search API](https://github.com/mohammadamer/msgext-graph-search)