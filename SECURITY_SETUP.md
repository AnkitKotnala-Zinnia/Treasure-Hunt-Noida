# Security Setup

This project is hosted on public GitHub Pages, so no real secret can live in the
HTML, JavaScript, committed QR images, or committed config files.

The anti-cheat flow uses private Firestore data:

1. `generate_qr_codes.py` creates `qr-secrets.local.json`.
2. The generated `task*_qr.png` files encode those private tokens.
3. Firestore rules compare submitted scan tokens with private `qrSecrets` docs.
4. The browser can submit a scanned token, but it cannot read the private
   `qrSecrets` collection.

## One-Time Setup

1. Generate fresh private QR images:

   ```bash
   python generate_qr_codes.py
   ```

2. In Firebase Console, create this private collection:

   - Collection: `qrSecrets`
   - Document ID: `2`, field `token`, value from `qr-secrets.local.json` key `2`
   - Document ID: `3`, field `token`, value from `qr-secrets.local.json` key `3`
   - Document ID: `4`, field `token`, value from `qr-secrets.local.json` key `4`
   - Document ID: `5`, field `token`, value from `qr-secrets.local.json` key `5`

3. Deploy the Firestore rules:

   ```bash
   firebase deploy --only firestore:rules
   ```

4. Print the local `task*_qr.png` files and place them at the clue locations.

## Do Not Commit

These files are intentionally ignored:

- `qr-secrets.local.json`
- `task*_qr.png`
- `qr_codes/`

If any QR image or token is ever pushed publicly, generate new tokens, seed
Firestore again, and print new QR codes.

## Admin Note

The public admin dashboard can read leaderboard data, but destructive actions
such as clearing all data are blocked by the secure Firestore rules. Use the
Firebase Console or a trusted server-side/admin script for resets.
