# Listserv Sign-Up Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a standalone listserv sign-up page that collects name and email, auto-captures date/time, and POSTs to an existing Google Apps Script webhook that writes to Google Sheets.

**Architecture:** Single self-contained `listserv-signup/index.html` file (HTML + CSS + JS). On submit, JavaScript captures the current date/time and POSTs JSON to the Apps Script webhook. On success the form is replaced with a thank-you message; on error an inline message prompts the user to retry.

**Tech Stack:** Vanilla HTML/CSS/JS, Bootstrap 5.3.3 (CDN), Google Fonts (Work Sans + Open Sans)

---

## File Map

| Action | Path | Responsibility |
|--------|------|----------------|
| Create | `listserv-signup/index.html` | Complete sign-up page — structure, styles, form, submission logic |
| Modify | `index.html` | Add link to `listserv-signup/` in the tools list |

---

### Task 1: Create page shell with card layout and branding

**Files:**
- Create: `listserv-signup/index.html`

- [ ] **Step 1: Create the file with full HTML shell**

Create `listserv-signup/index.html` with this exact content:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1"/>
  <title>ERC Newsletter Sign-Up</title>

  <link rel="preconnect" href="https://fonts.googleapis.com"/>
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin/>
  <link href="https://fonts.googleapis.com/css2?family=Work+Sans:wght@400;500;600;700&family=Open+Sans:wght@400;600&display=swap" rel="stylesheet"/>
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet"/>

  <style>
    *, *::before, *::after { box-sizing: border-box; }
    body {
      font-family: 'Open Sans', sans-serif;
      background: #fff;
      color: #666;
      font-size: 14px;
      line-height: 1.7;
      margin: 0;
      min-height: 100vh;
      display: flex;
      align-items: flex-start;
      justify-content: center;
      padding: 60px 20px;
    }
    h1, h2, h3, h4, h5, h6 {
      font-family: 'Work Sans', sans-serif;
      color: #333;
      line-height: 1.3;
    }

    /* ── Card ─────────────────────────────────────── */
    .signup-card {
      width: 100%;
      max-width: 480px;
      border: 1px solid #e2e2e2;
      border-radius: 6px;
      overflow: hidden;
      background: #fff;
    }
    .signup-card-header {
      background: #500000;
      padding: 18px 24px;
    }
    .signup-card-header h1 {
      color: #fff;
      font-size: 1.1rem;
      font-weight: 600;
      margin: 0;
    }
    .signup-card-body {
      padding: 24px;
    }

    /* ── Form fields ──────────────────────────────── */
    .field-label {
      font-family: 'Work Sans', sans-serif;
      font-size: 0.72rem;
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.06em;
      color: #999;
      display: block;
      margin-bottom: 5px;
    }
    .field-input {
      font-family: 'Open Sans', sans-serif;
      font-size: 0.875rem;
      color: #333;
      width: 100%;
      border: 1px solid #e2e2e2;
      border-radius: 4px;
      padding: 9px 12px;
      outline: none;
      transition: border-color 0.15s;
    }
    .field-input:focus {
      border-color: #500000;
      box-shadow: 0 0 0 3px rgba(80,0,0,0.08);
    }
    .field-group {
      margin-bottom: 16px;
    }

    /* ── Submit button ────────────────────────────── */
    .btn-subscribe {
      font-family: 'Work Sans', sans-serif;
      font-size: 0.875rem;
      font-weight: 600;
      background: #500000;
      color: #fff;
      border: none;
      border-radius: 4px;
      padding: 10px 24px;
      cursor: pointer;
      transition: background 0.15s;
      margin-top: 4px;
    }
    .btn-subscribe:hover:not(:disabled) { background: #3a0000; }
    .btn-subscribe:disabled {
      background: #999;
      cursor: not-allowed;
    }

    /* ── Error message ────────────────────────────── */
    .error-msg {
      font-size: 0.82rem;
      color: #c0392b;
      margin-top: 10px;
      display: none;
    }

    /* ── Success state ────────────────────────────── */
    .success-body {
      display: none;
      padding: 24px;
    }
    .success-title {
      font-family: 'Work Sans', sans-serif;
      font-size: 1rem;
      font-weight: 600;
      color: #333;
      margin-bottom: 10px;
    }
    .success-body p {
      font-size: 0.875rem;
      color: #555;
      line-height: 1.7;
      margin: 0;
    }
    .success-body a {
      color: #500000;
    }
    .success-body a:hover { color: #3a0000; }
  </style>
</head>
<body>

  <div class="signup-card">

    <div class="signup-card-header">
      <h1>Subscribe to the ERC Newsletter</h1>
    </div>

    <!-- Form state -->
    <div class="signup-card-body" id="form-body">
      <form id="signup-form" autocomplete="off" novalidate>

        <div class="field-group">
          <label class="field-label" for="field-name">Name</label>
          <input
            class="field-input"
            type="text"
            id="field-name"
            name="name"
            autocomplete="off"
            required
          />
        </div>

        <div class="field-group">
          <label class="field-label" for="field-email">Email</label>
          <input
            class="field-input"
            type="email"
            id="field-email"
            name="email"
            autocomplete="off"
            required
          />
        </div>

        <button class="btn-subscribe" type="submit" id="submit-btn">Subscribe</button>
        <div class="error-msg" id="error-msg">Something went wrong. Please try again.</div>

      </form>
    </div>

    <!-- Success state (hidden until submission succeeds) -->
    <div class="success-body" id="success-body">
      <div class="success-title">Thank you for subscribing!</div>
      <p>You have been successfully added to our mailing list. We look forward to sharing our latest research findings and keeping you connected with our ongoing work at the <a href="https://erc.cehd.tamu.edu/" target="_blank" rel="noopener">Education Research Center</a>.</p>
    </div>

  </div>

<script>
const WEBHOOK_URL = 'https://script.google.com/macros/s/AKfycbxPhQtZrDiIeNhjAzDCOFkYfwhhh2WPuqPGRFsvslZyM5XwruEBNM1uOPOu4EWerGQW/exec';

document.getElementById('signup-form').addEventListener('submit', async function (e) {
  e.preventDefault();

  const name  = document.getElementById('field-name').value.trim();
  const email = document.getElementById('field-email').value.trim();
  const btn   = document.getElementById('submit-btn');
  const errEl = document.getElementById('error-msg');

  // Basic client-side validation
  if (!name || !email) return;

  // Capture date and time at moment of submission
  const now = new Date();
  const date_subscribed = now.toISOString().slice(0, 10); // YYYY-MM-DD
  const time = now.toTimeString().slice(0, 8);            // HH:MM:SS

  // Disable button and hide any previous error
  btn.disabled = true;
  btn.textContent = 'Submitting…';
  errEl.style.display = 'none';

  try {
    const res = await fetch(WEBHOOK_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ action: 'listserv_signup', name, email, date_subscribed, time })
    });

    if (!res.ok) throw new Error('Non-OK response');

    // Show success state
    document.getElementById('form-body').style.display    = 'none';
    document.getElementById('success-body').style.display = 'block';

  } catch {
    // Re-enable button and show error
    btn.disabled = false;
    btn.textContent = 'Subscribe';
    errEl.style.display = 'block';
  }
});
</script>

</body>
</html>
```

- [ ] **Step 2: Open in browser and verify the card renders correctly**

Open `listserv-signup/index.html` in a browser. Check:
- Maroon header bar with white "Subscribe to the ERC Newsletter" title
- Name and email fields visible, no autocomplete suggestions when you start typing
- Maroon "Subscribe" button

- [ ] **Step 3: Commit**

```bash
git add listserv-signup/index.html
git commit -m "Add listserv sign-up page"
```

---

### Task 2: Verify form submission and success/error states

**Files:**
- Modify (if needed): `listserv-signup/index.html`

- [ ] **Step 1: Test autocomplete is disabled**

Click into the Name field and start typing a name you've used before in your browser. Confirm no autocomplete dropdown appears. Repeat for Email.

- [ ] **Step 2: Test required field validation**

Click Subscribe without filling in either field. Confirm nothing is submitted (the `required` attributes and the `if (!name || !email) return` guard prevent it). The button should not disable.

- [ ] **Step 3: Test successful submission**

Fill in a real name and email, click Subscribe. Confirm:
- Button text changes to "Submitting…" and becomes disabled
- After the response, the form disappears and the thank-you message appears
- "Education Research Center" in the success message is a clickable maroon link
- Check the Google Sheet (`https://docs.google.com/spreadsheets/d/1U_kFmkji6tPeD6OzRVcOWkaWz46pcQcogD1nYtObO2E`) to confirm a new row was appended with name, email, date, and time

- [ ] **Step 4: Test error handling (optional — simulate a network failure)**

Temporarily change `WEBHOOK_URL` in the JS to a bad URL (e.g. `https://example.invalid`), fill in the form, submit. Confirm:
- Error message "Something went wrong. Please try again." appears in red below the button
- Button re-enables and reads "Subscribe" again

Revert the URL change when done.

---

### Task 3: Link new page from root index

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add the link**

In `index.html`, find:
```html
    <li><a href="./newsletters/">Newsletter Archive</a></li>
```

Add a new list item after it:
```html
    <li><a href="./listserv-signup/">Listserv Sign-Up</a></li>
```

- [ ] **Step 2: Verify in browser**

Open the root `index.html`. Confirm "Listserv Sign-Up" appears in the tools list and clicking it navigates to the sign-up page.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "Add listserv sign-up link to tools index"
```
