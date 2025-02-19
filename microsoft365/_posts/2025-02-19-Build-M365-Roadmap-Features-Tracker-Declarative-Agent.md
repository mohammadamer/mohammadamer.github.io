---
layout: post
date: 2025-02-19 21:14 +0300
#categories: [Microsoft 365 Copilot, Microsoft Graph Connectors]

#categories: [M365, Microsoft Graph connectors, Microsoft 365 copilot, m365 development]
# tags: [M365, Microsoft Graph connectors, Microsoft 365 copilot, MS Graph]

title: Build A M365 Roadmap Features Tracker Declarative Agent
image: /assets/img/posts/2025-02-17-Build-M365-Roadmap-Features-Tracker-Declarative-Agent/m365rroadmap-features-tracker-declarative-agent.jpeg
description: >
  After attending the Microsoft Copilot Developer Camp, I got inspired to build my own army of Declarative Agents-making work smarter, faster, and more efficient!
  I started with my first Declarative Agent, a fun GeoLocator Game demo. But I didn’t stop there—I took on a bigger challenge and built my second Declarative Agent, and it was an exciting journey!
---

# Build A M365 Roadmap Features Tracker Declarative Agent

* toc
{:toc}


### Overview
Staying updated with Microsoft 365 features and changes is crucial for all people who are working with Microsoft for productivity and collaboration. With the rapid updates of Microsoft 365, tracking feature rollouts and enhancements across tools like Microsoft Teams, SharePoint, and Outlook can be a challenging task.

I Inspired by the Microsoft Copilot Developer Camp, I decided to take advantage of Declarative Agents to automate and simplify this process. This led me to create the M365 Roadmap Features Tracker Declarative Agent — a smart, efficient solution that keeps teams informed about the latest updates.


### The idea
After diving into the Microsoft Copilot Developer Camp [Extend Path - Extend Microsoft 365 Copilot](https://microsoft.github.io/copilot-camp/pages/extend-m365-copilot/). Built the first demo the GeoLocator Game Declarative Agent, I wanted to take on a more practical and impactful challenge. This brought me to the idea of building an agent that could provide updates on Microsoft 365 Roadmap features.

### What is Declarative Agent and Why?
Declarative agents leverage the same scalable infrastructure and platform of Microsoft 365 Copilot, You can tailor your agent specifically to meet specific needs on a special area of your needs. They function as subject matter experts in a specific area or business need, allowing you to use the same interface as a standard Microsoft 365 Copilot chat while ensuring they focus exclusively on the specific task.
I would recommend you to go through the the first [lab Lab E1](https://microsoft.github.io/copilot-camp/pages/extend-m365-copilot/01-declarative-copilot/) before build our sample project.

Declarative Agents offer a new way to automate complex workflows with minimal coding. You can rapidly develop smart assistants to tackle repetitive and data-driven tasks. Declarative Agents have seveal capabilities that allow you to specify the knowledge base it should access. They are called capabilities and there are five types of capabilities supported at the time of writing this article.

- Microsoft Graph Connectors - Pass connections of Graph connectors into the agent, allowing the agent to access and utilize the connector's knowledge.
- OneDrive and SharePoint - Provides URLs of files and sites to agent, for it to gain access to those contents.
- Web search - Enables or disables web content as part of the agent's knowledge base.
- Code interpreter - Enables the capabilities for better solve the Math problem and leverage Python code to do the complex data analysis or generate chart if needed.
- GraphicArt - Enable the agent to support image or video generation by using DALL-e capability.

Our focus in this article will be to leverage Microsoft Graph Connector to ingest the external content of Microsoft 365 features updates then specify it as knowledge base for our agent.


This agent is part of my mission to build a "little army" of Declarative Agents, each designed to make work smarter, faster, and more efficient. The M365 Roadmap Features Tracker is the second agent in my growing collection, and the journey has been both fun and insightful!

### Project Goal: M365 Roadmap Features Tracker
The M365 Roadmap Features Tracker agent is part of my broader mission to build a "little army" of Declarative Agents. Each agent is designed to automate and enhance specific business workflows.

This tracker integrates Graph Connectors to monitor and deliver the latest Microsoft 365 updates—offering a hands-free solution for staying informed.

### Prerequisites
- Graph Connector for M365 roadmap features

To follow along, you’ll need to set up a Graph Connector for Microsoft 365 Roadmap features. I’ve detailed this process in a prior article [Ingest M365 Roadmap content in Microsoft 365 using Microsoft Graph connectors](https://mohamadamer.com/blog/microsoft365/2024-02-18-Ingest-M365-Roadmap-Content-in-Microsoft-365-Using-Microsoft-Graph-Connectors/)

### Scaffold a declarative agent from template

![declarative agent from template](/assets/img/posts/2025-02-17-Build-M365-Roadmap-Features-Tracker-Declarative-Agent/01-create-new-app.png)


![declarative agent from template](/assets/img/posts/2025-02-17-Build-M365-Roadmap-Features-Tracker-Declarative-Agent/02-copilot-extension.png)


![declarative agent from template](/assets/img/posts/2025-02-17-Build-M365-Roadmap-Features-Tracker-Declarative-Agent/03-app-feature.png)


![declarative agent from template](/assets/img/posts/2025-02-17-Build-M365-Roadmap-Features-Tracker-Declarative-Agent/04-type.png)


![declarative agent from template](/assets/img/posts/2025-02-17-Build-M365-Roadmap-Features-Tracker-Declarative-Agent/05-folder.png)

### App files

| Folder/File                       | Contents     |
|-----------------------------------|----------------------------|
| .vscode                           | VSCode files for debugging                   | 
| appPackage                        | Templates for the Teams application manifest, the agent manifest, and the API specification, if any   | 
| env                               | Environment files with a default .env.dev file                     | 
| appPackage/color.png              | Application logo image                     |
| appPackage/outline.png            | Application logo outline image                   | 
| appPackage/declarativeAgent.json  | Defines settings and configurations of the declarative agent.  | 
| appPackage/instruction.txt        | Defines the behaviour of declarative agent.                     | 
| appPackage/manifest.json          |Teams application manifest that defines metadata for your declarative agent.                     |
|teamsapp.yml                       | Main Teams Toolkit project file. The project file defines two primary things: Properties and configuration Stage definitions. |

### Code Overview

I created the conversation starters using Copilot to have prompts that assist with beginning conversations.
Important thing here is that you have to build the Graph Connector, ingest M365 features updates then add the Graph Connector connection Id in the agent capabilities as the code shows.

To enable the agent to track roadmap features, you must:
- Build the Graph Connector
- Ingest Microsoft 365 Roadmap updates
- Add the Graph Connector ID to the agent’s capabilities

```
{
  "$schema": "https://developer.microsoft.com/json-schemas/copilot/declarative-agent/v1.2/schema.json",
  "version": "v1.2",
  "name": "M365 Roadmap Features Tracker",
  "description": "Declarative agent created with Teams Toolkit",
  "instructions": "$[file('instruction.txt')]",
  "conversation_starters": [
    {
      "title": "Discover What's New",
      "text": "Curious about the latest Microsoft 365 updates? I can summarize the newest features from the M365 Roadmap for you!"
    },
    {
      "title": "Track Feature Rollouts",
      "text": "Want to know which upcoming M365 features will impact your workflow? Let me check the roadmap and highlight key updates!"
    },
    {
      "title": "Plan for Upcoming Changes",
      "text": "I can help you prepare for upcoming Microsoft 365 changes by summarizing features that will roll out soon. Want a quick overview?"
    },
    {
      "title": "Stay Ahead with M365 Updates",
      "text": "Need to keep your team informed? I can provide a summary of the most important feature updates for Microsoft 365."
    },
    {
      "title": "Feature Deep Dive",
      "text": "Interested in a specific M365 feature? Let me find the latest details from the roadmap and explain how it might affect you."
    },
    {
      "title": "Your Personalized Update Report",
      "text": "Tell me which Microsoft 365 apps you use the most, and I’ll filter out the most relevant roadmap updates for you!"
    }
  ],
  "capabilities": [
    {
      "name": "GraphConnectors",
      "connections": [
        {
          "connection_id": "roadmapmicrosoft365"
        }
      ]
    }
  ]
}

```

I updated the instructions file with the help of M365 Copilot.

```
Welcome Message

Use this to introduce your capabilities:
Welcome to the Microsoft 365 Roadmap Tracker! I can help you stay updated on the latest feature releases, upcoming rollouts, and changes that may impact your workflow. Just tell me what you're looking for!

Search Instructions:
Feature Search by Keyword or Category
Prompt the user for a keyword, Microsoft 365 service (e.g., Teams, Outlook, SharePoint), or feature type (e.g., security updates, UI changes).
Query the Microsoft 365 Roadmap for relevant updates.
Extract key details such as feature name, description, rollout stage, and expected release date.

Response Format for any Roadmap feature updates, When providing update you must follow the following response format:
Example structured response for features results, respond accordingly following the response format:
Feature title: New Teams Meeting AI Enhancements
Status: Rolling Out
Expected Release: Q2 2024
Service: Microsoft Teams
View on Microsoft 365 Roadmap
Summary: AI-powered meeting summaries and action items will be available in Teams. This will help users catch up on missed meetings efficiently.

If no matching results are found:
I couldn't find any updates matching your search. Try using a broader keyword or selecting a different M365 service!

Filter by Status (Planned, Rolling Out, Launched, etc.)
Allow users to filter features by development stage:
Planned – Features in early development.
In Development – Actively being worked on.
Rolling Out – Being gradually released.
Launched – Fully available.

Example prompt:
Do you want updates on features that are Planned, In Development, or Rolling Out?
Filter by Release Timeline
Provide search options based on release periods (e.g., Next Month, Next Quarter, 2024 Features).

Example prompt:
Would you like updates for the next month, next quarter, or the entire year?
Region-Specific Availability
If applicable, check whether the feature is available in a specific region (e.g., Global, Europe, GCC).
If a feature is region-limited, display a message:
This feature is currently rolling out in [Region]. Let me know if you’d like updates on global availability!

Export & Notifications
Save & Share Updates
Offer to export the update list as a Markdown or CSV file.
Ask if the user wants a summary emailed to them.
Example prompt:
Would you like me to generate a summary file or send you a notification when new updates are available?

Weekly or Monthly Alerts
Allow users to set update alerts for specific services or feature categories.
Example prompt:
Do you want to receive weekly or monthly updates for new roadmap changes?

Next Steps Prompt
After providing the update:
Would you like to search for another feature, track a specific service, or explore upcoming releases?
```
Custom Logo
You can personalize your agent’s appearance by changing Color.png using the Microsoft 365 Visual Creator for logos. It should be 192*192.

manifest.json
I updated the manifest.json app version and the declarativeAgents with a unique Id

```
    "copilotAgents": {
        "declarativeAgents": [            
            {
                "id": "daM365RoadmapFeaturesTracker",
                "file": "declarativeAgent.json"
            }
        ]
    },
```

### Test the agent
You can easily deploy and test your agent using the Teams Toolkit in Visual Studio Code:
- Open the Teams Toolkit in VS Code.
- Under LIFECYCLE, select Provision.
- Follow the prompts to deploy your Declarative Agent.
The Teams Toolkit streamlines deployment, making it faster to test and iterate.

![declarative agent from template](/assets/img/posts/2025-02-17-Build-M365-Roadmap-Features-Tracker-Declarative-Agent/06-test.png)

### Conclusion 🎯
The M365 Roadmap Features Tracker Declarative Agent is a powerful agent for staying on top of Microsoft 365 Roadmap features updates. By leveraging Declarative Agents and Microsoft Graph Connectors, you can automate repetitive tasks, deliver timely information, and enhance productivity.

✅ What’s next? I’m continuing to expand my collection of Declarative Agents—exploring new ways to automate and simplify work. If you're curious or want to collaborate, feel free to reach out or explore the code on GitHub 
- [Microsft365 Roadmap Graph Connector](https://github.com/mohammadamer/GraphConnectorM365RoadMap)
- [Roadmap Features Tracker declarative agent](https://github.com/mohammadamer/m365-roadmap-features-tracker-da-agent)

### References:
* [Microsoft Copilot Developer Camp](https://aka.ms/copilotdevcamp)
* [Build Your First declarative agent](https://microsoft.github.io/copilot-camp/pages/extend-m365-copilot/01-declarative-copilot/)
* [Youtube Video - Demo](https://youtu.be/3zBANCzFcpM)

### GitHub Repos
* [Microsft365 Roadmap Graph Connector - GitHub Repo](https://github.com/mohammadamer/GraphConnectorM365RoadMap)
* [Roadmap Features Tracker declarative agent - GitHub Repo](https://github.com/mohammadamer/m365-roadmap-features-tracker-da-agent)
* [Microsft365 Roadmap Graph Connector - GitHub Repo in Samples Gallery](https://adoption.microsoft.com/en-us/sample-solution-gallery/sample/pnp-graph-connector-dotnet-csharp-m365-roadmap/)

### Aditional Resources
* [Microsoft 365 Copilot extensibility](https://learn.microsoft.com/en-us/collections/p11pb18wmq6xqq?wt.mc_id=MVP_433449)