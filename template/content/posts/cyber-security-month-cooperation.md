---
date: 2021-10-12
publishdate: 2021-10-12
published_url: "/posts/cyber-security-month-cooperation"
title: "Enabling the Cooperative Ecosystem"
author: "Adam Montville"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/cyber_background.jpg"
summary: "In the second post for Cyber Security Month, Adam Montville, Chief Architect for Security Best Practices at CIS, is discussing how and why the OCA is working to enable a normalization of workflows and perspectives across the full range of contexts in which a system operates."
tag: "blog"
---


**By Adam Montville, Chief Architect for Security Best Practices at CIS and member of the [OCA PGB](https://github.com/opencybersecurityalliance/oasis-open-project/blob/master/PROJECT-GOVERNING-BOARD.md)**


The [Center for Internet Security (CIS)](https://www.cisecurity.org/) has a mission to improve the actual practice of cyber defense, and we do this, in part, by ensuring that our best practice recommendations (in the form of CIS Benchmarks recommendations and CIS Controls Safeguards) track clear and transparent measures of security value for our users. Most recently, we've embarked on the journey of mapping our various recommendations to the MITRE ATT&CK framework, and this has exemplified a challenge that has existed in cybersecurity for some time: a lack of normalization across different cybersecurity disciplines.   
A more holistic example of this challenge can be expressed by looking at a typical system or platform deployed by an average enterprise. Theoretically, this system exists within a larger context composed of development, operations, threats, and governance (thanks to Dennis Moreau of VMware for this perspective). As you might imagine, each of these contextual elements bring subtly different terms and meanings, primarily because they operate at different levels of abstraction.   

To take this example further, consider the system's various resources. A given system may have a hybrid architecture including some on-premises and cloud-based resources. Some of those cloud-based resources may exist across multiple cloud service providers (CSPs) and additionally take advantage of specific services offered "in the cloud" (i.e., CI/CD pipeline management, SaaS solutions such as Salesforce). Within a specific group of cloud resources, there may be instances of traditional endpoints (i.e., Windows, Linux), and there are likely containers, functions (as a service), and various communication mechanisms at play.   

When development and operations (DevOps) is looking at all of these resources, they do so from their perspective, and they will typically want to apply a globally unique (to them) identifier for each resource. This is, after all, a necessity from the perspective of governance. Managing threats against the system will do the same thing, but through a different lens; they'll also want to apply a globally unique (to them) identifier for each resource. The pattern repeats through the lens of governance, not taking into account the fact that every specific operating environment (a given CSP, for example) chooses to identify resources in their own way.   

Because DevOps, threat management, and governance all see the same system a little differently, they all have a piece of the proverbial elephant. Moreover, they each use a set of disparate tools that may or may not respect their desire for a globally unique ID, and that is within each context. Now imagine trying to bring each of those perspectives together, so that each perspective could rely on a single identifier with classification properties (in other words, by virtue of the identifier, it's possible to classify the given resource as, for example, a Microsoft Windows 10 instance).   

When I've talked to vendors and other standards participants in this space about this specific problem, they all agree that it is an issue – none ever seem to have a solution, but agree that a solution is needed. Integration between solutions from disparate vendors has proven to be a concern to everyone BUT the systems integrators so often hired to accomplish the integration. We must move more quickly in this space, so, as cybersecurity leaders, we are always on the lookout for better approaches.   

## Seeing the entire elephant

Two years ago, CIS decided to join an OASIS Open Project: The Open Cybersecurity Alliance (OCA). Why did we do this? Because they play from the same sheet of music that we do. Improving the actual practice of cyber defense requires a maximum degree of automation, not just within the same product or family of products, but between products from multiple sources. Put another way, if you buy products from vendors X, Y, and Z to support your security program, odds are that you’re going to want to integrate them at least in terms of normalized reporting, if not for fully integrated workflows. This is the challenge that OCA seeks to address: “OCA is building an open ecosystem where cybersecurity products interoperate without the need for customized integrations.” To date, most of the OCA efforts have been around threat intelligence, sharing, hunting, and management, but the scope of what the OCA would like to accomplish is more encompassing than that.

CIS has a long history of leading and contributing to community-based efforts, especially those that aid in speeding cybersecurity policy to implementation. In fact, CIS has been a key proponent of the [Security Automation and Continuous Monitoring (SACM)](https://datatracker.ietf.org/wg/sacm/charter/) architecture, which seeks to standardize a generic architecture suitable for any cybersecurity subject matter, but which is first focused on posture assessment (technology software load, vulnerability state, and configuration state). CIS has recently proposed a SACM implementation effort known as Posture Attribute Collection and Evaluation (PACE) to the OCA.

If accepted, PACE will expand the scope of implementations under the OCA umbrella a step further in the direction of holistic security program automation. This is the win-win combination, as there’s a bilateral relationship between PACE work (expected to be done in the OCA) and the standardization effort (SACM). If OCA hadn’t already brought together a critical mass of vendors in the cybersecurity automation space, PACE would be standing alone in its own world instead of being something that will ultimately integrate with threat intelligence solutions out-of-the-box.

So, taking this all the way back to the top, the idea that OCA has put in motion, and that which CIS supports, is to enable a normalization of workflows and perspectives across the full range of contexts in which a system operates. With a normalized ecosystem comprised of discrete components playing specific roles, and despite the fact that they’re certainly from different vendors, the entire security program can become more automated, more efficient, and clearer – the enterprise can see the entire elephant without looking at only one part at a time.

### About the author
Adam Montville is Chief Architect for Security Best Practices at CIS, where he helps lead a diverse team responsible for developing products and services supporting information security best practices and automation. Adam brings more than two decades of information security experience to his team, and actively participates in several standards organizations. Adam is a member of the Project Governing Board for the Open Cybersecurity Alliance (OCA).
