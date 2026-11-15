# ReviewIQ MVP v1.0

ReviewIQ helps owners of small independent cafés in Kazakhstan turn a batch of customer reviews from 2GIS or Google Maps into a short list of recurring problems. Each problem shows the reviews behind it. The owner then picks one check to do next, with a person and a date. The interface is in English and Kazakh. Reviews can be in Russian, Kazakh or a mix.

## What it does

1. **Entry:** venue name, reviews pasted one per line (3–200 unique, up to 4,000 characters each) and a permission checkbox. "Load demo reviews" fills in 12 fictional reviews.
2. **Analysis:** lines are trimmed, normalised to NFC and de-duplicated. Each review is matched against a small keyword dictionary. A topic counts as recurring only if at least 2 reviews match it. The top 3 are shown as "3 of 12 reviews" with a bar.
3. **Evidence:** the source reviews for a topic, with IDs (R01…), the matched keyword highlighted and an approximate language tag (RU / KZ / Mixed).
4. **Action:** choose one suggested check and enter the person responsible and a review date.
5. **Feedback:** a 1–5 usefulness score and an optional comment.
6. **Export:** downloads `ReviewIQ_session_YYYYMMDD-HHMM.json` with counts, the action, the feedback and an event log. **It contains no review text.**

If no topic repeats, the app says so and shows how many reviews had no match. It never invents a topic.

## Architecture

- **One static page:** `index.html` holds all the HTML, CSS and plain JavaScript. There is no framework, build step or backend.
- **In-memory state:** a single `state` object is re-rendered into the page. Every value a user enters is HTML-escaped before display. Keyword highlighting escapes the text first and only then wraps matches in `<mark>`.
- **Topic dictionary:** the `TOPICS` array at the top of the script. To add a topic, add `{ id, keywords, name: {en, kk}, check: {en, kk} }`.
- **Strings:** all UI text lives in `const i18n = { en, kk }`. Static text is bound through `data-i18n`, `data-i18n-placeholder` and `data-i18n-aria`. Switching language keeps the whole session.
- **Local export:** the JSON file is built in the browser and downloaded as a Blob.

`screens.html` is a review board that shows every state at 1440 px and 375 px in both languages. It loads the app with `index.html?lang=kk&preset=errors` and similar URLs. The app itself doesn't need it.

## Data storage and privacy

- Review text stays in the browser tab's memory and is lost when the tab is closed or the session is reset. It is never sent anywhere and never written to storage.
- The only thing stored is the chosen interface language (`localStorage` key `reviewiq-lang`). If storage is blocked, the app falls back to English.
- No cookies, analytics or tracking.
- Users must confirm they have permission to use the reviews and have removed personal details.

## Dependencies

The only dependency is the IBM Plex Sans, Serif and Mono fonts from Google Fonts. Loading them makes one request to Google, which therefore sees the visitor's IP address. If the fonts can't load (offline or blocked), the app falls back to system fonts that cover Kazakh letters: PT Sans/Serif, Segoe UI, Roboto, Noto or Arial.

## Deploy

- **Locally:** open `index.html` in a browser. A `file://` address works.
- **GitHub Pages:** push the repository, then go to Settings → Pages → Deploy from a branch, choose the branch and `/ (root)`. The app is served at `https://<user>.github.io/<repo>/`.
- **Netlify:** drag the folder onto app.netlify.com/drop, or connect the repository with no build command and publish directory `.`.

## Known limitations

- The dictionary is small (3 topics, 7 keywords). It misses slang, typos, synonyms, negation ("не долго ждали" still counts as waiting time) and many forms of code-switching.
- Counts are keyword matches, not proof of business impact. With fewer than 30 reviews the report is marked as an exploratory sample.
- Nothing is kept between sessions, and there is no integration with 2GIS or Google Maps: reviews are pasted by hand.
- **The Kazakh text is a draft that needs review by a Kazakh speaker.** Every Kazakh string is listed in a comment above `i18n.kk`.
- The language tag is a rough guess based on Kazakh-specific letters and a few common Russian words.
- The pilot terms (7-day trial, then optional 7,000 KZT per month) are informational only. No payment is collected in the app.
