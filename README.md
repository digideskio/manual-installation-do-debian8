## Hakushu. Manual installation: Digital Ocean + Debian 8.1

create droplet and get some IP: `188.166.59.209`

on local machine if have custom ssh key

`~/.ssh/config`

```
Host do 188.166.59.209
  HostName 188.166.59.209
  IdentityFile ~/.ssh/custom_rsa
  ForwardAgent yes
```

go to new server

```
ssh root@188.166.59.209
> InitialPassword
>> NewPassword123
>> NewPassword123

cat ~/.ssh/id_rsa.pub | ssh root@188.166.59.209 'cat >> ~/.ssh/authorized_keys'
> NewPassword123

ssh root@188.166.59.209
```

```sh
apt-get install tmux -y
tmux new -s rails_server_install

wget https://raw.githubusercontent.com/hakushu-publishing/manual-installation-do-debian8/master/install_script.sh
time ( source ./install_script.sh )
```

```sh
tmux ls
tmux a -t rails_server_install

wget https://raw.githubusercontent.com/hakushu-publishing/manual-installation-do-debian8/master/check_soft.sh
time ( source ./check_soft.sh )
```

### After Install (as root)

configure and start your NewRelic monitor

```
nrsysmond-config --set license_key=YOUR_LICENSE_KEY
/etc/init.d/newrelic-sysmond start
```

Change `root` and `rails` users passwords
```
passwd root  # NewRootPassword
passwd rails # NewRailsPassword
```

Change `rails` user password for PSQL

```
su -s /bin/bash -l postgres -c "psql -U postgres -c \"ALTER USER rails WITH PASSWORD 'NewRailsUserPsqlPassword';\""
```

or

```
su - postgres
psql
ALTER USER rails WITH PASSWORD 'NewRailsUserPsqlPassword';
\q
exit
```

Change `root` and `rails` user password for MySQL

```
mysql -u root -pqwerty12345 -D mysql -r -B -N -e "SET PASSWORD FOR 'rails'@'localhost' = PASSWORD('NewRailsUserMysqlPassword')"
mysql -u root -pqwerty12345 -D mysql -r -B -N -e "SET PASSWORD FOR 'root'@'localhost' = PASSWORD('NewRootUserMysqlPassword')"
```

Your DB's connection data

PSQL:

```
user: rails
db: rails_app_db
pass: NewRailsUserPsqlPassword
```

MySQL:

```
user: rails
db: rails_app_db
pass: NewRailsUserMysqlPassword
```
