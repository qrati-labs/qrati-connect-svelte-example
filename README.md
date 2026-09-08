# Qrati Connect — Svelte Example

[![Qrati Connect — embeddable event photo galleries](public/qrati-connect-og.png)](https://qrati.com/connect)

Add a live event photo gallery to Svelte sites with guest uploads, full-screen lightbox, emoji reactions, and contest leaderboards. [Explore Qrati Connect](https://qrati.com/connect) or [view the live Svelte example](https://qrati.com/connect/svelte-example).

Embeds [Qrati Connect](https://qrati.com) into a Svelte + Vite app using the
framework-agnostic **web component** integration, with a host-controlled
light/dark theme. The demo org used here is configured for custom storage on
the Qrati backend — that's a server-side setting with no frontend impact, so
the embed code below is unchanged from a standard org.

## Integration method: Web component

Svelte renders custom elements natively, so we load the element bundle from the
CDN and drop `<qrati-connect>` into the markup:

```svelte
<qrati-connect organization-id={ORGANIZATION_ID} theme={theme} router="hash" />
```

The bundle (and its styles) are loaded once on mount — see `src/App.svelte`.

## Run it

```bash
pnpm install
cp .env.example .env   # optional — sensible defaults are baked in
pnpm dev
```

## Configuration

Set these in `.env` (all optional; the demo org is used as a fallback):

| Variable               | Description                                                |
| ---------------------- | ----------------------------------------------------------|
| `VITE_ORGANIZATION_ID` | Your Qrati organization ID                                |
| `VITE_CDN_URL`         | CDN URL of the web-component bundle (`element/web.es.js`) |

## Other integration methods

- **React component** — `import { QratiConnect }` (see the React/Next/Preact examples).
- **Embed (no-code)** — single `<script>` tag with `data-*` attributes (see the Vanilla JS / Marko / Ember examples).

Docs: <https://www.npmjs.com/package/@qratilabs/qrati-connect>
