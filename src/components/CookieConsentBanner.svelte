<script lang="ts">
  import { onMount } from 'svelte';
  import 'vanilla-cookieconsent/dist/cookieconsent.css';
  import {
    initGtm,
    checkConsentRequired,
    ensureCookieConsentInitialized,
    showCookiePreferences,
  } from '../lib/cookieConsent';

  onMount(() => {
    initGtm();

    if (typeof window !== 'undefined') {
      window.showCookiePreferences = () => {
        void showCookiePreferences();
      };
    }

    let cancelled = false;
    void checkConsentRequired().then((required) => {
      if (!cancelled) {
        void ensureCookieConsentInitialized(required);
      }
    });

    return () => {
      cancelled = true;
    };
  });
</script>

<!-- vanilla-cookieconsent injects its own accessible DOM into document.body -->
<div class="cookie-consent-container" aria-hidden="true"></div>
