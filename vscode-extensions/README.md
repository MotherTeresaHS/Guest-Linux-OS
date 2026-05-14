# Installing VS Code Extensions

NOTE: download the following extension in the "/usr/local/share/vscode-extensions" folder
```BASH
mkdir /usr/local/share/vscode-extensions
cd /usr/local/share/vscode-extensions
wget https://open-vsx.org/api/hediet/vscode-drawio/1.6.6/file/hediet.vscode-drawio-1.6.6.vsix
wget https://open-vsx.org/api/medo64/render-crlf/1.9.8/file/medo64.render-crlf-1.9.8.vsix
wget https://open-vsx.org/api/oderwat/indent-rainbow/8.3.1/file/oderwat.indent-rainbow-8.3.1.vsix
wget https://open-vsx.org/api/ms-python/debugpy/linux-x64/2026.6.0/file/ms-python.debugpy-2026.6.0@linux-x64.vsix
wget https://github.com/microsoft/vscode-cpptools/releases/download/v1.32.2/cpptools-linux-x64.vsix
wget https://open-vsx.org/api/vscjava/vscode-java-debug/0.59.0/file/vscjava.vscode-java-debug-0.59.0.vsix
wget https://open-vsx.org/api/redhat/java/linux-x64/1.55.2026051209/file/redhat.java-1.55.2026051209@linux-x64.vsix
```
Place the *.vsix files in /usr/local/share/vscode-extensions, run the script in BASH to install all into the same directory. This will automatically create the proper named subdirectories that CS Code requires.

Create the install script
```BASH
nano /usr/local/share/vscode-extensions/install-extensions.sh
```

Place in this script:
```BASH
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
```

Run the script:
```BASH
sh /usr/local/share/vscode-extensions/install-extensions.sh
```

Remember to place the following in "/etc/environment" file, so it perssists across reboots:
```BASH
nano /etc/environment
```
```BASH
export VSCODE_EXTENSIONS="/usr/local/share/vscode-extensions"
```

Then make sure "standard" users can see the folder:
```BASH
chmod -R 755 /usr/local/share/vscode-extensions
```

## C/C++ Note

- remember, the .vscode folder and files need to be in each repo for the debugger to work!
