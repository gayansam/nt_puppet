# PUPPET

## Enable puppet repository

``` bash
wget https://apt.puppet.com/<PLATFORM_VERSION>-release-<VERSION CODE NAME>.deb
sudo dpkg -i <package>.deb
sudo apt update
```
## Install agent

```
sudo apt install puppet-agent
```

## Configure agent

### Add puppet master to /etc/hosts

```
grep puppet /etc/hosts
10.8.0.10 puppet
```

### Add puppet binary path to PATH
```
grep puppet ~/.profile
if [ -d "/opt/puppetlabs/bin" ] ; then
    PATH=/opt/puppetlabs/bin:$PATH
fi
```

### Add puppet master to configuration

```
sudo /opt/puppetlabs/puppet/bin/puppet config set server puppet --section main

cat /etc/puppetlabs/puppet/puppet.conf
[main]
server = puppet
# This file can be used to override the default puppet settings.
# See the following links for more details on what settings are available:
# - https://puppet.com/docs/puppet/latest/config_important_settings.html
# - https://puppet.com/docs/puppet/latest/config_about_settings.html
# - https://puppet.com/docs/puppet/latest/config_file_main.html
# - https://puppet.com/docs/puppet/latest/configuration.html

```

### Contact server and get the certificate signed

In the client run
```
sudo /opt/puppetlabs/bin/puppet ssl bootstrap
Info: Creating a new RSA SSL key for sydney
Info: csr_attributes file loading from /etc/puppetlabs/puppet/csr_attributes.yaml
Info: Creating a new SSL certificate request for sydney
Info: Certificate Request fingerprint (SHA256): 9D:84:94:DD:B1:12:58:BC:EE:F8:C8:70:41:3D:F1:F7:77:C7:CE:CE:78:2B:93:FD:3C:09:80:39:81:8D:F9:D6
Info: Certificate for sydney has not been signed yet
Couldn't fetch certificate from CA server; you might still need to sign this agent's certificate (sydney).
Info: Will try again in 120 seconds.
```

In the server
```
sudo puppetserver ca list --certname sydney
Requested Certificates:
    sydney       (SHA255)  9D:84:94:DD:B1:12:58:BC:EE:F8:C8:70:41:3D:F1:F7:77:C7:CE:CE:78:2B:93:FD:3C:09:80:39:81:8D:F9:D6

sudo puppetserver ca sign --certname sydney
Successfully signed certificate request for sydney

sudo puppetserver ca list --certname sydney
Signed Certificates:
    sydney       (SHA256)  0E:9E:C5:53:9F:DF:95:95:9C:30:48:2C:00:0E:92:F1:90:E6:A7:3D:6B:29:EB:02:89:77:71:E7:53:06:EB:DF     alt names: ["DNS:sydney"]
```

You will see these messages in the client once the certificate is successfully signed.

```
Info: csr_attributes file loading from /etc/puppetlabs/puppet/csr_attributes.yaml
Info: Creating a new SSL certificate request for sydney
Info: Certificate Request fingerprint (SHA256): 9D:84:94:DD:B1:12:58:BC:EE:F8:C8:70:41:3D:F1:F7:77:C7:CE:CE:78:2B:93:FD:3C:09:80:39:81:8D:F9:D6
Info: Downloaded certificate for sydney from https://puppet:8140/puppet-ca/v1
Notice: Completed SSL initialization
```


