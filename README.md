# Syncthing

### Add the "stable" channel to your APT sources:
```
echo "deb https://apt.syncthing.net/ syncthing stable" | sudo tee /etc/apt/sources.list.d/syncthing.list
```

### Add the release PGP keys:
```
curl -s https://syncthing.net/release-key.txt | sudo apt-key add -
```

### Increase preference of Syncthing's packages ("pinning")
```
printf "Package: *\nPin: origin apt.syncthing.net\nPin-Priority: 990\n" | sudo tee /etc/apt/preferences.d/syncthing
```

### Update and install syncthing:
```
sudo apt-get update
sudo apt-get install syncthing
```

### Depending on your distribution, you may see an error similar to the following when running apt-get:
```
E: The method driver /usr/lib/apt/methods/https could not be found.
N: Is the package apt-transport-https installed?
E: Failed to fetch https://apt.syncthing.net/dists/syncthing/InRelease
```

If so, please install the apt-transport-https package and try again:
```
sudo apt-get install apt-transport-https
```

Add the syncthing user
```
useradd -m synthing
```

Copy syncthing service
```
cp /lib/systemd/system/syncthing@.service /etc/systemd/system
```

Enable and start the service to generate the config file
```
systemctl enable syncthing@syncthing.service
systemctl start syncthing@syncthing.service
```

Edit the config file to change the IP
```
vi /home/syncthing/.config/syncthing/config.xml
```

Stop the service
```
systemctl stop syncthing@syncthing.service
```

We will now create the storage on our RAID'd USB drives

# Creating storage

Prepare the storage devices
```
fdisk /dev/sda
fdisk /dev/sdb
```

Create the new partition on each device
```
n
p
*ENTER*
*ENTER*
```

Install mdadm
```
apt install mdadm
```

Create the RAID1 array
```
mdadm --create --verbose /dev/md0 --level=mirror --raid-devices=2 /dev/sda1 /dev/sdb1
```

Mount the drive
```
mkdir -p /mnt/usbstorage
mkfs.ext4 /dev/md0
mount /dev/md0 /mnt/usbstorage/
```

Edit fstab
```
vi /etc/fstab
```

Add
```
/dev/md0 /mnt/usbstorage/ ext4 defaults,noatime 0 1
```

Run the following to ensure the RAID array starts up correctly
```
mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf
```

Move the syncthing home directory over to the new mounted USB drive
```
mv /home/syncthing /mnt/usbstorage
```

Create a symlink in the `/home/` directory
```
ln -s /mnt/usbstorage/syncthing syncthing
```

cd into the syncthing directory and make sure ownership is set to the synthing user
```
cd syncthing
chown -R syncthing: *
chown -R syncthing: .config*
```

Start the syncthing service
```
systemctl start syncthing@syncthing.service
```

Done

Now you can access the webgui on
```
https://youripaddress:8384/
```
