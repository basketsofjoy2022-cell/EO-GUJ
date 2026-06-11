# EO Gujarat - GST Declaration Form

Online declaration form for Entrepreneurs Organisation (Gujarat) members.

Live form: https://basketsofjoy2022-cell.github.io/EO-GUJ/

## What happens on submit
1. A signed PDF is generated, named `GST-Declaration-<Member-Name>-<date>.pdf`
2. A copy downloads to the member's device
3. The PDF auto-uploads to the **EO GST Declarations** folder in the eogujarat@gmail.com Google Drive (all copies in one place)
4. A copy is also emailed to eogujarat@gmail.com

The form works even if email fails. As long as the Drive upload or the email succeeds, the submission goes through.

## Setup (one time each)
1. **Drive auto-save:** follow `google-apps-script-SETUP.md`, then paste the web app URL into `index.html` in place of `PASTE_APPS_SCRIPT_URL_HERE`
2. **Email:** the first submission sends an activation email to eogujarat@gmail.com. Click "Activate" in it once.

## Files
- `index.html` - the live form (single file, no build needed)
- `google-apps-script-SETUP.md` - Drive auto-save setup guide
