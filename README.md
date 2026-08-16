# NomadNet Node - Alpine Linux Guide
This is a guide for installing a [NomadNet Node](https://github.com/markqvist/NomadNet) on Alpine Linux.

This guide will:
* Provide a [OpenRC](https://en.wikipedia.org/wiki/OpenRC) daemon for [nomadnet](https://github.com/markqvist/NomadNet#how-do-i-get-started), that's configured to start on boot
* Provide a cronjob to handle unattended system updates

## What is a NomadNet Node
Here is a quote from the [Official GitHub Repo](https://github.com/markqvist/NomadNet):

> Nomad Network allows you to build private and resilient communications platforms that are in complete control and ownership of the people that use them. No signups, no agreements, no handover of any data, no permissions and gatekeepers.
>
> Nomad Network is build on [LXMF](https://github.com/markqvist/LXMF) and [Reticulum](https://github.com/markqvist/Reticulum), which together provides the cryptographic mesh functionality and peer-to-peer message routing that Nomad Network relies on. This foundation also makes it possible to use the program over a very wide variety of communication mediums, from packet radio to fiber optics.
>
> Nomad Network does not need any connections to the public internet to work. In fact, it doesn't even need an IP or Ethernet network. You can use it entirely over packet radio, LoRa or even serial lines. But if you wish, you can bridge islanded networks over the Internet or private ethernet networks, or you can build networks running completely over the Internet. The choice is yours. Since Nomad Network uses Reticulum, it is efficient enough to run even over *extremely* low-bandwidth medium, and has been succesfully used over 300bps radio links.

As far as hardware provisioning, something like a Raspberry Pi should more than suffice as a hardware platform, as Reticulum is designed for low bandwidth and high latency.

## Installation Overview
First, this guide will walk you through the process of installing Alpine Linux. After Alpine Linux is installed and configured, there's a installation script that will handle the process of installing the lxmd daemon and a cronjob to handle unattended system updates.

## Installing Alpine Linux
The guide for installing Alpine Linux can be found [here](https://github.com/danlbarron/alpine-install-guide)

## Configuring Alpine Linux
Once Alpine Linux is installed and rebooted, you'll be greeted by the terminal prompting you to log-in. This time, choose the user account you created.  
Once logged in, type `doas -s`. This command will require your password, which in turn, will escalate your permissions to act as root.  
Let's disable the login for root by entering `passwd -l root`.  
Let's also add a standard user that will be used for running the lxmd daemon by entering `adduser rnsuser`. The reason we create another user is we want the user to adhere to the [principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) by not being able to escalate permissions to act as root. You can also disable the login for rnsuser if you'd like by entering `passwd -l rnsuser`.

Now for the star of the show, let's run the installation script:
```shell
cd ~
wget https://raw.githubusercontent.com/danlbarron/nomadnet-alpine/refs/heads/main/install_nomadnet.sh
chmod +x install_nomadnet.sh
./install_nomadnet.sh
```

You'll need to configure the nomadnet config file, specifically the section prefixed with `[node]`. At the very least, you'll want to set `enable_node = yes` and change `node_name` to a unique name that fits your narrative.
```shell
apk add nano
nano /home/rnsuser/.nomadnetwork/config

# OR
vi /home/rnsuser/.nomadnetwork/config
```

After updating the config, you'll want to restart the daemon via `rc-service nomadnet restart`. Be sure to type `exit` or `reboot` to ensure you fully logout.

Congratulations, you now have a NomadNet Node!
