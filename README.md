# Finance Tracker

This is the finance tracker app from the Complete Ruby on Rails Developer course.

## Requirements

* Ruby 3.1.0
* Rails 6.1.7
* SQLite 3 in development and test, PostgreSQL in production
* Node 20 and Yarn 1.x (for Webpacker)

## Getting started

```bash
bundle install
yarn install
rails db:create
rails db:migrate
rails server
```

The app is then available at http://localhost:3000.

## Running the tests

```bash
rails test
```

## Deploying to Heroku

The app runs on the `heroku-24` stack with the Node and Ruby buildpacks:

```bash
heroku buildpacks:add heroku/nodejs
heroku buildpacks:add heroku/ruby
heroku config:set RAILS_MASTER_KEY=$(cat config/master.key)
heroku addons:create heroku-postgresql:essential-0
git push heroku master
```

`config/master.key` is deliberately not in git, so `RAILS_MASTER_KEY` has to be set as a
config var or the app cannot decrypt its credentials and will fail to boot.

The `Procfile` runs migrations on every release and boots Puma for the web dyno.

## Notes on running this stack on modern machines

Rails 6 predates current Ruby, Node and OpenSSL releases, so a few pins are in place:

* **Ruby is 3.1.0, not the course's 2.7.8.** Ruby 2.7 is end-of-life and Heroku no longer
  ships a binary for it on any current stack, so the app cannot deploy on 2.7. Rails 6.1 is
  the earliest Rails 6 that supports Ruby 3.x.
* `concurrent-ruby` is pinned to 1.3.4 in the `Gemfile`. Version 1.3.5 dropped its
  implicit `require "logger"`, which Rails 6.1 relies on.
* Webpacker is 5.4.4 rather than the course's 4.x. Webpacker 4 reads its YAML config in a
  way that Psych 4 (bundled with Ruby 3.1) rejects, and 5.4 also replaces `node-sass` with
  dart-sass, which removes the native build step entirely.
* `bin/webpack` and `bin/webpack-dev-server` add `--openssl-legacy-provider` to
  `NODE_OPTIONS`, since webpack 4 hashes with md4 and OpenSSL 3 no longer provides it.
