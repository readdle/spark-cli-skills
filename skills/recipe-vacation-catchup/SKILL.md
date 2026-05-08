---
name: spark-recipe-vacation-catchup
description: >-
  Process a large email backlog after time away: assess by category,
  batch-archive noise, and surface what needs attention.
metadata:
  version: 1.0.0
  requires:
    skills:
      - use-spark
    accessLevel: triage
---

# Recipe: Vacation Catchup

Process a large email backlog after vacation or extended absence. Prioritize ruthlessly, batch-archive noise, and surface only what truly needs attention.

**Prerequisite:** Read the `use-spark` base skill for command reference and filter syntax.

**Access level required:** triage.

## Steps

### Step 1: Assess the backlog

Count unread emails by category for the absence period. Adjust the time window to match how long the user was away:

```bash
spark emails Inbox --filter "category:priority is:unread newer_than:14d"
spark emails Inbox --filter "category:personal is:unread newer_than:14d"
spark emails Inbox --filter "category:invitation newer_than:14d"
spark emails Inbox --filter "category:notification is:unread newer_than:14d"
spark emails Inbox --filter "category:newsletter newer_than:14d"
```

Report the totals: "You have N unread across all categories: X priority, Y people, Z invitations, W notifications, V newsletters."

### Step 2: Process priority mail first

```bash
spark emails Inbox --filter "category:priority is:unread"
```

Read each thread and present the key items. These are the most important and should be handled individually.

### Step 3: Process people mail

```bash
spark emails Inbox --filter "category:personal is:unread"
```

For each, read the thread to check if the matter is still open or was resolved while the user was away:

```bash
spark thread <id>
```

Mark resolved threads as read: `spark action markAsSeen <id>`

### Step 4: Process invitations

```bash
spark emails Inbox --filter "category:invitation"
```

Check which invitations are for past dates (already missed) vs. future dates. Only future invitations need a decision.

### Step 5: Batch-archive notifications

Most notifications older than a few days are no longer actionable:

```bash
spark emails Inbox --filter "category:notification is:unread"
```

Review the senders briefly, then archive in bulk:

```bash
spark action archive <id1> <id2> <id3> <id4> <id5>
```

### Step 6: Batch-archive newsletters

Old newsletters have no urgency:

```bash
spark emails Inbox --filter "category:newsletter"
```

Archive all but the most recent from each sender:

```bash
spark action archive <id1> <id2> <id3>
```

### Step 7: Review new senders (GateKeeper)

New senders may have accumulated during the absence:

```bash
spark emails Inbox --new-senders
```

Accept legitimate contacts and block unwanted ones:

```bash
spark contact-action acceptContact sender@company.com
spark contact-action blockContact spammer@example.com
```

### Step 8: Set reminders for deferred items

For items that need attention but not right now:

```bash
spark action snooze <id> --date 2026-04-12
spark action changeReminder <id> --date 2026-04-15
```

### Step 9: Report

Summarize: "Processed N emails. X need your attention (listed above), Y archived, Z snoozed for later."

## Tips

- The key principle is aggressive batch processing. Don't read every notification and newsletter individually.
- Priority and people mail get individual attention; notifications and newsletters get batch treatment.
- Past-date invitations can be archived without action - the meeting already happened.
- If the user was away for more than two weeks, increase the `newer_than` window accordingly.
- After the catchup, run `recipe-inbox-by-category` to verify the inbox is in good shape.
- GateKeeper review is easy to forget - new senders may include important contacts who emailed during the absence.
- Snooze items to spread the follow-up work across multiple days rather than trying to handle everything at once.
