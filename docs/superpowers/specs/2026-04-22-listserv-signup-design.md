# Listserv Sign-Up — Design Spec
**Date:** 2026-04-22

## Overview

A standalone sign-up page for the ERC newsletter listserv. Users enter their name and email; the page automatically captures the date and time of submission and writes the record to a Google Sheet via an existing Google Apps Script webhook.

---

## File

| Path | Description |
|------|-------------|
| `listserv-signup/index.html` | Single self-contained file — HTML, CSS, JS in one file. No build step. |

The new page will be linked from the root `index.html` alongside the other tools.

---

## Branding & Libraries

Matches all existing erc-tools pages exactly:

| Element | Value |
|---------|-------|
| Primary color | `#500000` (Aggie Maroon) |
| Fonts | Work Sans (headings/labels) + Open Sans (body/inputs) via Google Fonts |
| Bootstrap | 5.3.3 via CDN |
| Body background | `#ffffff` |

No DataTables needed — this page has no table.

---

## Layout

Centered white card on a white page. The card has:
- A maroon (`#500000`) header bar with the title **"Subscribe to the ERC Newsletter"** in white Work Sans
- The form fields and submit button inside the card body
- Max-width ~480px, horizontally centered

---

## Form

```
[ Name        ]
[ Email       ]
[ Subscribe   ]  ← maroon button
```

- `autocomplete="off"` on the `<form>` element and both `<input>` elements
- Both fields are required
- Name: `type="text"`
- Email: `type="email"` (browser validates email format)
- Submit button: maroon fill, Work Sans, reads "Subscribe"

---

## Submission Flow

1. User clicks **Subscribe**
2. Button immediately becomes disabled and shows "Submitting…" to prevent double-submit
3. JavaScript captures:
   - `date_subscribed` — today's date, formatted `YYYY-MM-DD`
   - `time` — current local time, formatted `HH:MM:SS`
4. POSTs JSON to the Apps Script webhook:
   ```json
   {
     "action": "listserv_signup",
     "name": "<user input>",
     "email": "<user input>",
     "date_subscribed": "2026-04-22",
     "time": "14:32:05"
   }
   ```
5. **On success:** form is hidden and replaced with the thank-you message (see below)
6. **On error:** button re-enables, an inline red error message appears below the button: *"Something went wrong. Please try again."*

---

## Webhook

| Property | Value |
|----------|-------|
| URL | `https://script.google.com/macros/s/AKfycbxPhQtZrDiIeNhjAzDCOFkYfwhhh2WPuqPGRFsvslZyM5XwruEBNM1uOPOu4EWerGQW/exec` |
| Method | `POST` |
| Content-Type | `application/json` |
| Google Sheet ID | `1U_kFmkji6tPeD6OzRVcOWkaWz46pcQcogD1nYtObO2E` |

The Apps Script is already deployed and handles the `listserv_signup` action.

---

## Google Sheet Columns

| Column | Source |
|--------|--------|
| `name` | User input |
| `email` | User input |
| `date_subscribed` | Auto-captured at submission (JS `Date`) |
| `time` | Auto-captured at submission (JS `Date`) |

---

## Success State

The form card body is replaced with:

> **Thank you for subscribing!**
> You have been successfully added to our mailing list. We look forward to sharing our latest research findings and keeping you connected with our ongoing work at the [Education Research Center](https://erc.cehd.tamu.edu/).

"Education Research Center" is a hyperlink to `https://erc.cehd.tamu.edu/` opening in a new tab.

---

## Autocomplete

Browser autofill/autocomplete is suppressed via:
- `autocomplete="off"` on `<form>`
- `autocomplete="off"` on the name `<input>`
- `autocomplete="off"` on the email `<input>`

---

## Root Index Update

Add a link to `listserv-signup/` in `/index.html`:
```html
<li><a href="./listserv-signup/">Listserv Sign-Up</a></li>
```
