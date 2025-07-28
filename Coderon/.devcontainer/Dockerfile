FROM mcr.microsoft.com/devcontainers/ruby:3.2
ENV DEBIAN_FRONTEND=noninteractive \
    BUNDLE_PATH=~/.bundle \
    BUNDLE_PATH_SYSTEM=true \
    BUNDLE_APP_CONFIG=/usr/local/bundle \
    BUNDLE_USER_HOME=~/.bundle \
    BUNDLE_USER_CACHE=~/.bundle/cache \
    BUNDLE_USER_CONFIG=~/.bundle/config \
    BUNDLE_USER_PLUGIN=~/.bundle/plugin \
    GEM_PATH=/usr/local/rvm/gems/default:/usr/local/rvm/gems/default@global \
    GEM_HOME=/usr/local/rvm/gems/default \
    MY_RUBY_HOME=/usr/local/rvm/rubies/default \
    RUBY_VERSION=3.4.3

USER vscode

WORKDIR /home/vscode/IdeaProjects/coderon-jekyll

RUN export PATH="/usr/local/rvm:/usr/local/rvm/bin/rvm":$PATH
RUN echo "export PATH='/usr/local/rvm/bin/rvm:$PATH'" >> ~/.zshrc
RUN echo "export PATH='/usr/local/rvm/bin/rvm:$PATH'" >> ~/.bashrc

RUN /usr/local/rvm/bin/rvm install 3.4.3 && /usr/local/rvm/bin/rvm automount 3.4.3 \
 && /usr/local/rvm/bin/rvm use 3.4.3 && gem install bundler:2.6.7

RUN export PATH="~/.bundle":$PATH

RUN echo "export PATH='~/.bundle:$PATH'" >> ~/.zshrc
RUN echo "export PATH='~/.bundle:$PATH'" >> ~/.bashrc

COPY . .

RUN bundle install --gemfile=Gemfile && bundle exec jekyll build






