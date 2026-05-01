# temp-share

A Claude Code plugin that uploads files or directories to a temporary sharing service and returns a download link.

## Install

```bash
# Add the marketplace
claude plugin marketplace add github:dhruv304c2/temp-share

# Install the plugin
claude plugin install share
```

## Usage

```
/share <file_or_directory_path>
```

Examples:

```
/share ./report.pdf
/share ~/projects/my-app
```

- Files are uploaded directly
- Directories are zipped automatically before upload
- Links expire after ~60 minutes
- Max file size: 100MB

## Uninstall

```bash
claude plugin uninstall share
```
