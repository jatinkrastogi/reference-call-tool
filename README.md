Request a Reference Call — GitHub Pages version

A standalone, no-login version of the Reference Call Request tool, built to run on GitHub Pages 
so anyone with the link can use it, whether or not they have a Claude account.

What's different from the Claude artifact version

This version has no server and no Claude runtime behind it, so a few things work differently:
Feature	Claude artifact version	This GitHub version
Reference list	Shared live storage, editable in-app	Loaded from data.json in this repo — edit the file on GitHub to update it
Admin passcode	Client-side gate	Same — still not real security, just a speed bump
Reference request history	Logged in-app on "Draft Email," viewable as a table	Logged to a Google Form → Google Sheet on "Draft Email" (needs one-time setup below)
Sync from SharePoint	Pulls live data via Claude + Microsoft 365	Not possible without a real backend — removed. Use the JSON-generator helper in Admin instead
⸻
1. Publish it on GitHub Pages

1. Create a new public GitHub repository (e.g. reference-call-tool).
2. Upload these three files to the repo root: index.html, data.json, netchex-logo.png.
3. Go to Settings → Pages. Under "Build and deployment", choose Deploy from a branch, 
4. pick your default branch (usually main) and / (root), then save.
5. GitHub gives you a public URL, typically https://<your-username>.github.io/reference-call-tool/. 
6. That's the link to send to your AEs.

Changes to any file (especially data.json) redeploy automatically within about a minute of 
committing.
⸻
2. Update the reference list

The list lives entirely in data.json. To update it:

1. On GitHub, open data.json and click the pencil (edit) icon.
2. Edit the JSON directly, or use the in-app helper: open the tool, go to Admin Console → 
3. Manage Reference List, paste rows copied straight from the sheet (in its current column 
4. order: Industry, Account, Company Code, State, Region, Employee Count, Contact, Account Owner, 
5. Account Owner Email, Note) into the box, click Generate JSON, then copy the result and 
6. paste it in to replace the file's contents on GitHub.
7. Commit. The live site updates automatically shortly after.

Each row needs these fields: industry, account, contact, owner, ownerEmail, note, 
state, region, employeeCount (use "" for an empty note — it's how Client Success Center 
accounts are flagged and de-prioritized in matching; region should be one of the standard 
US Census divisions, e.g. "West South Central", since that's what the optional region filter 
matches against; employeeCount should be a plain number with no commas or text).
⸻
3. Set up search logging (Google Form)

GitHub Pages can't write data anywhere on its own, so logging goes through a Google Form 
submitting into a Google Sheet. One-time setup:

1. Create the form. Go to forms.google.com → new form. Add exactly these 7 questions, in 
2. this order, all set to Short answer:
    1. AE Name
    2. Prospect Name
    3. Industry Requested
    4. Selected Account
    5. Selected Industry
    6. Account Owner
    7. Match Type

2. Link it to a Sheet. In the form editor, go to the Responses tab, click the green 
3. Sheets icon, and create a new spreadsheet. This is where every search will land — Google Forms 
4. adds its own timestamp column automatically.

3. Get the pre-filled link. Open the live form (preview / eye icon), click the ⋮ menu top 
4. right, choose Get pre-filled link. Type anything into all 7 fields, click Get link, then 
5. Copy link.

4. Extract the IDs. Paste that copied link somewhere you can read it — it'll look like:
5. https://docs.google.com/forms/d/e/1FAIpQLSxxxxxxx/viewform?usp=pp_url&entry.111111111=x&entry.222222222=x...
    - The part after /d/e/ and before /viewform is your form ID.
    - Each entry.NNNNNNNNN= right before an & (or the end) is that question's entry ID, in 
    - the same order you added the questions.

5. Fill in index.html. Near the top of the <script> section, replace:
6. const GOOGLE_FORM_ACTION = "https://docs.google.com/forms/d/e/YOUR_FORM_ID/formResponse";
7. const FORM_ENTRY = {
8.   aeName: "entry.111111111",
9.   prospectName: "entry.222222222",
10.   industryRequested: "entry.333333333",
11.   selectedAccount: "entry.444444444",
12.   selectedIndustry: "entry.555555555",
13.   accountOwner: "entry.666666666",
14.   matchType: "entry.777777777"
15. };
16. const RESPONSE_SHEET_URL = "";
17. with your real form ID, your real entry IDs (matched to the right question), and the link to 
18. the Google Sheet from step 2 (so the "Open response sheet" button in Admin works).

6. Commit. Searches will now log to your Sheet within a few seconds of an AE clicking 
7. "Find references."

Until this is configured, the tool shows a banner at the top saying logging isn't set up yet, 
and it will simply skip logging rather than error out.
⸻
4. Change the admin passcode (optional)

Find this line near the top of index.html's script and change the value:
const ADMIN_CODE = "Netchex123";
Remember this is visible to anyone who views the page source — it keeps casual AEs out of the 
admin panel, it isn't real access control.
⸻
5. Test it locally before publishing (optional)

Opening index.html directly by double-clicking it won't load data.json correctly (browsers 
block local file fetches). Instead, from this folder, run a tiny local server:

python3 -m http.server 8000
# or: npx serve

Then open http://localhost:8000 in your browser.
