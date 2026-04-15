---
name: use-spark
description: >-
  Use the spark CLI to access the user's Spark email data - list emails,
  search by topic, read threads, check calendar events, find availability,
  look up contacts, and view team info. Use when the user asks about their
  emails, calendar, contacts, meetings, or scheduling.
metadata:
  version: 1.0.0
  requires:
    bins:
      - spark
---

# Using spark

`spark` is a CLI for the Spark email client. Use it to query the user's mailbox, calendar, contacts, meetings, and team data.

## Running spark

```bash
spark <command> [options]
```

**Environment:** `spark` is a thin client that talks to the user's running Spark macOS Desktop app. Do not try to execute it inside a sandbox, container, CI runner, or any environment isolated from the user's desktop session - it will fail to connect.

## Commands

| Command | Description |
|---------|-------------|
| `accounts` | List accounts, calendars, teams, shared inboxes |
| `folders` | List folders/labels with message counts |
| `emails` | List emails with filters and pagination |
| `search` | Hybrid keyword + semantic search with full bodies |
| `thread` | Read full thread - headers, bodies, attachments |
| `events` | List calendar events for a time range |
| `availability` | Find free time slots, optionally with attendees |
| `contacts` | Search contacts by name or email |
| `team` | Show team info, members, shared inboxes, assignments |
| `meetings` | List meeting transcripts |
| `meeting` | Read a single meeting transcript |

### accounts

List all configured accounts with their calendars, teams, and shared inboxes.

```bash
spark accounts
```

Run this first to discover what accounts, calendars, and teams are available.

### folders

List folders with message counts. Output includes folder identifiers in parentheses - use these as arguments to `emails` and `search`. Mailboxes backed by a Google account show `(Gmail labels)` on the **Email Account** or **Shared Inbox** header. Teams show the team name as a usable identifier for `emails`.

```bash
spark folders                        # all accounts
spark folders user@example.com       # single account
```

### emails

List emails with metadata (ID, From, Date, Subject, Flags). Supports pagination and Gmail-style filters.

```bash
spark emails                                                   # Unified Inbox
spark emails user@example.com:Archive                          # specific folder
spark emails "My Team"                                         # all shared threads in a team
spark emails --filter "from:alice@co.com is:unread"             # filtered
spark emails --filter "newer_than:7d has:attachment"            # recent with attachments
spark emails --page 2 --page-size 20                           # pagination
spark emails --order ascending                                 # oldest first
spark emails --new-senders                                     # show only new sender emails
```

**GateKeeper filtering:** When viewing the Inbox with GateKeeper in explicit mode, new sender emails are automatically filtered out and a "New Senders" count is shown at the top. Use `--new-senders` to view those emails.

**Folder identifier formats** (run `folders` to see available ones):

| Format | Example | Meaning |
|--------|---------|---------|
| Bare name | `Inbox`, `Archive` | Unified folder (cross-account) |
| `email` | `user@example.com` | Account inbox shorthand |
| `email:Folder` | `user@example.com:Archive` | Specific account folder |
| `"Team Name"` | `"My Team"` | All shared threads in a team (quote if spaces) |
| `shared@email:Folder` | `shared@co.com:Inbox` | Shared inbox folder |

**Filter operators** (combinable, Gmail-style):

| Operator | Example |
|----------|---------|
| `from:<addr>` | `from:alice@co.com` |
| `to:<addr>` | `to:bob@co.com` |
| `cc:<addr>` | `cc:team@co.com` |
| `subject:<text>` | `subject:"quarterly report"` |
| `before:yyyy/MM/dd` | `before:2026/03/01` |
| `after:yyyy/MM/dd` | `after:2026/01/01` |
| `newer_than:Xd` | `newer_than:7d` (also `w`, `m`, `y`) |
| `older_than:Xd` | `older_than:30d` |
| `has:attachment` | also `document`, `spreadsheet`, `presentation`, `reminder` |
| `is:unread` | also `read`, `starred`, `pinned`, `unreplied` |
| `is:shared` | emails shared to any team (alias for `is:shared_email`) |
| `is:shared_inbox_open` | open items in shared inbox |
| `is:shared_inbox_done` | completed/closed items in shared inbox |
| `category:personal` | also `priority`, `notification`, `newsletter`, `invitation`, `invitation_response` |
| `assigned_to:me` | emails assigned to current user |
| `assigned_to:<email>` | emails assigned to specific teammate |
| `assigned_to:unassigned` | shared inbox items with no assignee |
| `assigned_to:other` | emails assigned to someone else (not me) |
| `assigned_by:me` | emails delegated by current user |
| `filename:<name>` | `filename:report.pdf` |

### search

Hybrid keyword + semantic search returning up to 20 emails with full bodies, sorted by relevance.

```bash
spark search "quarterly report"
spark search "API integration" --filter "from:alice@co.com"
spark search "budget" --in user@example.com:Archive
spark search "vacation" --in user@example.com              # all folders in account
```

| Parameter | Required | Description |
|-----------|----------|-------------|
| `<about>` | Yes | Search topic (positional) |
| `--filter` | No | Gmail-style filter (same operators as `emails`) |
| `--in` | No | Scope: account, team, folder, or shared inbox. All folders if omitted. |

**Use `search` when the user asks about a topic.** It returns email bodies so you can answer questions about content. Use `emails` when listing/browsing by folder or filters without needing bodies.

### thread

Print every message in a thread - headers, full plain-text bodies, and attachment info. After the thread summary line, lists **custom (non-system) folder labels** once for the whole thread, using qualified names like `account@domain.com:MyLabel` (same style as `folders`).

```bash
spark thread 1114                          # by message ID from emails/search output
spark thread --download-attachments 1114   # also fetch attachments via IMAP
```

Use `emails` or `search` to find message IDs (the ID column), then `thread` to read the full conversation.

### events

List calendar events for a time range.

```bash
spark events                                          # today's remaining events
spark events --tomorrow
spark events --week
spark events --week --in user@example.com             # specific account
spark events --week --in user@example.com:Work        # specific calendar
spark events --start 2026-03-16 --end 2026-03-20      # custom range
```

Date formats: `yyyy-MM-dd`, `dd/MM/yyyy`, or `yyyy-MM-ddTHH:mm`.

Run `accounts` to see available calendar accounts and calendar names.

### availability

Find free time slots. Without `--attendees`, shows the user's own availability. With `--attendees`, computes mutual free windows.

```bash
spark availability                                                        # today
spark availability --tomorrow
spark availability --week --attendees alice@co.com
spark availability --start 2026-03-16 --end 2026-03-20 --attendees a@co.com,b@co.com
```

Free slots are within working hours (08:00-20:00), skip weekends, and ignore events marked "free".

### contacts

Search contacts by name or email. Strict match first, then fuzzy fallback.

```bash
spark contacts "john"
spark contacts "example.com"
```

### team

Show team info - metadata, shared inboxes with members, full member list, assigned emails, assignment summary.

```bash
spark team                     # list available teams
spark team "Readdle"           # specific team
```

### meetings

List meeting transcripts with optional filters and pagination.

```bash
spark meetings
spark meetings --filter "newer_than:30d"
spark meetings --filter "subject:standup" --page-size 10
```

Filter operators: `subject:<text>`, `before:yyyy/MM/dd`, `after:yyyy/MM/dd`, `newer_than:Xd`, `older_than:Xd`.

### meeting

Read a single meeting transcript's summary. Optionally include the full transcript and/or notes.

```bash
spark meeting 42                            # summary only
spark meeting --transcript 42               # include transcript
spark meeting --notes 42                    # include notes
spark meeting --transcript --notes 42       # everything
```

Use `meetings` to find meeting IDs.

## Smart Categories

Spark automatically classifies incoming email into six categories. Use the `category:` filter operator with `emails` and `search` to view mail by category.

| Category | Filter | Typical Content |
|----------|--------|-----------------|
| Priority | `category:priority` | Auto-prioritized or manually marked as priority |
| People | `category:personal` | Direct person-to-person email |
| Notifications | `category:notification` | Service notifications, alerts, receipts |
| Newsletters | `category:newsletter` | Subscriptions, digests, marketing |
| Invites | `category:invitation` | Calendar invitations |
| Invite Responses | `category:invitation_response` | RSVPs, accepts, declines |

**Browse by category:**

```bash
spark emails Inbox --filter "category:priority is:unread"       # unread priority mail
spark emails Inbox --filter "category:personal is:unread"       # unread people mail
spark emails Inbox --filter "category:invitation"               # pending invites
spark emails Inbox --filter "category:notification is:unread"   # unread notifications
spark emails Inbox --filter "category:newsletter newer_than:7d" # recent newsletters
```

**Category-first review pattern:** Process inbox in priority order - priority first, then people, then invites, then notifications, then newsletters. This ensures the most important messages get attention first.

## Typical Workflows

**Answer a question about emails:**
1. `spark search "topic"` - find relevant emails with bodies
2. Read the output and answer the user's question

**Find and read a specific email:**
1. `spark emails --filter "from:sender subject:keyword"` - locate the email
2. `spark thread <ID>` - read the full conversation

**Check someone's schedule for a meeting:**
1. `spark availability --tomorrow --attendees alice@co.com,bob@co.com`
2. Suggest a time from the free slots

**Get team workload overview:**
1. `spark team "Team Name"` - see members and assigned emails

**Look up a contact:**
1. `spark contacts "name or domain"` - find their email address

**Review assigned shared inbox items:**
1. `spark emails shared@co.com:Inbox --filter "assigned_to:unassigned"` - unassigned items
2. `spark emails --filter "assigned_to:bob@co.com"` - items assigned to a teammate
3. `spark emails --filter "is:shared_inbox_open"` - all open shared inbox items
4. `spark emails --filter "is:shared_inbox_done"` - completed items

**Read meeting notes:**
1. `spark meetings --filter "newer_than:30d"` - find recent meetings
2. `spark meeting <ID> --transcript --notes` - read summary, transcript, and notes

## Keeping this skill up to date

The CLI and this skill share a single version (`metadata.version` in the front-matter above). New features in newer Spark Desktop releases ship with an updated embedded skill. Backward compatibility is guaranteed within a major version, so the commands and flags documented here keep working - but newly added features won't appear in this file until it is reinstalled.

Check for an update when - and only when - you hit one of these signals:

- The user asks about a Spark feature, command, or flag that isn't documented here.
- A `spark` command fails with an "unknown command" or "unknown option" error you didn't expect.
- The user explicitly says they upgraded Spark or that the skill is out of date.

In those cases:

```bash
spark --version
```

If the printed version is greater than `metadata.version` above, reinstall and re-read this file before answering:

```bash
spark skill --install <parent-of-use-spark>
```

`<parent-of-use-spark>` is the directory containing the `use-spark/` folder this `SKILL.md` lives in.

Do not check on every session or before every command - this skill is the source of truth unless one of the signals above tells you otherwise.

## Tips

- Always quote multi-word arguments: `spark search "project update"`
- Combine filter operators in a single `--filter` string: `--filter "from:alice@co.com is:unread newer_than:7d"`
- Use `folders` to discover exact folder identifiers before passing them to `emails` or `search --in`
- Use `accounts` to discover calendar names before passing them to `events --in`
- The `search` command is best for topic-based queries; `emails` is best for browsing/filtering by metadata
- `thread` returns the full conversation - use it when you need the complete email text, not just metadata
- This release is read-only. If the user asks you to send, archive, snooze, assign, or comment - explain that those actions aren't available yet and they'll need to use Spark Desktop directly.
