# Raspberry pi iot edge on raspian
1. Start Raspberry Pi Imager
    - select Raspberry Pi Os Lite 32-bit
    - configure hostname
    - enable ssh
    - set password
    - configure wireless lan
    - set timezone
Write to SD and put in pi wen finished. I used rpi4iotedge.local as a hostname


2. ssh pi@rpi4iotedge.local
    -sudo raspi-config
    - advance options -> expand file system
    - interface options -> enable SPI and GPIO
    - update

3. portal.azure.com
    - Create IOT Hub (rpi4iothub)
    - Create Iot Edge Device (rpi4device)

4. Install IOT Edge https://docs.microsoft.com/en-us/azure/iot-edge/how-to-provision-single-device-linux-symmetric?view=iotedge-2020-11&tabs=visual-studio-code%2Crpios on Raspberry Pi OS


curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

5. Install Container engine from same do on Raspberry Pi OS

sudo apt-get update; \
  sudo apt-get install moby-engine

6. Install IoT Edge runtime from doc above

sudo apt-get update; \
  sudo apt-get install aziot-edge

7. Created default modules on iot edge device
    - $edgeAgent
    - $edgeHub

7. provision device with cloud identity in doc above using "Primary Connection String" from IoT Edge device

sudo iotedge config mp --connection-string 'PASTE_DEVICE_CONNECTION_STRING_HERE'

sudo iotedge config apply -c '/etc/aziot/config.toml'

    - Test your connectivity between cloud and rpi with
        sudo iotedge check


1. arm32v7 Remote development requires docker group to run on pi without sudo ssh to pi and:
groupadd docker
usermod -aG docker $USER
newgrp docker 
 -test setting like this
docker run hello-world 

2. in vs code add these lines only ip of rpi works
    "azure-iot-edge.executor.env": {
        "DOCKER_HOST": "ssh://pi@192.168.2.73"
    },


3. during sample build getting internet connectivity errors and not able to restore packages on rpi:
 - sudo iotedge check was also throwing container connectivity errors doc: https://docs.microsoft.com/en-us/azure/iot-edge/troubleshoot-common-errors?view=iotedge-2020-11
sudo nano /etc/sysctl.conf
    - uncomment line below
net.ipv4.ip_forward=1
sudo systemctl restart docker