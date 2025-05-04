# Docker usage instructions

The Docker distribution of DCE provides a way to run the app in a virtualized and isolated environment. Due to the nature of Docker, you also don't need to install any prerequisites otherwise required by DCE.

Only the CLI version of DCE is available for use with Docker.

## Pulling

The following command will download the [Docker image from the registry](https://hub.docker.com/r/tyrrrz/discordchatexporter) to your computer. When a new version is released, you can run this command again to update the image.

```console
docker pull tyrrrz/discordchatexporter:stable
```

Note the `:stable` tag.  
DiscordChatExporter images are tagged according to the following patterns:

- `stable` — latest stable version release. This tag is updated with each release of a new project version. Recommended for personal use.
- `x.y.z` (e.g. `2.30.1`) — specific stable version release. This tag is pushed when the corresponding version is released and never updated thereafter. Recommended for use in automation scenarios.
- `latest` — latest (potentially unstable) build. This tag is updated with each new commit to the `master` branch. Not recommended, unless you want to test a new feature that has not been released in a stable version yet.

[See all the available tags here](https://hub.docker.com/r/tyrrrz/discordchatexporter/tags?ordering=name).

## Usage

To run the CLI in Docker and render help text:

```console
docker run --rm tyrrrz/discordchatexporter:stable
```

To export a channel:

```console
docker run --rm -v /path/on/machine:/out tyrrrz/discordchatexporter:stable export -t TOKEN -c CHANNELID
```

If you want colored output and real-time progress reporting, pass the `-it` (interactive + pseudo-terminal) option:

```console
docker run --rm -it -v /path/on/machine:/out tyrrrz/discordchatexporter:stable export -t TOKEN -c CHANNELID
```

The `-v /path/on/machine:/out` option instructs Docker to bind the `/out` directory inside the container to a path on your host machine.  
Replace `/path/on/machine` with the directory you want the files to be saved at.

> [!NOTE]  
> If you are running SELinux, add `:z` to the end of the bind mount option as in `-v /path/on/machine:/out:z`.  
> For more information, refer to the [Docker docs SELinux labels for bind mounts page](https://docs.docker.com/storage/bind-mounts/#configure-the-selinux-label).

You can also use the current working directory as the output directory by specifying:

- `-v $PWD:/out` in Bash
- `-v $pwd.Path:/out` in PowerShell

For more advanced information, refer to the project's [Dockerfile](https://github.com/Tyrrrz/DiscordChatExporter/blob/master/DiscordChatExporter.Cli.dockerfile).

To get your Token and Channel IDs, refer to [this page](Token-and-IDs.md).

## Unix permissions issues

This image was designed with a user running as uid:gid of 1000:1000.

If your current user has different IDs, and you want to generate files directly editable for your user, you might want to run the container like this:

```console
mkdir data # or chown -R $(id -u):$(id -g) data
docker run -it --rm -v $PWD/data:/out --user $(id -u):$(id -g) tyrrrz/discordchatexporter:stable export -t TOKEN -g CHANNELID
```

## Environment variables

DiscordChatExpoter CLI accepts the `DISCORD_TOKEN` environment variable as a fallback for the `--token` option. You can set this variable either with the `--env` Docker option or with a combination of the `--env-file` Docker option and a `.env` file.

Refer to the [Docker documentation](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file) for more information.
