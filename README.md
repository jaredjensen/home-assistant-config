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
  -keyout privkey.pem -x509 -days 730 -out /ssl/certificate.pem

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

## Reference

[Getting Started Guide](https://www.home-assistant.io/getting-started/)
