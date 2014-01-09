FROM ubuntu:precise
MAINTAINER Kim Hirokuni <kimh@kvhasia.com>

RUN apt-get update
RUN apt-get -y dist-upgrade
RUN apt-get -y install curl build-essential m4 git libncurses5-dev libssh-dev libyaml-dev libexpat-dev

# user & group
RUN addgroup ejabberd
RUN adduser --system --ingroup ejabberd --home /opt/ejabberd --disabled-password --disabled-login ejabberd

# erlang
RUN mkdir -p /src/erlang \
&& cd /src/erlang \
&& curl http://erlang.org/download/otp_src_R16B03.tar.gz > otp_src_R16B03.tar.gz \
&&  tar xf otp_src_R16B03.tar.gz \
&& cd otp_src_R16B03 \
&& ./configure \
&& make \
&& make install

# ejabberd
RUN mkdir -p /src/ejabberd \
&& cd /src/ejabberd \
&& curl -L "http://www.process-one.net/downloads/downloads-action.php?file=/ejabberd/13.12/ejabberd-13.12.tgz" > ejabberd-13.12.tgz \
&& tar xf ejabberd-13.12.tgz \
&& cd ejabberd-13.12 \
&& ./configure --enable-user=ejabberd \
&& make \
&& make install

# cleanup
RUN cd / && rm -rf /src
RUN apt-get -y purge curl build-essential m4 git libncurses5-dev libssh-dev libyaml-dev libexpat-dev

# copy config
RUN rm /etc/ejabberd/ejabberd.yml
ADD ./ejabberd.yml /etc/ejabberd/
ADD ./ejabberdctl.cfg /etc/ejabberd/

# Let ejabberd to run sudo
RUN apt-get install -y sudo && echo "ejabberd ALL=(ALL) NOPASSWD: /sbin/ejabberdctl" >> /etc/sudoers
USER ejabberd

# Add admin user
RUN sudo -u ejabberd ejabberdctl start && sleep 3 && ejabberdctl register admin localhost password && whoami
#CMD ejabberdctl start && tail -f
