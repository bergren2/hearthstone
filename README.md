# Hearthstone Collection
[![Codacy Badge](https://api.codacy.com/project/badge/Grade/abe6092230fa48dfb033b56a5b78fd96)](https://www.codacy.com/app/bergren2/hearthstone?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=bergren2/hearthstone&amp;utm_campaign=Badge_Grade)
[![Build Status](https://travis-ci.org/bergren2/hearthstone.svg?branch=master)](https://travis-ci.org/bergren2/hearthstone)

Well met!

This site uses data from [Hearthstone JSON](http://hearthstonejson.com).

## Environment Prereqs

You should have the following minimally setup:

- [Git](https://help.github.com/articles/set-up-git) (duh)
- [rbenv](https://github.com/sstephenson/rbenv)
& [ruby-build](https://github.com/sstephenson/ruby-build)
- ImageMagick
  - `$ brew install imagemagick`

## Initial Project Setup

    $ git clone git@github.com:bergren2/hearthstone.git
    $ cd heymrbass
    $ rbenv install
    $ gem install bundler
    $ bundle install

## Development

Fire up

    $ bin/middleman server

and then check out the site at [localhost:4567](http://localhost:4567).

## Testing

You can run:

    $ bin/rake jasmine

Then check out [localhost:8888](http://localhost:8888) to see the results of the
Jasmine specs.

## Deployment

This project uses Travis CI to run CICD. :sunglasses:

## Adding Cards

To add cards to your collection:

    $ bin/rake collection:edit

There are other useful Rake tasks for building out and adding to
`collection.json`, so check them out!

### Caveats

If your directory is dirty, `git stash` before deploying.

If Rake complains about there already being an `origin` remote, remove the `build`
directory in its entirety.

## Configuration

After launching the development server, navigate to
[localhost:4567/__middleman/config](http://localhost:4567/__middleman/config)
for documentation on configuring Middleman. You should not have to relaunch the
server after making changes to `config.rb`.

## License

See LICENSE for details. Hearthstone is &copy; Blizzard Entertainment.
