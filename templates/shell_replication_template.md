# Shell Script Template – Replication Package

The script below bundles code, data, and output into a timestamped zip file while recording every action in a log. Adapt paths and commands to fit your project.

---

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

#-------------------------------------------#
# Configuration
#-------------------------------------------#
PROJECT_ROOT="$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"
PACKAGE_DIR="$PROJECT_ROOT/replication_package"
LOG_FILE="$PROJECT_ROOT/output/logs/replicate_$(date +%Y%m%d_%H%M%S).log"
ZIP_FILE="$PROJECT_ROOT/output/replication_$(date +%Y%m%d).zip"

log() {
  local ts=$(date '+%Y-%m-%d %H:%M:%S')
  echo "[$ts] $1" | tee -a "$LOG_FILE"
}

hash_file() {
  sha256sum "$1" | awk '{print $1}'
}

main() {
  log "Creating replication package."

  # 1. Clean previous package
  rm -rf "$PACKAGE_DIR"
  mkdir -p "$PACKAGE_DIR"/{{code,data,output}}

  # 2. Copy essential files (editlists as needed)
  log "Copying code, data, and output."
  rsync -av --exclude='*.log' "$PROJECT_ROOT/code/" "$PACKAGE_DIR/code/" | tee -a "$LOG_FILE"
  rsync -av "$PROJECT_ROOT/data/input/" "$PACKAGE_DIR/data/input/" | tee -a "$LOG_FILE"
  rsync -av "$PROJECT_ROOT/output/figures/" "$PACKAGE_DIR/output/figures/" | tee -a "$LOG_FILE"

  # 3. Generate manifest with checksums
  log "Generating manifest.json."
  {
    echo "{\n  \"files\": ["
    find "$PACKAGE_DIR" -type f | while read -r file; do
      checksum=$(hash_file "$file")
      rel_path="${file#$PACKAGE_DIR/}"
      echo "    {\"path\": \"$rel_path\", \"sha256\": \"$checksum\"},"
    done
    echo "  ]\n}"
  } > "$PACKAGE_DIR/manifest.json"

  # 4. Zip the package
  log "Compressing package to $ZIP_FILE."
  (cd "$PACKAGE_DIR/.." && zip -r "$ZIP_FILE" "$(basename "$PACKAGE_DIR")") | tee -a "$LOG_FILE"

  log "Replication package created successfully."
}

main "$@"
```

---

## How to Use

1. Save the script as `create_replication_package.sh` in `templates/`.
2. Make it executable: `chmod +x create_replication_package.sh`.
3. Run from project root: `./templates/create_replication_package.sh`.
4. Verify the ZIP in `output/` and share it alongside your paper.

---

## Checklist Embedded in Script

- [x] Create fresh directory structure
- [x] Copy only required files
- [x] Generate checksums for integrity
- [x] Log every step
- [x] Compress into portable archive