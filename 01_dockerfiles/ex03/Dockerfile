# docker build --build-arg CHAR_IP=`docker-machine ip Char` -t ex03 .
# docker run -it --rm --privileged -p 2222:22 -p 80:80 -p 443:443 ex03

# env GIT_SSL_NO_VERIFY=true git clone https://`docker-machine ip Char`/oearlene/docker.git

# ssh-keygen -t rsa -C 'oearlene@student.21-school.ru' -f id_rsa
# ssh -T git@`docker-machine ip Char` -p 2222
# env GIT_SSH_COMMAND='ssh -i /User/oearlene/.ssh/id_rsa -p 2222' git clone git@`docker-machine ip Char`:oearlene/docker.git

FROM debian

ARG CHAR_IP=localhost

ENV DEBIAN_FRONTEND=noninteractive

ENV TERM=xterm

RUN apt-get update
RUN apt-get upgrade -y
RUN apt-get install -y xterm git apt-utils curl openssh-server ca-certificates
RUN apt-get install -y postfix
RUN apt-get install -y tzdata
RUN curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
RUN apt-get update
RUN apt-get install -y gitlab-ce
RUN mkdir -p /etc/gitlab/ssl
RUN chmod 700 /etc/gitlab/ssl
RUN openssl req -new -x509 -nodes -newkey rsa:2048 -days 365 -subj /CN=${CHAR_IP} \
	-keyout /etc/gitlab/ssl/gitlab.key \
	-out /etc/gitlab/ssl/gitlab.crt
RUN echo "external_url \"https://${CHAR_IP}\"" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['redirect_http_to_https'] = true" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate'] = \"/etc/gitlab/ssl/gitlab.crt\"" >> /etc/gitlab/gitlab.rb
RUN echo "nginx['ssl_certificate_key'] = \"/etc/gitlab/ssl/gitlab.key\"" >> /etc/gitlab/gitlab.rb
RUN echo "gitlab_rails['gitlab_shell_ssh_port'] = 22" >> /etc/gitlab/gitlab.rb

EXPOSE 22 80 443

ENTRYPOINT service ssh start && (/opt/gitlab/embedded/bin/runsvdir-start &) && gitlab-ctl reconfigure && gitlab-ctl tail
