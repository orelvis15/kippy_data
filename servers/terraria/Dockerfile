FROM ubuntu:latest

ARG version_url
ARG version_number
ARG game_config


#INSTALL
RUN apt-get update -y && apt-get upgrade -y
RUN apt-get install -y curl zip screen iproute2 openssh-server


#SSH
RUN mkdir /var/run/sshd
RUN echo 'root:pass' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd


#TERRARIA SERVER
RUN mkdir -p /opt/volume/data
RUN mkdir /opt/volume/log
RUN mkdir /opt/volume/config
RUN mkdir /root/terraria && cd /root/terraria
RUN mkfifo /opt/volume/log/serverout.txt


#CREATE GAME CONFIG FILE
RUN printf '%s\n' $game_config > /opt/volume/config/config.txt
RUN cat /opt/volume/config/config.txt


WORKDIR /root/terraria

RUN curl $version_url -o terraria_server.zip && unzip terraria_server.zip
RUN mv $version_number/Linux/* $version_number
RUN rm -r $version_number/Windows $version_number/Mac $version_number/Linux

RUN chmod +x $version_number/TerrariaServer.bin.x86*
RUN mv $version_number/* /root

WORKDIR /root


#create script init server
RUN echo "#!/bin/bash\n ./TerrariaServer.bin.x86_64 -config /opt/volume/config/config.txt > /opt/volume/log/serverout.txt" > init.sh
RUN chmod +x init.sh

ENTRYPOINT ["/usr/bin/screen", "-D", "-m", "-A", "-S", "terraria", "./init.sh"]

EXPOSE 7777/tcp
EXPOSE 22/tcp

CMD ["/usr/sbin/sshd", "-D"]