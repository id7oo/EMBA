# Connecting your sources

| Source | Status | How Claude reads it |
|---|---|---|
| Personal Gmail | Connected | Gmail connector. MBSC copies almost all of its mail to your personal address, so this is the main source. |
| Google Calendar | Connected | Google Calendar connector. Also how Blackboard deadlines arrive (below). |
| Blackboard | One step to do | Blackboard's calendar feed, added to Google Calendar. |
| MBSC Outlook (student mailbox) | Recommended | Forward it to Gmail. The Microsoft 365 connector needs MBSC IT's approval. |
| Blackboard content, SIS, MBSC portals | On request | Claude in Chrome, using your own signed-in browser. |

**Never give Claude (or anyone) your MBSC password.** Nothing here needs it.

## Blackboard → Google Calendar (5 minutes, on a computer)

1. Sign in at [mbsc.blackboard.com](https://mbsc.blackboard.com) ("Third Party" → MBSC).
2. Open **Calendar**, then the settings (gear) icon, then **Get external calendar link**. Copy the link.
3. Open [Google Calendar → Add calendar → From URL](https://calendar.google.com/calendar/u/0/r/settings/addbyurl), paste the link and add it.
4. Rename the new calendar **Blackboard**. Claude looks for that name.

Treat the link like a password: anyone who has it can see your course calendar. Google refreshes subscribed calendars every few hours, so a brand-new deadline can take a while to appear.

## MBSC Outlook → Gmail

Some mail only reaches your student mailbox: Blackboard notifications, Outlook calendar invites, Microsoft Forms receipts. You read it in the Outlook app, but Claude can't see it there.

1. Open [Outlook on the web](https://outlook.office.com/mail/options/mail/forwarding) and sign in with your MBSC account.
2. **Settings → Mail → Forwarding → Enable forwarding.**
3. Forward to your personal Gmail and tick **Keep a copy of forwarded messages**. Save.

If MBSC blocks forwarding to outside addresses, a bounce saying forwarding isn't allowed will arrive. Then use **Power Automate** (included with your MBSC Microsoft 365): a flow with the trigger "When a new email arrives (V3)" and the action "Send an email (V2)" to your Gmail. Ask Claude to walk you through it.

## Microsoft 365 connector (optional)

Claude has an official Microsoft 365 connector (Outlook, Teams, SharePoint, OneDrive). For a school account, an MBSC Microsoft administrator has to approve it for the whole college before students can connect. Ask the IT Service Desk whether it's approved. If it is, connect it at claude.ai → Settings → Connectors → Microsoft 365.

## Blackboard content: syllabi, rubrics, announcements, grades

Blackboard's API only works with an app the college registers, so Claude can't sign in by itself. Three ways that work:

1. **Notifications**: Blackboard emails your student mailbox. Forward it (above) and Claude sees them.
2. **Claude in Chrome**: install the [Claude extension](https://claude.com/chrome) on your computer. When you ask ("open Blackboard and summarise the Module 2 syllabus"), Claude uses your own signed-in browser. Your password never leaves the browser.
3. **Files**: download a syllabus or calendar PDF and attach it in a Claude chat. Module dates live in the academic calendar PDF, so this is the fastest way to get them onto your schedule.

## MBSC public website

This cloud environment's network policy blocks `mbsc.edu.sa` and `mbsc.blackboard.com`. To let syncs read public MBSC pages (news, calendars), add both domains: in Claude Code, open the environment menu in the session's title bar, choose **Edit**, then add them under **Network access → allowed domains**. Access levels are explained at [code.claude.com/docs/en/claude-code-on-the-web](https://code.claude.com/docs/en/claude-code-on-the-web).

## Claude and ChatGPT subscriptions

Claude Pro/Max and ChatGPT Plus are app subscriptions; neither includes API access. This workspace runs directly on your Claude subscription: Ask Claude and the sync routine use it, and no API keys are needed. If you also want ChatGPT to see your MBSC mail, connect Gmail inside ChatGPT separately. The two don't need to be linked.
