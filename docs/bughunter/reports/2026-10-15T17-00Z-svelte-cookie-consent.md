# BugHunter audit: Svelte cookie-consent workflow

- **Workflow:** Svelte demo cookie consent and Google Consent Mode initialization.
- **Repository / audited SHA:** `qrati-connect-svelte-example` / `89354235fdd3b9cb7c862ca5a1add090bbeb9cc7`.
- **Outcome:** No reproduced product defect. Report-only; no production change or untested behavioral assumption introduced.
- **Severity:** No confirmed issue. Testability gap: moderate.

## Flow and scope

The app mounts `CookieConsentBanner` (`src/App.svelte:388`). Its `onMount` calls `initGtm`, exposes a preferences callback on `window`, checks geo consent requirements, and passes the result to `ensureCookieConsentInitialized` (`src/components/CookieConsentBanner.svelte:11-30`). The helper caches the geo response in `sessionStorage`, defaults to requiring consent on lookup failure, configures CookieConsent, and maps category callbacks to Google Consent Mode updates (`src/lib/cookieConsent.ts:91-113,121-247`). The footer can request preferences lazily through the same helper (`src/App.svelte:48-54`, `src/lib/cookieConsent.ts:252-261`).

Bounded cone included the Svelte mount/entry, banner lifecycle, consent state and geo lookup, GTM setup, lazy preferences path, config usage, package scripts and available test wiring. Excluded deployed GTM configuration, remote geo endpoint implementation, and browser behavior on production domains; those could not be verified from this repository.

## Findings

1. **No confirmed functional/security defect.** Geo lookup failures conservatively require opt-in; the initialization promise prevents duplicate concurrent CookieConsent setup, and banner cleanup prevents starting initialization after unmount once the lookup resolves (`src/lib/cookieConsent.ts:102-112,121-126`; `src/components/CookieConsentBanner.svelte:20-29`). These safeguards were source-reviewed, not runtime browser-tested.
2. **Test coverage gap (moderate):** `package.json` defines build and type-check scripts but no test script or test dependency. The consent boundary behaviors (geo cache, lookup failure, regional mode, Gtag consent mapping, lazy preferences and mount cleanup) have no automated regression evidence in this example. Adding a test harness solely for this audit was not warranted without confirming intended test conventions and reliable DOM mocking for the third-party consent library.
3. **Potential follow-up, unverified:** `ensureCookieConsentInitialized` retains a rejected `initPromise` permanently, preventing a later retry after CookieConsent initialization rejects (`src/lib/cookieConsent.ts:121-126,243-246`). No failure/retry reproduction or definitive library rejection contract was established; this is recorded for targeted follow-up, not classified as a bug.

## Tests and verification

- Unit: none defined or run; test suite absent.
- Integration/E2E: none defined or run; no browser workflow verified.
- Coverage: not measured.
- Performance: not measured.
- Source review: traced via CodeGraph; corroborated against current checkout source.
- `git diff --cached --check`: passed.
- Build/type-check: not run; `pnpm run check` could not start because `pnpm` is not installed (`command not found`); `node_modules` is absent.

## Recommendations

Add focused tests for consent-mode choice mapping and conservative geo failure behavior when the repository adopts a test harness. Separately, verify whether CookieConsent.run can reject and whether clearing a failed initialization promise is safe before changing retry semantics. Validate the deployed GTM container and geo endpoint in a separately authorized production configuration review.

## Research

- Google Consent Mode API reference: https://developers.google.com/tag-platform/security/guides/consent
- CookieConsent v3 documentation: https://cookieconsent.orestbida.com/essential/getting-started.html

Google documents the need to set default consent and update it after user interaction, including region-specific defaults.[1] CookieConsent v3 is the configured consent library in this example.[2]

## Sources

[1] Google for Developers, “Set up consent mode on websites” — https://developers.google.com/tag-platform/security/guides/consent
[2] CookieConsent, “Getting started” — https://cookieconsent.orestbida.com/essential/getting-started.html

## PR state

Report-only PR; no merge or deployment performed. CI/check state is recorded in the final receipt after opening the PR.