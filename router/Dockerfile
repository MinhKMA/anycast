FROM ubuntu:16.04

MAINTAINER minhkma

RUN echo "net.ipv4.conf.all.forwarding=1" | tee -a /etc/sysctl.conf
RUN apt-get update 
RUN apt-get install -y vim 
RUN apt-get install -y net-tools
RUN apt-get install -y inetutils-ping
RUN apt-get install -y quagga
RUN apt-get install -y inetutils-traceroute
RUN apt-get install -y telnet
RUN sed -i 's/zebra=no/zebra=yes/g' /etc/quagga/daemons
RUN sed -i 's/ospfd=no/ospfd=yes/g' /etc/quagga/daemons
RUN chown quagga:quagga /etc/quagga
RUN cp /usr/share/doc/quagga/examples/ospfd.conf.sample /etc/quagga/ospfd.conf
RUN cp /usr/share/doc/quagga/examples/zebra.conf.sample /etc/quagga/zebra.conf

CMD ["echo", "MinhKMA"]
