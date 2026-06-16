# TI Scope Estimator — InDecca Build

A tenant improvement cost estimator that captures leads directly into HubSpot.

---

## How it works

1. Prospect fills out estimator (SF, finish level, space type, timeline, condition)
2. Live cost range updates as they interact
3. They click "Request a free estimate" and fill in contact info
4. On submit → `/api/estimate` proxy calls Claude with HubSpot MCP
5. Claude creates Contact + Company + Deal in your HubSpot account (owner: Tom White)
6. Prospect sees confirmation + a direct link to the HubSpot deal

---

## Deploy to Vercel (preview URL, not public)

### Step 1 — Install Vercel CLI

```bash
npm install -g vercel
```

### Step 2 — Log in

```bash
vercel login
```

### Step 3 — Deploy (preview only, no custom domain yet)

From inside this folder:

```bash
vercel
```

Vercel will ask a few questions:
- **Set up and deploy?** → Yes
- **Which scope?** → your personal account or InDecca team
- **Link to existing project?** → No
- **Project name?** → `indecca-ti-estimator` (or anything)
- **In which directory is your code located?** → `./` (current)

It will give you a preview URL like:
`https://indecca-ti-estimator-abc123.vercel.app`

Share that URL with your team. It is NOT indexed by search engines (the page has `noindex`).

### Step 4 — Add your Anthropic API key

In the Vercel dashboard (vercel.com), go to:
**Project → Settings → Environment Variables**

Add:
| Key | Value |
|-----|-------|
| `ANTHROPIC_API_KEY` | `sk-ant-...` (your key) |

Then redeploy:
```bash
vercel --prod=false
```

### Step 5 — Test the form

Fill in the form on the preview URL. Check:
- [ ] Cost range updates as you adjust sliders/buttons
- [ ] Form validates (name, email, company required)
- [ ] Submission shows "You're in the pipeline" success state
- [ ] A new Contact, Company, and Deal appear in HubSpot (Tom White's name, portal 2873841)
- [ ] The "View deal in HubSpot" link works

---

## Going live (when ready)

1. Add a custom domain in Vercel dashboard → Settings → Domains
   - Suggested: `estimate.indecca.com`
2. Remove the preview banner from `public/index.html` (line ~60, the `<div class="preview-banner">` block)
3. Remove `<meta name="robots" content="noindex, nofollow">` from `public/index.html`
4. Run `vercel --prod` to deploy to production

---

## File structure

```
ti-estimator/
├── api/
│   └── estimate.js       ← Serverless proxy (hides Anthropic API key)
├── public/
│   └── index.html        ← The full estimator page
├── vercel.json           ← Routing config
└── README.md
```

---

## Customization notes

- **PSF ranges** — edit the `PSF.ranges` object in `public/index.html` to match your actual pricing
- **HubSpot owner** — `hubspot_owner_id: "19650197"` is Tom White; change if assigning to someone else
- **HubSpot portal** — deal link uses portal ID `2873841`; already set correctly
- **Pipeline / dealstage** — currently `default` pipeline, `appointmentscheduled` stage; update in `api/estimate.js` prompt if you use a different pipeline

---

## Questions?

Built by Claude for InDecca. Contact tom.white@indecca.com.
