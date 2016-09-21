# ART+COM Engineering Blog

Based on [Jekyll Now](https://github.com/barryclark/jekyll-now), see the [README](https://github.com/barryclark/jekyll-now/blob/master/README.md) for details.

## Local Setup

### Preparation

* Install [`rvm`](https://rvm.io/)
* Configure Ruby version and gemset:
```
ln -s .ruby-version.template .ruby-version
ln -s .ruby-gemset.template .ruby-gemset
cd .
```
* Install dependencies: `bundle install`

### Troubleshooting Mac

* [nokogiri install problems]( http://www.nokogiri.org/tutorials/installing_nokogiri.html#mac_os_x)

### Start Jekyll Server

* `bundle exec jekyll serve --draft`
* Open `http://127.0.0.1:4000/` in your browser
