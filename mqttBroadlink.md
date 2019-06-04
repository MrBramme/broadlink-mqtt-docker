# MQTT Broadlink

This image isn't perfect as it has it's config & commands baked in. But it works for me ever since my broadlink device has been stable (static ip and no more new codes to be added).

Python 2 container running broadlink-mqtt. Based off the python 2 slim image to reduce size. Image is approx 158MB at the time of writing this document.  
This Dockerfile will:
- pull the latest master of broadlink-mqtt from git
- remove the commands folder
- remove the mqtt.conf file
- copy the local commands folder into the container
- copy the local custom.conf as mqtt.conf into the container

Make sure to:
- update the commands folder
- update the custom.conf file
- run the following to create your image

```docker
FROM python:2-slim

RUN pip install paho-mqtt broadlink

WORKDIR /app
RUN  apt-get update \
  && apt-get install -y wget \
  && rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/eschava/broadlink-mqtt/archive/master.tar.gz \
  && tar -zxvf master.tar.gz \
  && mv broadlink-mqtt-master/* ./ \
  && rm -rf master.tar.gz broadlink-mqtt-master \
  && rm -rf ./commands \
  && rm -r ./mqtt.conf

COPY commands/ /app/commands/
COPY custom.conf /app/mqtt.conf

CMD [ "python", "./mqtt.py" ]
```