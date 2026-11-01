# Infocraft DevWeb · espacio privado de archivos

Static frontend for the private Supabase project. Sign-in uses a magic link restricted in the app to the authorized owner account. The table remains `public.vault_files`; metadata reads and writes use the currently known columns only. Never add a service-role key to this repository.

## Files and folders
- Individual files retain the existing storage layout and remain compatible with existing rows.
- Folder uploads use the browser's native folder picker (`webkitdirectory`) and keep a generated folder UUID, root folder name, and sanitized relative path in object storage: `<uid>/<category>/<yyyy>/<mm>/folder/<folder-uuid>/<root>/<relative-path>`.
- Rows are grouped into one visible folder entry. Download creates a ZIP named for the folder and preserves its root directory plus nested paths. A folder deletion removes its members individually.
- Selection limits: max 200 files and 200 MiB total per folder, and 50 MiB per file. Individual files may be selected separately. Folder batches upload sequentially; failures are reported and successful items remain visible. A failed folder upload can leave partial files; remove the folder in the UI if it should be rolled back.
- Empty folders are not represented because browser directory uploads expose files, not empty directories.

## Security and limits
This is not end-to-end encrypted. Authentication, RLS, and private bucket policies are the security boundary; the UI is not an authorization boundary. No file/folder password lock is offered. The requested per-resource password and three-attempt/10-minute lockout must be implemented and tested server-side with atomic database attempt accounting and direct Storage reads disabled for protected objects. See `../DEVWEB-VAULT-SECURITY-PLAN.md`; do not add a client-side password gate as a substitute.

The frontend includes Supabase publishable project configuration, not a secret key. Verify database and Storage RLS/policies against the actual deployed Supabase policies before changing them. Do not upload credentials or private keys.

## External libraries
Supabase JS v2 and JSZip 3.10.1 are loaded from jsDelivr CDN. ZIP downloads temporarily hold downloaded files in browser memory and are bounded by the folder limits above.