# Installing VS Code Extensions

Place the *.vsix files in /usr/local/share/vscode-extensions, run the script in BASH to isntall all into the same directory. This will automatically create the proper named subdirectories that CS Code requires.

Remember to palce the following in "/etc/environment" file, so it perssists across reboots:
```BASH
export VSCODE_EXTENSIONS="/usr/local/share/vscode-extensions"
```

Then make sure "standard" users can see the folder:
```BASH
sudo chmod -R 755 /usr/local/share/vscode-extensions
```
