# Scheduling exports with Cron

## What is Cron?

Cron is a time-based job scheduler in Unix-like operating systems such as Linux and macOS. It allows users to schedule jobs (commands or scripts) to run at specific intervals or times.

## Creating the script

1. Open Terminal

2. Set the script's path to a variable with `SCRIPT_PATH="/path/to/DiscordChatExporter.Cli/cron-script.sh"` so you don't have to type it repeatedly

3. Create a new text file with `nano "$SCRIPT_PATH"`

> [!TIP]
> You can't use your mouse in nano, use the arrow keys to control the cursor (caret).

3. Copy the code below and paste it into the text file. Use <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>V</kbd>.

```bash
#!/bin/bash
# DiscordChatExporter - Cron Script
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
    output_file="${EXPORT_FOLDER}/${FILENAME}-${timestamp}.${FILE_EXTENSION}"

    echo "[INFO] Moving exported file to: $output_file"
    if ! mv "${FILENAME}.tmp" "$output_file"; then
        echo "[WARN] Failed to move file. Cleaning up..."
        rm -f "${FILENAME}.tmp" || echo "[WARN] Could not delete temporary file."
        error_exit "Failed to move exported file."
    fi
    echo "[SUCCESS] Chat exported to: $output_file"
}

##########################
# Main Execution         #
##########################

validate_inputs
FILE_EXTENSION=$(get_file_extension)
export_chat
finalize_export

```

4. On the configuration section, replace insert the values between the quotes:

- `TOKEN`: Insert the [Token](Token-and-IDs.md).
- `CHANNEL_ID`: Insert a [Channel ID](Token-and-IDs.md).
- `DCE_FOLDER`: DCE's **folder** path (e.g. `"/path/to/DCE.Cli"`, not `"/path/to/DCE.Cli/DiscordChatExporter.dll"`).
- `EXPORT_FOLDER`: Directory the exported chats will be saved to (e.g. `"/home/user/Documents/Discord Exports"`).
- `FILENAME`: Exported channel's filename.
- `EXPORT_FORMAT`: Export format. Options: `PlainText`, `HtmlDark`, `HtmlLight`, `Json`, `Csv`.

> [!IMPORTANT]  
> Make sure not to delete the quotes (") when replacing: `DCE_FOLDER="/home/my user/DCE"`.
> Don't use quotes as part of the paths: `"/home/my user/"Carpe Diem" server"`.

> [!TIP]
> To save, hold down <kbd>Ctrl</kbd> and then press <kbd>O</kbd>. Press <kbd>Ctrl</kbd> + <kbd>X</kbd> to exit the text editor.  
> [Check out the Gentoo Nano Guide](https://wiki.gentoo.org/wiki/Nano/Guide) if you want to know more about `nano`.

5. Make your script executable with `chmod +x "$SCRIPT_PATH"`

6. To run the script with user privileges, edit the cron file with `crontab -e` (preferred).  
   To run the script as root, edit it with `sudo crontab -e`.  
   If you're running this command for the first time, you might be asked to select a text editor: Press <kbd>1</kbd> and then <kbd>⏎ Enter</kbd> to confirm `nano` as your text editor.

7. Add the following to the end of the file:

```
* * * * * "/path/to/DiscordChatExporter.Cli/cron-script.sh" >/tmp/discordchatexporter.log 2>/tmp/discordchatexportererror.log
```

> [!IMPORTANT]  
> Replace `"/path/to/DiscordChatExporter.Cli/cron-script.sh"` with the path to your script.

> [!TIP]
> If you don't want logs to be saved, replace both `/tmp/discordchatexporter[error].log` with `/dev/null`.

Then replace the asterisks (`*`) with the desired schedule as follows:

![](https://i.imgur.com/RY7USM6.png)

**Examples:**

- If you want to execute the script at minute 15 of every hour: `15 * * * *`
- Every 30 minutes: `*/30 * * * *`
- Every day at midnight: `0 0 * * *`
- Every day at noon: `0 12 * * *`
- Every day at 3, 4 and 6 PM: `0 15,16,18 * * *`
- Every Wednesday at 9 AM: `0 9 * * 3`

Verify if your cron time is correct with [crontab.guru](https://crontab.guru).

## Additional information

### Cron

The week starts on Sunday. 0 = SUN, 1 = MON ... 7 = SUN.

Use military time (24-hour format) for hours.

> [!WARNING]  
> If you set the day to '31', the script will only run on months that have the 31st day.  
> [Learn more about running a cron job on the last day of the month here](https://stackoverflow.com/questions/6139189/cron-job-to-run-on-the-last-day-of-the-month) (expert).

### Script

The default filename for the exported channel is `YYYY-MM-DD-hh-mm-ss-yourfilename.ext`. You can change it if you'd like.

You can test the script anytime by running it with `./cron-script.sh`.

Don't forget to update your token in the script if it has been reset!
