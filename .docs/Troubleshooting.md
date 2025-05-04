# Troubleshooting

- ❓ If you still have unanswered questions after reading this page, feel free to [create a new discussion](https://github.com/Tyrrrz/DiscordChatExporter/discussions/new).
- 🐞 If you have encountered a problem that's not described here, has not [been discussed before](https://github.com/Tyrrrz/DiscordChatExporter/discussions), and is not a [known issue](https://github.com/Tyrrrz/DiscordChatExporter/issues?q=is%3Aissue), please [create a new discussion](https://github.com/Tyrrrz/DiscordChatExporter/discussions/new) or [open a bug report](https://github.com/Tyrrrz/DiscordChatExporter/issues/new). Don't forget to include your platform (Windows, macOS, Linux or Docker), and a detailed description of your question/problem.

## Frequently Asked Questions

Refer to the the [FAQ section](FAQ.md) for answers to common questions.

## DCE outputs an error and the exported file is incomplete

Make sure you're using the latest version of DCE and try exporting again.

If you're using the latest version, [check if your issue has already been reported](<(https://github.com/Tyrrrz/DiscordChatExporter/issues?q=is%3Aissue)>).
In case it hasn't, [open a bug report](https://github.com/Tyrrrz/DiscordChatExporter/issues/new).

Providing an invitation link to the channel you are attempting to export from can significantly speed up the resolution of the issue.  
If sharing an invitation link is not possible, include a detailed and comprehensive description of the problem to assist in troubleshooting and reproducing the issue.

## Common Issues

```yml
DiscordChatExporter.Domain.Exceptions.DiscordChatExporterException: Authentication token is invalid.
```

↳ Make sure the provided token is correct.

```yml
DiscordChatExporter.Domain.Exceptions.DiscordChatExporterException: Requested resource does not exist.
```

↳ Check your channel ID, it might be invalid. [Read this if you need help](Token-and-IDs.md).

```yml
DiscordChatExporter.Domain.Exceptions.DiscordChatExporterException: Access is forbidden.
```

↳ You don't have permission to access the channel.

```console
The application to execute does not exist:
```

↳ The `DiscordChatExporter.Cli.dll` file is missing. Keep the `.exe` and all the `.dll` files together.  
If you haven't moved the files, try unzipping again.

```yml
System.Net.WebException: Error: TrustFailure ... Invalid certificate received from server.
```

↳ Try running cert-sync.

Debian/Ubuntu: `cert-sync /etc/ssl/certs/ca-certificates.crt`

Red Hat: `cert-sync --user /etc/pki/tls/certs/ca-bundle.crt`

If it still doesn't work, try mozroots: `mozroots --import --ask-remove`
