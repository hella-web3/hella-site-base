[![Deployment](https://github.com/anataliocs/hella-site-base/actions/workflows/jekyll.yml/badge.svg)](https://github.com/anataliocs/hella-site-base/actions/workflows/jekyll.yml)

# Hella Base Site

Base site for [Hella](https://www.hella.website/).

* * *

### Install and Run

Install rbenv
```terminaloutput
brew install rbenv
rbenv init
```

Install ruby
```terminaloutput
rbenv install 3.4.3
rbenv global 3.4.3
```

Update bundler
```terminaloutput
gem install bundler -v 2.6.7
```

Run app
```
bundle install && bundle exec jekyll serve --livereload
``` 

