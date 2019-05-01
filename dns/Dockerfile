FROM ubuntu:16.04

MAINTAINER minhkma

RUN apt-get update 
RUN apt-get install -y bind9 bind9utils dnsutils openssh-server vim net-tools inetutils-ping telnet
RUN apt-get install -y quagga
RUN sed -i 's/zebra=no/zebra=yes/g' /etc/quagga/daemons
RUN sed -i 's/ospf=no/ospf=yes/g' /etc/quagga/daemons
RUN chown quagga:quagga /etc/quagga
RUN cp /usr/share/doc/quagga/examples/ospfd.conf.sample /etc/quagga/ospfd.conf
RUN cp /usr/share/doc/quagga/examples/zebra.conf.sample /etc/quagga/zebra.conf



CMD ["echo", "MinhKMA"]




