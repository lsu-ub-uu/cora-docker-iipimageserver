FROM alpine:edge

EXPOSE 80 9000

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories && \
	apk update && \
	apk upgrade && \
	apk add --no-cache iipsrv nginx
	
ADD default.conf /etc/nginx/http.d/default.conf
ADD nginx.conf /etc/nginx/nginx.conf
ADD start-servers /usr/local/bin/start-servers

RUN chmod u+x /usr/local/bin/start-servers

ENV HOST="0.0.0.0" PORT="9000" LOGFILE="/dev/stdout" URI_MAP="iiif=>IIIF" VERBOSITY=6
ENTRYPOINT start-servers