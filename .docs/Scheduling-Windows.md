# Scheduling exports on Windows

## Creating the script

1. Open a text editor such as Notepad and paste:

```powershell
# Info: https://github.com/Tyrrrz/DiscordChatExporter/blob/master/.docs

$TOKEN = "tokenHere"
$CHANNEL_ID = "channelIdHere"
$DCE_FOLDER = "dceFolderHere"
$EXPORT_DIRECTORY = "exportFolderHere"
$FILENAME = "filenameHere"
$EXPORT_FORMAT = "formatHere"
# Available export formats: PlainText, HtmlDark, HtmlLight, Json, Csv
# You can't use partitioning (-p) and dynamic output (-o %) with this script.
# You can edit the export command on line 47 if you'd like to include more options like date ranges and date format.

Switch ($EXPORT_FORMAT)
{
    "PlainText" {
      $FILE_EXTENSION = "txt"
    }
    "HtmlDark" -or "HtmlLight" {
      $FILE_EXTENSION = "html"
    }
    "Json" {
      $FILE_EXTENSION = "json"
    }
    "Csv" {
      $FILE_EXTENSION = "csv"
    }
    Default {
       # If the format is not listed (invalid), the script will exit.
      Write-Host "$EXPORT_FORMAT is not a valid export format. Exiting."
      exit 1
    }
}

# This will set the script's directory to DCE_FOLDER.
# If unable to do so, the script will exit.
cd $DCE_FOLDER || exit 1
./DiscordChatExporter.Cli export -t $TOKEN -c $CHANNEL -f $EXPORTFORMAT -o "$FILENAME.tmp"

# This will export your chat.
.\DiscordChatExporter.Cli.exe export -t $TOKEN -c $CHANNEL_ID -f $EXPORT_FORMAT -o "$FILENAME.tmp"

# This sets the current time to a variable so it can be used in the filename.
$Date = Get-Date -Format "yyyy-MM-dd-HH-mm-ss"

# This will move the .tmp file to the desired export location.
mv "$FILENAME.tmp" -Destination "$EXPORT_DIRECTORY\$FILENAME-$Date.$FILE_EXTENSION"
exit
```

2. Replace:

- `tokenhere` with your [Token](Token-and-IDs.md)
- `channelhere` with a [Channel ID](Token-and-IDs.md)
- `exefolderhere` with the .exe **directory's path** (e.g. C:\Users\User\Desktop\DiscordChatExporter)
- `filenamehere` with a filename without spaces
- `dirhere` with the export directory (e.g. C:\Users\User\Documents\Exports)
- `formathere` with one of the available export formats

Make sure not to delete the quotes (")

3. Save the file as `filename.ps1`, not as `.txt`

> **Note**: You can also modify the script to use other options, such as `include-threads` or switch to a different command, e. g. `exportguild`.

## Export at Startup

1. Press Windows + R, type `shell:startup` and press ENTER
2. Paste `filename.ps1` or a shortcut into this folder

## Scheduling with Task Scheduler

Please note that your computer must be turned on for the export to happen.

1. Press Windows + R, type `taskschd.msc` and press ENTER
2. Select `Task Scheduler Library`, create a Basic Task, and follow the instructions on-screen

<img src="https://i.imgur.com/MHRVGDi.png" height="500"/>

![Screenshot from Task Scheduler](https://i.imgur.com/m2DKhA8.png)

3. At 'Start a Program', write `powershell -file -ExecutionPolicy ByPass -WindowStyle Hidden "C:\path\to\filename.ps1"` in the Program/script text box

![](https://i.imgur.com/FGtWRod.png)

4. Click 'Yes'

![](https://i.imgur.com/DuaRBt3.png)

5. Click 'Finish'

![](https://i.imgur.com/LHgXp9Q.png)

---

Special thanks to [@Yudi](https://github.com/Yudi)
