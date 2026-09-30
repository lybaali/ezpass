# Publish your E-ZPass Tracker on Netlify

This package gives your existing tracker a second public address ending in `.netlify.app`. It connects to your current hosted tracker; both addresses use the same vehicles, transaction records, and uploaded documents. The original tracker must remain published and public. This is an address layer, not an independent database or a complete migration away from the current host.

## Option 1: Deploy from GitHub using your browser

1. Download and unzip `ezpass_tracker_netlify.zip`.
2. Sign in to GitHub and create a repository named `ezpass-tracker-netlify`. A private repository is fine. No report PDFs or credentials are included in this package.
3. In that repository, choose **Add file > Upload files**.
4. Open the extracted `ezpass_tracker_netlify` folder. Upload its contents, including the `netlify` and `public` folders, `netlify.toml`, and `package.json`. Commit the files. Do not put an extra `ezpass_tracker_netlify` folder around the project in the repository.
5. Check that `netlify.toml` is visible in the repository's top-level file list and that the function is located at `netlify/edge-functions/tracker.js`.
6. In your Netlify account, go to your team projects and select **Add new project > Import an existing project**.
7. Select **GitHub**, grant Netlify access to this repository, then select `ezpass-tracker-netlify`.
8. Use these settings:
   - Branch: `main` (or the repository's default branch).
   - Base directory: leave blank.
   - Build command: leave blank. No compilation step is needed.
   - Publish directory: `public`.
   - Environment variables: none required.
   Netlify reads `netlify.toml` and deploys the Edge Function along with the public directory.
9. Click **Deploy** and wait for the deployment to finish.
10. Open the production URL shown by Netlify. The working fleet dashboard should appear, not a setup placeholder page.
11. Optional: in the project's domain settings, edit its Netlify subdomain to an available name. Use the actual URL Netlify assigns; no preferred name is guaranteed available.

Do not deploy only `public/` with drag and drop. The Edge Function is required for the tracker to work.

## Option 2: Deploy from your own computer without GitHub

Install Node.js LTS from https://nodejs.org if you do not already have it. Open Terminal in the extracted project folder, then run:

```bash
npx --yes netlify-cli login
npx --yes netlify-cli deploy --prod --dir=public
```

Complete Netlify login in your own browser. If prompted, create a new project under your `lyba-ali1` team. Use an available project name. Run the commands from the folder containing `netlify.toml`; the complete project includes the Edge Function.

The command prints the live Netlify URL when deployment succeeds. Do not paste authentication tokens or passwords into chat.

## Verify before sharing

- Your existing sample should show `K50UTH-NJ`, 55 transactions, and $178.63 in charges (assuming you have not subsequently edited it).
- Open that vehicle and check its transaction history.
- Download the original report from Uploaded documents.
- Upload the same sample again: it should report that it was already uploaded, with no change to the transaction count.
- Upload another supported NJ Transaction View PDF or CSV, then reload the page and confirm the new records remain saved.
- Multiple selected files are processed sequentially. Each file may be up to 20 MB, subject to hosting limits.

Anyone with this public Netlify link can see the shared fleet and uploaded reports, upload files, associate vehicles, and add manual entries. They do not receive separate accounts or data.

## What was tested

The included relay passed tests for safe header forwarding, multipart uploads, cross-origin write rejection, route restrictions, document responses, and error handling. It was also tested against the live tracker for dashboard data, JavaScript/PDF assets, the sample PDF, repeat-upload protection, vehicle history, and source document download. The test confirmed the existing 55 transactions and $178.63 total without changing records.

Netlify-hosted runtime verification still needs to be completed after you deploy. Netlify's free plan has usage limits; check your account's Usage & billing page. This package has no monthly-limit bypass or guarantee of unlimited traffic.

## Troubleshooting

- **Setup placeholder page:** the Edge Function was not deployed. Import the complete project from GitHub or use Netlify CLI; do not upload only `public/`.
- **Tracker unavailable:** confirm the original hosted tracker is still public and online. This package does not contain a private-site credential.
- **404:** confirm `netlify.toml` is at the repository root, the `netlify` folder was uploaded, and the base directory is blank.
- **Login to Netlify fails:** use the account method you registered with; deployment settings do not fix login issues.
- **Large upload fails:** try a smaller document and check your Netlify project logs and usage limits. No transaction is added if the upload cannot be saved.
- **Different report layouts/scanned PDFs:** use a supported NJ Transaction View export or CSV. Overlapping different documents are not automatically deduplicated by transaction.

## Official references

- Add a project: https://docs.netlify.com/manage/projects/add-new-project/
- Edge Functions: https://docs.netlify.com/build/edge-functions/get-started/
- Edge API: https://docs.netlify.com/build/edge-functions/api/
- CLI deployment: https://cli.netlify.com/commands/deploy/
