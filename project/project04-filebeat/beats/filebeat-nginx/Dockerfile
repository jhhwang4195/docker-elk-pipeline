# Dockerfile to illustrate how Filebeat can be used with nginx
# Filebeat OSS 7.8.0

# Build with:
# docker build -t filebeat-nginx-example .

# Run with:
# docker run -p 80:80 -it --link <elk-container-name>:elk \
#     --name filebeat-nginx-example filebeat-nginx-example

FROM nginx

###############################################################################
#                                INSTALLATION
###############################################################################

### install Filebeat
ARG ELK_VERSION
# ENV FILEBEAT_VERSION oss-7.8.0


RUN apt-get update -qq \
 && apt-get install -qqy curl \
 && apt-get clean

RUN curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-${ELK_VERSION}-amd64.deb \
 && dpkg -i filebeat-${ELK_VERSION}-amd64.deb \
 && rm filebeat-${ELK_VERSION}-amd64.deb


###############################################################################
#                                CONFIGURATION
###############################################################################

### tweak nginx image set-up

# remove log symlinks
RUN rm /var/log/nginx/access.log /var/log/nginx/error.log


### configure Filebeat

# config file
ADD filebeat.yml /etc/filebeat/filebeat.yml
RUN chmod 644 /etc/filebeat/filebeat.yml

# CA cert
# RUN mkdir -p /etc/pki/tls/certs
# ADD logstash-beats.crt /etc/pki/tls/certs/logstash-beats.crt

# create template based on filebeat version (assumption: it is the same version as elasticsearch version)
RUN filebeat export template --es.version ${ELK_VERSION} > /etc/filebeat/filebeat.template.json

###############################################################################
#                                    DATA
###############################################################################

### add dummy HTML file

COPY html /usr/share/nginx/html


###############################################################################
#                                    START
###############################################################################

ADD ./start.sh /usr/local/bin/start.sh
RUN chmod +x /usr/local/bin/start.sh
CMD [ "/usr/local/bin/start.sh" ]
