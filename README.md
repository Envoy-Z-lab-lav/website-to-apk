# Website-to-APK Converter

A simple tool to convert any website into an Android APK without requiring Android Studio or Java programming knowledge. The app acts as a WebView wrapper around your chosen website.

## Features

- Simple command-line interface with colorful output
- Automatic Java 17 downloading option
- Automated Android SDK tools installation
- APK signing and building process

## Quick Start

1. Clone this repository:
```bash
git clone https://github.com/Jipok/website-to-apk
cd website-to-apk
```

2. Create a configuration file `webapk.conf`:
```ini
id = myapp                          # Application ID (will be com.myapp.webtoapk)
name = My App Name                  # Display name of the app
mainURL = https://example.com       # Target website URL
icon = example.png                  # Path to your app icon (PNG format)

allowSubdomains = true              # Allow navigation between example.com and sub.example.com
requireDoubleBackToExit = true      # Require double back press to exit app

enableExternalLinks = true          # Allow/block external links
openExternalLinksInBrowser = true   # If allowed: open external links in browser or WebView
confirmOpenInBrowser = true         # Show confirmation before opening external browser
```

3. Generate signing key (only needed once, keep the generated file safe):
```bash
./make.sh keygen
```

4. Apply configuration and build:
```bash
./make.sh build
```

The final APK will be created in the current directory.

## Available Commands

- `./make.sh build` - Apply configuration and build
- `./make.sh keygen` - Generate signing key
- `./make.sh test` - Install and test APK on connected device
- `./make.sh clean` - Clean build files
- `./make.sh apply_config` - Apply settings from configuration file
- `./make.sh apk` - Build signed APK
- `./make.sh get_tools` - Download Android command-line tools
- `./make.sh get_java` - Download OpenJDK 17 locally

## App Links / Deep Links

You can make your app handle links to the website by setting the `deeplink` option in your configuration file. When set, clicking links to your website on the device will open them in your app instead of a browser.

For example, if your website is `https://example.com`, set:
```ini
deeplink = example.com
```

## Userscripts Support

The app supports userscripts (similar to Tampermonkey/Violentmonkey scripts) through the `scripts` configuration option:

```ini
scripts = scripts/*.js             # Load all .js files from scripts directory
# OR
scripts = site-*.js                # Load all files matching pattern
# OR
scripts = script1*.js script20.js  # Load specific script files
```

### How Userscripts Work

- Scripts are executed early in page load cycle - after `document.body` is available but before `DOMContentLoaded` event fires
- Scripts can use Tampermonkey/Violentmonkey/etc `@match` directive to specify target URLs
- If no `@match` is specified, the script will run on all pages
- Only `GM_addStyle` supported from Greasemonkey API
- There are `WebToApk.showShortToast("toast")` method
- Script console output (console.log/alert/warn) can be monitored using:
```bash
./make.sh test
```

Common use cases include:
- Adding dark mode to websites
- Customizing website appearance
- Adding new functionality
- Fixing mobile compatibility issues

Example of some useful scripts:
- [dark-mode.js](https://gist.github.com/Jipok/01d12591491816625649a467db898518) - Universal dark theme that respects system preferences
- [instant.js](https://raw.githubusercontent.com/instantpage/instant.page/refs/heads/master/instantpage.js) - Speed up page loads by preloading pages when the user taps

## Additional WebView Options
The following advanced options can also be configured:
```toml
cookies = "key1=value1; key2=value2"  # Cookies for mainURL
JSEnabled = true                      # Enable JavaScript execution
JSCanOpenWindowsAutomatically = true  # Allow JS to open new windows/popups

DomStorageEnabled = true              # Enable HTML5 DOM storage
DatabaseEnabled = true                # Enable HTML5 Web SQL Database
SavePassword = true                   # Allow saving passwords in WebView
AllowFileAccess = true
AllowFileAccessFromFileURLs = true

MediaPlaybackRequiresUserGesture = false # Disable autoplay of media files
```

## Technical Details

- Target Android API: 33 (Android 13)
- Minimum Android API: 24 (Android 7.0)
- Build tools version: 33.0.2
- Gradle version: 7.4
- Required Java version: 17

## Notes

- All app data is stored in the app's private directory
- The keystore `app/my-release-key.jks` password is set to "123456" by default
- Internet permission is required and automatically included
- If you need to support [different Android versions](https://apilevels.com/), edit `app/build.gradle` accordingly
- Based on the original work from: https://github.com/successtar/web-to-app  