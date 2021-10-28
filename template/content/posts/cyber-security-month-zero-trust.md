---
date: 2021-10-28
publishdate: 2021-10-28
published_url: "/posts/cyber-security-month-zero-trust"
title: "Zero Trust Working Group forms at Open Cyber Security Alliance"
author: "Dennis Moreau"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/cyber_background.jpg"
summary: "Zero Trust promises a level of resilience to “inside out” exploitation by enabling containment of the “blast radius” of malware. Dennis Moreau, Senior Engineering Architect for CyberSecurity and Compliance at VMware, writes about a new OCA effort to address Zero Trust co-existence with other policy structuring approaches."
tag: "blog"
---


**By Dennis Moreau, Sr Engineering Architect for CyberSecurity and Compliance, Office of the CTO at VMware.**

The seemingly endless stream of supply chain exploitation and ransomware disruptions have made it painfully clear that malware is already inside the perimeter, and has been there – undetected – for some time. With mounting pressure to address this issue, a significant number of regulatory and standards efforts have begun to consider Zero Trust as part of the answer.

With its focus on continuous verification against policy, applied granularly at the logical intersection between subject and objects, Zero Trust promises a level of resilience to “inside out” exploitation by enabling containment of the “blast radius” of malware. Much like the way that the establishment of many watertight compartments in the construction of a warship prevents a single torpedo from sinking the entire vessel, the compartmentalization employed must be strong enough to resist malware “escape”.

However, perfectly compartmentalized or isolated software components would be completely useless. While intended interactions must be allowed, other interactions should be prohibited. Zero Trust advocates the placement of policy enforcement points, logically between the subjects that need access and the objects to which that access is needed. Further, because the environment is already compromised, Zero Trust embraces continuous verification of the tests that support each Zero Trust policy, independently verifying their consistency. Because malware can conduct reconnaissance and lateral movement very quickly, continuous verification is most effective if it happens inline and in real-time. Existing standards refer to access and transaction levels of temporal granularity, for policy enforcement.

Zero Trust policy is more effective when it can refer to the context associated with components that we intend to protect. Component characteristics like level of curation, degree of threat modelling, thoroughness of testing, temporal consistency, and peer-consistency can establish a more comprehensive basis for trust. Zero Trust integration with Open-Source curation pipelines and DevSecOps pipelines is already included in existing Zero Trust reference architectures.

Finally, the invocation of granular containment and real-time enforcement raises the prospect of potentially overwhelming signal volume. Zero Trust leans on leveraging business flow and risk classification (e.g., impact), to support aggregation and triage of alerts, logs, and events into coherent groupings with actionable context.

These provisions are embodied in the [NIST Zero Trust Architecture standard, SP 800-207](https://csrc.nist.gov/publications/detail/sp/800-207/final). The intent of this standard is to help define a platform that supports the expression and enforcement of Zero Trust policy.

## Transforming Security Defense with Open Source and Open Standards

[The Open Cybersecurity Alliance (OCA)](https://opencybersecurityalliance.org/) is particularly interested in how open-source software and standards may contribute to and facilitate resilient protection, like that promised by Zero Trust. Zero Trust calls for collaboration among the security systems (especially Identity and Access Management and Network and Data security). In these areas, OCA is working on transforming security defense from independent, isolated solutions to a team of collaborative controls working together.  Other areas of interest being evaluated are identity, policy expression/evaluation, risk expression/modeling, data classification, and security operations.

While Zero Trust holds much promise for newly developed, modern applications and infrastructures, Zero Trust policy domains will need to co-exist and integrate with non-Zero Trust domains for the foreseeable future. The current expectation is that many existing systems will need to be re-implemented or replaced to realize Zero Trust levels of resilience - a process likely to require years of effort. With that in mind, the OCA will be addressing Zero Trust co-existence with other policy structuring approaches and the consequent implications for communicating about both risk and trust.

At no time in the past has there been a clearer appetite for improvement in the ways we cultivate security. [OCA](https://github.com/opencybersecurityalliance/) is excited at the prospect of leveraging open-source software and standards to address the important security, privacy, and trust challenges before us. If you are interested in collaborating on the opportunities and challenges regarding Zero Trust, [consider joining the Open Cybersecurity Alliance](https://lists.oasis-open-projects.org/g/oca-architecture-wg) in this effort.

### About the Author:
[Dennis Moreau](https://www.linkedin.com/in/dennismoreau/) is a Sr Engineering Architect, focusing on Cyber Security Directions in the Advanced Technology Group, of the Office of the CTO at VMware. He works across the Dell/VMware portfolio and with partners on security innovation, and collaborates with the National Science Foundation, NIST, Open Cybersecurity Alliance (OCA) and Mitre on security automation standards. Prior to joining VMware, he was a Sr. Technology Strategist at RSA. He was also a co-founder and the CTO of Configuresoft and the CTO for Baylor College of Medicine.  He holds a doctorate in Computer Science and his research in software engineering, cybersecurity, computational biology, and scientific visualization has been sponsored by the NASA, Caltech/JPL, the US Department of Commerce, the National Institutes of Health, AT&T Bell Laboratories, and IBM.
