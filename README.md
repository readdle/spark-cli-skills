# Spark CLI Skills

AI agent skills for the [Spark](https://sparkmailapp.com) CLI. Install individual skills or the full set to give your AI agent structured workflows for email, calendar, contacts, teams, and meetings.

## Requirements

- macOS with a recent build of [Spark Desktop](https://sparkmailapp.com), signed in to at least one account.
- Spark CLI enabled: in Spark, go to **Settings → AI Agents → Spark CLI Setup** and follow the prompts.

## Skills Index

### Base Skill

| Skill | Description |
|-------|-------------|
| [use-spark](skills/use-spark/SKILL.md) | Complete command reference for the Spark CLI - mail, calendar, contacts, teams, meetings |

### Recipes

| Skill | Description |
|-------|-------------|
| [recipe-inbox-by-category](skills/recipe-inbox-by-category/SKILL.md) | Process inbox in priority order by smart category |
| [recipe-morning-standup](skills/recipe-morning-standup/SKILL.md) | Today's events + unread by category + team assignments |
| [recipe-meeting-prep](skills/recipe-meeting-prep/SKILL.md) | Gather context for an upcoming meeting |
| [recipe-invitation-manager](skills/recipe-invitation-manager/SKILL.md) | Process pending invitations with availability checks |
| [recipe-schedule-meeting](skills/recipe-schedule-meeting/SKILL.md) | Find mutual availability and suggest times |
| [recipe-end-of-day](skills/recipe-end-of-day/SKILL.md) | Close out the day, review loose ends, preview tomorrow |
| [recipe-weekly-digest](skills/recipe-weekly-digest/SKILL.md) | Weekly summary of events, email, and team status |
| [recipe-multi-account-review](skills/recipe-multi-account-review/SKILL.md) | Review inbox status across multiple accounts |
| [recipe-topic-timeline](skills/recipe-topic-timeline/SKILL.md) | Build a chronological narrative from meetings and emails |
| [recipe-stakeholder-brief](skills/recipe-stakeholder-brief/SKILL.md) | Compile a relationship dossier from all interactions with a person |
| [recipe-shared-inbox-status](skills/recipe-shared-inbox-status/SKILL.md) | Review shared inbox health - open, done, and unassigned items |
| [recipe-team-workload](skills/recipe-team-workload/SKILL.md) | Audit team assignment distribution and spot imbalances |
| [recipe-calendar-audit](skills/recipe-calendar-audit/SKILL.md) | Analyze meeting load, back-to-back chains, and free blocks |

## Installation

```bash
# Install all skills at once
npx skills add https://github.com/readdle/spark-cli-skills

# Or pick only what you need
npx skills add https://github.com/readdle/spark-cli-skills/tree/main/skills/use-spark
npx skills add https://github.com/readdle/spark-cli-skills/tree/main/skills/recipe-inbox-by-category
```

## License

[MIT](LICENSE) © Spark Mail Limited
