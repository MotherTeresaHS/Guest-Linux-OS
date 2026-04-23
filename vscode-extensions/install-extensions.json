#!/bin/bash

# The directory containing your .vsix files
EXT_DIR="/usr/local/share/vscode-extensions"

# Temporary directory for VS Code to use during installation
TEMP_DATA_DIR="/tmp/vscode-root-setup"

for vsix in "$EXT_DIR"/*.vsix; do
    if [ -f "$vsix" ]; then
        echo "Processing $vsix..."
        # Added --no-sandbox and --user-data-dir to allow root/sudo execution
        code --no-sandbox --user-data-dir "$TEMP_DATA_DIR" --extensions-dir "$EXT_DIR" --install-extension "$vsix"
    fi
done

# Cleanup the temp directory
rm -rf "$TEMP_DATA_DIR"

echo "All extensions have been extracted to $EXT_DIR"
