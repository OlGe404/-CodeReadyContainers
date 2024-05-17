# CodeReadyContainers
This repo can be used to setup a local code ready containers (CRC) installation (openshift 4 equivalent) on Ubuntu/Debian (only tested on Ubuntu 22.04).

## Requirements
To install CRC with this repo, you need:
* [A free redhat account](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwjGw8bPx9L3AhUNCewKHT11D7EQFnoECAYQAQ&url=https%3A%2F%2Fwww.redhat.com%2Fwapps%2Fugc%2Fregister.html&usg=AOvVaw0XN5agOwobjJWWJmiitUP7)
* [Download the mandatory pullsecrets file](https://console.redhat.com/openshift/install/pull-secret) and save it as "pull-secret.json"
* 4 cpu cores (default 8)
* 10GB of memory (default 14)
* 35GB of free storage space
* One of the following operating systems:
    - Ubuntu 18.04 LTS or later
    - Debian 10 or later

The cluster monitoring is disabled by default, because it increases cpu and memory consumption by ~50%.
To enable it, set <code>crc_cluster_monitoring: true</code> in [vars.yaml](vars.yaml) or provide it when calling the playbook with
<code>-e crc_cluster_monitoring=true</code>.

CPU/memory settings can be overwritten by providing <code>-e crc_cluster_cpus=6</code> and <code>-e crc_cluster_memory=10900</code>.

## Install
When you've created a redhat account and downloaded the pullsecret file, run:
  * <code>python3 -m pip install --user -r requirements.txt</code>
  * <code>ansible-playbook install.yaml</code>

**NOTE:** If passwordless sudo is disabled, force ansible to ask for your sudo password by appending <code>-K</code> or <code>--ask-become-pass</code> to
the <code>ansible-playbook install.yaml</code> command.

### Known Issues
If the installation fails, it can get stuck and prevent new installations to run succesfully. If you have trouble running the playbook consecutively after an installation failed, or if the crc VM doesn't come up, see https://github.com/code-ready/crc/issues/1027 for more information on how to resolve this.

```bash
# TL;DR
crc delete --force
sudo virsh list --all
sudo virsh destroy crc
sudo virsh undefine crc
```

## Deinstall
To deinstall CRC, run <code>ansible-playbook deinstall.yaml</code>.
If passwordless sudo is disabled, run <code>ansible-playbook deinstall.yaml -K</code> instead.

## Update
If you want to update an existing CRC installation to a new release, set the new version in
the [vars.yaml file](vars.yaml) and run <code>ansible-playbook update.yaml</code>. You can find the available
versions at https://developers.redhat.com/content-gateway/rest/mirror/pub/openshift-v4/clients/crc/.

**WARNING:** By the time of writing, there is no way to update an existing CRC installation in place, so we have to deinstall/install the current CRC installation. This will delete the CRC instance including *all data and deployments in it*.

## Work with CRC
The CRC installation will be up and running after the installation, but is not added to your systems autostart. You can use the following commands to start/stop/interact with it.

| Command     | Description                                        |
| ----------- | -------------------------------------------------- |
| crc status  | Display the status of the current CRC installation |
| crc start   | Start a stopped CRC installation                   |
| crc stop    | Stop a started CRC installation                    |
| crc console | Open the OpenShift webconsole in your browser      |
