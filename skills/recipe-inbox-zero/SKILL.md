---
name: spark-recipe-inbox-zero
description: >-
  Process inbox to zero using category-first triage: review by category,
  read threads, then archive, move, or mark done.
metadata:
  version: 1.0.0
  requires:
    skills:
      - use-spark
    accessLevel: triage
---

# Recipe: Inbox Zero

Process the inbox to zero using category-first triage. Work through categories in priority order, then take action on each email.

**Prerequisite:** Read the `use-spark` base skill for command reference and filter syntax.

**Access level required:** triage.

## Steps

### Step 1: Scan by category (use recipe-inbox-by-category)

Process inbox in priority order:

```bash
spark emails Inbox --filter "category:priority is:unread"
spark emails Inbox --filter "category:personal is:unread"
spark emails Inbox --filter "category:invitation"
spark emails Inbox --filter "category:notification is:unread"
spark emails Inbox --filter "category:newsletter newer_than:7d"
```

### Step 2: Review and act on each email

For emails that need attention, read the thread:

```bash
spark thread <id>
```

Then decide the action:

**Archive** - handled or informational, no further action needed:
```bash
spark action archive <id>
```

**Move to folder** - needs filing:
```bash
spark action moveToFolder <id> --folder "user@example.com:Projects"
```

**Mark as done** - completed/resolved:
```bash
spark action markAsDone <id>
```

**Pin** - important, need to come back to:
```bash
spark action pin <id>
```

**Snooze** - not relevant now, revisit later:
```bash
spark action snooze <id> --date 2026-04-10
```

**Set aside** - save for later review:
```bash
spark action setAside <id>
```

### Step 3: Batch process notifications and newsletters

For categories with many items, batch archive:

```bash
spark action archive <id1> <id2> <id3> <id4>
```

### Step 4: Verify

```bash
spark emails Inbox --filter "is:unread"
```

Confirm the inbox is clear or report what's left.

## Tips

- Work through categories in order - don't jump to newsletters before handling people mail.
- Batch archive is efficient for notifications: list them, confirm with the user, then archive all at once.
- Pin or snooze items that need future attention rather than leaving them unread.
- If an email needs a reply, use `spark draft --reply-to <id>` and then archive - the draft keeps the task alive.
- If you spot miscategorized contacts while triaging, use `spark contact-action changeCategory*` to reclassify all future mail from that sender.
