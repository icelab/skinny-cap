# Skinny Cap #

Simple Capistrano script for deploying a directory of stuff to a server. We use it for pushing static templates up to a client server so we can show them off.

## Usage ##

1. Install Capistrano
2. Move the `Capfile` into the directory you want to deploy
3. Change the values in the `#settings` block
4. `cap deploy:setup`
5. `cap deploy`

The script will upload everything in the current directory to the path you've specified under a timestamped folder. It'll then symlink the last release to `latest` in the same directory. You should end up with something like:

    - /path-to-files/client-code/job-name/
      - /201104191200/
      - /latest/

This means you can just give the `/latest/` link to your clients and whenever you push changes they'll see the most up-to-date version.