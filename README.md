# chenv -- Lightweight Environment Management #

chenv is similar to Python's virtualenv, without the Python stuff. In a
nutshell, chenv allows you to execute a particular .profile for a named
environment.

## For Example ##

If you were working with HashiCorp Vault, you usually want to have the
environment variable `VAULT_ADDR` set to your Vault server. However, you may
have multiple Vault servers (eg vault-prod and vault-dev). Adding this
variable to your global .profile means that you'll have to juggle this
value depending on what you're working on.

`chenv` helps you here by only setting up this environment when you intend to
use it, as well as adding to your shell prompt to let you know what
environment you're in.

To set up an environment for Vault, just run `chenv -e vault`. This will open
up your `$EDITOR` with the environment file named 'vault'. You can then add
whatever you want here (the file is just sourced by your shell when it's run,
just like .bashrc/.profile). You might set up a file like this:

```sh
export VAULT_ADDR='https://vault.example.com:8200/'
export VAULT_NAMESPACE='some/namespace/'
```

Now, you can run `chenv vault` and be dropped into a shell with those
variables set. At any time you can `exit` (or `^D`) to return to your
normal shell.

## Install ##

Just copy the `chenv` file from this repo to somewhere on your $PATH. A good
choice for this is usually `/usr/local/bin`:

```sh
sudo cp chenv /usr/local/bin/chenv
sudo chmod 755 /usr/local/bin/chenv
```

Then try `chenv` to see if you get help output. If not, inspect your $PATH to
find somewhere else to put it (`echo "$PATH"`).

## Options ##

chenv's `-h` option should tell you all that you need to know (note that my
`$EDITOR` is `nvim` in this output):

```sh
$ chenv -h
Usage: chenv [-l] [-c|-e] <env>
    -l : list environments
    -c : show an environment
    -e : edit environment with nvim
    -h : show help output
```

## Protip: Storing Secrets ##

`chenv` can also be used to securely handle secret environment variables!
When used in combination with PGP, you can set up an environment file like
this:

```sh
export AWS_ACCESS_KEY_ID='...'

export AWS_SECRET_ACCESS_KEY="$(echo '-----BEGIN PGP MESSAGE-----

[ ... PGP data ... ]
-----END PGP MESSAGE-----' | gpg -d)"
```

and the `AWS_SECRET_ACCESS_KEY` will be in your environment without being
stored in plaintext on disk.

(If you're unfamiliar with PGP, you can get the above data using
`gpg --armor -r <your-email> -e`, though you need a working PGP keychain to
do so)
