ARG BASE_TAG="develop"
ARG BASE_IMAGE="core-ubuntu-jammy"
FROM kasmweb/$BASE_IMAGE:$BASE_TAG
USER root

ENV HOME /home/kasm-default-profile
ENV STARTUPDIR /dockerstartup
ENV INST_SCRIPTS $STARTUPDIR/install
WORKDIR $HOME

######### Customize Container Here ###########

RUN apt-get update && apt-get install -y tmux screen nano dnsutils zip

RUN echo "set -g mouse on" > $HOME/.tmux.conf && chown 1000:1000  $HOME/.tmux.conf

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
RUN apt install -y wget unzip net-tools nmap nano iputils-ping openssh-server btop ubuntu-dev-tools

# Sudo is not strictly necessary when using docker exec to start tailscale but will be needed to start tailscale manually
RUN apt-get update \
&& apt-get install -y sudo \
&& echo 'kasm-user ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers \
&& echo 'david ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers \
&& rm -rf /var/lib/apt/list/*

#start the  ssh service#
CMD service ssh start && tail -F /var/log/sshd/error.log


### Update .bashrc to run an arbitrary command if specified as an environment variable
RUN echo "if [ ! -z \"\${SHELL_EXEC}\" ] && [ \"\${TERM}\" == \"xterm-256color\" ] ; \n\
then \n\
    set +e \n\
    eval \${SHELL_EXEC} \n\
fi  " >> $HOME/.bashrc && chown 1000:1000  $HOME/.bashrc

### Install Ansible
COPY ./src/ubuntu/install/ansible $INST_SCRIPTS/ansible/
RUN bash $INST_SCRIPTS/ansible/install_ansible.sh  && rm -rf $INST_SCRIPTS/ansible/

### Install Terraform
COPY ./src/ubuntu/install/terraform $INST_SCRIPTS/terraform/
RUN bash $INST_SCRIPTS/terraform/install_terraform.sh  && rm -rf $INST_SCRIPTS/terraform/

COPY ./src/ubuntu/install/terminal/custom_startup.sh $STARTUPDIR/custom_startup.sh
RUN chmod +x $STARTUPDIR/custom_startup.sh
RUN chmod 755 $STARTUPDIR/custom_startup.sh

# Update the desktop environment to be optimized for a single application
RUN cp $HOME/.config/xfce4/xfconf/single-application-xfce-perchannel-xml/* $HOME/.config/xfce4/xfconf/xfce-perchannel-xml/
RUN cp /usr/share/backgrounds/bg_kasm.png /usr/share/backgrounds/bg_default.png
RUN apt-get remove -y xfce4-panel


######### End Customizations ###########

RUN chown 1000:0 $HOME
RUN $STARTUPDIR/set_user_permission.sh $HOME

ENV HOME /home/kasm-user
WORKDIR $HOME
RUN mkdir -p $HOME && chown -R 1000:0 $HOME

USER 1000
