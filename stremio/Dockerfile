##########################################################################

# Builder image
FROM node:alpine AS builder

# Build args
ARG SERVER_VERSION=master
ARG DOWNLOAD_URL=https://dl.strem.io/four/${SERVER_VERSION}

WORKDIR /srv/stremio
RUN apk add --no-cache git wget dos2unix
RUN git clone https://github.com/Stremio/stremio-web.git

WORKDIR /srv/stremio/dist
COPY files/*.sh ./
RUN chmod +x *.sh && dos2unix *.sh

WORKDIR /srv/stremio/dist/shell
RUN wget -mkEpnp -nH https://staging.strem.io || true

WORKDIR /srv/stremio/stremio-web
RUN npm ci && npm run build && cp -r build ../dist/web

WORKDIR /srv/stremio/dist/server
RUN wget $DOWNLOAD_URL/server.js $DOWNLOAD_URL/stremio.asar && cp server.js server.js.bak

##########################################################################

# Main image
FROM node:alpine

WORKDIR /srv/stremio
RUN apk add --no-cache ffmpeg
COPY --from=builder /srv/stremio/dist ./
RUN npm install -g http-server

# Expose default ports
EXPOSE 11470 8080 8081

CMD ["./run.sh"]