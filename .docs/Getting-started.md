# Getting started

In this page, you'll find the necessary information to get started with **DiscordChatExporter** (**DCE** for short).  
For other things you can do with DCE, check out the [Guides](Readme.md#guides) section.

If you still have unanswered questions after reading this page or if you have encountered a problem, please visit our [FAQ](FAQ.md) and [Troubleshooting](Troubleshooting.md) sections.

## How to download?

### Choose your version of DCE

![GUI vs CLI](https://i.imgur.com/j9OTxRB.png)

You can use DCE in two ways:

- **Graphical User Interface (GUI)**

  - Best for beginners
  - Easy to use, with a visual interface

- **Command-Line Interface (CLI)**

  - Designed for advanced users
  - Offers more features and customization options
  - Can be used in scripts to automate tasks

### Download the correct files

Go to the [**latest releases page**](https://github.com/Tyrrrz/DiscordChatExporter/releases/latest) to download the version you chose.

- 😃 **For GUI:**  
  Look for files _without_ `.Cli.` in their names.

- 🤓 **For CLI:**  
  Look for files _with_ `.Cli.` in their names.

Choose the right file for your operating system:

- **Windows:**
  - Not sure about your system specifications? Download `DiscordChatExporter.win-x64.zip`.
- **macOS:**
  - If your Mac has [**Apple silicon**](https://support.apple.com/116943), download the `.osx-arm64.zip` file.
  - If your Mac has an **Intel** processor, download the `.osx-x64.zip` file.
- **Linux**:
  - Look for files with the `.linux-*.zip` suffix.  
    Choose the one matching your system architecture.

## File formats

DCE can export messages in different formats.  
The format you choose will depend on your needs and preferences.

### HTML

![](https://i.imgur.com/S7lBTkV.png)
The HTML format replicates Discord's interface, making it the most user-friendly option.
It's the best format for viewing media, such as images and videos, and for sharing with others.

You can open `.html` files with a web browser, such as Google Chrome.

> [!WARNING]
> If a picture is deleted, or if a user changes its avatar, the respective images will no longer be displayed.  
> Export using the "Download assets" (`--media`) option to avoid this.

### Plain Text

<img src="https://i.imgur.com/PbUyRXD.png" height="400"/>

The Plain Text format is a simple text file that contains the messages in a readable format. It has the smallest size of all formats.
You can open `.txt` files with a text editor, such as Notepad.

### JSON

<img src="https://i.imgur.com/FAeSA4O.png" height="400"/>

The JSON format contains more technical information about the messages and is easily parsable.  
You can open `.json` files with a text editor, such as Visual Studio Code.

### CSV (deprecated)

![](https://i.imgur.com/VEVUsKs.png)
![](https://i.imgur.com/1vPmQqQ.png)

The CSV format allows for easy parsing of the chat log. Depending on your needs, the JSON format might be better.  
You can open `.csv` files with a text editor, such as Notepad, or a spreadsheet app, like Microsoft Excel and Google Sheets.

## Exporting messages

Check the dedicated guides for each version:

- [Using the GUI](Using-the-GUI.md)
- [Using the CLI](Using-the-CLI.md)
