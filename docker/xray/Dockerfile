FROM --platform=$TARGETPLATFORM centos:7 as builder

RUN curl -L -s https://raw.githubusercontent.com/iiTzIsh/multi-v2ray/master/go.sh -o go.sh && bash go.sh -x

FROM --platform=$TARGETPLATFORM alpine:latest

LABEL maintainer "iiTzIsh <euvkzx@iiTzIsh.com>"

ENV COMPLETION_FILE "/usr/share/bash-completion/completions/v2ray"

ENV SOURCE_COMPLETION_FILE "https://raw.githubusercontent.com/iiTzIsh/multi-v2ray/master/v2ray"

ENV VERSION_LIST "https://api.github.com/repos/iiTzIsh/multi-v2ray/tags"

COPY --from=builder /usr/bin/xray/xray /usr/bin/xray/
COPY --from=builder /usr/bin/xray/geoip.dat /usr/bin/xray/
COPY --from=builder /usr/bin/xray/geosite.dat /usr/bin/xray/
COPY run.sh /root

WORKDIR /root

RUN apk --no-cache add python3 bash bash-completion ca-certificates curl socat openssl iptables ip6tables && \
    python3 -m ensurepip && \
    rm -r /usr/lib/python*/ensurepip && \
    ln -s $(which pip3) /usr/local/bin/pip && \
    LATEST_VERSION=`curl -s $VERSION_LIST|grep name|grep -o "[0-9].*[0-9]"|head -n 1` && \
    pip install v2ray-util==$LATEST_VERSION && \
    curl $SOURCE_COMPLETION_FILE > $COMPLETION_FILE && \
    mkdir /var/log/xray/ && \
    chmod +x /usr/bin/xray/xray && \
    chmod +x /root/run.sh && \
    chmod +x $COMPLETION_FILE && \
    echo "source $COMPLETION_FILE" > /root/.bashrc && \
    ln -s $(which v2ray-util) /usr/local/bin/xray && \
    rm -r /root/.cache

ENTRYPOINT ["./run.sh"]
