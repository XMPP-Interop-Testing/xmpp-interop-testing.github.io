# Readme

The source code for the website that's on https://xmpp-interop-testing.github.io

## Contributing

Needs ruby.

Install deps: `bundle install`

Run locally: `bundle exec jekyll serve`

### Notes for macOS

By default, macOS ships with some out-of-date ruby which will make the above impossible to run. First:

```sh
brew install ruby
echo 'export PATH="/opt/homebrew/opt/ruby/bin:$PATH"' >> ~/.zshrc
```
