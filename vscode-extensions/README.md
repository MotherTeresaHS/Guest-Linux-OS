# Installing VS Code Extensions

NOTE: the 2 Java extensions are over 100 MB, so GitHub will not allow you to save them. Download from:
- https://open-vsx.org/extension/vscjava/vscode-java-debug
- https://open-vsx.org/extension/redhat/java
```BASH
wget https://open-vsx.org/api/redhat/java/linux-x64/1.55.2026051209/file/redhat.java-1.55.2026051209@linux-x64.vsix


Place the *.vsix files in /usr/local/share/vscode-extensions, run the script in BASH to install all into the same directory. This will automatically create the proper named subdirectories that CS Code requires.

Remember to palce the following in "/etc/environment" file, so it perssists across reboots:
```BASH
export VSCODE_EXTENSIONS="/usr/local/share/vscode-extensions"
```

Then make sure "standard" users can see the folder:
```BASH
chmod -R 755 /usr/local/share/vscode-extensions
```
