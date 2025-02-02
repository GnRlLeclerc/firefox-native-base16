# Firefox Native Base16

A simple native application that can watch any base16 TOML color file for changes and communicate them to your browser.

## Installation

For a quick installation on Linux, you can just run the [`install.sh`](./install.sh) script. Else, follow the instructions.

### Building the binary

You must first build and install the native application, which requires `cargo` or `rustup` to be installed:

```bash
cargo install --git https://github.com/GnRlLeclerc/firefox-native-base16
```

On Linux systems, the binary will be installed to `~/.cargo/bin/firefox-native-base16`.

### Installing the native app

In order for the browser to indentify the native application, it needs a `manifest.json` file that points to the binary.

NOTE: instructions for Linux / MacOS only. See [here](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Native_manifests#manifest_location) for more information on where to put the app manifest.

The binary application must be launched from a bash script.
The binary application can be run with the `--colors-path` flag to specify a different path to the file holding the colors.
The default is `~/.mozilla/colors.toml`.
Note that on Unix platforms, Firefox sends a `SIGTERM` signal to native applications when exiting. We need to explicitely transfer it to our underlying binary in the bash script in order to ensure that it is stopped when firefox exits.

`~/.local/bin/firefox-native-base16-launcher`

```bash
#!/bin/bash

# Kill the binary when SIGTERM is received
trap 'kill -SIGTERM $native_pid' SIGTERM

# Run the binary in the background, storing its PID
~/.cargo/bin/firefox-native-base16 &
native_pid=$!
wait $native_pid
```

Move this script in a directory present in your `PATH` (like `~/.local/bin`), and indicate this path instead in your native application manifest.

```bash
mkdir ~/.mozilla/native-messaging-hosts
jq ".path = \"$HOME/.local/bin/firefox-native-base16-launcher\"" manifest.json > ~/.mozilla/native-messaging-hosts/firefox_native_base16.json
```

## Related Extensions

Once your native application is setup, install the relevant browser extension.

### Firefox

See the [Firefox Base16 Extension](https://github.com/GnRlLeclerc/firefox-dynamic-base16).

You can now update your `colors.toml` based on the [mustache template](./template.mustache) and watch your browser theme change dynamically!
