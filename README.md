# Installing Pi-Hole and PiVPN on a Digital Ocean VPS

## Digital Ocean

- Create a droplet using Ubuntu 16.04
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

    # Open ports for secure FTP
    sudo ufw allow sftp

    # Optionally, allow all access from your IP Address
    sudo ufw allow from your_ip_address
    ```

## Pi-Hole

TODO

## PiVPN

TODO

## Sources
- https://github.com/pi-hole/pi-hole
- https://github.com/pivpn/pivpn
- https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04
- https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29
- https://itchy.nl/raspberry-pi-3-with-openvpn-pihole-dnscrypt
- http://kamilslab.com/2017/01/22/how-to-turn-your-raspberry-pi-into-a-home-vpn-server-using-pivpn/