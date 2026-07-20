# CryptoPulse — Bot specification

**Archetype:** finance

**Voice:** professional and concise — write every user-facing message, button label, error, and empty state in this voice.

Telegram bot for tracking crypto prices with customizable price threshold and percent change alerts, morning summaries, and quiet hours. Users manage private watchlists while owners receive aggregated usage metrics via owner-only commands.

> This is the complete contract for the bot. Implement EVERY entry point, flow, feature, integration, and edge case below. The completeness review checks the bot against this document after each build pass.

## Primary audience

- private crypto traders
- investors
- crypto price monitors

## Success criteria

- Users receive accurate price alerts with timestamps and change metrics
- Users can manage watchlists with inline buttons
- Owners receive privacy-preserving aggregated stats via /stats command

## Entry points

Every feature must be reachable from the bot's command/button surface (button-first; only /start and /help are slash commands).

- **/start** (command, actor: user, command: /start) — Onboarding flow with default settings
- **/add** (command, actor: user, command: /add) — Add coin to watchlist with popular ticker buttons
- **/watchlist** (command, actor: user, command: /watchlist) — View and manage watchlist items
- **/price** (command, actor: user, command: /price) — Check current price for specific ticker or full watchlist
- **/stats** (command, actor: owner, command: /stats) — View aggregated user and alert metrics

## Flows

### Onboarding
_Trigger:_ /start

1. Greeting with quick tips
2. Set default timezone and quiet hours
3. Show main menu buttons

_Data touched:_ User profile

### Add coin
_Trigger:_ /add

1. Show popular ticker buttons
2. Handle ticker selection/typing
3. Add to watchlist with default alert settings

_Data touched:_ Watchlist item

### Configure alerts
_Trigger:_ Configure alerts button

1. Set price thresholds
2. Set percent change rules
3. Toggle alert types

_Data touched:_ Watchlist item

### Morning summary
_Trigger:_ Scheduled time

1. Check user preferences
2. Generate summary with prices and thresholds
3. Send if enabled

_Data touched:_ User profile, Watchlist item

### Owner stats
_Trigger:_ /stats

1. Verify owner identity
2. Generate metrics report
3. Send via private message

_Data touched:_ Global stats

## Data entities

Durable data (must survive a restart) uses the toolkit's persistent store, never in-memory maps.

- **User profile** _(retention: persistent)_ — User preferences and settings
  - fields: telegram user id, timezone, quiet hours, morning summary time, notification preferences
- **Watchlist item** _(retention: persistent)_ — Monitored crypto ticker with alert rules
  - fields: ticker symbol, alert types, threshold values, last-known-price, last-alert-timestamp, active status
- **Global stats** _(retention: persistent)_ — Aggregated usage metrics for owner
  - fields: total users, alert counts by ticker, alert type distribution

## Integrations

- **Telegram** (required) — User messaging and inline buttons
- **Price API** (required) — Market price data with retry logic
Call external APIs against their real contract (correct endpoints, ids, params); credentials from env. Do not fake responses.

## Owner controls

- /stats command for aggregated metrics

## Notifications

- Price threshold alerts
- Percent change alerts
- Morning price summaries
- Quiet hours suppression/delay

## Permissions & privacy

- All user data is private and isolated
- Owner-only /stats command
- No user data sharing between accounts

## Edge cases

- Price API failures with retry logic
- Unknown/invalid ticker normalization
- Alert suppression during quiet hours

## Required tests

- Add coin flow with validation
- Alert triggering with cooldown/hysteresis
- Morning summary delivery with timezone handling

## Assumptions

- Default quiet hours 23:00-07:00
- 4-hour pause-after-alert cooldown
- 0.5% hysteresis for threshold alerts
