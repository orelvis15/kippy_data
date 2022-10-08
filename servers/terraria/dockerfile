FROM ubuntu:latest

ARG version_url
ARG version_number
ARG ssh_pub_key

#INSTALL
RUN apt-get update -y && apt-get upgrade -y
RUN apt-get install -y curl zip screen iproute2 openssh-server sudo

#SSH
RUN mkdir /var/run/sshd
CMD ["/usr/sbin/sshd","-D"]

RUN mkdir /root/.ssh
RUN echo "$ssh_pub_key" > /root/.ssh/authorized_keys
EXPOSE 22/tcp

#TERRARIA SERVER
EXPOSE 7777/tcp
RUN mkdir -p /opt/volume/data
VOLUME /opt/volume/data

RUN mkdir /opt/volume/log
VOLUME /opt/volume/log

RUN mkdir /opt/volume/config
VOLUME /opt/volume/config

RUN mkdir /root/terraria && cd /root/terraria
RUN mkfifo /opt/volume/log/log.txt

WORKDIR /root/terraria

RUN curl -L $version_url -o terraria_server.zip && unzip terraria_server.zip
RUN mv $version_number/Linux/* $version_number
RUN rm -r $version_number/Windows $version_number/Mac $version_number/Linux

RUN chmod +x $version_number/TerrariaServer.bin.x86*
RUN mv $version_number/* /root

WORKDIR /root

#create script init server
RUN echo "#!/bin/bash\n service start ssh\n ./TerrariaServer.bin.x86_64 -config /opt/volume/config/config.txt > /opt/volume/log/log.txt" > init.sh
RUN chmod +x init.sh

ENTRYPOINT ["screen", "-D", "-m", "-A", "-S", "terraria", "./init.sh"]