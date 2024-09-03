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
TODO
