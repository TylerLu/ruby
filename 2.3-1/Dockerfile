FROM ruby:2.3.3
MAINTAINER Azure App Services Container Images <appsvc-images@microsoft.com>

COPY init_container.sh /bin/

RUN apt-get update -qq \
    && apt-get install -y nodejs unzip openssh-server --no-install-recommends \
    && chmod 755 /bin/init_container.sh \
    && echo "root:Docker!" | chpasswd

COPY sshd_config /etc/ssh/

# Rollback bundler version for deployment reasons (14 had a breaking change for us)
RUN  gem uninstall -i /usr/local/lib/ruby/gems/2.3.0 bundler
RUN gem install bundler --version "=1.13.6"

# Install passenger
RUN gem install rubygems-bundler
RUN gem regenerate_binstubs 
RUN gem install --no-user-install passenger

# Logs will be sent to /usr/local/passenger/logs which will show up in LogFiles for user
RUN mkdir -p /usr/local/passenger
RUN ln -s /home/LogFiles /usr/local/passenger/logs

COPY splashsite.zip /tmp
RUN unzip -q /tmp/splashsite.zip -d /opt/splash 

RUN cd /opt/splash/splash; bundle install --deployment

HEALTHCHECK CMD curl --fail http://localhost:3000/ || exit 1 

EXPOSE 2222

CMD ["/bin/init_container.sh"]

