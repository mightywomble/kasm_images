ARG BASE_TAG="1.14.0"
FROM kasmweb/ubuntu-jammy-desktop:$BASE_TAG
USER root

ENV HOME /home/kasm-default-profile
ENV STARTUPDIR /dockerstartup
ENV INST_SCRIPTS $STARTUPDIR/install
WORKDIR $HOME

######### Customize Container Here ###########

# Install Tailscale

#RUN curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.gpg |  apt-key add -
#RUN curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.list |  tee -a /etc/apt/sources.list.d/tailscale.list

# Add Tailscale's GPG key
RUN mkdir -p --mode=0755 /usr/share/keyrings
RUN curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.noarmor.gpg | tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null
# Add the tailscale repository
RUN curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.tailscale-keyring.list | tee /etc/apt/sources.list.d/tailscale.list
RUN apt-get update && \
apt-get install -y tailscale


#add a user David and add the user to sudo
RUN useradd -ms /bin/bash david && usermod -aG sudo david
RUN sudo usermod -p '$1$5RPVAd$biEdF3CikG0Er3.zR1Qm20' david

#install apps
RUN apt install -y wget unzip net-tools nmap nano iputils-ping openssh-server  

# Sudo is not strictly necessary when using docker exec to start tailscale but will be needed to start tailscale manually
RUN apt-get update \
&& apt-get install -y sudo \
&& echo 'kasm-user ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers \
&& echo 'david ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers \
&& rm -rf /var/lib/apt/list/*

#start the  ssh service#
RUN service ssh start
CMD ["/usr/sbin/sshd", "-D"]

######### End Customizations ###########

RUN chown 1000:0 $HOME

ENV HOME /home/kasm-user
WORKDIR $HOME
RUN mkdir -p $HOME && chown -R 1000:0 $HOME

USER 1000


