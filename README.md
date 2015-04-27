# Hearthstone Collection

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

For now, check out [localhost:4567/jasmine](http://localhost:4567/jasmine) to
see the results of Jasmine tests.

Jasmine Rake tasks are currently not functioning, but I hope to get them
up-and-running at some point.

## Deployment

    $ bin/rake publish

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
