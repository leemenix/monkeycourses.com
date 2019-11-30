

### Build a free blog using devlopr jekyll and Github Pages

[![Gem Version](https://badge.fury.io/rb/devlopr.svg)](https://badge.fury.io/rb/devlopr)
[![Netlify Status](https://api.netlify.com/api/v1/badges/4232ac2b-63e0-4c78-92e0-e95aad5ab8c3/deploy-status)](https://app.netlify.com/sites/devlopr/deploys)
![](https://ruby-gem-downloads-badge.herokuapp.com/devlopr?type=total&color=brightgreen&style=plastic)

### Demo - [here](https://devlopr.netlify.com)

## Build and Deploy a new blog using devlopr-jekyll theme using 3 Easy Steps - [Guide](https://devlopr.netlify.com/guides/2019/05/20/build-a-blog-using-devlopr-jekyll/)

## Screenshots:

![Screenshot 1](https://raw.githubusercontent.com/leemenix/devlopr-starter/master/assets/img/screenshot1.png)

![Screenshot 2](https://raw.githubusercontent.com/leemenix/devlopr-starter/master/assets/img/screenshot2.png)

![Screenshot 3](https://raw.githubusercontent.com/leemenix/devlopr-starter/master/assets/img/screenshot3.png)

![Screenshot 4](https://raw.githubusercontent.com/leemenix/devlopr-starter/master/assets/img/screenshot4.png)


# Run using Docker 

```
$ git clone 
$ cd repo
$ export JEKYLL_VERSION=3.8
$ docker run --rm --volume="$PWD" -it jekyll/jekyll:$JEKYLL_VERSION jekyll build
$ jekyll serve
```

## Licence

The theme is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).



# monkeycourses-jekyll
#Install Ruby development environment
sudo apt install ruby-full build-essential zlib1g-dev

#Avoid Ruby Gems installation as root user
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$PATH:$GEM_HOME/bin"' >> ~/.bashrc
source ~/.bashrc

# Install jekyll
gem install jekyll bundler

## Start new project/site/blog
jekyll new myblog
cd myblog
bundle exec jekyll serve

## Install gem based theme
# add theme gem to Gemfile
gem "jekyll-theme-minimal"
# or if you've started with the jekyll new commmand, replace
# gem "minima", "~> 2.0" with gem you want
- gem "minima", "~> 2.0"
+ gem "jekyll-theme-miimal"
# install theme
bundle install
# update _config.yml to activate theme
theme: jekyll-theme-minimal
# build site
bundle exec jekyll serve
# go in prod
bundle exec jekyll build --destination monkeycourses.com
