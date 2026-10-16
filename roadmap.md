# Roadmap

- [ ] Fix visuele/UI-problemen in "Live integratie toevoegen" pop-up (AddLiveIntegrationModal): verticale lijst, categoriebalk bovenaan (Aanbevolen / Alles / Games / …)
- [ ] AddComponentModal: "Live Status & Feeds" niet meer als eerste bovenaan; keuzes onder elkaar in de pop-up met categoriebalk bovenaan
- [ ] Totaal bereik verhuizen naar per-koppeling uitklapbare rij (zoals Live Integraties): verifiëren/ongedaan maken, verwijderen, bereik/volgers tonen met platform-specifieke naam (YouTube → abonnees); optie verbergen als platform geen data kan ophalen of echte koppeling vereist
- [ ] QR, privacy & delen: automatische per-user preview-afbeelding (/api/public/og/<handle>.png) en preview-tekst tonen in ShareInspector; gebruiker kan overschrijven

- [ ] Vertalingen: ~2000 vaste teksten in componenten/pagina's omzetten (scan: bun scripts/i18n-hardcoded.ts)
- [x] Legal: privacy OAuth/API section, terms third-party/UGC/indemnity, /data-deletion page, footer links
- [x] YouTube Kaart (vault koppeling, profielkaart, ontkoppelen)
- [ ] db/95_youtube_vault.sql toepassen op Neon (wacht op NEON_API_KEY / Vercel deploy) + Google Cloud: redirect URI, YouTube Data API v3, youtube.readonly scope
- [x] Mails: transactional tags + X-Mailin-Tag, one light design for all mails (incl. fallback frame), sync script
- [ ] Brevo-templates uploaden: `bun scripts/sync-brevo-templates.ts` (wacht op BREVO_API_KEY)
