---
name: bee-upload-file
description: Upload a file (CSV, Excel, JSON, TXT, XML, Parquet, Avro...) from the user's machine into an organization file folder on Bee Intelligence. Use when the user wants to put / upload a file into a Bee Intelligence data folder (data source, org files) (vi - "upload file", "tải file lên thư mục").
---

# Bee Intelligence - Upload a File into a Folder

The file must be processed by the plugin script before upload (Excel to CSV, sheet selection, TCVN3 conversion, row numbers), exactly as when uploading on the web. Never PUT the original file yourself or convert the file yourself.

The `bee-intelligence` MCP tool names are prefixed by site (e.g. `acme_get_organization_file_detail`). Below, tools are referred to by their name suffix; use the tool whose name ends with that suffix.

Script: `${CLAUDE_PLUGIN_ROOT}/scripts/upload-org-file.mjs`. Each command prints exactly one JSON line: the result to stdout, errors to stderr as `{"error": ...}` with exit code 1.

## Preparation

1. Check the runtime: `node --version` (20 or later required); if missing, `bun --version`. If neither is available, stop and guide the user to install Node.js 20+ (https://nodejs.org). The runtime found is called `<runtime>` below.
2. Two things are needed from the user; ask for whichever is missing, never guess:
   - **File path** on the machine.
   - **Folder link** on the Bee Intelligence web app, in the form `.../data-sources/org-files?id=<uuid>`. Take `folder_id` = the link's `id` parameter. There is no tool to list folders, so use only the link the user gives.

## Steps

1. **Inspect the folder**: call `get_organization_file_detail(file_id = folder_id)`.
   - `is_folder` is false: tell the user this is a file and ask for a folder link.
   - `keep_original` is true: this folder stores files in their original form, while the script always converts files. Stop, don't call the later steps; tell the user to upload that file on the Bee Intelligence web app.
   - Not found / no permission to view: tell the user; if the folder belongs to another company, suggest switching the login scope with `switch_login_scope` and trying again.
2. **Process the file**:
   ```
   <runtime> "${CLAUDE_PLUGIN_ROOT}/scripts/upload-org-file.mjs" prepare "<file path>" [--target-sheet "<target_sheet_name>"] [--merge-all-sheets]
   ```
   - Add `--target-sheet` only when `target_sheet_name` is not null, and `--merge-all-sheets` only when `merge_all_sheets` is true, exactly per the result of step 1. Never choose a sheet yourself.
   - Result: `{"path", "file_name", "file_size", "notices"}`. Keep `path`, `file_name`, `file_size`.
   - If there are `notices`, relay them to the user (e.g. TCVN3 text was converted to Unicode, or a warning that an unsupported legacy encoding may break Vietnamese characters).
   - Errors (disallowed format, over 100 MB, empty file, conversion failure): report the exact error message and stop.
   - If `--target-sheet` was passed and the error is "File is empty.", the file usually has no sheet with that exact name (case- and space-sensitive). Tell the user the folder requires sheet `<target_sheet_name>` and ask them to check the sheet name in the file; never rename the sheet or drop `--target-sheet` yourself.
3. **Confirm**: ask the user to confirm before uploading, stating the stored file name (`file_name`), size, and destination folder (`path` from step 1). Continue only when the user agrees.
4. **Get the upload URL**: call `get_organization_file_upload_url(folder_id, file_name, file_size)` with exactly the `file_name`, `file_size` from step 2. Receive `upload_id`, `url`, `expires_in`.
   - Error 400 (format / size), 422 (invalid folder), 403 (no upload permission): report the error and stop.
5. **Upload**:
   ```
   <runtime> "${CLAUDE_PLUGIN_ROOT}/scripts/upload-org-file.mjs" put "<path>" "<url>"
   ```
   - Success: `{"ok": true}`.
   - Error (403 because the URL expired after about 20 minutes, or another code): go back to step 4 for a new URL, then rerun `put` with the same `path`. Don't rerun `prepare`.
6. **Complete**: call `complete_organization_file_upload(upload_id, description?)`. Pass `description` only if the user gave one. The result contains `file_id`: tell the user the file is in the folder.

## Errors at the Complete Step

| Error | What to do |
|---|---|
| 422 "File has not been uploaded." | Rerun `put` (step 5), then complete again. |
| 422 "Upload not found or expired." | If this file already got a `file_id`, stop (it's done). If not, go back to step 4 one time only. |
| 409 "This upload is already being processed." | Don't retry; tell the user to check the folder later. |
| Other errors | Report the exact error message; the file may already have been created in the folder, as with web uploads. |

## Rules

- `url` is a one-time secret: never show it to the user or write it to a file, memory, or `bee_config`; pass it only to the `put` command.
- Upload only one file at a time. For several files, repeat the steps for each file and confirm each one.
- If the tools `get_organization_file_detail`, `get_organization_file_upload_url`, `complete_organization_file_upload` are not available, tell the user the current account has no permission to view or upload data sources; don't look for another way.
