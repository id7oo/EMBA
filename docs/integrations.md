# MBSC platforms: how each one can connect

The options for each MBSC platform, best first. **Official** means the platform or the college supports it. **Workaround** means it works with your own access but isn't a product feature; the risks are listed next to each one. One rule applies to all of them: never put your MBSC password into a script, a file or a chat. Every option below either uses your own signed-in browser or something you switch on yourself.

## Summary

| Platform | Best option today | Who has to act | Fully automatic? |
|---|---|---|---|
| Outlook (student mailbox) | Outlook rule or Power Automate → personal Gmail | You, 5 min | Yes |
| Outlook calendar | Publish the calendar as ICS → Google Calendar | You, 3 min | Yes |
| Blackboard due dates | Blackboard calendar feed (ICS) → Google Calendar | You, 5 min | Yes |
| Blackboard content (announcements, grades, syllabi) | Blackboard notifications by email + Claude in Chrome on request | You | Partly |
| SIS (schedule, grades, fees) | Claude in Chrome on request | You | No |
| Payments / tuition | Email receipts (already in Gmail) + SIS via Claude in Chrome | — | Partly |
| Zoom | Meeting invites and links via email and calendar | — | Yes |
| Teams / OneDrive | Microsoft 365 connector (needs MBSC IT) or Claude in Chrome | IT, or you | Only with IT |
| Library | Claude in Chrome on request | You | No |
| mbsc.edu.sa (public site) | Allow the domain in the cloud environment | You, 1 min | Yes |

## Microsoft 365: Outlook, Calendar, Teams, OneDrive

**Official**
1. **Claude's Microsoft 365 connector** (claude.ai → Settings → Connectors). It reads mail, calendar, Teams chats and SharePoint/OneDrive. On a school tenant, a Microsoft Entra administrator has to grant consent once for everyone. Try connecting: if you see "Need admin approval", send IT the drafted request.
2. **A Microsoft Graph app of our own**: an Azure app registration with `Mail.Read` and `Calendars.Read`. It runs into the same consent wall as option 1, and most universities block user consent for unverified apps. It isn't worth trying before option 1.

**Workarounds (no IT needed)**
3. **Forwarding rule**: Outlook on the web → Settings → Mail → Forwarding, or a rule that redirects all mail to Gmail. Many Microsoft 365 tenants block automatic external forwarding (bounce `550 5.7.520 Access denied`). If the bounce arrives, use option 4.
4. **Power Automate** (make.powerautomate.com, with your MBSC account). Use the trigger *When a new email arrives (V3)* and the action *Send an email (V2)* to your Gmail, copying subject, sender and body. This usually works even when forwarding is blocked, because it sends a new email instead of forwarding. Risk: a data-loss policy can still block it, and MBSC IT can see the flow.
5. **Publish the calendar**: Outlook on the web → Settings → Calendar → Shared calendars → Publish a calendar → *Can view all details* → copy the ICS link → Google Calendar → Add from URL. Class invites from the Programs Office then reach Google Calendar. It may be disabled by policy.
6. **Outlook app on your phone**: keep using it for reading. It has no export, so it doesn't help Claude.
7. **Claude in Chrome** on your computer: opens outlook.office.com in your signed-in browser when you ask and reads it. Nothing runs automatically.

## Blackboard Learn (mbsc.blackboard.com)

**Official**
1. **Calendar feed (ICS)**: Calendar → settings → *Get external calendar link*. All due dates and course events, read-only and student-level, with no approval needed. Add it to Google Calendar as "Blackboard" and the sync picks it up. **Best first step.**
2. **Notifications**: Profile → Notification settings → email for announcements, new content, grades and due dates. They go to the student mailbox, so combine with the Outlook options above.
3. **REST API** (`/learn/api/public/v1/...`): needs an application registered in Anthology's developer portal and approved by MBSC's Blackboard administrator. Realistic only if the college wants it.

**Workarounds**
4. **Claude in Chrome** reads Ultra pages in your session: announcements, course content, grades, rubrics, syllabus PDFs.
5. **The same REST endpoints from inside your signed-in browser**. Blackboard's own Ultra pages call JSON endpoints such as `/learn/api/public/v1/users/me/courses` and `/learn/api/public/v1/courses/{id}/contents` with your session. A browser extension or Claude in Chrome can read them for a clean, structured export (courses, content tree, due dates, grades) instead of scraping screens. Risk: it's unsupported, it can change without notice, and heavy automated use may breach the acceptable-use policy. Use it on demand, not as a bot.
6. **Not recommended**: a headless robot logging in with your password. MFA (Microsoft Authenticator) blocks it anyway, and storing the password is unsafe.

## SIS (sis.mbsc.edu.sa)

Holds your timetable, grades, holds, tuition statement and payment plan. It has no student API and no calendar export. It works best from a computer.

- **Claude in Chrome on request**: "open SIS and tell me my next installment and any holds".
- Statements and receipts: download the PDF and attach it in a Claude chat; Claude reads it and fills in the workspace.
- Payment confirmations from the payment gateway already arrive in Gmail, and the sync reads them.

## Payments and tuition

- The *MBSC Payment Plan Student Guide* is a PDF attachment. The Gmail connector can't open attachments: forward it to yourself as a file and attach it in a chat, or open it in Claude in Chrome.
- The English-course fee is a bank transfer; its confirmation will come by email.

## Zoom (mbsc.zoom.us)

- **Official**: the Zoom API needs a Marketplace app approved by MBSC's Zoom admin, so it isn't practical.
- **What works**: session links live in Blackboard (Books and Tools → Zoom Meeting) and in invites. Once the Blackboard and Outlook calendars flow into Google Calendar, the times and links come with them.

## Library (library.mbsc.edu.sa)

Claude in Chrome, on request: search the catalogue and databases in your signed-in session and summarise articles for assignments.

## Public website (mbsc.edu.sa)

The cloud environment blocks it. Add `mbsc.edu.sa` and `mbsc.blackboard.com` under the environment's **Network access → allowed domains** so syncs can read public pages such as news and academic calendars.

## Getting Claude into your browser

This workspace's sync runs in the cloud, which can't see your computer. To let Claude open MBSC platforms in your own Chrome:

1. Install **Claude in Chrome** (claude.com/chrome) and sign in, or use the **Claude desktop app**.
2. Sign in once to Blackboard, SIS and Outlook in that Chrome. Microsoft Authenticator stays on your phone.
3. Ask Claude in Chrome: *"Open each MBSC platform (Blackboard, SIS, Outlook, Zoom, Library), list what's there for me, and propose how to connect each one to my EMBA workspace."* Paste the result into a Claude Code chat on this repo and it goes into the workspace.
