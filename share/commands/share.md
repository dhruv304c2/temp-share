---
description: Upload a file or directory to a temporary sharing service and return a download link
allowed-tools: Bash, Read
argument-hint: <file_or_dir_path>
---

Upload the file or directory specified by the user argument `$ARGUMENTS` to a temporary file sharing service so they can download it on another device.

## Steps

1. Resolve the path from `$ARGUMENTS`. If the argument is empty, ask the user which file or directory to share.
2. Verify the path exists. If it does not exist, tell the user and stop.
3. If the path is a **directory**, compress it first by cd-ing into the parent directory so the zip only contains the directory itself, not the full path:
   ```
   cd <parent_of_dir> && zip -r /tmp/<dirname>.zip <dirname>
   ```
   For example, for `/Users/me/projects/mydir`, run:
   ```
   cd /Users/me/projects && zip -r /tmp/mydir.zip mydir
   ```
   Then use `/tmp/<dirname>.zip` as the upload path.
4. Check the file size with `wc -c`. Warn the user if the file is larger than 100MB (the service limit).
5. Upload the file using this exact curl command:
   ```
   curl -s -F "file=@<upload_path>" https://tmpfiles.org/api/v1/upload
   ```
6. The response is JSON like `{"status":"success","data":{"url":"http://tmpfiles.org/12345/filename"}}`.
   - Parse the URL from the JSON response using jq: `echo '<response>' | jq -r '.data.url'`
   - Convert it to a **direct download link** by inserting `/dl/` after `tmpfiles.org/`:
     `http://tmpfiles.org/12345/file.txt` → `https://tmpfiles.org/dl/12345/file.txt`
   - Display it clearly:

   **Download link:** `<direct_download_url>`

   Tell the user the link is temporary (files expire after ~60 minutes).
7. If a zip was created in `/tmp` during step 3, clean it up after upload (whether successful or not):
   ```
   rm -f /tmp/<dirname>.zip
   ```

## Error handling

- If tmpfiles.org fails, try the fallback:
  ```
  curl -s -T "<upload_path>" https://temp.sh
  ```
  This returns the URL directly on stdout.
- If both services fail, tell the user and suggest they check their internet connection.

## Important

- Do NOT read or display the file contents in chat — just upload it.
- For binary files, skip the Read verification and go straight to upload.
- Always use the exact path the user provided; do not modify it.
