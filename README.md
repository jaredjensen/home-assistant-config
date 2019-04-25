# Home Assistant Configuration

## Installation

1. Use [Etcher](https://etcher.io/) to burn [an image](https://www.home-assistant.io/hassio/installation/) to a microSD card.
1. Insert the microSD into the internal Pi slot and boot.
1. Access the web interface at http://hassio.local:8123.
1. Install the SSH Server add-on and proceed with configuration.

## Configuration

### SSH Server Add-On

Configure via the web interface. Get the key from `~/.ssh/id_rsa.pub` or generate a new one.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@domain.com"
```

Add the key to the authorized list in the HA SSH add-on.

```json
{
  "authorized_keys": ["ssh-rsa AKDJD3839...== my-key"],
  "password": ""
}
```

After restarting the SSH Server add-on, you should be able to access HA as `root`.

```bash
ssh -l root hassio.local
```

### Restore Configuration

Connect via ssh and restore the configuration from GitHub.

```bash
# Install tools
apk add git
apk add openssl

# Generate self-signed SSL certificate
openssl req -sha256 -newkey rsa:4096 -nodes \
  -keyout /ssl/privkey.pem -x509 -days 730 -out /ssl/fullchain.pem

## Restore configuration
cd /config
git init
git remote add origin https://github.com/jaredjensen/home-assistant-config.git
git fetch origin
git checkout --force --track origin/master
```

Now restore `/config/secrets.yaml` by copying the file, editing in `vi`, or whatever works.

**All done!** Confirm the configuration is valid via the web interface, and then restart Hassio.

### Samba Share Configuration (Optional)

```json
{
  "workgroup": "HOME",
  "username": "hassio",
  "password": "choose a password",
  "interface": "",
  "allow_hosts": ["192.168.0.0/24"]
}
```

### Back Up Configuration

Commit and push changes to remote repo.

**If you're using HTTPS with 2FA enabled**, you'll need to use a [personal access token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line) instead of your password. I save mine in my password manager.

## Manage Devices

### Add Device

1. In HA web interface, go to Configuration > Z-Wave > Add Node (or Add Node Secure)
1. Put the device in "add" mode (usually pressing a button)
1. Go to the Node Information dialog on the Device States page and rename it, e.g.
   - Name: TV left outlet
   - Entity ID: switch.tv_left_outlet_switch
1. Update customize.yaml with same names
1. Add device to groups and/or automations

## Common Commands

| Command            | Purpose                        |
| ------------------ | ------------------------------ |
| hassio ha check    | Validate current configuration |
| hassio ha restart  | Restart homeassistant          |
| hassio host reboot | Reboots the host machine       |

## Reference

[Getting Started Guide](https://www.home-assistant.io/getting-started/)
