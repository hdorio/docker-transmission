FROM alpine:3.13.5 as build

ENV TRANSMISSION_VERSION 3.00

RUN apk update         \
    && apk add         \
          ca-certificates  \
          cmake            \
          curl             \
          curl-dev         \
          g++              \
          jq               \
          libevent-dev     \
          make             \
    && true

RUN set -x \
    && curl -SL "https://github.com/hdorio/docker-transmission/blob/master/gosu?raw=true" > /bin/gosu \
    \
    && mkdir /release \
    && curl -SL "https://github.com/transmission/transmission/releases/download/3.00/transmission-$TRANSMISSION_VERSION.tar.xz" | tar -xJC /release \
    \
    && cd /release/transmission-$TRANSMISSION_VERSION \
    && mkdir build                                    \
    && cd build                                       \
    && cmake -D ENABLE_TESTS=OFF ..                   \
    && make                                           \
    && make install                                   \
    && true

FROM alpine:3.13.5

COPY --from=build /bin/gosu /bin/gosu

COPY --from=build /usr/local/share/transmission/ /usr/local/share/transmission/
COPY --from=build /usr/local/share/doc/transmission/ /usr/local/share/doc/transmission/
COPY --from=build /usr/local/share/man/man1/transmission-show.1 /usr/local/share/man/man1/transmission-show.1
COPY --from=build /usr/local/share/man/man1/transmission-daemon.1 /usr/local/share/man/man1/transmission-daemon.1
COPY --from=build /usr/local/share/man/man1/transmission-create.1 /usr/local/share/man/man1/transmission-create.1
COPY --from=build /usr/local/share/man/man1/transmission-edit.1 /usr/local/share/man/man1/transmission-edit.1
COPY --from=build /usr/local/share/man/man1/transmission-remote.1 /usr/local/share/man/man1/transmission-remote.1
COPY --from=build /usr/local/bin/transmission-daemon /usr/local/bin/transmission-daemon
COPY --from=build /usr/local/bin/transmission-create /usr/local/bin/transmission-create
COPY --from=build /usr/local/bin/transmission-edit /usr/local/bin/transmission-edit
COPY --from=build /usr/local/bin/transmission-remote /usr/local/bin/transmission-remote
COPY --from=build /usr/local/bin/transmission-show /usr/local/bin/transmission-show

RUN set -x \
    && apk add     \
          libcurl  \
          libevent \
          shadow   \
    && groupadd -r transmission && useradd --no-log-init -r -g transmission transmission \
    && true

COPY default-settings.json /default-settings.json
COPY entrypoint.sh /entrypoint.sh

VOLUME /home/transmission/.config/transmission-daemon
VOLUME /home/transmission/Downloads

EXPOSE 9091
EXPOSE 51413
EXPOSE 51413/udp

ENTRYPOINT ["/entrypoint.sh"]
CMD ["transmission-daemon", "-f"]
