# Scheduling exports on macOS

## Creating the script

1. Open TextEdit and create a new file (⌘N)

2. Convert the file to a plain text one in 'Format > Make Plain Text' (⇧⌘T)

![](https://i.imgur.com/WXrTtXM.png)

3. Paste the following into the text editor:

<!-- TODO test -->

```bash
#!/bin/bash
# DiscordChatExporter - macOS Script
# Project Info: https://github.com/Tyrrrz/DiscordChatExporter

# -e - exit immediately if a command exits with a non-zero status
# -u - treat unset variables as an error when substituting
# -o pipefail - return the exit status of the last command in the pipeline that failed
set -euo pipefail

##########################
# Configuration Section  #
##########################

# Edit the following variables to configure the script
# Make sure to not delete the quotes (") when inserting your values

TOKEN=""            # Your bot/user token
CHANNEL_ID=""       # Discord channel ID
DCE_FOLDER=""       # Folder containing DiscordChatExporter.Cli.sh
EXPORT_FOLDER=""    # Output folder for exported file
FILENAME=""         # Base name for the exported file
EXPORT_FORMAT=""    # Options: PlainText, HtmlDark, HtmlLight, Json, Csv

##########################
# Helper Functions       #
##########################

error_exit() {
    echo "[ERROR] $1"
    exit 1
}

validate_inputs() {
    [[ -z "$TOKEN" ]] && error_exit "TOKEN is not set."
    [[ -z "$CHANNEL_ID" ]] && error_exit "CHANNEL_ID is not set."
    [[ -z "$DCE_FOLDER" ]] && error_exit "DCE_FOLDER is not set."
    [[ -z "$EXPORT_FOLDER" ]] && error_exit "EXPORT_FOLDER is not set."
    [[ -z "$FILENAME" ]] && error_exit "FILENAME is not set."
    [[ -z "$EXPORT_FORMAT" ]] && error_exit "EXPORT_FORMAT is not set."
}

# Function to get the file extension based on the export format
get_file_extension() {
    local format_lc
    # Convert the export format to lowercase for case-insensitive comparison
    # This helps avoid issues with the user inputting the format in different cases
    format_lc=$(echo "$EXPORT_FORMAT" | tr '[:upper:]' '[:lower:]')
    case "$format_lc" in
        plaintext) echo "txt" ;;
        htmldark | htmllight) echo "html" ;;
        json) echo "json" ;;
        csv) echo "csv" ;;
        *) error_exit "\"$EXPORT_FORMAT\" is not a valid export format. Valid options: PlainText, HtmlDark, HtmlLight, Json, Csv." ;;
    esac
}

# Function to export the chat using DiscordChatExporter
export_chat() {
    cd "$DCE_FOLDER" || error_exit "Failed to change directory to $DCE_FOLDER"
    echo "[INFO] Exporting chat..."
    ./DiscordChatExporter.Cli.sh export \
        -t "$TOKEN" \
        -c "$CHANNEL_ID" \
        -f "$EXPORT_FORMAT" \
        -o "${FILENAME}.tmp" || error_exit "Export failed"
}

# Function to finalize the export by renaming and moving the file
finalize_export() {
    local timestamp output_file
    # Get the current timestamp in the format YYYY-MM-DD-HH-MM-SS
    # This will be used to create a unique filename for the exported chat
    # Otherwise, the file would be overwritten each time the script runs
    timestamp=$(date +"%Y-%m-%d-%H-%M-%S")
    # Construct the output file path using the export folder, base filename, timestamp, and file extension
    output_file="${EXPORT_FOLDER}/${FILENAME}-${timestamp}.${FILE_EXTENSION}"

    echo "[INFO] Moving exported file to: $output_file"
    if ! mv "${FILENAME}.tmp" "$output_file"; then
        echo "[WARN] Failed to move file. Cleaning up..."
        rm -f "${FILENAME}.tmp" || echo "[WARN] Could not delete temporary file."
        error_exit "Failed to move exported file."
    fi
    echo "[INFO] Chat successfully exported to: $output_file"
}

##########################
# Main Execution         #
##########################

validate_inputs
FILE_EXTENSION=$(get_file_extension)
export_chat
finalize_export
```

4. On the configuration section, edit the following variables without deleting the quotes (`"`):

- `TOKEN`: Insert the [Token](Token-and-IDs.md).
- `CHANNEL_ID`: Insert a [Channel ID](Token-and-IDs.md).
- `DCE_FOLDER`: DCE's **folder** path (e.g. `"/path/to/DCE.Cli"`, not `"/path/to/DCE.Cli/DiscordChatExporter.dll"`).
- `EXPORT_FOLDER`: Directory the exported chats will be saved to (e.g. `"/home/user/Documents/Discord Exports"`).
- `FILENAME`: Exported channel's filename.
- `EXPORT_FORMAT`: Export format. Options: `PlainText`, `HtmlDark`, `HtmlLight`, `Json` or `Csv`.

  Example:

  ```bash
  TOKEN="mfa.xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  CHANNEL_ID="123456789012345678"
  …
  ```

  To quickly get paths in Finder, press ⌥⌘C while inside the folder (or while selecting it) to copy its path to the clipboard.

5. Save the file as `filename.sh`, not `.txt`
6. Open Terminal.app, type `chmod +x`, press the SPACE key, then drag & drop the `filename.sh` into the Terminal window and hit RETURN. You may be prompted for your password, and you won't be able to see it as you type.

## Creating the .plist file

Open TextEdit, make a Plain Text (⇧⌘T) and then paste the following into it:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>local.discordchatexporter</string>
    <key>Program</key>
    <string>/path/to/filename.sh</string>
    REPLACEME
  </dict>
</plist>
```

- The `Label` string is the name of the export job, it must be something unique. Replace the `local.discordchatexporter` between the `<string>` with another name if you'd like to run more than one script.
- The `Program` string is the path to the script. Replace `/path/to/filename.sh` between the `<string>` with the path of the previously created script.
- Replace the `REPLACEME` with the content presented in the following sections according to <u>when</u> you want to export.

When you're done, save the file with the same name as the `Label` and with the `.plist` extension (not `.txt`), like `local.discordchatexporter.plist`.

### Exporting on System Boot/User Login

```xml
<key>RunAtLoad</key>
<true/>
```

### Export every _n_ seconds

The following example is to export every 3600 seconds (1 hour), replace the integer value with your desired time:

```xml
<key>StartInterval</key>
<integer>3600</integer>
```

### Export at a specific time and date

```xml
<key>StartCalendarInterval</key>
<dict>
  <key>Weekday</key>
  <integer>0</integer>
  <key>Month</key>
  <integer>0</integer>
  <key>Day</key>
  <integer>0</integer>
  <key>Hour</key>
  <integer>0</integer>
  <key>Minute</key>
  <integer>0</integer>
</dict>
```

| Key         | Integer |
| ----------- | ------- |
| **Month**   | 1-12    |
| **Day**     | 1-31    |
| **Weekday** | 0-6     |
| **Hour**    | 0-23    |
| **Minute**  | 0-59    |

**Sunday** - 0; **Monday** - 1; **Tuesday** - 2; **Wednesday** - 3; **Thursday** - 4; **Friday** - 5; **Saturday** - 6

Replace the template's `0`s according to the desired times.

You can delete the `<key>`s you don't need, don't forget to remove the `<integer>0</integer>` under it.
Omitted keys are interpreted as wildcards, for example, if you delete the Minute key, the script will run at every minute, delete the Weekday key and it'll run at every weekday, and so on.

Be aware that if you set the day to '31', the script will only run on months that have the 31st day.

**Check the examples below ([or skip to step 3 (loading the file)](#3-loading-the-plist-into-launchctl)):**

Export everyday at 5:15 PM:

```xml
<key>StartCalendarInterval</key>
<dict>
  <key>Hour</key>
  <integer>17</integer>
  <key>Minute</key>
  <integer>15</integer>
</dict>

```

Every 15 minutes of an hour (xx:15):

```xml
<key>StartCalendarInterval</key>
<dict>
  <key>Minute</key>
  <integer>15</integer>
</dict>

```

Every Sunday at midnight and every Wednesday full hour (xx:00). Notice the inclusion of `<array>` and `</array>` to allow multiple values:

```xml
<key>StartCalendarInterval</key>
<array>
  <dict>
    <key>Weekday</key>
    <integer>0</integer>
    <key>Hour</key>
    <integer>00</integer>
    <key>Minute</key>
    <integer>00</integer>
  </dict>
  <dict>
    <key>Weekday</key>
    <integer>3</integer>
    <key>Minute</key>
    <integer>00</integer>
  </dict>
</array>
```

## Loading the .plist into launchctl

1. Copy your `filename.plist` file to one of these folders according to how you want it to run:

- `~/Library/LaunchAgents` runs as the current logged-in user.

- `/Library/LaunchDaemons` runs as the system "_administrator_" (root).

- If macOS has a single user:
  - If you want to export only when the user is logged in, choose the first one.
  - If you want the script to always run on System Startup, choose the second one.
- If macOS has multiple users:
  - If you want the script to run only when a certain user is logged in, choose the first one.
  - If you want the script to always run on System Startup, choose the second one.

To quickly go to these directories, open Finder and press ⇧⌘G, then paste the path into the text box.

2. To load the job into launchctl, in Terminal, type `launchctl load`, press SPACE, drag and drop the `.plist` into the Terminal window, then hit RETURN. It won't output anything if it was successfully loaded.

### Extra launchctl commands

**Unloading a job**

```
launchctl unload /path/to/Library/LaunchAgents/local.discordchatexporter.plist
```

**List every loaded job**

```
launchctl list
```

**Check if a specific job is enabled**
You can also see error codes (2nd number) by running this command.

```
launchctl list | grep local.discordchatexporter
```

---

Further reading: [Script management with launchd in Terminal on Mac](https://support.apple.com/guide/terminal/script-management-with-launchd-apdc6c1077b-5d5d-4d35-9c19-60f2397b2369/mac) and [launchd.info](https://launchd.info/).
Special thanks to [@Yudi](https://github.com/Yudi)
