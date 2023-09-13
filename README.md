[![Logo Image](https://cdn.pterodactyl.io/logos/new/pterodactyl_logo.png)](https://pterodactyl.io)

![Discord](https://img.shields.io/discord/122900397965705216?label=Discord&logo=Discord&logoColor=white)
![GitHub Releases](https://img.shields.io/github/downloads/pterodactyl/wings/latest/total)
[![Go Report Card](https://goreportcard.com/badge/github.com/pterodactyl/wings)](https://goreportcard.com/report/github.com/pterodactyl/wings)

# Pterodactyl Second Wings on Same Node


Internal web server port is 8081. (not 8080)

Internal SFTP port is 2023. (not 2022)

## Dependencies
**Do not repeat these steps in the dependencies section if you have already setup wings previously on the system.**
- Docker
- curl
```
curl -sSL https://get.docker.com/ | CHANNEL=stable bash
systemctl enable --now docker
# nano /etc/default/grub and enable swap by changing this line to
# GRUB_CMDLINE_LINUX_DEFAULT="swapaccount=1"
# Okay, once swap is enabled, run `sudo update-grub` and `sudo reboot`
```
## Setup Directions for second wings
The first step for installing Wings is to ensure we have the required directory structure setup. To do so, run the commands below, which will create the base directory and download the wings executable.

**Basically, we need to append -"2" to everything because we do not want conflicts with our current Wings installation.**

```
mkdir -p /etc/pterodactyl2
curl -L -o /usr/local/bin/wings2 "DOWNLOAD TO THE BINARY HERE"
chmod u+x /usr/local/bin/wings2
```

## Configure on web panel

Okay, grab the congiuration file for the node in Pterodactyl Panel.
This is what it looks like, but this is **wrong**:
```
debug: false
uuid: 66929f67-8699-4b72-ad3b-2c0d171051cb
token_id: pk63qdxxujviq5gh
token: fq2czsl8qgdp29ewqv6ol0wovmnnwwjp2cqnxpffvz1d7szoh30r7pnxtvigl0tf
api:
  host: 0.0.0.0
  port: 8080
  ssl:
    enabled: true
    cert: /etc/letsencrypt/live/dfw1.furweb.com/fullchain.pem
    key: /etc/letsencrypt/live/dfw1.furweb.com/privkey.pem
  upload_limit: 100
system:
  data: /var/lib/pterodactyl/volumes
  sftp:
    bind_port: 2022
allowed_mounts: []
remote: 'https://panel.furweb.com'
```
### ^^^ DO NOT JUST COPY AND PASTE SHIT AND DON'T EVEN THINK ABOUT GENERATING THE "AUTO-DEPLOY" COMMAND.
What we need to change:
- Change the web server port to 8081
- Change the sftp port to 2023
So it should look like this:
```
debug: false
uuid: 66929f67-8699-4b72-ad3b-2c0d171051cb
token_id: pk63qdxxujviq5gh
token: fq2czsl8qgdp29ewqv6ol0wovmnnwwjp2cqnxpffvz1d7szoh30r7pnxtvigl0tf
api:
  host: 0.0.0.0
  port: 8081
  ssl:
    enabled: true
    cert: /etc/letsencrypt/live/dfw1.furweb.com/fullchain.pem
    key: /etc/letsencrypt/live/dfw1.furweb.com/privkey.pem
  upload_limit: 100
system:
  data: /var/lib/pterodactyl/volumes
  sftp:
    bind_port: 2023
allowed_mounts: []
remote: 'https://panel.furweb.com'
```

Okay we done, manually save the above as `config.yml` in `/etc/pterodactyl2`

## Starting second wings

Okay, we done, let's test it.
```
sudo wings2 --debug
```
To differentiate between 2 wings, it should say "wingscum" not "wings."

## Daemonizing second wings (using systemd)

Running Wings in the background is a simple task, just make sure that it runs without errors before doing this. Place the contents below in a file called `wings2.service` in the `/etc/systemd/system` directory.

`wings2.service`:
```
[Unit]
Description=Pterodactyl Wings2 Daemon
After=docker.service
Requires=docker.service
PartOf=docker.service

[Service]
User=root
WorkingDirectory=/etc/pterodactyl2
LimitNOFILE=4096
PIDFile=/var/run/wings2/daemon.pid
ExecStart=/usr/local/bin/wings2
Restart=on-failure
StartLimitInterval=180
StartLimitBurst=30
RestartSec=5s

[Install]
WantedBy=multi-user.target
```

Then, run the commands below to reload systemd and start Wings2.
```
systemctl enable --now wings2
```

# ^^^ END OF MY SETUP ^^^
# ORIGINAL DESCRIPTION BELOW

# Pterodactyl Wings

Wings is Pterodactyl's server control plane, built for the rapidly changing gaming industry and designed to be
highly performant and secure. Wings provides an HTTP API allowing you to interface directly with running server
instances, fetch server logs, generate backups, and control all aspects of the server lifecycle.

In addition, Wings ships with a built-in SFTP server allowing your system to remain free of Pterodactyl specific
dependencies, and allowing users to authenticate with the same credentials they would normally use to access the Panel.

## Sponsors

I would like to extend my sincere thanks to the following sponsors for helping find Pterodactyl's development.
[Interested in becoming a sponsor?](https://github.com/sponsors/matthewpi)

| Company                                                   | About                                                                                                                                                                                                                           |
|-----------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [**WISP**](https://wisp.gg)                               | Extra features.                                                                                                                                                                                                                 |
| [**Aussie Server Hosts**](https://aussieserverhosts.com/) | No frills Australian Owned and operated High Performance Server hosting for some of the most demanding games serving Australia and New Zealand.                                                                                 |
| [**BisectHosting**](https://www.bisecthosting.com/)       | BisectHosting provides Minecraft, Valheim and other server hosting services with the highest reliability and lightning fast support since 2012.                                                                                 |
| [**MineStrator**](https://minestrator.com/)               | Looking for the most highend French hosting company for your minecraft server? More than 24,000 members on our discord trust us. Give us a try!                                                                                 |
| [**Skynode**](https://www.skynode.pro/)                   | Skynode provides blazing fast game servers along with a top-notch user experience. Whatever our clients are looking for, we're able to provide it!                                                                              |
| [**VibeGAMES**](https://vibegames.net/)                   | VibeGAMES is a game server provider that specializes in DDOS protection for the games we offer. We have multiple locations in the US, Brazil, France, Germany, Singapore, Australia and South Africa.                           |
| [**Pterodactyl Market**](https://pterodactylmarket.com/)  | Pterodactyl Market is a one-and-stop shop for Pterodactyl. In our market, you can find Add-ons, Themes, Eggs, and more for Pterodactyl.                                                                                         |
| [**DutchIS**](https://dutchis.net?ref=pterodactyl)        | DutchIS provides instant infrastructure such as pay per use VPS hosting. Start your game hosting journey on DutchIS.                                                                                                            |
| [**Skoali**](https://skoali.com/)                         | Skoali is a French company that hosts game servers and other types of services (VPS, WEB, Dedicated servers, ...). We also have a free plan for Minecraft and Garry's Mod.                                                      |

## Documentation

* [Panel Documentation](https://pterodactyl.io/panel/1.0/getting_started.html)
* [Wings Documentation](https://pterodactyl.io/wings/1.0/installing.html)
* [Community Guides](https://pterodactyl.io/community/about.html)
* Or, get additional help [via Discord](https://discord.gg/pterodactyl)

## Reporting Issues

Please use the [pterodactyl/panel](https://github.com/pterodactyl/panel) repository to report any issues or make
feature requests for Wings. In addition, the [security policy](https://github.com/pterodactyl/panel/security/policy) listed
within that repository also applies to Wings.
