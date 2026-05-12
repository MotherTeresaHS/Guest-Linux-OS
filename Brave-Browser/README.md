# Install Brave Browser

- login as "root":
  ```bash
  curl -fsSLo /usr/share/keyrings/brave-browser-archive-keyring.gpg https://brave-browser-apt-release.s3.brave.com/brave-browser-archive-keyring.gpg
  echo "deb [signed-by=/usr/share/keyrings/brave-browser-archive-keyring.gpg] https://brave-browser-apt-release.s3.brave.com/ stable main" | tee /etc/apt/sources.list.d/brave-browser-release.list
  apt update && apt install brave-browser -y
  ```
- now to set Brave as the default browser:
  ```bash
  update-alternatives --install /usr/bin/x-www-browser x-www-browser /usr/bin/brave-browser 200
  update-alternatives --set x-www-browser /usr/bin/brave-browser
  update-alternatives --install /usr/bin/gnome-www-browser gnome-www-browser /usr/bin/brave-browser 200
  update-alternatives --set gnome-www-browser /usr/bin/brave-browser
  ```
- now change the default landing page:
  ```bash
  mkdir -p /etc/brave/policies/managed
  nano /etc/brave/policies/managed/initial_settings.json
  ```
  - add to this new file:
  ```bash
  {
    "RestoreOnStartup": 4,
    "RestoreOnStartupURLs": [
      "http://172.22.52.50"
    ],
    "HomepageLocation": "http://172.22.52.50",
    "HomepageIsNewTabPage": false,
    "FirstRunTabs": [""],
    "PromotionsEnabled": false,
    "MetricsReportingEnabled": false,
    "BraveP3AEnabled": false
  }
  ```
    - add the following to "/etc/brave/policies/managed/search_policy.json:
  ```bash
  {
    "DefaultSearchProviderEnabled": true,
    "DefaultSearchProviderName": "Google",
    "DefaultSearchProviderSearchURL": "https://www.google.ca/search?q={searchTerms}",
    "DefaultSearchProviderSuggestURL": "https://www.google.ca/complete/search?output=chrome&q={searchTerms}",
    "DefaultSearchProviderNewTabURL": "https://google.ca",
    "DefaultSearchProviderKeyword": "google.ca",
    "DefaultSearchProviderIconURL": "https://www.google.ca/favicon.ico"
  }
  ```
  - now update the "Exec" line: nano /etc/skel/.local/share/applications/brave-browser.desktop, with (and the incognito line as well):
  ```bash
  Exec=/usr/bin/brave-browser-stable --password-store=basic --user-data-dir="/home/guest/.config/brave" --no-default-browser-check --no-first-run http://172.22.52.50 %U
  ```