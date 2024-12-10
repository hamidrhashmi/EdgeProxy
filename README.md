# EdgeProxy
Works on opensips-3.4 (LTS). Load balance REGISTER and INVITE requests

## Configuration
### Step 1 Install MySQL Server
Install mysql using this [blog](https://hrhashmi.blogspot.com/2023/07/mysql-8-installation-steps.html)

### Step 2 Install Opensips
```bash
curl https://apt.opensips.org/opensips-org.gpg -o /usr/share/keyrings/opensips-org.gpg
echo "deb [signed-by=/usr/share/keyrings/opensips-org.gpg] https://apt.opensips.org bookworm 3.4-releases" >/etc/apt/sources.list.d/opensips.list
echo "deb [signed-by=/usr/share/keyrings/opensips-org.gpg] https://apt.opensips.org bookworm cli-nightly" >/etc/apt/sources.list.d/opensips-cli.list
apt install opensips
aptitude install opensips-cli
apt install opensips-mysql-module opensips-tls-module opensips-tls-openssl-module opensips-tlsmgm-module
opensips-cli -x database create
```
### STEP 3 Insert Nodes in Db to LoadBalance SIP Traffic
Use the following frmat to add data in data 
```sql
insert into dispatcher (setid, destination, socket) values (2,'sip:node-ip:5060;transport=tcp','tcp:opensips-listening-socket:6060');
```

### STEP 4 Download and Configure EdgeProxy
Download the Configuration file
```bash
wget https://raw.githubusercontent.com/hamidrhashmi/EdgeProxy/refs/heads/opensips-3.4/opensips.cfg
```
Please change following IPs before deploying
> Publicly Accessible IP => 8.8.8.8          // EIP for SIP
>
> localIP => 192.168.26.180
>
> advertised_port => 5060
>
> Regex for local Subnet => '^192.168.26.[0-2][0-9][0-9]'

Vim Editor:
```
:%s/8.8.8.8/NEW_PUBLIC_IP/g
:%s/advertised_port=5060/advertised_port=NEW_PUBLIC_SIP_PORT/g
:%s/192.168.26.180:5060/NEW_PRIVATE_IP4:NEW_PRIVATE_PORT0/g
:%s/192.168.26.180/NEW_PRIVATE_IP4/g
:%s/2a05:X:X:X:X:X:X:X/NEW_PRIVATE_IP6/g
:%s/sip.domain.com/YOUR_DOMAIN/g
```
Change Regex Manualy
```
:%s/mysql:\/\/opensips:opensips@localhost\/opensips/mysql:\/\/DB_USER:DB_PASSWORD@NEW_DB_IP\/DB_NAME/g
:%s/LOG_LOCAL0/LOG_LOCAL6/g
```

### STEP 5 Replace opensips.cfg
copy the new `opensips.cfg` file with the existing `opensips.cfg`
```
cp /etc/opensips/opensips.cfg /etc/opensips/opensips.cfg-bkp
cp opensips.cfg /etc/opensips/opensips.cfg
```
check configuration file
```
opensips -c
```
if it all okay (no error), then restart 
```
systemctl restart opensips
```

Enjoy :wink:
