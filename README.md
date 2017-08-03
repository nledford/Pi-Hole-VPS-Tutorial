# Installing Pi-Hole and PiVPN on a Digital Ocean VPS

**This guide is still a work-in-progress. Many critical steps are missing. I would not recommend following the guide as-is right now.**

- Create a droplet
  - Pick Ubuntu 16.04 x64 as your image
    - You may pick another image, but this guide assumes you are using Ubuntu
  - Pick `$5/mo` as your size
  - Pick your datacenter region
      - Pick a region that is as geographically close to you as possible
  - Select additional options
      - Check **IPv6** so that ads served on IPv6 can be blocked
      - (Optional) Check **Monitioring** to enable DigitalOcean's enhanced monitoring
  - This guide skips adding or using SSH keys for now
  - Choose a hostname
      - E.g., `nledford-pihole`
  - Click the **Create** button
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

## Notes

- SFTP ports are opened so that your `.ovpn` file(s) can be retrieved later via an FTP program such as Filezilla or Transmit once PiVPN is installed.

## Sources

- [Pi-Hole](https://github.com/pi-hole/pi-hole)
- [PiVPN](https://github.com/pivpn/pivpn)
- [Digital Ocean: Initial Server Setup with Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04)
- [Debian Wiki: Uncomplicated Firewall](https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29)
- https://itchy.nl/raspberry-pi-3-with-openvpn-pihole-dnscrypt
- http://kamilslab.com/2017/01/22/how-to-turn-your-raspberry-pi-into-a-home-vpn-server-using-pivpn/

## Suggested Blocklists

- [The Big Blocklist Collection](https://wally3k.github.io/)
- [Pi-Hole: Commonly Whitelisted Domains](https://discourse.pi-hole.net/t/commonly-whitelisted-domains/212)