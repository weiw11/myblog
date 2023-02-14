---
title: "Duplicati Setup on Ubuntu"
tags: ["duplicati", "ubuntu"]
date: 2023-02-13T12:07:14-05:00
draft: false
---

## Package Repository Setup

### Adding Mono Repository

Add the Mono repository to your system as Duplicati requires mono in order to work.

```sh
sudo apt install gnupg ca-certificates

sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF

echo "deb https://download.mono-project.com/repo/ubuntu stable-focal main" | sudo tee /etc/apt/sources.list.d/mono-official-stable.list

sudo apt update

sudo apt install mono-devel gtk-sharp2
```

## Downloading Duplicati

Get the url of the latest version of Duplicat from this [link](https://www.duplicati.com/download).

Run wget to download the file. Replace the link below with the link you got from above.

```sh
wget https://updates.duplicati.com/beta/duplicati_2.0.5.1-1_all.deb
```

## Installing Duplicati

Install Duplicati using the package manager. You can press <kbd>TAB</kbd> after typing in `./dupli` to have it autocomplete the rest of the file name for you.

```sh
sudo apt install ./duplicati_2.0.5.1-1_all.deb
```

### Starting System Service

Start duplicati and check the status.

```sh
sudo systemctl enable duplicati

sudo systemctl start duplicati

sudo systemctl status duplicati
```

If you are going to connect to duplicati from a different machine, you must edit the duplicati.service file in order to allow connection from your LAN.

By default, you can edit it using the following command. If it's not there, check the location of the file by running sudo systemctl status duplicati. It will be listed after the Loaded: loaded line.

```sh
sudo nano /lib/systemd/system/duplicati.service
```

Find and add to the `ExecStart=` line so it looks like the following. You can adjust the default port duplicati uses by changing 8200 to the desired port. If you do not plan on changing the default port, you can just remove `--webservice-port=8200`.

The final file should look like this:

```ini
[Unit]
Description=Duplicati web-server
After=network.target

[Service]
IOSchedulingClass=idle
EnvironmentFile=-/etc/default/duplicati
ExecStart=/usr/bin/duplicati-server $DAEMON_OPTS --webservice-port=8200 --webservice-interface=any
Restart=always

[Install]
WantedBy=multi-user.target
```

Now we have to reload the daemon and restart Duplicati in order for it to use the new configuration.

```sh
sudo systemctl restart duplicati
```

If you have a firewall, you must allow the ports that Duplicati uses. If you are using ufw, you can allow it with the following command. If you changed your port above, replace `8200` with the port you specified.

```sh
sudo ufw allow 8200
```

Now you should be able to connect to Duplicati using your server's **IP address**. If you do not know your server's IP address, you can use the following command to see it if you're connecting locally.

```sh
hostname -I
```

## Using Duplicati

On your web browser, type in the following. Replace `[Server IP]` with the ip of your server, and `8200` if you changed the default port previously.

```sh
[Server IP]:8200
```

As this is the first time you're connecting, Duplicati will ask if you're going to be the only user. However, as we are connecting to Duplicati through our LAN and not locally on the machine, it's recommended to set up a password. So click Yes.

Set up the password and you can now configure Duplicati to backup your server!

I won't go into the details of setting up the backup as Duplicati already does a great job with it's easy to follow instructions.
