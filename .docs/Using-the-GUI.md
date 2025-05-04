# Using the GUI

## Video tutorial

[![Video tutorial](https://i.ytimg.com/vi/jjtu0VQXV7I/hqdefault.jpg)](https://youtube.com/watch?v=jjtu0VQXV7I)

> Video by [NoIntro Tutorials](https://youtube.com/channel/UCFezKSxdNKJe77-hYiuXu3Q).

## Guide

### Step 1 - Download, extract and run

To download, visit to the [**latest releases page**](https://github.com/Tyrrrz/DiscordChatExporter/releases/latest).

<!-- The platforms list below is replicated on Getting-started.md to help beginners.
     If you change it here, change it there too. -->

- **Windows:**
  - Not sure about your system specifications? Download `DiscordChatExporter.win-x64.zip`.
- **macOS:**
  - If your Mac has [**Apple silicon**](https://support.apple.com/116943), download the `DiscordChatExporter.macos-arm64.zip` file.
  - If your Mac has an **Intel** processor, download the `DiscordChatExporter.macos-x64.zip` file.
- **Linux**:
  - Look for files with the `DiscordChatExporter.linux-*.zip` suffix.  
    Choose the one matching your system architecture.

Extract the downloaded `.zip` file to a folder of your choice.

If you are using **Windows**, open `DiscordChatExporter.exe`.  
If you are using **macOS**, use the instructions provided in [macOS workaround](macOS-workaround.md).
If you are using **Linux**, open `DiscordChatExporter`.

### Step 2 - Authenticate

Refer to the on-screen instructions to get your token, then paste your token in the upper text box and hit <kbd>ENTER</kbd> or click the arrow (→).  
If the instructions seem unclear, check the detailed guide on [how to obtain your token](Token-and-IDs.md).

<!-- prettier-ignore -->
> [!WARNING]
> **Never share your token!** A token gives full access to an account, treat it like a password.

<img src="https://i.imgur.com/SuLQ5tZ.png" height="400"/>

### Step 3 - Select a channel

DCE will display your Direct Messages and a sidebar with your server list. Select the channel you'd like to export, then click the ![](https://i.imgur.com/dnTOlDa.png) button to continue.

> [!TIP]
> You can export multiple channels at once by holding <kbd>Ctrl</kbd> (⌘ on macOS) or <kbd>Shift</kbd> while selecting.  
> You can also double-click a channel to export it directly.

<img src="https://i.imgur.com/JHMFRh2.png" height="400"/>

### Step 4 - Export

In this screen you can customize your export. You need to scroll down to see all available options.

- **Output path** - The folder where the exported chat(s) will be saved.

- **Export format** - HTML (Dark), HTML (Light), TXT, JSON and CSV (deprecated)

- **Date range (after/before)** (Optional) - If set, only messages sent in the provided date range will be exported. Only one value (either after or before) is required if you want to use this option.

> [!NOTE]
> The time defaults to **12:00 AM** (midnight, 00:00). This means that if you choose to export between November 14th and November 15th, messages sent on November 15th will not be included in the export.

- **Partition limit** (Optional) - Split output into partitions, each limited to a number of messages (e.g. 100) or a maximum file size (e.g. 10mb).  
  For example, a channel with 36 messages set to be partitioned every 10 messages will output 4 files.

- **Message Filter** (Optional) - Special notation for filtering the messages that get included in the export. See [Message filters](Message-filters.md) for more info.

- **Format markdown** (Optional) - Disable markdown processing when exporting. You can use this to produce JSON or plain text exports without unwrapping mentions, custom emoji, and certain other special tokens.

- **Download assets** (Optional) - If this option is set, the export will include additional files such as user avatars, attached files, images, etc. Only files that are referenced by the export are downloaded, which means that, for example, user avatars will not be downloaded when using the plain text (TXT) export format. A folder containing the assets will be created along with the exported chat. They must be kept together.

- **Reuse assets** (Optional) - If this option is set, the export will reuse already downloaded assets to skip redundant requests. This option is only available when **Download assets** is enabled.

- **Assets directory path** (Optional) - If this option is set, the export will use the specified directory to store assets from all exported channels in the same place.

## Settings

- **Auto-update** - Perform automatic updates on every launch.  
  Default: Enabled

> [!NOTE]
> Keep auto-updates enabled to receive the latest features and bug fixes!

- **Dark mode** - Use darker colors in the User Interface (UI).  
  Default: Disabled

- **Persist token** - Persist last used token between sessions. This will save your token to a plain text file in the same directory as the executable.  
  Default: Enabled

- **Show threads** - Controls whether threads are shown in the channel list.  
  Default: none

- **Locale** - Customize how dates are formatted in the exported files.

- **Date format** - Customize how dates are formatted in the exported files in the settings menu (⚙).

- **Parallel limit** - The number of channels that will be exported at the same time.
  Default: 1

  > **Note**:
  > Try to keep this number low so that your account doesn't get flagged.

- **Normalize to UTC** - Convert all dates to UTC before exporting.
