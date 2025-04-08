# DHCP Server Configuration
DHCP server configuration with Ubuntu and Windows clients in VMware environment.

## Objective

In this lab, I configured a **DHCP server using ISC DHCP** on a Linux VM to dynamically assign IP addresses to client machines on the network. The setup includes defining a specific IP range, gateway, DNS, and lease time. Configuration was tested using a client VM and verified via command-line tools.

### Skills Learned

- Installing and configuring ISC DHCP Server.
- Defining DHCP pools, options, and exclusions.
- Assigning static IPs based on MAC addresses.
- Troubleshooting IP assignment issues.

### Tools Used

- Ubuntu Server (VM)
- Client PC (Ubuntu or Windows)
- ISC DHCP Server
- VMware Workstation Pro
- Terminal (CLI tools: `nano`, `ip`, `dhclient`, `systemctl`)

![01](https://github.com/user-attachments/assets/147e0502-38cc-4585-a302-b14576e5e36c)

---

## Steps

### 1. Install DHCP Server

Update the system and install the DHCP package:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install isc-dhcp-server
```
![02](https://github.com/user-attachments/assets/6982bed5-dbb1-4061-a840-b939682b125d)

![03](https://github.com/user-attachments/assets/13b007a8-3b4d-47d4-99c0-078310cbd429)

---

### 2. Assign Network Interface

Edit the DHCP server default file to specify the correct interface:

```bash
sudo nano /etc/default/isc-dhcp-server
```

Set:

```bash
INTERFACESv4="ens33"
```

> Replace `ens33` with your actual interface name (use `ip a` to find it).

![06](https://github.com/user-attachments/assets/8c8738fa-71a3-455d-9dfe-32850394b63a)


---

### 3. Configure DHCP Settings

Edit the main DHCP configuration file:

```bash
sudo nano /etc/dhcp/dhcpd.conf
```

Add or modify the following:

#### Global Settings:

```bash
option domain-name "prasad.org";
option domain-name-servers 8.8.8.8, 8.8.84.4;
default-lease-time 600;
max-lease-time 7200;
ddns-update-style none;
authoritative;
```

#### Subnet Declaration:

```bash
subnet 192.168.8.0 netmask 255.255.255.0 {
  range 192.168.8.170 192.168.8.200;
  option routers 192.168.8.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 8.8.4.4;
}
```

![05](https://github.com/user-attachments/assets/95e81f17-c70d-4244-b80f-6ee06b2354cf)

---

### 4. Static IP Assignment (Optional)

You can bind a MAC address to a specific IP:

```bash
host pc1 {
  hardware ethernet 08:00:27:12:34:56;
  fixed-address 192.168.8.110;
}
```

![05 1](https://github.com/user-attachments/assets/90a05aa6-e5f8-4e5c-84a6-1ac45bc8c6ba)


---

### 5. Enable and Start the DHCP Server

Restart the service to apply configuration:

```bash
sudo systemctl restart isc-dhcp-server
sudo systemctl enable isc-dhcp-server
sudo systemctl status isc-dhcp-server
```

![04](https://github.com/user-attachments/assets/1d69662a-2b2f-4fbf-96ba-c6aa547a817a)

---

### 6. Configure Client for DHCP

Make sure the client is set to use DHCP (usually the default).

For Ubuntu/Debian:

```bash
sudo dhclient -v ens33
```

> Again, replace `ens33` with the correct interface name.

---

### 7. Verify IP Assignment

Check IP on the client machine:

For Ubuntu/Debian:

```bash
ip a
```

For Windows:

```bash
ipconfig /all
```

![07](https://github.com/user-attachments/assets/19f14b8e-c48f-40b5-9a9c-5054b6be6a96)

![08](https://github.com/user-attachments/assets/4eb3283c-65db-4843-a957-51e5099652fe)


