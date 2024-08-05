# nixos-in-10-minutes

A simple repo which makes it easy to bring up a NixOS instance in 10 minutes. This is based on
nixos-anywhere.

Prerequisites:
- `docker` and `docker-compose` running on the local machine
- a remote linux VM in which the root account can be accessed via ssh

How this works:
- bring up the docker container which provides a flake enabled nix environment
- create a new ssh key inside the container and put the pubkey on the remote VM in the root account
- check the disk configuration
- run nixos-anywhere

See this post for more details.

# Manual
adopted from [here](https://seanrmurphy.medium.com/bringing-up-a-nixos-vm-in-10-minutes-using-nixos-anywhere-6590b49ad146)
```bash
git clone git@github.com:TrueBad0ur/nixos-in-10-minutes.git
cd docker
docker compose run --rm nixos-anywhere

bash-5.2# mkdir -p /root/.config/nix
bash-5.2# echo experimental-features = nix-command flakes > /root/.config/nix/nix.conf
bash-5.2# # the following is required on an aarch64 machine building x86_64 binaries
bash-5.2# echo filter-syscalls = false >> /root/.config/nix/nix.conf
bash-5.2# nix shell nixpkgs#nano
bash-5.2# add id_rsa to ~/.ssh/id_rsa

remote: add id_rsa.pub to root authorized_keys
remote: allow access via root
remote: check lsblk disk type(vda/sda) on remote ->
  local: change in ./nixos-anywhere/disk-config.nix
  local: add public ssh key in ./nixos-anywhere/configuration.nix

bash-5.2# cd nixos-anywhere
bash-5.2# nix run github:nix-community/nixos-anywhere -- --flake .#nixos-anywhere-vm root@<vm-ip-address> 
```
