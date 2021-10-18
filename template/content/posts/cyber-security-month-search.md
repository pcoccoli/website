---
date: 2021-10-18
publishdate: 2021-10-18
published_url: "/posts/cyber-security-month-search"
title: "Federated Search"
author: "RoseAnn Guttierrez"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/cyber_background.jpg"
summary: "How to help your Security Operations Center (SOC) search smarter not harder is the focus of the third blog post for Cyber Security Month."
tag: "blog"
---

**By RoseAnn Guttierrez, IBM Security**

Visibility is an ongoing problem for security operations. Throughout an investigation, many tools are utilized to gather and collect the context needed to make informed decisions. That context is critical to advise security teams on what actions to take and what potential threats require further research. Gathering information across multiple tools and disparate data sources takes time, and time is a precious commodity especially in your SOC where seconds matter.

## Why Federated Search?
Many SOC’s use a Security Information and Event Management (SIEM) to collect and correlate events. SIEMs can provide security analytics and prioritized alerts but require threat telemetry from various sources (ex. users, endpoints, cloud) to fully understand the scope of a potential threat.

In cybersecurity, federated search provides security teams with the ability to search across disparate sources, where the data is located, to gain the right context. It can thus provide enrichment to existing tools, enable faster threat detection, and reduce manual efforts for SOC analysts.

## The challenge and a better path forward
Implementing Federated Search can be very time consuming especially when you consider that not all tools can talk to one another and not all data has the same structure.  This added complexity leads security analysts to spend time working on their tools instead of having the tools work for them.

Imagine what it would be like if your team could learn everything about a single internal IP address with just one query including the asset type, who has access to it, what apps are installed, vulnerabilities and much more.

This is where open standards, interoperability, and collaboration provide a better path forward. I mentioned a project under the [Open Cybersecurity Alliance](https://opencybersecurityalliance.org/) called [STIX-shifter](https://github.com/opencybersecurityalliance/stix-shifter) in [my last blog](https://digitalavenger.com/open-source-interoperability-better-information-security). STIX-shifter is a step towards federated search. With STIX-shifter you can use a single query to search across multiple data sources no matter where the data resides, augmenting the tools you already have in place.

STIX-shifter works by using STIX patterns. The patterns are translated into the native query language of the data source and then transmitted to the data source where the query is run. The results of the query are then returned and translated into STIX observable objects. These types of queries can provide additional context when you need it instead of having to toggle across each tool individually.
It’s not just another tool

Some might argue that federated search is ‘just another tool.’ I would propose that it’s a critical step in enabling your teams to understand the full scope and context of threats across distributed environments while saving them precious time to focus on what matters most. Instead of spending your time creating proprietary internal integrations, consider shifting your focus to integrations that can be used everywhere. That’s time well spent as your team embraces a collaborative and open approach to threat management.

## Where do you go from here?

* Consider getting [involved in the OCA](https://opencybersecurityalliance.org/get-involved/).
* Take a look at [the OCA Projects](https://github.com/opencybersecurityalliance).
* Join the community [on Slack](https://docs.google.com/forms/d/1vEAqg9SKBF3UMtmbJJ9qqLarrXN5zeVG3_obedA3DKs).

No contribution is too small, and the whole security ecosystem will benefit.  

### About the author
RoseAnn Guttierrez is a Technical Enablement Specialist at IBM Security Business Development Technical Alliance Program (TAP). More about RoseAnn: [https://digitalavenger.com/open-source-interoperability-better-information](https://digitalavenger.com/open-source-interoperability-better-information)
