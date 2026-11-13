---
layout: default
report: true
title: "Microsoft 365 outage of 12 to 13 November 2026"
date: "2026-11-13"
summary: "Exchange Online failed from 11:17 AM ET Thursday 12 November and grew into a global Microsoft 365 outage (EX1550543, MO1550821) that is recovering but not resolved as of Microsoft's 8:00 AM ET Friday update. No root cause yet, no evidence of an attack, and mail is being held for up to 48 hours rather than bounced."
---

*Published 2026-11-13. Every factual claim links to the source it came from. [All research](https://pete-builds.github.io/research/)*

## TL;DR

On Thursday 12 November 2026 Exchange Online began failing at 11:17 AM ET (Microsoft incident EX1550543), and by 1:26 PM ET it had become a global Microsoft 365 incident (MO1550821) that Microsoft said could affect users in any region, disrupting Outlook mail and calendar and the Exchange-dependent features of Teams, Copilot, Intune, Purview, SharePoint sharing, Universal Print and more. As of Microsoft's 8:00 AM ET Friday update it is recovering in North America, Europe and Asia Pacific but is not resolved: there is no root cause, no ETA, intermittent access and delayed mail continue, and Microsoft says it has "no evidence of malicious activity". For an IT shop: this is server-side, so client fixes will not help; expect a mail backlog rather than bounces; recheck Intune compliance once it clears; warn staff about outage-themed scams; and, separately, the Outlook flaw CVE-2026-124194 went onto CISA's KEV list on 10 November with a 24 November due date.

## Current Status

- **Not resolved.** Both incidents read "serviceDegradation" and "End Time: Not ended yet" in Microsoft's admin-center text, last updated 8:00 AM ET Friday, next update due 9:00 AM ET ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)).
- **Recovering unevenly.** Microsoft reports "broader improvements now observed in North America and sustained improvements across much of Europe" and "encouraging recovery signals in Asia Pacific, although some users may continue to experience intermittent access issues or delayed email delivery" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)).
- **No root cause.** The Root Cause field reads "N/A" on both incidents. Microsoft's working explanation centres on an overwhelmed secure-connection process, the revert of "a recent network configuration change that correlates with the start of impact", and an "upstream authentication/routing configuration component patch" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). A Post-Incident Report is promised after mitigation.
- **Scale.** Downdetector peaked at more than 130,000 Outlook reports around 1:27 PM ET Thursday ([source](https://www.techrepublic.com/article/news-microsoft-outlook-outage-130000-reports-2026/)), and reports spread to 74 countries and territories within about an hour ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)). Microsoft has not said how many users were affected ([source](https://www.techrepublic.com/article/news-microsoft-outlook-outage-130000-reports-2026/)).
- **Not an attack, so far.** "At this time, we have no evidence of malicious activity associated with this incident" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)).
- **AI connectors still down.** Anthropic's Microsoft 365 connector incident (which covers Claude for Outlook) and OpenAI's Microsoft 365 connector incident were both still open when their status feeds were read about 8:20 AM ET Friday ([source](https://status.claude.com/api/v2/incidents.json), [source](https://status.openai.com/api/v2/incidents.json)).

## Table of Contents

- [TL;DR](#tldr)
- [Current Status](#current-status)
- [Table of Contents](#table-of-contents)
- [Findings](#findings)
  - [What happened](#what-happened)
  - [Timeline (newest first)](#timeline-newest-first)
  - [What broke and what kept working](#what-broke-and-what-kept-working)
  - [Cause: what Microsoft has and has not said](#cause-what-microsoft-has-and-has-not-said)
  - [Scale and who was hit](#scale-and-who-was-hit)
  - [Knock-on effects on AI connectors](#knock-on-effects-on-ai-connectors)
  - [What it means for a campus IT shop](#what-it-means-for-a-campus-it-shop)
  - [Security angle](#security-angle)
  - [Where the press got it wrong or disagrees](#where-the-press-got-it-wrong-or-disagrees)
- [Confidence Assessment](#confidence-assessment)
- [Open Questions](#open-questions)
- [Sources](#sources)
- [Update History](#update-history)

## Findings

**About the main source.** Microsoft's public status page (status.cloud.microsoft) renders only "You need to enable JavaScript to run this app", so the admin-center text below comes from the University of Pennsylvania's public extract of its own tenant's Microsoft incident feed, which says it is "a data extract from the PennO365 administrative console of incident reports sent by Microsoft" refreshed "every 15 minutes", generated "Fri Nov 13 08:20:01 2026 EST" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). It is a third-party mirror, but its wording matches what BleepingComputer, NBC and The Verge quote from Microsoft independently ([source](https://www.bleepingcomputer.com/news/microsoft/massive-microsoft-365-outage-disrupts-outlook-teams-copilot-worldwide/), [source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118), [source](https://www.theverge.com/tech/1032862/microsoft-outlook-down-outage)). Computerworld links to this same mirror as its source, so it is not an independent check. Microsoft's own times are given in UTC; every ET time below was converted with Python's zoneinfo, not by hand.

### What happened

Two linked Microsoft incidents make up the outage ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)):

| Incident | Title (Microsoft's words) | Started | Status at 8:00 AM ET Fri |
|---|---|---|---|
| EX1550543 | "Users may be unable to connect to their mailboxes in Exchange Online through multiple connection methods" | Thu 12 Nov, 11:17 AM ET | serviceDegradation, not ended |
| MO1550821 | "Users may be unable to connect to multiple Microsoft 365 services" | Thu 12 Nov, 1:26 PM ET | serviceDegradation, not ended |

Microsoft tied them together at 1:29 PM ET: "While Exchange is the primarily impacted service, we've identified additional Microsoft 365 services that are also experiencing connectivity issues, and we're communicating those issues under MO1550821" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). Reuters reports Microsoft first pointed to the Americas and later confirmed the issue affected "Microsoft 365 service connectivity for users in any region" ([source](https://www.channelnewsasia.com/business/microsoft-global-outage-outlook-teams-thousands-users-6547306)).

A third, separate incident hit Teams earlier the same morning: TM1550488 ran from 9:45 to 10:53 AM ET with a preliminary root cause of "Resource contention within the underlying service infrastructure" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). "Microsoft has not indicated whether the two incidents are related" ([source](https://www.bleepingcomputer.com/news/microsoft/massive-microsoft-365-outage-disrupts-outlook-teams-copilot-worldwide/)).

### Timeline (newest first)

All entries are Microsoft's admin-center updates ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)) unless another source is named.

| Time (ET) | Event |
|---|---|
| Fri 9:00 AM | Next Microsoft update due. |
| Fri 8:00 AM | "broader improvements now observed in North America and sustained improvements across much of Europe"; Asia Pacific "encouraging"; intermittent access and delayed mail continue; full Post-Incident Report promised "after mitigation is complete". |
| Fri 7:00 AM | "encouraging recovery progress in Europe"; North America improving. |
| Fri 6:01 AM | "recovery actions completed across portions of the affected infrastructure in the Asia Pacific region". |
| Fri 4:03 AM | First posted under MO1550821, repeated under EX1550543 at 4:55 AM: "early signs of stabilization, but service capacity has not yet returned to normal levels"; Microsoft is "cycling affected infrastructure through traffic drain, restart, remediation, and staged return-to-service" and "extending the email timeout window to 48 hours". |
| Fri 3:06 AM | Patch rollout expanded to "regions currently within or entering peak business hours". |
| Fri 2:25 AM | "positive results for server stability after applying an upstream authentication/routing configuration component patch". |
| Fri 1:56 AM | "we have observed marginal success". |
| Thu 11:59 PM | A "surge in outbound call volume from our routing services to our backend servers"; machines stuck in a bad state. |
| Thu 11:10 PM | Actions "aren't resulting in sustained recovery". |
| Thu 10:34 PM | "there is no indication this is the result of malicious activity". |
| Thu 9:54 PM | "confirmed that change is not taking as expected". |
| Thu 9:30 PM | Downdetector still shows more than 25,000 US Outlook reports, Reuters reports ([source](https://www.gmanetwork.com/news/scitech/technology/1009380/microsoft-outage-drags-on-for-hours-disrupting-outlook-teams-for-businesses-gov-ts/story/)). |
| Thu 8:45 PM | "positive results with our efforts to restart affected infrastructure". |
| Thu 8:06 PM | A secure-connection process on the servers is overwhelmed; Microsoft begins reverting "a recent network configuration change that correlates with the start of impact". |
| Thu 6:59 PM | Returned servers "can become unresponsive again once they begin receiving traffic"; Microsoft is "rebuilding a subset of affected servers using a previous software configuration". |
| Thu 5:57 PM | "begun returning a portion of the affected servers to service". |
| Thu 5:00 PM | "a core process on the affected servers is becoming overwhelmed"; actions have "not provided the anticipated relief". |
| Thu 3:56 PM | "a small number of configuration updates that we're investigating as potential contributors". |
| Thu 2:18 PM | Load is caused by "a large number of servers being unexpectedly unavailable", not more traffic. |
| Thu 2:06 PM | "paused all deployments and configuration changes". |
| Thu 1:57 PM | "completed redistributing Outlook connection traffic across our worldwide infrastructure". |
| Thu 1:29 PM | "redirected a portion of traffic away from the affected infrastructure in the US"; MO1550821 linked to EX1550543. |
| Thu ~1:27 PM | Downdetector peak: more than 130,000 Outlook reports, Reuters via TechRepublic ([source](https://www.techrepublic.com/article/news-microsoft-outlook-outage-130000-reports-2026/)). |
| Thu 1:26 PM | MO1550821 start time. |
| Thu 11:20 AM | First EX1550543 post: "We're investigating a potential issue and checking for impact to your organization." |
| Thu 11:17 AM | EX1550543 start time. |
| Thu 9:45 to 10:53 AM | Separate Teams incident TM1550488, resolved. |

### What broke and what kept working

Microsoft's own scope line for MO1550821: "Features of the Microsoft 365 services below that interact with Exchange Online and email services are impacted. Elements of those services that do not depend on interaction with Exchange Online remain functional" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). From the same impact list:

- **Exchange Online / Outlook:** mailbox access fails through Outlook on the web, Outlook desktop (MAPI), Exchange ActiveSync and REST; "on-prem mail flow may be affected if it goes to or from Exchange Online".
- **Intune:** "device enrollment, device check-in, and device compliance policies may have been intermittently impacted", a line NBC also carries ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)).
- **Microsoft 365 Apps Admin Center:** "Cloud Update, Cloud Policy, and Inventory are affected".
- **Microsoft 365 Education:** "Features including Assignments, Reading Progress, and Speaker Progress may be impacted".
- **Teams:** calendar, scheduling, invites, live and virtual event creation fail, but "Most of the core functionality including joining calls, meetings, group chats, and channel messages are not impacted", also carried by NBC ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)).
- **SharePoint Online:** external sharing issues, but "Accessing files stored directly in SharePoint Online remains functional".
- **Also listed:** Copilot, Defender for Office 365, Defender XDR, Graph, Planner, Power Apps, Purview, Power BI, To Do, Universal Print ("Users may encounter errors submitting print jobs"), and Windows 365 Cloud PCs that use Purview Customer Key.

Azure itself was not listed: NBC reports Microsoft's status showed "Microsoft Azure: No issues detected at this time" ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)), and the Azure status history has no November 2026 entry, the newest being 30 September ([source](https://azure.status.microsoft/en-us/status/history/)). That page lists post-incident reviews published after the fact, so an empty November this early is weak evidence either way.

### Cause: what Microsoft has and has not said

**No root cause has been stated.** Both incidents carry "Root Cause: | N/A", and Microsoft says it "will share a full Post-Incident Report with root cause and defense-in-depth actions after mitigation is complete" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)).

Microsoft's working explanation shifted through the night, all in its own updates ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)):

1. 1:27 PM ET: reviewing "an unexpected increase in a specific type of service traffic".
2. 2:18 PM ET: not more traffic but "a large number of servers being unexpectedly unavailable".
3. 2:43 PM ET: servers "failing automated health checks", possibly from a recent change; fourteen minutes later: "we haven't identified any recent changes that correlate with the start of impact".
4. 3:56 PM ET: "a small number of configuration updates that we're investigating as potential contributors".
5. 8:06 PM ET: a secure-connection process overwhelmed, and a revert of "a recent network configuration change that correlates with the start of impact"; Computerworld reported the same revert ([source](https://www.computerworld.com/article/4248253/microsoft-struggles-to-get-m365-services-back-online-after-widespread-outage-2.html)).
6. 11:59 PM ET: a "surge in outbound call volume from our routing services to our backend servers".
7. 2:25 AM ET Friday: the fix that worked was "an upstream authentication/routing configuration component patch".

**Reported but unconfirmed by Microsoft:** The Verge says senior correspondent Tom Warren "reports that the outage is linked to Azure VNet config changes" ([source](https://www.theverge.com/tech/1032862/microsoft-outlook-down-outage)). Microsoft's admin-center text never mentions VNet, virtual networks, Azure Front Door or DNS (searched the full extract; a control phrase, "Post-Incident Report", matched, so the search works). Australian minister Tony Burke said Microsoft told the government "They believe they are dealing in some ways similar to what we saw with respect to CrowdStrike where there is an update that has gone awry" ([source](https://www.gmanetwork.com/news/scitech/technology/1009380/microsoft-outage-drags-on-for-hours-disrupting-outlook-teams-for-businesses-gov-ts/story/)).

**No spokesperson statement** beyond the status updates: BleepingComputer says "Microsoft has not yet responded to BleepingComputer's questions about the root cause" ([source](https://www.bleepingcomputer.com/news/microsoft/massive-microsoft-365-outage-disrupts-outlook-teams-copilot-worldwide/)).

### Scale and who was hit

**Numbers.** Downdetector "recorded more than 130,000 Outlook problem reports around 1:27 p.m. ET" plus "more than 46,000 reports involving Microsoft 365 and approximately 3,000 involving Teams", Reuters via TechRepublic ([source](https://www.techrepublic.com/article/news-microsoft-outlook-outage-130000-reports-2026/)); Gizmodo puts the peak at "over 129,500" around 1:28 PM ET ([source](https://gizmodo.com/microsoft-365-outage-stretches-late-into-the-evening-with-services-like-outlook-and-teams-still-unstable-2000839374)); AP says "Roughly 130,000 users from dozens of countries" ([source](https://www.kmbc.com/article/microsoft-global-outage-disrupts-outlook-teams-copilot/74421314)). NBC reports peaks of "19,650 per minute across five Microsoft services" against "fewer than 15 on a typical Thursday", spreading "to 74 countries and territories within about an hour" ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)). "Millions of users" in some headlines is the outlets' characterization; "Microsoft has not disclosed how many people or organizations experienced problems" ([source](https://www.techrepublic.com/article/news-microsoft-outlook-outage-130000-reports-2026/)). Former UK NCSC head Ciaran Martin said it "looks like being one of the biggest outages of all time" ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)).

**Governments.** The US House of Representatives "said the outage impacted its users" ([source](https://www.gmanetwork.com/news/scitech/technology/1009380/microsoft-outage-drags-on-for-hours-disrupting-outlook-teams-for-businesses-gov-ts/story/)). "Australia's spy agency and federal police were among organisations caught up"; Japan's Deputy Chief Cabinet Secretary said it "was affecting the government's information system infrastructure"; "Immigration New Zealand said it has paused the processing of all visa applications until further notice" ([source](https://www.channelnewsasia.com/business/microsoft-global-outage-outlook-teams-thousands-users-6547306)). Baltimore County Government staff were "currently unable to send or receive emails", and the Philippines Supreme Court said the outage "disrupted its legal work" ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)). At 8:07 AM ET Friday the BBC reported New York's Department of Investigation "told city marshals to suspend evictions scheduled for Friday" ([source](https://www.bbc.com/news/live/cq5y10ge3dp3t)).

**Education.** At 6:37 AM ET Friday the BBC carried Northern Ireland's Education Authority ("Outlook is currently unavailable to NI schools and the Education Authority") and Queen's University Belfast ("Our students and staff are currently unable to receive or send emails") ([source](https://www.bbc.com/news/live/cq5y10ge3dp3t)). No US university-specific reporting was found.

**Companies.** IndiGo and Akasa Air "reported problems with their customer-support email systems, while Air India has temporarily rerouted its email services" ([source](https://www.foxbusiness.com/technology/microsoft-outlook-returns-some-millions-users-face-second-day-outages)). Workhorse Group moved its earnings call to 16 November ([source](https://www.gmanetwork.com/news/scitech/technology/1009380/microsoft-outage-drags-on-for-hours-disrupting-outlook-teams-for-businesses-gov-ts/story/)). Richemont said "its half-year numbers will be delayed" ([source](https://www.bbc.com/news/live/cq5y10ge3dp3t)).

### Knock-on effects on AI connectors

**Anthropic.** Incident "Elevated errors affecting the Microsoft 365 connector" opened 12:52 PM ET Thursday; at 3:28 PM ET it added "Claude for Outlook is also affected: mail and calendar actions may fail, and some users may not be able to open the add-in"; its latest update, 5:23 PM ET, says "mail, calendar and search tools in the Microsoft 365 connector are still failing for most requests. Claude for Outlook remains affected", with status "identified" and no resolved time ([source](https://status.claude.com/api/v2/incidents.json)).

**OpenAI.** Incident "Elevated errors affecting the Microsoft 365 connector" opened 1:29 PM ET Thursday: "new connections may be broken and tools are not working as expected"; still "investigating" as of the page's 12:55 AM ET Friday update ([source](https://status.openai.com/api/v2/incidents.json)).

**Two unrelated Anthropic incidents to keep separate.** A login incident on claude.ai and Claude Code was resolved at 6:58 AM ET Friday, and a new one ("Elevated errors on claude.ai, the Claude API, and Claude Code") opened at 8:09 AM ET Friday. Neither mentions Microsoft in its updates ([source](https://status.claude.com/api/v2/incidents.json)). Claude problems this morning are not automatically the Microsoft outage.

**Xbox.** Kotaku says the outage "reportedly includes Xbox Live services, although the Xbox Status page currently states that everything is fine and dandy" ([source](https://kotaku.com/xbox-live-microsoft-365-copilot-outlook-outages-ongoing-2000756565)). User reports only.

### What it means for a campus IT shop

Items marked *inference* are reasoning from Microsoft's statements, not something a source says.

1. **Track the incidents in your own tenant.** Microsoft points admins to "EX1550543 and MO1550821 in the Microsoft 365 admin center" ([source](https://kotaku.com/xbox-live-microsoft-365-copilot-outlook-outages-ongoing-2000756565)). That is the authoritative view of whether your tenant is still affected.
2. **Do not chase client fixes.** Microsoft's own title says mailbox access fails "through multiple connection methods" (web, desktop MAPI, ActiveSync, REST), so moving users from desktop to web or mobile routes around nothing ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). Microsoft's client-side workarounds are all app-specific: Power Apps and Field Service users can switch to the web versions, and "users who are already signed in can potentially avoid impact by not signing out, clearing app data, or reinstalling while the issue is ongoing"; for Microsoft Fabric items, "users can remove the sensitivity label from the affected item" (same source). None covers Outlook. *Inference:* the same logic argues against having users reinstall Office or rebuild Outlook profiles this week; the fault is in Microsoft's servers.
3. **Expect a backlog, not bounces.** Microsoft is "extending the email timeout window to 48 hours" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)) "to help preserve delayed messages while it works through email queues and backlogs" ([source](https://www.bleepingcomputer.com/news/microsoft/massive-microsoft-365-outage-disrupts-outlook-teams-copilot-worldwide/)). Thursday's mail may arrive late and out of order. Hybrid shops: on-prem mail flow to or from Exchange Online is affected.
4. **Recheck device state after recovery.** Intune enrollment, check-in and compliance were intermittently impacted, and Microsoft 365 Apps Cloud Update, Cloud Policy and Inventory are affected ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). *Inference:* compliance and inventory reports from Thursday and Friday may be stale or wrong; do not act on noncompliance flags from this window without a fresh check-in. Universal Print errors and Education features (Assignments, Reading Progress) are on the same list.
5. **Message users about scams.** No outage-themed phishing campaign had been reported in the sources read, but banks issued warnings: ING Australia said "Scammers may contact you offering to resolve the Microsoft or other systems issues" ([source](https://www.theweek.in/news/sci-tech/2026/11/13/global-microsoft-outage-banks-fear-cyber-scammers-outlook-office-365-issue.html)). Queen's University Belfast's notice is a usable template: "staff and students should not use personal email accounts to send, receive or store university-related communications or information" ([source](https://www.bbc.com/news/live/cq5y10ge3dp3t)).

### Security angle

**No attack.** Microsoft: "At this time, we have no evidence of malicious activity associated with this incident" ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)). AP: "The company said it did not have any indication that the outage was caused by malicious activity" ([source](https://www.kmbc.com/article/microsoft-global-outage-disrupts-outlook-teams-copilot/74421314)). No outlet read reported a claim of responsibility or an attack.

**A real, separate Outlook vulnerability.** CVE-2026-124194, "Microsoft Office Outlook Missing User Interaction for Potentially Unsafe Action Vulnerability", which "allows an unauthorized attacker to perform spoofing over a network", was added to CISA's Known Exploited Vulnerabilities catalog on 2026-11-10 with a due date of 2026-11-24 ([source](https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json)). NVD's record carries the same KEV dates ([source](https://services.nvd.nist.gov/rest/json/cves/2.0?cveId=CVE-2026-124194)). SaskToday, relaying a Cyber Security News report, covered it alongside the outage and said the exploitation "has not been independently confirmed through an official CISA advisory in the information available for this report"; it has since been confirmed by the KEV listing. The same article says Microsoft has released security updates for the flaw and that it scores 6.5 on CVSS, and that "There is also no confirmed connection between the reported vulnerability and the widespread service outage" ([source](https://www.sasktoday.ca/provincial-news/global-microsoft-outlook-outage-reported-as-us-cybersecurity-agency-confirms-active-exploitation-of-security-flaw-13010558)). No source links them. It went into KEV two days before the outage began.

### Where the press got it wrong or disagrees

- **Start time.** NBC says "the incident began at 11:17 a.m. ET" and also "about 12:50 p.m. ET" ([source](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)); BleepingComputer says "1:26 PM ET" ([source](https://www.bleepingcomputer.com/news/microsoft/massive-microsoft-365-outage-disrupts-outlook-teams-copilot-worldwide/)). Both Microsoft times are right for different incidents: 11:17 AM is EX1550543, 1:26 PM is MO1550821 ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)).
- **Business Insider's "3:34 a.m. update"** on malicious activity has no time zone ([source](https://www.businessinsider.com/microsoft-365-outage-outlook-teams-down-claude-what-we-know-2026-11)). Microsoft stamps it 03:34 UTC, which is 10:34 PM ET Thursday ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)).
- **Business Insider says Anthropic "said the issues were fixed by 6:58 a.m. ET on Friday"** ([source](https://www.businessinsider.com/microsoft-365-outage-outlook-teams-down-claude-what-we-know-2026-11)). The only Anthropic incident resolved at that time is the unrelated login incident; the Microsoft 365 connector incident is still open ([source](https://status.claude.com/api/v2/incidents.json)).
- **Downdetector geography.** Reuters frames the 130,000 reports as US reports ([source](https://www.gmanetwork.com/news/scitech/technology/1009380/microsoft-outage-drags-on-for-hours-disrupting-outlook-teams-for-businesses-gov-ts/story/)); AP says "from dozens of countries" ([source](https://www.kmbc.com/article/microsoft-global-outage-disrupts-outlook-teams-copilot/74421314)).
- **"Hack ruled out."** Fox Business's framing that Microsoft "ruled out" a hack ([source](https://www.foxbusiness.com/technology/microsoft-outlook-returns-some-millions-users-face-second-day-outages)) is stronger than Microsoft's "no evidence of malicious activity".
- **"Teams is down."** Widely reported, but Microsoft says core calls, meetings and chats were not impacted; the failures are calendar and Exchange-dependent features ([source](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)).

## Confidence Assessment

| Claim | Confidence | Why |
|---|---|---|
| Incident IDs, titles, start times (11:17 AM and 1:26 PM ET) | High | Microsoft's admin-center text, matched independently by BleepingComputer and NBC |
| Not resolved, no ETA, as of 8:00 AM ET Friday | High | Microsoft's text; BleepingComputer, The Verge and Business Insider agree |
| No root cause stated; PIR promised | High | Microsoft's text; BleepingComputer and NBC agree |
| No evidence of malicious activity | High | Microsoft's text; AP, The Verge, BBC repeat it |
| Exchange-dependent scope for Exchange, Intune and Teams | High | Microsoft's impact list via the mirror; NBC carries the same Intune and Teams lines |
| Apps Admin Center, Education, Universal Print and Windows 365 items | Medium | Microsoft's text via one third-party mirror only |
| Downdetector peak about 130,000 Outlook reports | High | Reuters, AP and Gizmodo agree within 1% |
| Network configuration change and authentication/routing patch as the mechanism | Medium | Microsoft's own words, but stated mid-investigation and revised repeatedly; not a root cause |
| Azure VNet config changes as the trigger | Low | One reporter's claim relayed by The Verge; absent from Microsoft's text |
| 74 countries within an hour | Medium | NBC citing Downdetector, single source |
| Named organizations affected | Medium | Each named by one major outlet (Reuters, NBC, BBC, Fox), mostly quoting the organization |
| Anthropic and OpenAI connector incidents open | High | Each vendor's own status feed, the primary record; Fox Business carries Anthropic's "still failing" wording; Business Insider reports both connectors disrupted but misdates Anthropic's fix |
| Business Insider conflated two Anthropic incidents | Medium | Exact time match to the unrelated incident; BI's own sourcing not visible |
| CVE-2026-124194 in KEV, due 2026-11-24 | High | CISA's KEV feed (the issuer); NVD's record carries the same dates |
| Client workarounds will not help (Outlook) | Medium | Inference from Microsoft's "multiple connection methods" scope |
| Xbox Live affected | Low | User reports only; Xbox status said otherwise |

## Open Questions

- **Root cause.** Waiting on Microsoft's Post-Incident Report; whether the Azure VNet report holds up.
- **When "resolved" lands.** Microsoft's next update was due 9:00 AM ET Friday; this report reflects the 8:00 AM ET update.
- **Was TM1550488 (the morning Teams incident) a precursor?** Microsoft has not said.
- **Your own tenant.** This report did not check any specific organization's Service health view; the admin center is the source for that.
- **Service credits.** One outlet (Cybernews) reportedly covers SLA credits, but its page returned 403 and Microsoft has said nothing on credits. Unknown.
- **Delayed-mail fallout.** Whether any mail is lost beyond the 48-hour window if recovery drags past Saturday afternoon is not addressed by Microsoft.

## Sources

**Microsoft (official text and status)**
- [University of Pennsylvania mirror of Microsoft's admin-center incident text (EX1550543, MO1550821, TM1550488)](https://mailservices.isc.upenn.edu/computing/email/penno365/alerts/ms-incidents.html?q=MO1550821)
- [Azure status history](https://azure.status.microsoft/en-us/status/history/)

**Wire services and general press**
- [AP via KMBC: Microsoft global outage disrupts Outlook, Teams, Copilot](https://www.kmbc.com/article/microsoft-global-outage-disrupts-outlook-teams-copilot/74421314)
- [Reuters via GMA Network: Microsoft outage drags on for hours](https://www.gmanetwork.com/news/scitech/technology/1009380/microsoft-outage-drags-on-for-hours-disrupting-outlook-teams-for-businesses-gov-ts/story/)
- [Reuters via CNA: Microsoft global outage, Outlook, Teams](https://www.channelnewsasia.com/business/microsoft-global-outage-outlook-teams-thousands-users-6547306)
- [NBC News live blog](https://www.nbcnews.com/tech/tech-news/live-blog/microsoft-outage-outlook-teams-updates-fix-rcna609118)
- [BBC News live page](https://www.bbc.com/news/live/cq5y10ge3dp3t)
- [Fox Business: Outlook returns for some, millions face second day](https://www.foxbusiness.com/technology/microsoft-outlook-returns-some-millions-users-face-second-day-outages)
- [Business Insider: Microsoft 365 outage, what we know](https://www.businessinsider.com/microsoft-365-outage-outlook-teams-down-claude-what-we-know-2026-11)
- [The Week: banks fear cyber scammers](https://www.theweek.in/news/sci-tech/2026/11/13/global-microsoft-outage-banks-fear-cyber-scammers-outlook-office-365-issue.html)
- [SaskToday: outage reported as CISA confirms exploitation of a flaw](https://www.sasktoday.ca/provincial-news/global-microsoft-outlook-outage-reported-as-us-cybersecurity-agency-confirms-active-exploitation-of-security-flaw-13010558)

**Tech press**
- [BleepingComputer: Massive Microsoft 365 outage disrupts Outlook, Teams, Copilot](https://www.bleepingcomputer.com/news/microsoft/massive-microsoft-365-outage-disrupts-outlook-teams-copilot-worldwide/)
- [Computerworld: Microsoft struggles to get M365 services back online](https://www.computerworld.com/article/4248253/microsoft-struggles-to-get-m365-services-back-online-after-widespread-outage-2.html)
- [The Verge: Microsoft Outlook down](https://www.theverge.com/tech/1032862/microsoft-outlook-down-outage)
- [TechRepublic: Outlook outage, 130,000 reports](https://www.techrepublic.com/article/news-microsoft-outlook-outage-130000-reports-2026/)
- [Gizmodo: outage stretches late into the evening](https://gizmodo.com/microsoft-365-outage-stretches-late-into-the-evening-with-services-like-outlook-and-teams-still-unstable-2000839374)
- [Kotaku: Xbox Live, Microsoft 365, Teams and Copilot still broken](https://kotaku.com/xbox-live-microsoft-365-copilot-outlook-outages-ongoing-2000756565)

**Vendor status feeds**
- [Anthropic status incidents feed](https://status.claude.com/api/v2/incidents.json)
- [OpenAI status incidents feed](https://status.openai.com/api/v2/incidents.json)

**Government**
- [CISA Known Exploited Vulnerabilities catalog feed](https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json)
- [NVD API record for CVE-2026-124194](https://services.nvd.nist.gov/rest/json/cves/2.0?cveId=CVE-2026-124194)

## Update History

| When (ET) | Change |
|---|---|
| 2026-11-13 8:36 AM | Independent verification pass: 20 of 20 URLs live, 0 unsupported, 6 partial. Fixed: TL;DR "broke ... in all regions" narrowed to Microsoft's "any region" wording; independence line now names BleepingComputer, NBC and The Verge (Computerworld uses the same mirror; Mashable dropped, no URL); "may affect" removed from the CNA quote; 4:55 AM timeline row moved to 4:03 AM, when MO1550821 first posted it; "only client-side advice" corrected to include the Fabric workaround; SaskToday quote restored with its hedge and "wrongly said" removed; NVD added as a second record for the KEV dates; Azure-history caveat added; confidence rows for scope, connectors and root cause re-sourced, Apps Admin Center items lowered to Medium. |
| 2026-11-13 8:26 AM | Initial report from 20 cited sources. Reflects Microsoft's 8:00 AM ET Friday update. |

<!-- rev: 01a732dd189c -->
