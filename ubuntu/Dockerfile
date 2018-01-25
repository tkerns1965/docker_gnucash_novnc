ARG UBUNTU_VERSION
FROM ubuntu:${UBUNTU_VERSION}

ENV DEBIAN_FRONTEND=noninteractive \
    TERM=xterm \
    DISPLAY=:1 \
    VNC_PORT=5901 \
    NO_VNC_PORT=6901 \
    HOME=/docker

EXPOSE $VNC_PORT \
       $NO_VNC_PORT

RUN apt update && \
    apt clean && \
    apt install -y \
        fluxbox \
        net-tools \
        python \
        wget \
        wmctrl \
        x11vnc \
        xvfb && \
    mkdir -p $HOME/noVNC/utils/websockify && \
    wget -qO- https://github.com/novnc/noVNC/archive/v0.6.2.tar.gz | \
        tar xz --strip 1 -C $HOME/noVNC && \
    wget -qO- https://github.com/novnc/websockify/archive/v0.6.1.tar.gz | \
        tar xz --strip 1 -C $HOME/noVNC/utils/websockify && \
    chmod +x -v $HOME/noVNC/utils/*.sh && \
    ln -s $HOME/noVNC/vnc_auto.html $HOME/noVNC/index.html

COPY fluxbox/* $HOME/.fluxbox/
COPY bootstrap.sh .

RUN chmod u+x bootstrap.sh

RUN mkdir -p $HOME/gnucash_source && \
    wget -qO- https://sourceforge.net/projects/gnucash/files/gnucash%20%28stable%29/2.6.19/gnucash-2.6.19.tar.gz | \
        tar xz --strip 1 -C $HOME/gnucash_source && \
    apt install -y \
        build-essential \
        cmake \
        guile-2.0-dev \
        intltool \
        libdbi-dev \
        libglib2.0-dev \
        libgnomecanvas2-dev \ 
        libgoffice-0.8-dev \ 
        libwebkit-dev \ 
        libxml++2.6-dev \ 
        libxslt1-dev\  
        texinfo \ 
        xsltproc && \
    cd $HOME/gnucash_source && \
    mkdir build-cmake && \
    cd build-cmake && \
    cmake -D CMAKE_INSTALL_PREFIX=$HOME/gnucash_cmake -D WITH_AQBANKING=OFF -D WITH_OFX=OFF $HOME/gnucash_source && \
    make && \
    make install

RUN apt install -y evince

ENV GOSU_VERSION 1.10

RUN set -ex; \
	dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
	wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
	wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
	gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
	rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc; \
	chmod +x /usr/local/bin/gosu; \
	gosu nobody true

RUN groupadd -g 1000 tkerns && \
    useradd -c "Tony Kerns" -d $HOME/docker -g 1000 -u 901 tkerns

USER tkerns

CMD ["gosu","root","/bootstrap.sh"]
