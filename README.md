# syncthing

You can choose between the “stable” (latest release) or “candidate” (earlier release candidate) tracks. The stable channel is updated usually every first Tuesday of the month.

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
