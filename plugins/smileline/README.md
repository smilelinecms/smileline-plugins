# Smileline for Claude

Work in your [Smileline](https://smileline.io) dental practice from Claude:
check today's calls and tasks, find patients and enquiries, move an enquiry
along, log call outcomes, add tasks and notes, read and answer messages, book
appointments and read practice reports.

## What it contains

- **One Smileline connection**, `smileline` → `https://mcp.smileline.io/mcp`,
  for practices in Europe and the United States alike. **Settings →
  Integrations → Apps → AI agents** in Smileline shows your address.
- **A skill** (`skills/smileline`) that tells Claude how Smileline's
  operations fit together for everyday front-desk work.

The plugin runs no code on your computer and needs no API key.

## Install

In Claude Code:

```bash
/plugin install smileline --marketplace smilelinecms/smileline-plugins
```

## Sign in

In Claude Code, run `/mcp`, choose `smileline` and sign in. Your browser
opens Smileline and asks where your practice's account is (Europe or the
United States). Sign in, choose the practice, and approve. Only
practice owners and managers can approve. The connection belongs to that one
practice, and Claude acts as you with your role's permissions. Every change it
makes is recorded in the practice's activity log under your name. On claude.ai
and in Cowork, connect it from the plugin's **Connectors** tab.

You can remove the connection at any time under **Settings → Integrations →
Apps** in Smileline.

## Data

Through this connection, Claude reads and changes practice data, including
patient details, on your behalf. That data is sent to Anthropic and processed
under Anthropic's terms rather than Smileline's. Connect only if your practice
accepts that responsibility. The plugin itself sends nothing anywhere else.

## Not available to Claude

Card payments and refunds, billing purchases, paid SMS registration and
numbers, ad spend, AI image and video generation, and AI-call setup stay in
the Smileline app. Sending a patient a deposit link and pausing a campaign are
available. Clinical records, calls and call recordings, and your own account
also stay in the app.

## Links

- Documentation: <https://docs.smileline.io/guides/settings/ai-agents>
- API operations: <https://docs.smileline.io/reference/mcp>
- Privacy: <https://smileline.io/legal/privacy>
- Terms: <https://smileline.io/legal/terms>
- Support: giulio@smileline.io

Published by SmileLine Technologies Ltd under the MIT license.
