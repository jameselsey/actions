# discord-notify

Sends a formatted Discord embed notification with retry logic and error handling.

## Usage

```yaml
- uses: jameselsey/actions/discord-notify@main
  with:
    webhook-url: ${{ secrets.DISCORD_WEBHOOK_URL }}
    title: 'Build Failed'
    description: 'Something went wrong on main'
    color: '15158332'   # red
    priority: 'critical'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `webhook-url` | ✅ | — | Discord webhook URL |
| `title` | ✅ | — | Embed title |
| `description` | | `''` | Main message body |
| `color` | | `3447003` | Embed color in decimal (blue) |
| `fields` | | `[]` | JSON array of `{name, value, inline}` objects |
| `priority` | | `medium` | `low` / `medium` / `high` / `critical` |
| `footer` | | `''` | Footer text |

### Color reference

| Color | Decimal |
|-------|---------|
| Red | `15158332` |
| Orange | `16744272` |
| Yellow | `16776960` |
| Green | `5763719` |
| Blue | `3447003` |

## Features

- Rich Discord embed formatting
- Automatic timestamp
- Priority emoji appended to footer
- Exponential backoff retry (up to 2 retries: 2s, 4s)
- Validates webhook URL and fields JSON before sending
