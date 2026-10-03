# File Browser Setup

This section documents how I configured File Browser on an Android smartphone using Termux and an Alpine Linux environment running through `proot-distro`.

The goal was to turn the smartphone into a small personal file server that can be accessed remotely from another computer on the same local network.

## Architecture

The setup consists of:

- Android smartphone
- Termux
- `proot-distro`
- Alpine Linux
- OpenSSH
- tmux
- File Browser

The Android storage is accessed from the Alpine environment through:

```text
/storage/emulated/0
```
The File Browser web interface runs on port 8083.

## 1. Accessing Android Storage from Termux

Termux provides access to the Android shared storage through the ~/storage directory.

First, I checked the available storage directories:

```bash
ls ~/storage
```
This returned directories such as:
```text
audiobooks
dcim
documents
downloads
movies
music
pictures
podcasts
shared
```
For example, files stored in the Android Downloads directory can be accessed with:

```bash
ls ~/storage/downloads
```
The Android shared storage was already configured, so running:

```bash
termux-setup-storage
```
was not necessary again.

## 2. Installing proot-distro
I installed proot-distro in Termux:
```bash
pkg install proot-distro
```
Then I installed Alpine Linux:
```bash
proot-distro install alpine
```
To enter the Alpine environment:
```bash
proot-distro login alpine
```
## 3. Verifying Android Storage from Alpine
```bash
ls -la /storage/emulated/0/
```
The command showed the Android shared storage, including directories such as:
```text
Alarms
Android
Audiobooks
DCIM
Documents
Download
Movies
Music
Pictures
Podcasts
Recordings
Ringtones
```
This confirmed that the Android storage was accessible from inside the Alpine environment.

## 4. Installing OpenSSH
I installed OpenSSH inside Alpine:
```bash
apk add openssh
```
I generated the SSH host keys:
```bash
ssh-keygen -A
```
I also configured the SSH server by editing:
```bash
nano /etc/ssh/sshd_config
```
The following settings were changed:
```bash
Port 8023
PermitRootLogin yes
```
The SSH server was then started with:
```bash
/usr/sbin/sshd
```
SSH allows the Alpine environment to be accessed remotely from another computer.

**Security note:** PermitRootLogin yes was enabled for this personal lab environment. This configuration is not recommended for a production server exposed to the Internet. A non-root user and SSH key authentication would be preferable for a real deployment.

## 5. Installing tmux
I installed tmux:
```bash
apk add tmux
```
This allows server processes to continue running after disconnecting from an SSH session.

For example:
```bash
tmux new -s arquivos
```
The session can later be listed with:
```bash
tmux ls
```
## 6. Installing File Browser
I installed File Browser using its installation script:
```bash
curl -fsSL https://raw.githubusercontent.com/filebrowser/get/master/get.sh | bash
```
The installation detected the ARM64 architecture of the smartphone and installed the File Browser binary.

I then initialized its configuration database:
```bash
filebrowser config init
```
The database was created at:
```text
/root/filebrowser.db
```
## 7. Configuring File Browser
I configured File Browser to listen on all network interfaces and use port 8083:
```bash
filebrowser config set --address 0.0.0.0 --port 8083 --root /var
```
Initially, the root directory was set to:
```text
/var
```
A local administrator account was then created:
```bash
filebrowser users add admin <password>
```
The password should not be stored in the Git repository.

## 8. Accessing Android Files
The Android storage is available inside Alpine at:
```text
/storage/emulated/0
```
Therefore, File Browser can use this directory as its root:
```bash
filebrowser config set --root /storage/emulated/0
```
With this configuration, File Browser can expose directories such as:
```text
Alarms
Android
Audiobooks
DCIM
Documents
Download
Movies
Music
Pictures
Podcasts
Recordings
Ringtones
```
## 9. Running File Browser
File Browser can then be started with:
```bash
filebrowser
```
Because the server is configured to listen on:
```text
0.0.0.0:8083
```
it can be accessed by another device on the same local network.
For example:
```text
http://<PHONE-IP>:8083
```
The smartphone's local IP address can be used in place of <PHONE-IP>.

## 10. Current Setup
The final architecture is approximately:
```text
                Local Wi-Fi Network
                         │
              ┌──────────┴──────────┐
              │                     │
           PC/Laptop            Smartphone
                                   │
                                Android
                                   │
                                Termux
                                   │
                             proot-distro
                                   │
                                Alpine
                                   │
                             File Browser
                                   │
                         /storage/emulated/0
                                   │
                  ┌────────────────┼───────────────┐
                  │                │               │
               Download          DCIM           Documents
                  │
                Files
```
The smartphone therefore acts as a small file server, allowing files stored in Android's shared storage to be managed through a web browser.
