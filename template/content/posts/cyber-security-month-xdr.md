---
date: 2021-10-29
publishdate: 2021-10-29
published_url: "/posts/cyber-security-month-xdr"
title: "XDR: A Blessing for SOC Teams, or Another Fad?"
author: "Anshul Garg"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/cyber_background.jpg"
summary: "In the last blog post for Cyber Security Month, Anshul Garg, marketing co-leader for OCA, takes a closer look at the potential of XDR and how OCA is working towards an Open XDR."
tag: "blog"
---


**By Anshul Garg, Marketing Co-chair, Open Cybersecurity Alliance**

The security industry has evolved over the years to combat new and emerging cyber threats, and as we evolved, new products were launched to help security teams. Some of these products have been great breakthroughs - driven by the venture capital and innovation flowing to the security industry - but a lot of them have been a fad as they die out as soon as the marketing fizz around those die down.

## A Problem of Too Many

It has become noticeably clear in the last few years that security teams have been struggling with a problem of abundance - too many point products; too much data spread across endpoints, networks, and clouds; and too many alerts that are humanly impossible to dive into.  Most often, the security teams have functioned as the human glue to stitch disparate tools together, but that requires a lot of time and effort and can change with any new release. This has been a big challenge for most of the scarcely staffed security teams.

It has been difficult to collect security telemetry across the enterprise. Security teams have been grappling with this problem for some time now. XDR aims to simplify this problem by bringing together telemetry from endpoints, networks, clouds, logs, and users on a unified console. This allows for faster detection of threats and improved investigation and response times through security analysis. It has the potential to solve the biggest challenge SOC teams face today. But the big question remains - Can XDR solve this challenge?

## XDR: Innovation or Fad

I think the answer would differ depending on which solution we are referencing. Let me elaborate a little more: The technology is evolving, not all XDR solutions are created equal - some have evolved from EDR solutions, some have evolved from SIEM solutions, and the like. Major security vendors are hoping to latch on the XDR bandwagon and tout their solution the best.

There is ample research being done on this subject by industry analysts. Jon Oltsik, from ESG, recently [authored a blog](https://www.csoonline.com/article/3633896/5-observations-about-xdr.html) on this topic and called out his observations on XDR. Some of the key points highlighted by Jon:
-	XDR solutions will win or lose based on advanced analytics, emphasizing the significant role AI and ML will play.
-	XDR is all about turnkey automated response, emphasizing the importance of building and customizing response playbooks.
-	The MITRE ATT&CK framework is the lingua franca of XDR, highlighting how an XDR solution can help operationalize the MITRE framework.
-	“Openness” is critical, zooming in on the fact that the XDR tool should interoperate with other tools.


## Open XDR

Since the topic is evolving, leading industry analysts have different perspectives on this topic. While Jon believes XDR is all about driving better outcomes for security teams, Allie Mellen, Forrester Research, [stresses](https://www.forrester.com/blogs/xdr-faq-frequently-asked-questions-on-extended-detection-and-response/) that an XDR solution must be an extension of the Endpoint Detection and Response (EDR) solution.  While analyst viewpoints on the subject vary widely, the analysts do agree that the solutions should be composable and integrable.

_According to [Forrester research](https://www.forrester.com/report/Adapt+Or+Die+XDR+Is+On+A+Collision+Course+With+SIEM+And+SOAR/-/E-RES165775?objectid=RES165775), security teams prioritize tooling with integration capabilities. Thus, both native XDR and hybrid XDR must support integration with third-party security tools by some means — there is simply no other option._

Clearly the analyst community agree that the success (or failure) of XDR depends on whether it is based on open source and open standards, and how the XDR tool/product interoperates with other products. And it makes sense – since XDR aims to bring together security operations on a single console, it may not be possible to do it if the XDR does not interoperate with other security products for a unified workflow.


## OCA Projects and Enabling XDR

It may be exceedingly difficult to create a framework for security tools to interoperate if only one vendor was leading this effort. There is a high probability that the vendor’s own financial and competitive interests may take precedence over pure ‘interoperability’ challenges. Thus, it is imperative that the development is led by a neutral organization, who has done it for other IT or Security projects and has the necessary technical and architectural expertise to take on a project of this magnitude.

One such organization is [Open Cybersecurity Alliance (OCA)](https://opencybersecurityalliance.org/). Formed under the auspices of [OASIS Open](https://www.oasis-open.org/), this organization aims to foster collaboration between vendors, organizations and security practitioners and drive security interoperability. Open Cybersecurity Alliance is building an open ecosystem where cybersecurity products interoperate without the need for customized integrations. Using community-developed standards and practices, OCA is simplifying integration across the threat lifecycle. In a [recent blog on top technology trends](https://www.networkworld.com/article/3636972/gartner-top-strategic-technology-trends-for-2022.html), Gartner refers to a cybersecurity mesh architecture, which aligns closely with the work underway in OCA.

Current OCA projects include:

-	[Stix Shifter:](https://github.com/opencybersecurityalliance/stix-shifter) To allow data to be normalized across security domains for comprehensive security analysis
-	[OCA Ontology:](https://github.com/opencybersecurityalliance/opendxl-ontology) To enable real time data exchange and cross vendor orchestration
-	[Kestrel:](https://github.com/opencybersecurityalliance/kestrel-lang) To provide an abstraction for threat hunters and focus on what to hunt instead of how to hunt

In addition, to combat the increasing threat landscape, OCA has recently kicked off new working groups including:

-	[OCA Unified Architecture & Zero Trust](https://opencybersecurityalliance.org/posts/cyber-security-month-zero-trust/)
-	[Indicators of Behavior](https://www.youtube.com/channel/UCjTpPl2oEGH_Ws251m827Cg/videos)

## Success or Failure of XDR Depends on Whether it is Open

There is no doubt there is a lot of excitement about XDR in the security industry. We may be around the ‘peak of inflated expectations,’ per the [Gartner Hype Cycle](https://en.wikipedia.org/wiki/Gartner_hype_cycle). While it may take a while to reach the ‘plateau of productivity,’ there will be some XDR solutions that effectively meet the requirements of bringing together telemetry across network, application, clouds, etc., while there may be others for whom this may just remain a wish-list. What will separate the winners from the losers is how Open the XDR is, so it can easily integrate with the existing infrastructure, and fight the good fight of beating the bad guys and protect our customers and customer’s customer data.

Cheers to an Open XDR that could modernize the SOC!

### About the Author:
**Anshul Garg** is a passionate marketer looking to help organizations stay ahead of threat actors. To this end, he is the marketing co-leader for Open Cybersecurity Alliance and Senior Product Marketing Manager, IBM Cloud Pak for Security. He has 14+ years of experience across Product Marketing and Product Management of IT Security Solutions and has worked on areas spanning Cloud Security, Security Consulting, Managed Security Services, Penetration Testing, Operational Technology Security, and Threat Management. He is a contributor to multiple white-papers and thought leadership assets.

He holds multiple Security certifications like Certified Network Defender (CND), Certificate of Cloud Security Knowledge (CCSK), along with certifications in various security products. He has an MBA in Systems and Finance, along with an engineering degree in Computer Science.
