# business-english

This is the source of <https://business-english-bocholt.de/>

# Quickstart

```
gem install bundler
bundle install
bundle exec jekyll serve
```

# Beta Preview and final upload

Create `Rakefile.config`:
```
$ftp_server='example.org'
$ftp_login='username'
$ftp_password='password'
```

and call `rake beta` and finally `rake live`.
