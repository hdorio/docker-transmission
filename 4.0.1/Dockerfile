FROM alpine:3.16.8 as build

ENV TRANSMISSION_VERSION 4.0.1

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
          gettext-dev      \
          linux-headers    \
          libintl          \
    && true

RUN set -eux \
    && curl -SL "https://github.com/hdorio/docker-transmission/blob/master/gosu?raw=true" > /bin/gosu \
    \
    && mkdir /release \
    && curl -SL "https://github.com/transmission/transmission/releases/download/$TRANSMISSION_VERSION/transmission-$TRANSMISSION_VERSION.tar.xz" | tar -xJC /release \
    \
    && cd /release/transmission-$TRANSMISSION_VERSION                                    \
    && true

RUN set -eux \
    && cd /release/transmission-$TRANSMISSION_VERSION                                    \
    && cmake -B build -DCMAKE_BUILD_TYPE=Release -D ENABLE_CLI=ON -D ENABLE_TESTS=OFF    \
    && cd build                                                                          \
    && cmake --build .                                                                   \
    && cmake --install .                                                                 \
    && true

FROM alpine:3.16.8

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

RUN set -eux \
    && apk add     \
          libcurl  \
          libevent \
          shadow   \
          libintl \
          libstdc++ \
          libgcc \
    && groupadd -r transmission && useradd --no-log-init -r -g transmission transmission \
    && true

COPY default-settings.json /default-settings.json
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /bin/gosu

VOLUME /home/transmission/.config/transmission-daemon
VOLUME /home/transmission/Downloads

EXPOSE 9091
EXPOSE 51413
EXPOSE 51413/udp

ENTRYPOINT ["/entrypoint.sh"]
CMD ["transmission-daemon", "-f"]
