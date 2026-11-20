# Privacy and Publication Checklist

**Complete this before adding the full Word/PDF report or screenshots to a public repository.** The original portfolio report contains captured screens that may still show identifying details.

- [ ] Remove or fully redact account names, computer hostnames, SIDs, serial numbers and local profile paths.
- [ ] Check IP addresses and private network structure before deciding what to publish. For a portfolio, generic placeholders are usually sufficient.
- [ ] Remove credentials, enrollment keys, authentication tokens, session cookies, logs with passwords, and certificate private keys.
- [ ] Do not upload VM disk images (`.ova`, `.vdi`) or raw Windows Event Log exports (`.evtx`).
- [ ] Use **flattened/redacted images** rather than merely placing editable Word shapes over private details in screenshots.
- [ ] Review both pictures **and** document properties/metadata before uploading a PDF or DOCX.
- [ ] Check all text for unnecessary private identifiers; verify screenshots at readable zoom.
- [ ] Review the **public** repository after committing to ensure no unintended files are visible.

## Recommended publishing flow

1. Keep your original report private for evidentiary integrity.
2. Create a **separate public copy** with screenshots permanently redacted before export.
3. Prefer a sanitized PDF for recruiter viewing; retain your editable DOCX privately unless you deliberately want to publish it.
4. Add clean screenshots under `evidence/` and link them from `README.md`.

Deleting a sensitive file in a later Git commit may not remove it from repository history. Review before the **first** public upload.
