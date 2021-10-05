---
date: 2021-10-05
publishdate: 2021-10-05
published_url: "/posts/cyber-security-month-interop"
title: "The Increased Need for Security Integration Standards"
author: "Matthew Gardiner"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/cyber_background.jpg"
summary: "As part of the OCA’s blog series in support of Cyber Security Month, Matthew Gardiner, Director at Rapid7 and Member of the OCA PGB, writes about the need to break down security silos."
tag: "blog"
---


**By Matthew Gardiner, Director at Rapid7 and Member of the [OCA PGB](https://github.com/opencybersecurityalliance/oasis-open-project/blob/master/PROJECT-GOVERNING-BOARD.md)**


My experience with security standards began many years ago in 2002 during my time at Netegrity and later the [Kantara Initiative](https://kantarainitiative.org/). There, I was involved with the creation, standardization, and popularization of the [OASIS](https://www.oasis-open.org/) standard, [SAML](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language), and federated SSO more broadly. The problem that we focused on solving — how to transition an authenticated user session (and the associated level of trust) from one security domain with its security system, to another independent domain, with its typically different security system — is one for the security record books.

Can you imagine today’s world of Saas/Cloud applications without federated SSO? It would be an utter mess and wouldn’t work from either a usability or security perspective. Defining and enabling SAML via an open international standard, under the umbrella of OASIS, was absolutely central to its ultimate success.

Unfortunately, while the world of cybersecurity has grown massively in terms of size and strategic importance, many of the similar problems of security system interoperability still linger.

## Silos still standing

Perhaps you’ve heard the phrase and the need for “breaking down security silos”? While SAML and federated SSO helped to break down a key area of security silo-ization — that of authentication/SSO — there are plenty more security silos lurking out there.

From my current perch at [Rapid7](https://www.rapid7.com/) — where I help develop our security operations solution, made up of the family of Rapid7 products covering multiple areas of vulnerability management, detection and response (D&R), and cloud security — I see many more silos that are standing in the way of more effective security system interoperability. And this is significantly impacting the overall efficiency and effectiveness of security teams.

Start by considering the detection part of D&R. To detect anomalous and potentially malicious activity well, access to a tremendous depth and variety of data is required: security events, logs, endpoints, vulnerabilities, external threat intelligence, network activity, and the like. This data is necessary so that both simple and sophisticated detective analytic models can be run and investigations can be efficiently conducted.

Collecting and making sense of this data is clearly doable — after all, it’s what we SIEM/XDR vendors are in the business of doing. But can’t it be accomplished more easily and less expensively? Developing hundreds of 1:1 integrations is primarily how it’s done today. The role of standards enabling this detective/investigative data gathering is relatively minor (with a positive shout out to [STIX](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=cti)/[TAXII](https://oasis-open.github.io/cti-documentation/taxii/intro.html)!).

## The Need for Standards to Ease Cross Product Interoperability

The response side of D&R is in much the same boat. For example, a key focus of SOAR products is [to manage and automate the responses](https://www.rapid7.com/blog/post/2021/08/13/energize-your-incident-response-and-vulnerability-management-with-crowdsourced-automation-workflows/) — the immediate security policy changes that are needed to stop an attack in its tracks — into the preventive security controls (firewalls, endpoints, directories, SWGs) where various types of enforcement happens.

Here again, the necessary integrations are developed and maintained one-by-one, even within the same control category. No two integrations are exactly the same. As a vendor to these markets, Rapid7 does what we need to do to help our customers improve their security now, while simultaneously thinking of how it can be done better.

Just like SAML 20 years ago, I believe one way many security operations can be done better is through the standardization and popularization of data exchange standards at the various interfaces between security data consumers and producers and between security enforcement and decision points. This is a key facilitating role that the OCA can play, and it’s a primary reason why Rapid7 supports the OCA and its mission.  
