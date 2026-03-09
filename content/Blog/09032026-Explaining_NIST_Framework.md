---
title: The NIST Framework Explained
date: 2025-09-15
tags:
  - blog
  - research
  - technology
  - compliance
  - NIST
---
# Introduction
Businesses, governments, people, and the like are all at risk of hackers - no one can ever be 100% secure. Some might say "Why try to protect against threats if we can never be 100% secure?", to which the answer is reducing risk. Risk is the likelihood and impact of something going wrong, which is great to reduce so less issues arise and the issues that do arise don't do as much damage. Large bodies (Companies, governments, and some people) use Risk Management Frameworks to help align themselves on what risks they have and how they can be reduced. When I say "risk" I really mean a "cyber-security risk", as anyone can have other risks (e.g. financial risks), as well as when I say "attack" and "incident" - this is a cyber-security post not a financial one.

This post is about delving deeper into the NIST Cyber Framework, an internationally recognised risk management framework written by the American National Institute of Standards and Technology. I have used this framework professionally in the past, and know that it can become quite detailed when expanded upon - Control spreadsheets in the past were over 5,000 rows long.
## Source
I have mainly used the [NIST Website](https://www.nist.gov/cyberframework) for use in this blog, but other references can be found in the [[09032026-Explaining_NIST_Framework#See Also|See Also]].
# Structure
The Framework is separated into **5** "*Functions*" - overarching themes to the categories that align with the incident response structure, which are then split up into "*Categories*" - a more granular subsection that aligns with the a specific *Function*. These *Functions* and *Categories* are:
- **Govern** (GV): *Establishing, communicating and monitoring the cyber-security risk management strategy, expectations, and policy.*
	- **Organisational Context** (GV.OC): *The circumstances - mission, expectations, dependencies, regulatory, and contractual requirements - surround the risk management decisions are understood, documented, and managed.*
	- **Risk Management Strategy** (GV.RM): *The priorities, constraints, risk tolerance and appetite statements, and assumptions are established, communicated, and used to support operational risk decisions.*
	- **Roles, Responsibilities, and Authorities** (GV.RR): *Roles, responsibilities, and authorities to foster accountability, performance, assessment, and continuous improvement are established and communicated.*
	- **Policy** (GV.PO): *A cyber-security policy is established, disseminated, communicated, and enforced.*
	- **Oversight** (GV.OV): *Results of risk management activities and performance are used to inform, improve, and adjust the risk management strategy.*
	- **Supply Chain Risk Management** (GV.SC): *Supply chain risk management process are identified, established, managed, monitored, and improved by stakeholders.*
- **Identify** (ID): *The current risks are observed understood.*
	- **Asset Management** (ID.AM): *Assets that enable functionality and purpose are identified and managed consistently with their relevant importance to objectives and risk strategy.*
	- **Risk Assessment** (ID.RA): *The risk to assets and individuals is recognised and acknowledged.*
	- **Improvement** (ID.IM): *Improvements to risk management processes, procedures and activities are identified across all Framework Functions.*
- **Protect** (PR): *Safeguards are managed for the risks.*
	- **Identity Management, Authentication, and Access Control** (PR.AA): *Access to physical and logical assets is limited to authorised users, services and hardware. Additions and changes are assessed with risk of unauthorised access, ensuring stronger controls for higher risk assets.*
	- **Awareness and Training** (PR.AT): *Personnel are provided with awareness and training so that they can perform cyber-security-related tasks.*
	- **Data Security** (PR.DS): *Data is managed consistently with the risk strategy to protect the CIA of information.*
	- **Platform Security** (PR.PS): *Hardware, software, and services of physical/virtual platforms are managed consistently with the risk strategy to protect their CIA.*
	- **Technology Infrastructure Resilience** (PR.IR): *Security Architecture is managed within the risk strategy to protect asset CIA and resilience.*
- **Detect** (DE): *Possible attacks and compromises are found and analysed.*
	- **Continuous Monitoring** (DE.CM): *Assets, systems, and their connections are monitored for anomalies, IoCs and other adversarial events.*
	- **Adverse Event Analysis** (DE.AE): *Anomalies, IoCs and other adversary events are analysed for categorisation and for the detection of incidents.*
- **Respond** (RS): *Actions regarding a detected incident are taken.*
	- **Incident Management** (RS.MA): *Responses to detected incidents are created, stored, and managed in an incident management system (e.g. SIEM).*
	- **Incident Analysis** (RS.AN): *Investigations are conducted for effective response and supporting forensics / recovery activities.*
	- **Incident Response Reporting and Communication** (RS.CO): *Response activities are coordinated with stakeholders as required by regulatory requirements.*
	- **Incident Mitigation** (RS.MI): *Activities are performed to prevent the "reach" of an event and mitigate its effects.*
- **Recover** (RC): *Affected systems, assets, and processes are restored after an incident.*
	- **Incident Recovery Plan Execution** (RC.RP): *Activities are performed to ensure availability of systems and services affected by incidents.*
	- **Incident Recovery Communication** (RC.CO): *Restoration activities and processes are coordinated with internal and external parties.*

These *Categories* are then split up into "*Subcategories*" that are more like the final control. There are over 100 *Subcategories* in total, so I will not be noting them here. However, they (along with the *Functions* and *Categories*) can be found in the [CSF 2.0 Informative References](https://www.nist.gov/cyberframework/informative-references). 
# See Also
## External Sources
### Quantum Computing
- [(Video) How Does a Quantum Computer Work? - Veritasium | Youtube](https://www.youtube.com/watch?v=g_IaVepNDT4)
- [Blockchain 101 - A Visual Demo - Anders Brownworth | Youtube](https://www.youtube.com/watch?v=_160oMzblY8)
- [How Does Bitcoin Work? | Learn me a bitcoin](https://learnmeabitcoin.com/beginners/how-does-bitcoin-work/)
- [Quantum Computing x Crypto Everything You Need To Know | Presto Labs](https://www.prestolabs.io/research/quantum-computing-x-crypto-everything-you-need-to-know)
- [Cryptocurrency vs. quantum computing | Coin Telegraph](https://cointelegraph.com/learn/articles/cryptocurrency-vs-quantum-computing-a-deep-dive-into-the-future-of-cryptocurrencies)