FROM alpine:3.16.2 as BUILDER
#INSTALL BUILD DEPS
RUN apk add --update --no-cache git build-base tar
WORKDIR /tmp
RUN git clone https://github.com/rofl0r/microsocks.git
WORKDIR /tmp/microsocks
RUN make
RUN ls -al
RUN chmod +x microsocks && \
    mkdir -p /tmp/microsocks-bin && \
    cp -v microsocks /tmp/microsocks-bin
#======
# RUNTIME CONTAINER
#======
FROM alpine:3.16.2
# COPY FROM BUILDER
COPY --from=BUILDER /tmp/microsocks-bin/microsocks /usr/local/bin/microsocks
# INSTALL DEPENDENCIES
RUN apk add --no-cache coreutils shadow tzdata curl
# CREATE USER & LOG DIR
RUN useradd -u 1000 -U -M -s /bin/false microsocks && \
    usermod -G users microsocks && \
    mkdir -p /var/log/microsocks && \
    chown -R nobody:nogroup /var/log/microsocks 
# HEALTHCHECK
HEALTHCHECK --interval=5m --timeout=10s \
  CMD curl -f -L -x socks5h://localhost:1080 'https://api.ipify.org' || exit 1
# EXPOSE SERVICE PORT
EXPOSE 1080
# CHANGE USER
USER microsocks
# DEFINE ENTRYPOINT
ENTRYPOINT [ "/usr/local/bin/microsocks" ]