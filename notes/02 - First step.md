# First Step

This entry logs the steps I took to turn my Android device into a local server environment, test a basic HTTP server, and configure SSH for remote management from my PC.

---

## Step 1: Package Updates & Preparation
First, I updated the package repositories and upgraded all existing packages to ensure system stability and security:

```bash
pkg update && pkg upgrade -y
```
## Step 2: Setting Up a Basic Web Server
To verify that my phone could serve web pages across my local network, I installed Python and the nano editor:

```bash
pkg install python nano -y

```
I created a simple index.html file using nano:

```code
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Smartphone Server Lab</title>
</head>
<body>
    <h1>TESTE</h1>
    <p>Esta página está sendo servida pelo meu smartphone usando Termux.</p>
</body>
</html>

```

I then started Python's built-in HTTP server:

```bash
python -m http.server 8000

```
After checking my phone's local IP address using ifconfig (or ip a), I opened http://<SMARTPHONE_IP>:8000 in my PC browser. The page loaded successfully over my local Wi-Fi.

OBS:
During the laboratory setup in Termux, tests were conducted to identify the smartphone's network information. Initially, the `ip addr` command was attempted; this command typically allows for viewing network interfaces and their respective IP addresses. However, in the environment used, the command returned the error: "Cannot open netlink socket: Permission denied." A similar limitation was encountered when using the `ss -tuln` command, which is used to query connections and listening ports.

The `hostname -i` command was also tested, returning `127.0.0.1`—the address associated with localhost. The `hostname -I` option, which could be used to retrieve the IP addresses of network interfaces, was unavailable in the implementation present in the environment.

Consequently, it was not possible to verify the smartphone's IP address using these commands within Termux. The local IP address was subsequently identified via Android's own network settings.

This situation highlighted an important characteristic of Termux: although it provides a Unix/Linux-like environment, it remains subject to Android's security model and limitations. Therefore, certain low-level commands and operations may not have the same level of access they would in a conventional Linux distribution.****


## Step 3: Inspecting System Specifications
I wanted to check the device hardware details and environment specs, so I installed and ran neofetch:


```bash
pkg install neofetch -y
neofetch
```

This gave me a detailed summary of the kernel, CPU architecture (AArch64), memory usage, and Android OS environment.

## Step 4: Configuring OpenSSH for Remote Access
To avoid typing commands on a touch screen, I set up SSH to manage the device directly from my PC terminal:

OpenSSH:

```bash
pkg install openssh -y
```
Set a password for my user account:
```bash
passwd
```
Verified my user identity:
```bash
whoami
```
Launched the SSH daemon:
```bash
sshd
```
## Step 5: Establishing the Connection from PC
Finally, I opened the terminal on my PC and initiated the connection using the -p 8022 flag:

```bash
ssh -p 8022 <USERNAME>@<SMARTPHONE_IP>
```
### Why Port 8022 Instead of Port 22?
Standard SSH runs on port 22. However, in Linux systems, ports 1 through 1023 are privileged ports that require root access to bind. Because Termux runs as a non-root application in Android user space, it uses port 8022 (an unprivileged high port) by default.

After authenticating with my password, I gained full terminal access to the smartphone server from my desktop workspace.






