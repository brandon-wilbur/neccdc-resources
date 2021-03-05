# Security Monitoring Solution NECCDC 2021

For an open-source SIEM / EDR solution, [Wazuh](https://wazuh.com/) is one of the easiest well-documented solutions that I've come across. It's dead simple to setup with a bash script. These instructions are for an Ubuntu server, but stuff should be similar across Linux distros.

## Wazuh Server Installation

Wazuh includes a Bash script that does everything for us, run the following **as root**:

```
sudo curl -so ~/all-in-one-installation.sh https://raw.githubusercontent.com/wazuh/wazuh-documentation/4.1/resources/open-distro/unattended-installation/all-in-one-installation.sh && sudo bash ~/all-in-one-installation.sh
```

From here, access the web interface at `https://<SERVER-IP>` and punch in `admin` for both the username and password. The web GUI is built on Kibana and the whole system runs on an Elastic stack. This can be easily modified if we want to setup Sysmon logging or other beats to push data into the stack.

## Wazuh Windows Client Installation

[Download the agent installer](https://packages.wazuh.com/4.x/windows/wazuh-agent-4.1.1-1.msi) from the Wazuh website. From an admin PowerShell prompt, run this command:

```
.\wazuh-agent-4.1.1-1.msi /q WAZUH_MANAGER="<SERVER-IP>" WAZUH_REGISTRATION_SERVER="<SERVER-IP>"
```

## Wazuh Debian Linux Client Installation 

Import the GPG key:

```
sudo curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -
```

Add the repository:

```
sudo echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list
```

Update and install the agent:

```
sudo apt update && WAZUH_MANAGER="<SERVER-IP>" sudo apt install -y wazuh-agent
```

Enable and start the service:

```
sudo systemctl enable wazuh-agent && sudo systemctl start wazuh-agent
```

## Wazuh Red Hat Linux Client Installation 

Import the GPG key:

```
sudo rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH
```

Add the repo:

```
cat > /etc/yum.repos.d/wazuh.repo << EOF
[wazuh]
gpgcheck=1
gpgkey=https://packages.wazuh.com/key/GPG-KEY-WAZUH
enabled=1
name=EL-$releasever - Wazuh
baseurl=https://packages.wazuh.com/4.x/yum/
protect=1
EOF
```

Update and install the agent:

```
WAZUH_MANAGER="<SERVER-IP>" && sudo yum install -y wazuh-agent
```

Enable and start the service:

```
sudo systemctl enable wazuh-agent && sudo systemctl start wazuh-agent
```

## Using Wazuh

From here, use the web GUI to interact with data coming back from endpoint agents.