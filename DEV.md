Once you've cloned the package:

### Install Ruby

I prefer [rvm](https://rvm.io/) for Ruby version management. I have most recently used Ruby 3.4.1 to build the website.

### First-time (or when you install a new Ruby version into RVM)

1. Install [Bundler](https://bundler.io/). This should be as simple as `gem install bundler` somewhere on the system.
1. From the root directory of this package, run `bundle update`

### Each time

1. `rvm default` (or `rvm use...`)
1. `jekyll build`
1. `jekyll serve --livereload`
