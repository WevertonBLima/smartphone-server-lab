# Smartphone Server Lab

A hands-on laboratory for learning how to turn an Android smartphone into a local server using Termux.

The main goal of this project is not simply to make a server work, but to understand the technologies involved in the process and for personal purposes.

This repository documents the entire learning process, including configurations, experiments, problems, troubleshooting, and technical concepts.

---

## Objectives

This laboratory is designed to study:

- Linux and Unix-like environments
- Android and Termux
- File systems
- Processes
- Package management
- Networking
- IPv4
- TCP/IP
- Ports and sockets
- HTTP
- Client-server architecture
- SSH
- Server administration
- Security
- Automation
- Monitoring

The project will evolve gradually as new concepts are studied and implemented.

---

## Laboratory Architecture

The current architecture is:

```text
                Local Wi-Fi Network
                       │
          ┌────────────┴────────────┐
          │                         │
          ▼                         ▼
     Client Computer           Smartphone
                                    │
                                  Android
                                    │
                                  Termux
                                    │
                                  Python
                                    │
                              HTTP Server
                                    │
                                 Port 8080
                                    │
                                index.html
