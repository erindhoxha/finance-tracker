# Finance Tracker

This is the finance tracker app from the Complete Ruby on Rails Developer course.

## Requirements

* Ruby 2.7.8
* Rails 6.0.6.1
* SQLite 3
* Node 18 and Yarn 1.x (for Webpacker)

## Getting started

```bash
bundle install
yarn install --ignore-engines
rails db:create
rails db:migrate
rails server
```

The app is then available at http://localhost:3000.

## Running the tests

```bash
rails test
```

## Notes on running this old stack on modern machines

Rails 6 and Webpacker 4 predate current Node, OpenSSL and Python releases, so a few
pins are in place to keep the toolchain building:

* `concurrent-ruby` is pinned to 1.3.4 in the `Gemfile`. Version 1.3.5 dropped its
  implicit `require "logger"`, which Rails 6.0 relies on.
* `node-sass` is pinned to 9.x via `resolutions` in `package.json` so it builds
  against Node 18 on Apple Silicon. Webpacker's `check_yarn_integrity` is disabled
  in `config/webpacker.yml` because it rejects that override.
* `bin/webpack` and `bin/webpack-dev-server` add `--openssl-legacy-provider` to
  `NODE_OPTIONS`, since webpack 4 hashes with md4 and OpenSSL 3 no longer provides it.
* `node-sass` compiles through node-gyp, which needs Python's `distutils`. That was
  removed in Python 3.12, so builds need a Python with `setuptools` installed:

  ```bash
  python3 -m venv ~/.venvs/nodegyp
  ~/.venvs/nodegyp/bin/pip install "setuptools<81"
  npm_config_python="$HOME/.venvs/nodegyp/bin/python" yarn install --ignore-engines
  ```

* `yarn install` needs `--ignore-engines` because some transitive dependencies now
  declare Node 20+ engine requirements.
