MongoDB
=======

Basics
------

```bash
#  installation
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
sudo apt-get update
sudo apt-get install -y mongodb-org

#  start, restart, stop
sudo service mongod start
sudo service mongod restart
sudo service mongod stop

#  start as a daemon and record log output to /var/log/mongodb.log
mongod --fork --logpath /var/log/mongodb.log

#  shutdown
mongod --shutdown
```


Run 
---

MongoDB has provides a shell when you type `mongo`

```bash
$ mongo
>
```

This is actually a javascript interpreter which lets us write js code:

```javascript
> var potion = {
	"name": "Invisibility",
	"vendor": "Kettlecooked"	
  }
> potion
{
  "name": "Invisibility",
  "vendor": "Kettlecooked"	
}
```
