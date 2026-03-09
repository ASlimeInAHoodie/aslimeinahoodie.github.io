---
title: The NIST Framework Explained
date: 2026-03-10
tags:
  - blog
  - research
  - technology
  - compliance
  - NIST
---
# Introduction
Businesses, governments, people, and the like are all at risk of hackers - no one can ever be 100% secure. Some might say "Why try to protect against threats if we can never be 100% secure?", to which the answer is reducing risk. Risk is the likelihood and impact of something going wrong, which is great to reduce so less issues arise and the issues that do arise don't do as much damage. Large bodies (Companies, governments, and some people) use Risk Management Frameworks to help align themselves on what risks they have and how they can be reduced. When I say "risk" I really mean a "cyber-security risk", as anyone can have other risks (e.g. financial risks), as well as when I say "attack" and "incident" - this is a cyber-security post not a financial one.

Depending on specific needs of a business/persons, different frameworks can be used. For example, if a business is dealing with credit card information, they need to be PCI DSS compliant, and therefore may choose the NIST CSF or ISO 27001.

This post is about delving deeper into the NIST Cyber Framework, an internationally recognised risk management framework written by the American National Institute of Standards and Technology. I have used this framework professionally in the past, and know that it can become quite detailed when expanded upon - Control spreadsheets in the past were over 5,000 rows long.
## Sources
I have mainly used the [NIST Website](https://www.nist.gov/cyberframework) for use in this blog, but other references can be found in the [[09032026-Explaining_NIST_Framework#See Also|See Also]].
# Structure
The Framework is separated into **5** "*Functions*" - overarching themes to the categories that align with the incident response structure, which are then split up into "*Categories*" - a more granular subsection that aligns with the a specific *Function*. These *Functions* and *Categories* are:
## Govern (GV)
*Establishing, communicating and monitoring the cyber-security risk management strategy, expectations, and policy.*
- **Organisational Context** (GV.OC): *The circumstances - mission, expectations, dependencies, regulatory, and contractual requirements - surround the risk management decisions are understood, documented, and managed.*
- **Risk Management Strategy** (GV.RM): *The priorities, constraints, risk tolerance and appetite statements, and assumptions are established, communicated, and used to support operational risk decisions.*
- **Roles, Responsibilities, and Authorities** (GV.RR): *Roles, responsibilities, and authorities to foster accountability, performance, assessment, and continuous improvement are established and communicated.*
- **Policy** (GV.PO): *A cyber-security policy is established, disseminated, communicated, and enforced.*
- **Oversight** (GV.OV): *Results of risk management activities and performance are used to inform, improve, and adjust the risk management strategy.*
- **Supply Chain Risk Management** (GV.SC): *Supply chain risk management process are identified, established, managed, monitored, and improved by stakeholders.*
## Identify (ID)
*The current risks are observed understood.*
- **Asset Management** (ID.AM): *Assets that enable functionality and purpose are identified and managed consistently with their relevant importance to objectives and risk strategy.*
- **Risk Assessment** (ID.RA): *The risk to assets and individuals is recognised and acknowledged.*
- **Improvement** (ID.IM): *Improvements to risk management processes, procedures and activities are identified across all Framework Functions.*
## Protect (PR)
*Safeguards are managed for the risks.*
- **Identity Management, Authentication, and Access Control** (PR.AA): *Access to physical and logical assets is limited to authorised users, services and hardware. Additions and changes are assessed with risk of unauthorised access, ensuring stronger controls for higher risk assets.*
- **Awareness and Training** (PR.AT): *Personnel are provided with awareness and training so that they can perform cyber-security-related tasks.*
- **Data Security** (PR.DS): *Data is managed consistently with the risk strategy to protect the CIA of information.*
- **Platform Security** (PR.PS): *Hardware, software, and services of physical/virtual platforms are managed consistently with the risk strategy to protect their CIA.*
- **Technology Infrastructure Resilience** (PR.IR): *Security Architecture is managed within the risk strategy to protect asset CIA and resilience.*
## Detect (DE)
*Possible attacks and compromises are found and analysed.*
- **Continuous Monitoring** (DE.CM): *Assets, systems, and their connections are monitored for anomalies, IoCs and other adversarial events.*
- **Adverse Event Analysis** (DE.AE): *Anomalies, IoCs and other adversary events are analysed for categorisation and for the detection of incidents.*
## Respond (RS)
*Actions regarding a detected incident are taken.*
- **Incident Management** (RS.MA): *Responses to detected incidents are created, stored, and managed in an incident management system (e.g. SIEM).*
- **Incident Analysis** (RS.AN): *Investigations are conducted for effective response and supporting forensics / recovery activities.*
- **Incident Response Reporting and Communication** (RS.CO): *Response activities are coordinated with stakeholders as required by regulatory requirements.*
- **Incident Mitigation** (RS.MI): *Activities are performed to prevent the "reach" of an event and mitigate its effects.*
## Recover (RC)
*Affected systems, assets, and processes are restored after an incident.*
- **Incident Recovery Plan Execution** (RC.RP): *Activities are performed to ensure availability of systems and services affected by incidents.*
- **Incident Recovery Communication** (RC.CO): *Restoration activities and processes are coordinated with internal and external parties.*

These *Categories* are then split up into "*Subcategories*" that are a granular control. There are over 100 *Subcategories* in total, so I will not be noting them here. However, they (along with the *Functions* and *Categories*) can be found in the [CSF 2.0 Informative References](https://www.nist.gov/cyberframework/informative-references). Here are a couple examples of *Subcategories* and how they could be implemented:
- **GV.OC-03** *Legal, regulatory, and contractual requirements regarding cyber-security (including privacy and civil liberty obligations) are understood and managed*: The business creates and assigns to an employee a process to track and manage the regulatory requirements regarding individuals' information (e.g. California Consumer Privacy Act, APRA CPS 234, Privacy Act 1988, etc.).
- **ID.RA-01** *Vulnerabilities in assets are identified, validated, and recorded*: The business implements vulnerability management systems to identify unpatched and misconfigured software.
- **DE.CM-06** *External service provider activities and services are monitored to find potential adverse events*: The business monitors remote and onsite administration and maintenance activities that external providers perform on systems, and implements abnormality alerts on these events.
- **RC.CO-03** *Recovery activities and progress in restoring operational capabilities are communicated to designated internal and external stakeholders*: The business regularly update senior leadership on the recovery rate/status when a major incident occurs.
# Benefits
Implementing any risk management framework provides several benefits:
- **Proactive risk reduction**: Identify threats before they can cause damage
- **Incident response readiness**: A structure/process for efficient detection, containment, and recovery should an incident occur.
- **Resource/budget prioritisation**: Seeing, testing, and evaluating risks and threats allows proper budgeting and allocating resources effectively.
- **Compliance**: Frameworks are built for regulatory and industry compliance, therefore they only have to be tweaked for 100% compliance[^1].
- **Trust**: Build trust with the community, clients, and relationships by demonstrating alignment with internationally and nationally recognised frameworks.
# See Also
## External Sources
### Cyber-security Risk Management
- [Cybersecurity risk management: Best practices and frameworks | TechTarget](https://www.techtarget.com/searchsecurity/tip/Cybersecurity-risk-management-Best-practices-and-frameworks)
- [What is PCI DSS (Payment Card Industry Data Security Standard)? | TechTarget](https://www.techtarget.com/searchsecurity/definition/PCI-DSS-Payment-Card-Industry-Data-Security-Standard)
- [Cyber Risk Management Frameworks Every Organisation Should Know | GCS Network](https://globalcybersecuritynetwork.com/blog/cyber-risk-management-frameworks-every-organisation-should-know/)
## Footnotes
[^1]: 100% compliance can be reached for a static "image" of all assets, systems and scope. Changes nearly always impact compliance in some way, and therefore 100% compliance can never reached as long as changes are occurring.