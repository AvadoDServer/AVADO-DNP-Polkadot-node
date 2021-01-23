FROM node as builder

WORKDIR /cert
RUN wget http://iso.ava.do/server.crt
RUN wget http://iso.ava.do/server.key

# build final image
FROM parity/polkadot:v0.8.27

USER root

WORKDIR  /usr/share/nginx
COPY files/nginx.conf /etc/nginx

RUN gpg --recv-keys --keyserver hkps://keys.mailvelope.com 9D4B2B6EB8F97156D19669A9FF0812D491B96798
RUN gpg --export 9D4B2B6EB8F97156D19669A9FF0812D491B96798 > /usr/share/keyrings/parity.gpg

# Add the Parity repository and update the package index
RUN echo 'deb [signed-by=/usr/share/keyrings/parity.gpg] https://releases.parity.io/deb release main' > /etc/apt/sources.list.d/parity.list
RUN apt update

RUN apt-get update
RUN apt-get install -y supervisor ntp nginx

COPY files/nginx.conf /etc/nginx/
RUN mkdir -p /etc/nginx/certs/

COPY --from=builder /cert/server.crt /etc/nginx/certs/
COPY --from=builder /cert/server.key /etc/nginx/certs/

# Config files
COPY files/supervisord.conf /etc/supervisord/

ENTRYPOINT supervisord -n -c /etc/supervisord/supervisord.conf 
