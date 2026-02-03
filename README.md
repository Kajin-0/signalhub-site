# Signal Hub – Agent‑Native Feeds

Signal Hub is a repository of machine‑readable signal feeds designed to be consumed by clawdbots and other AI agents. Each feed represents a specific type of operational or market signal (e.g., business pain, vendor friction) and can be parsed without scraping or prompt engineering.

## Universal Feed Contract

All feeds adhere to a common JSON envelope:

```json
{
  "feed_id": "<slug>",
  "feed_version": "<semver>",
  "generated_at": "<ISO 8601 timestamp>",
  "cadence": "<frequency>",
  "confidence_method": "<explanation>",
  "items": [ … ]
}
```

The structure of each `items[]` element is defined in `feeds/<feed_id>/schema.json`. See `feeds/business-pain/schema.json` for an example.

### Files per Feed

Each feed folder must include:

- `schema.json` – JSON Schema describing each item.
- `meta.json` – metadata (title, description, cadence, version, fields).
- `latest.json` – latest snapshot of the feed using the envelope above.
- `history/` – directory with dated snapshots (e.g., `2026-02-01.json`).

There is also a top-level `feeds/index.json` that lists all available feeds with minimal metadata so agents can discover them automatically.

## Subscribing via OpenClaw

Subscriptions and billing are handled via OpenClaw. An agent sends a subscription request specifying which feeds it needs, and the end user receives a confirmation prompt through Telegram, WhatsApp, or SMS. After approval, the agent is issued a single API key with access to all subscribed feeds. All feed requests are simple HTTPS GET requests; there is no scraping, only structured JSON.

## Creating Your Own Feed

To contribute a new feed:

1. Fork this repo or create a pull request.
2. Create a folder under `feeds/<your-feed-id>/`.
3. Provide `schema.json`, `meta.json`, `latest.json`, and optionally `history/` snapshots.
4. Add your feed entry to `feeds/index.json`.
5. Ensure your `items` follow the contract defined in your schema.

Adhering to the envelope and consistent versioning ensures your feed is agent‑friendly and composable with others.

## License

Specify your chosen license here (e.g., MIT).

## Use Cases

Here are a few examples illustrating how agents and users can benefit from Signal Hub feeds:

1. **Debugging a build failure:** A local LLM agent running on a developer’s machine can fetch `feeds/toolchain-breakage-patterns/latest.json` and quickly identify common root causes for recent build errors, such as the GCC `-fno-common` change or CMake 4.0 policy removals, without scraping StackOverflow or forums.

2. **Monitoring cloud outages:** An operations agent monitors `feeds/business-pain/latest.json` for new high‑severity outages. When Microsoft 365 experiences a prolonged disruption, the agent alerts the team with a concise summary and links to evidence sources.

3. **Troubleshooting hardware:** A support bot queries `feeds/hardware-troubleshooting/latest.json` when a user’s laptop overheats, returning a ranked list of likely causes (dust buildup, outdated BIOS) and proposed fixes sourced from forum posts and reviews.

Feeds can be polled on‑demand or subscribed to via RSS. For example, the RSS feed for the toolchain breakage patterns can be consumed at  
`feeds/toolchain-breakage-patterns/rss.xml`.  
Similarly, each feed directory exposes `rss.xml` alongside `latest.json` to provide lightweight change notifications.

These examples show how Signal Hub serves as a low‑compute, high‑signal knowledge layer that agents can leverage to answer questions immediately or trigger follow‑up research when needed.
