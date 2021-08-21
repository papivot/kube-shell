#FROM tsl0922/musl-cross
FROM ubuntu:18.04

RUN apt-get update && apt-get install -y autoconf automake curl cmake git libtool make && apt-get -y upgrade \
    && git clone --depth=1 https://github.com/tsl0922/ttyd.git /ttyd \
    && cd /ttyd && env BUILD_TARGET=x86_64 WITH_SSL=true ./scripts/cross-build.sh

FROM ubuntu:18.04
COPY --from=0 /ttyd/build/ttyd /usr/bin/ttyd
RUN apt-get update \
    && apt-get install -y vim net-tools dnsutils curl wget apt-transport-https gnupg2 openssh-client bash-completion && apt-get -y upgrade\
    && curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - \
    && echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list \
    && apt-get update \
    && apt-get install -y kubectl

ADD https://github.com/krallin/tini/releases/download/v0.19.0/tini /sbin/tini
RUN chmod +x /sbin/tini

RUN mkdir -p /user/kshell
RUN useradd kshell --uid 9999 -M -U --home-dir /user/kshell -s /bin/bash
RUN echo "source <(kubectl completion bash)" >> /user/kshell/.bashrc
RUN chmod +x /user/kshell/.bashrc
RUN chown kshell:kshell /user/kshell/.bashrc
USER kshell
WORKDIR /user/kshell

EXPOSE 7681
ENTRYPOINT ["/sbin/tini", "--"]
CMD ["ttyd", "bash"]
