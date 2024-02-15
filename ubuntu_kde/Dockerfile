ARG BASE_TAG="develop"
ARG BASE_IMAGE="core-ubuntu-jammy"
FROM kasmweb/$BASE_IMAGE:$BASE_TAG
USER root

ENV HOME /home/kasm-default-profile
ENV STARTUPDIR /dockerstartup
ENV INST_SCRIPTS $STARTUPDIR/install
WORKDIR $HOME

######### Customize Container Here ###########


########################################################################################
# KDE Config borrowed from:  https://github.com/mviereck/dockerfile-x11docker-kde-plasma
###########################################################################################
ENV LANG=en_US.UTF-8
RUN apt-get update && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      locales && \
    echo "$LANG UTF-8" >> /etc/locale.gen && \
    locale-gen && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      kwin-x11 \
      plasma-desktop \
      plasma-workspace && \
    apt-get remove -y bluedevil && \
    apt-get autoremove -y

# Dirty fix to avoid kdeinit error ind startkde. Did not find a proper solution.
RUN sed -i 's/.*kdeinit/###&/' /usr/bin/startkde

# Wayland: startplasmacompositor
RUN env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      kwin-wayland-backend-x11 kwin-wayland-backend-wayland && \
    env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      plasma-workspace-wayland && \
    sed -i 's/--libinput//' /usr/bin/startplasmacompositor

RUN env DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      konsole \
      kwrite \
      libcups2 \
      libpulse0 \
      procps \
      psmisc \
      sudo \
      synaptic \
      systemsettings



###########################################################################################
# / KDE Config borrowed from:  https://github.com/mviereck/dockerfile-x11docker-kde-plasma
###########################################################################################


# Remove XFCE thats in the Kasm core images
RUN apt-get remove -y xfce4 xfce4-terminal xterm xfce4-session

# Create a custom startup script to launch KDE
RUN echo '#! /bin/bash\n\
startkde &\n\
' > $STARTUPDIR/custom_startup.sh && chmod +x $STARTUPDIR/custom_startup.sh

######### End Customizations ###########

RUN chown 1000:0 $HOME
RUN $STARTUPDIR/set_user_permission.sh $HOME

ENV HOME /home/kasm-user
WORKDIR $HOME
RUN mkdir -p $HOME && chown -R 1000:0 $HOME

USER 1000
