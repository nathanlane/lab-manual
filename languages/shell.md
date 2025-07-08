# Shell Scripting Guide (Bash)

Robust shell scripts are invaluable for orchestrating workflows and creating replication packages.

---

## 🏗️ Script Structure

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

LOG_FILE="../output/logs/$(basename "$0" .sh)_$(date +%Y%m%d).log"

log() {
    local message="$1"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    echo "[$timestamp] $message" | tee -a "$LOG_FILE"
}

main() {
    log "Starting replication pipeline."
    # ... your commands here ...
    log "Finished!"
}

main "$@"
```

### Key Points

1. **`set -euo pipefail`** – Exit on error, undefined variable, or failed pipe.
2. **Logging Function** – Timestamped logs written to file and stdout.
3. **Main Function** – Encapsulate logic; call `main "$@"` at bottom.

---

## 🔒 Error Handling & Validation

- Check prerequisites:
  ```bash
  command -v stata >/dev/null || { echo "Stata not found"; exit 1; }
  ```
- Validate inputs:
  ```bash
  [[ -f "$1" ]] || { echo "Input file missing"; exit 1; }
  ```

---

## 🗂️ File Operations & Cleanup

```bash
# Create directory if it doesn't exist
mkdir -p ../output/archives

# Remove temporary files older than 30 days
find ../output/tmp -type f -mtime +30 -delete
```

---

## 📦 Cross-Platform Tips

- Use `/usr/bin/env bash` shebang for portability.
- Prefer `$(...)` over backticks for command substitution.
- Avoid `sed -i` without extension on macOS; use `sed -i ''`.

---

☑️ See an end-to-end shell template in [Templates → Shell Replication Package](../templates/shell_replication_template.md).