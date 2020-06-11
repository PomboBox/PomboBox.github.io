# PomboBox

Install ruby:
```shell
$ sudo apt install ruby-full
```

Install build-essentials:
```shell
$ sudo apt install build-essential
```

Solution for 'Cant find gem bundler (>= 0.a) with executable bundle'
https://bundler.io/blog/2019/05/14/solutions-for-cant-find-gem-bundler-with-executable-bundle.html:
```shell
$ sudo gem update --system
```

Install bundler and jekyll:
```shell
$ sudo gem install bundler jekyll
```

```shell
$ bundle update --bundler
```

Run:
```shell
bundle exec jekyll serve --livereload
```
