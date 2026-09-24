# Sanitized screenshot evidence

This directory contains 31 reviewed screenshot derivatives for the September 23 Stage 3 documentation update:

- 8 fresh cleanup captures (C01–C08), which take precedence for the current documented state.
- 10 earlier Parrot configuration captures (P01–P10), retained as before-cleanup history.
- 4 earlier runtime/performance captures.
- 9 historical management-access incident captures.

Twenty-five images have opaque pixel masks. Six cropped incident images have no visible sensitive content requiring a mask; their embedded metadata was removed. The eight fresh images retain their source dimensions, with all RGBA pixels outside the masks verified unchanged. The 23 earlier derivatives match their previously verified output hashes; their original pixel checks are retained from Revision 1. Source images are not modified by this review.

Removed where present: public WAN address, test server location, literal SSID names, administrator account/internal hostname, and unrelated desktop/browser context. Internal technical addresses, VLANs, rule aliases, hardware models, and test results are retained intentionally. Actual wireless names are replaced with functional roles in the documentation.

See [REDACTION_MANIFEST.json](REDACTION_MANIFEST.json) for filenames, checksums, mask coordinates, and verification results. No raw configuration backups are included. This folder is a review copy; this update has not been committed or pushed.

- [Stage 3 completion and evidence gallery](../../docs/00_Project_Overview/04_Stage_3_Completion_2026-09-23.md)
- [Historical management-access incident](../../docs/06_Hardening_Backup_and_Operations/incidents/2026-09-23-mgmt-to-legacy-management-access.md)
