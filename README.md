# Installing Pi-Hole and PiVPN on a Digital Ocean VPS

**This guide is still a work-in-progress. Many critical steps are missing. I would not recommend following the guide as-is right now.**

## Introduction

The purpose of this guide is to document the steps I took to create a droplet on [DigitalOcean](https://www.digitalocean.com/) with [Pi-Hole](https://pi-hole.net/) and [PiVPN](http://www.pivpn.io/) installed. The ultimate goal is to have an ad-blocker that will work both on my home network and on any device connected to the VPN.

Almost every tutorial I found was focused on installing Pi-Hole and PiVPN on a local Raspberry Pi instead of a VPS. The steps are mostly the same but there are some extra steps involved in securing the VPS to deny access from bad actors.

After completing this tutorial, you will have:

- A Pi-Hole accessible from anywhere
- A VPN that will provide an encrypted connection when using public Wi-Fi

## Prerequisites

In order to follow this tutorial you will need to have a DigitalOcean account. If you do not have one, you can [sign up here](https://cloud.digitalocean.com/registrations/new).

## Creating a Droplet

This tutorial will use **Ubuntu 16.04 x64** as the base image. 16.04 is a long-term support release of Ubuntu and supports all of the software we will be installing. You can pick a different Linux distrobution if you wish, but be cautioned that the software may not work without some extra effort on your part.

- Go the the [Create Droplet](https://cloud.digitalocean.com/droplets/new) page in your DigitalOcean account
- Choose an Image
  - Select _Distributions_ and then _Ubuntu_. If _16.04_ is not selected by default, select it in the dropdown menu.
- Choose a size
  - `$5/mo` will be large enough for this tutorial. You can select a larger size later if necessary.
- Choose a datacenter region
  - I recommend selecting a region that is closest to you
- Select Additional Options
  - Select `IPv6` so that we can block ads that are served on that protocol
  - (Optional) Select `Monitoring` for additional monitoring features provided by DigitalOcean
- Add your SSH keys
  - Do not add any SSH keys yet. We will add them later.
- Finalize and create
  - Make sure you are creating a single droplet
  - Choose a hostname, e.g. `nledford-pihole`
  - Click the **Create** button

DigitalOcean will start the process of creating the droplet for you and will send you an email containing the root password for your droplet. We will need that root password before we can log into and configure our new droplet.

- Log in as root
    ```shell
    ssh root@your_server_ip
    ```
- Create new user `pi`
    ```shell
    adduser pi
    ```
- Grant root privileges to `pi`
    ```shell
    usermod -aG sudo pi
    ```
- Set up firewall with [`ufw`](https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29)
    ```shell
    # Apply basic defaults
    sudo ufw default deny incoming
    sudo ufw default allow outgoing

    # Open ports for ssh/OpenSSh
    sudo ufw allow ssh
    sudo ufw allow OpenSSH

    # Optionally, allow all access from your IP Address
    sudo ufw allow from your_ip_address
    ```
- Open ports for Secure FTP
   ```shell
   # Open ports for secure FTP
    sudo ufw allow sftp
   ```
- (Optional) Install [`mosh`](https://mosh.org/)
    ```shell
    # Update your sources, if necessary
    sudo apt update

    # Install mosh
    sudo apt install mosh

    # Open ports in firewall
    sudo ufw allow mosh
    ```
- Run the offical [Pi-Hole Installer](https://github.com/pi-hole/pi-hole/blob/master/automated%20install/basic-install.sh)
    ```shell
    curl -sSL https://install.pi-hole.net | bash
    ```
- On a Raspberry Pi, we would be asked to set a static IP address, but since we are using a VPS a static IP has already been set for us
- When asked about which protocols to use for blocking ads, select both **IPv4** and **IPv6**, even if you cannot use IPv6 yet
- Run the offical [PiVPN Installer](https://github.com/pivpn/pivpn/blob/master/auto_install/install.sh)
  ```shell
  curl -L https://install.pivpn.io | bash
  ```
- On a Raspberry Pi, we would be asked to select a network interface, but since we are on a VPS the only available interface is `eth0` and that is automatically selected for us
- The static IP address is also automatically selected for us
- When asked to choose a local user to hold your `ovpn` configurations, select the user `pi`
- When asked about enabling Unattended Upgrades, pick yes
- When asked to select the protocol, pick `UDP`
- When asked to select the port, either accept the default `1194` or enter a random port such as `11948`
- When asked to set the size of your encryption key, select `2048`
  - Generating the encryption key will take a few minutes
- When asked to select a Public IP or DNS, select your server's IP address
- When asked to select a DNS provider, either accept Google as the default (`8.8.8.8`, `8.8.4.4`) or select custom and enter your preferred DNS providers
- Allow the installer to reboot your VPS
- Create an OpenVPN profile
    ```shell
    # Create ovpn profile with a password
    pivpn add

    # create ovpn profile WITHOUT a password
    pivpn add -nopass
    ```
- TODO add instructions on how to retrieve `.ovpn` profile from VPS using FTP application
- TODO add instructions on how to use `.ovpn` profile with OpenVPN clients
- Make final adjustments to files on your VPS
- TODO code from Step 8: Adjust the Server Networking Configuration

## Notes

- SFTP ports are opened so that your `.ovpn` file(s) can be retrieved later via an FTP program such as Filezilla or Transmit once PiVPN is installed.

## Sources

- [Pi-Hole](https://github.com/pi-hole/pi-hole)
- [PiVPN](https://github.com/pivpn/pivpn)
- [Digital Ocean: Initial Server Setup with Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)
- [Debian Wiki: Uncomplicated Firewall](https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29)
- https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-16-04
- <https://itchy.nl/raspberry-pi-3-with-openvpn-pihole-dnscrypt>
- <http://kamilslab.com/2017/01/22/how-to-turn-your-raspberry-pi-into-a-home-vpn-server-using-pivpn/>

## Suggested Blocklists

- [The Big Blocklist Collection](https://wally3k.github.io/)
- [Pi-Hole: Commonly Whitelisted Domains](https://discourse.pi-hole.net/t/commonly-whitelisted-domains/212)

### Other Links To Sort

- <https://www.reddit.com/r/raspberry_pi/comments/4cqf1t/need_help_with_getting_pihole_and_openvpn_working/>
- <https://github.com/pivpn/pivpn/wiki/FAQ#installing-with-pi-hole>
- <https://www.reddit.com/r/raspberry_pi/comments/5g8w3w/how_to_pair_pi_hole_and_openvpn_to_use_with/das3drs/?>
- <https://github.com/pivpn/pivpn/issues/308#issuecomment-315680658>
