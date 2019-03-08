# mosquitto on orange pi zero

## sources

```txt
https://www.mickmake.com/post/mqtt-mosquitto-pi-zero-w-5-minutes-tutorial
https://www.youtube.com/watch?v=gU5Vp0zCzak
```

## install software

```bash
sudo apt-get install mosquitto mosquitto-clients
```

- install pip

```bash
sudo apt-get install python-pip
pip install --upgrade setuptools
pip install paho-mqtt==1.3.0
pip list

sudo apt-get install python3-pip
pip3 install --upgrade setuptools
pip3 install paho-mqtt==1.3.0
pip3 list
```

- install python-mosquitto client via pip

```bash
sudo pip install paho-mqtt==1.3.0
```

## mosquitto start/start/enable/reenable

```bash
# start on boot
sudo systemctl enable mosquitto
# start
sudo systemctl start mosquitto
# stop
sudo systemctl stop mosquitto
# disable
sudo systemctl disable mosquitto

```

## simple test on localhost

```bash
# first shell
# subscribe (wait for messages) on topic test for messages
mosquitto_sub -h localhost -t /test

# second shell
# publish (sent/write to channel) on topic test some messages
 mosquitto_pub -h localhost -t /test -m "test123"

# we should see the send messages in the first shell immediate

```

## create mosquitto password

```bash
MOSQUITTO_PASSWORD_FILE="/etc/mosquitto/password"
# first time with flag c
# -c : create a new password file. This will overwrite existing files.
mosquitto_passwd -c $MOSQUITTO_PASSWORD_FILE admin

# follow password without flag c, else we overwrite the password file
mosquitto_passwd $MOSQUITTO_PASSWORD_FILE user1

# create password inside a script
mosquitto_passwd -b $MOSQUITTO_PASSWORD_FILE user_script user_password

```

- enable password usage in the /etc/mosquitto/mosquitto.conf

```bash
# append follow line to config
cat << EOF |sudo tee -a /etc/mosquitto/mosquitto.conf
allow_anonymous false
password_file /etc/mosquitto/password
EOF

# restart mosquitto
sudo systemctl stop mosquitto
sudo systemctl start mosquitto
```

- used mosquitto_sub/pub with password

```bash
## simple test on localhost

```bash
# first shell
# subscribe (wait for messages) on topic test for messages
mosquitto_sub -u admin -P test123 -h localhost -t /test


# second shell
# publish (sent/write to channel) on topic test some messages
mosquitto_pub -u admin -P test123 -h localhost -t /test -m "hallo123"

# we should see the send messages in the first shell immediate
```

## simple python script

- source

```txt
http://www.steves-internet-guide.com/into-mqtt-python-client/
```

```python
PYTHON_MOSQUITTO_PUB_SUP_PYTHON="test-mosquitto-pub-sub.py"
cat << EOF|tee $PYTHON_MOSQUITTO_PUB_SUP_PYTHON
import paho.mqtt.client as mqtt #import the client1
broker_address="127.0.0.1" # localhost
#broker_address="iot.eclipse.org"
print("creating new instance")
client = mqtt.Client("P1") #create new instance
print "set user/password"
client.username_pw_set("token:YOUR_CHANNEL_TOKEN")
print("connecting to broker")
client.connect(broker_address) #connect to broker
print("Subscribing to topic","/test")
client.subscribe("/test")
print("Publishing message to topic","/test")
client.publish("/test","OFF")
EOF
python $PYTHON_MOSQUITTO_PUB_SUP_PYTHON
```