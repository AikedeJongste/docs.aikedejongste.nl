---
layout: default
title: Linux
has_children: true
---

# Linux

## Download and verify iso

```bash
wget http://cdimage.ubuntu.com/ubuntu/releases/18.04/release/SHA256SUMS.gpg
wget http://cdimage.ubuntu.com/ubuntu/releases/18.04/release/SHA256SUMS

gpg2 --recv-keys 0x46181433FBB75451 0xD94AA3F0EFE21092
gpg2 --verify SHA256SUMS.gpg SHA256SUMS

sha256sum -c SHA256SUMS 2>&1 | grep OK
```

## Updates and upgrades

```
cat <<EOF > /etc/apt/apt.conf.d/10periodic
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "1";
APT::Periodic::Unattended-Upgrade "1";
EOF
```

## Ionotify

[https://github.com/mikesart/inotify-info](https://github.com/mikesart/inotify-info)
