# Installing VS Code Extensions

NOTE: the 2 Java extensions are over 100 MB, so GitHub will not allow you to save them. Download from:
- https://open-vsx.org/extension/vscjava/vscode-java-debug
- https://open-vsx.org/extension/redhat/java
```BASH
wget https://open-vsx.org/api/hediet/vscode-drawio/1.6.6/file/hediet.vscode-drawio-1.6.6.vsix
wget https://open-vsx.org/api/medo64/render-crlf/1.9.8/file/medo64.render-crlf-1.9.8.vsix
wget https://open-vsx.org/api/oderwat/indent-rainbow/8.3.1/file/oderwat.indent-rainbow-8.3.1.vsix
wget https://open-vsx.org/api/ms-python/debugpy/linux-x64/2026.6.0/file/ms-python.debugpy-2026.6.0@linux-x64.vsix
wget https://github.com/microsoft/vscode-cpptools/releases/download/v1.32.2/cpptools-linux-x64.vsix
wget https://open-vsx.org/api/vscjava/vscode-java-debug/0.59.0/file/vscjava.vscode-java-debug-0.59.0.vsix
wget https://open-vsx.org/api/redhat/java/linux-x64/1.55.2026051209/file/redhat.java-1.55.2026051209@linux-x64.vsix
```
Place the *.vsix files in /usr/local/share/vscode-extensions, run the script in BASH to install all into the same directory. This will automatically create the proper named subdirectories that CS Code requires.

Remember to palce the following in "/etc/environment" file, so it perssists across reboots:
```BASH
export VSCODE_EXTENSIONS="/usr/local/share/vscode-extensions"
```

Then make sure "standard" users can see the folder:
```BASH
chmod -R 755 /usr/local/share/vscode-extensions
```
