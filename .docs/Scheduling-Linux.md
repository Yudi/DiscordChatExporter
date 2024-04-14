# Scheduling exports with Cron

> **Note**:
> Make sure you have [.NET Core installed](Linux.md) before attempting to schedule exports.

## Creating the script

1. Open Terminal and set the script's path to a variable with `SCRIPT_PATH="/path/to/DiscordChatExporter.Cli/cron-script.sh"` so you don't have to type it repeatedly.

2. Create a new text file with `nano "$SCRIPT_PATH"`

> [!TIP]
> You can't use your mouse in nano, use the arrow keys to control the cursor (caret).

3. Copy the code below and paste it into the text file. Use <kbd>Ctrl</kbd> + <kbd>Shift</kbd> + <kbd>V</kbd>.

```bash
#!/bin/bash
# Info: https://github.com/Tyrrrz/DiscordChatExporter/blob/master/.docs

TOKEN="tokenHere"
CHANNEL_ID="channelIdHere"
DCE_FOLDER="dceFolderHere"
EXPORT_FOLDER="exportFolderhere"
FILENAME="filenameHere"
EXPORT_FORMAT="formatHere"
# Available export formats: PlainText, HtmlDark, HtmlLight, Json, Csv
#
# You can't use partitioning (-p) and dynamic output (-o %) with this script.
# You can edit the export command on line 47 if you'd like to include more options like date ranges and date format.

# This will convert EXPORT_FORMAT to lowercase.
EXPORT_FORMAT=$(echo $EXPORT_FORMAT | tr '[:upper:]' '[:lower:]')

# This will verify if EXPORT_FORMAT is valid.
# FILE_EXTENSION will be used in the final filename.
case "$EXPORT_FORMAT" in
plaintext)
  FILE_EXTENSION=txt
  ;;
htmldark | htmllight)
  FILE_EXTENSION=html
  ;;&
json)
  FILE_EXTENSION=json
  ;;
csv)
  FILE_EXTENSION=csv
  ;;
*)
 # If the format is not listed (invalid), the script will exit.
  echo "$EXPORT_FORMAT - Unknown export format"
  echo "Available export formats: PlainText, HtmlDark, HtmlLight, Json, Csv"
  echo "/\ CaSe-SeNsItIvE /\\"
  exit 1
  ;;
esac

# This will set the script's directory to DCE_FOLDER.
# If unable to do so, the script will exit.
cd $DCE_FOLDER || exit 1

# This will export your chat.
# If unable to do so, the script will exit.
if ! ./DiscordChatExporter.Cli.sh export -t "$TOKEN" -c "$CHANNEL_ID" -f "$EXPORT_FORMAT" -o "$FILENAME.tmp"; then
  echo "Failed to export chat."
  exit 1
fi

# This sets the current time to a variable so it can be used in the filename.
CURRENTTIME=$(date +"%Y-%m-%d-%H-%M-%S")

# This will move the .tmp file to the desired export location.
# If unable to do so, it will attempt to delete the .tmp file.
if ! mv "$FILENAME.tmp" "$EXPORT_FOLDER/$FILENAME-$CURRENTTIME.$FILE_EXTENSION"; then
  echo "Unable to move $FILENAME.tmp to $EXPORT_FOLDER/$FILENAME-$CURRENTTIME.$FILE_EXTENSION."
  echo "Cleaning up..."
  if ! rm -Rf "$FILENAME.tmp"; then
    echo "Unable to delete $FILENAME.tmp."
  fi
  exit 1
fi
exit 0
```

4. Replace:

- `tokenHere` with your [Token](Token-and-IDs.md).
- `channelIdHere` with a [Channel ID](Token-and-IDs.md).
- `dceFolderHere` with DCE's **folder** path (e.g. `"/path/to/DCE.Cli"`, not `"/path/to/DCE.Cli/DiscordChatExporter.dll"`).
- `exportFolderhere` with the directory the exported chats will be saved to (e.g. `"/home/user/Documents/Discord Exports"`).
- `filenamehere` with the exported channel's filename.
- `formatHere` with one of the available export formats.

> [!IMPORTANT]  
> Make sure not to delete the quotes (") when replacing: `DCE_FOLDER="/home/my user/DCE"`.
> Don't use quotes as part of the paths: `"/home/my user/"Carpe Diem" server"`.

> [!TIP]
> To save, hold down <kbd>Ctrl</kbd> and then press <kbd>O</kbd>. Press <kbd>Ctrl</kbd> + <kbd>X</kbd> to exit the text editor.  
> [Check out the Gentoo Nano Guide](https://wiki.gentoo.org/wiki/Nano/Guide) if you want to know more about Nano.

5. Make your script executable with `chmod +x "$SCRIPT_PATH"`

6. To run the script with user privileges, edit the cron file with `crontab -e` (preferred).  
   To run the script as root, edit it with `sudo crontab -e`.  
   If you're running this command for the first time, you might be asked to select a text editor: Press <kbd>1</kbd> and then <kbd>⏎ Enter</kbd> to confirm nano as your text editor.

7. Add the following to the end of the file:

```
* * * * * "/path/to/DiscordChatExporter.Cli/cron-script.sh" >/tmp/discordchatexporter.log 2>/tmp/discordchatexportererror.log
```

> [!IMPORTANT]  
> Replace `"/path/to/DiscordChatExporter.Cli/cron-script.sh"`!

> [!TIP]
> If you don't want logs to be saved, replace both `/tmp/discordchatexporter[error].log` with `/dev/null`.

Then replace the \*s according to:

![](https://i.imgur.com/RY7USM6.png)

**Examples:**

- If you want to execute the script at minute 15 of every hour: `15 * * * *`
- Every 30 minutes: `*/30 * * * *`
- Every day at midnight: `0 0 * * *`
- Every day at noon: `0 12 * * *`
- Every day at 3, 4 and 6 PM: `0 15,16,18 * * *`
- Every Wednesday at 9 AM: `0 9 * * 3`

Verify your cron time [here](https://crontab.guru).

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
