
# bellowsh

## What is this?

Social networking on the command-line. You know, for tildes.

I was setting up a tilde, found `fellowsh.lua`, but even when I got
it to run on my system, it didn't run *properly* ... So, I ported it to
Python.

This is still a pretty young implementation.


## System requirements

I really wanted this to run without third-party modules.

Unfortunately, Python 3 still doesn't have strong timezone support
without leveraging external modules. However, on at least Debian-based
Linux systems, you can use a system package instead of global `pip3`.

I'll create the Debian control files to make the dependencies easier
to install in the future.

### Packages for best experience

Python packages:
- Debian `python3-arrow` (or `pip3 install arrow`) : https://arrow.readthedocs.io/en/latest/

Linux packages with baked-in BSD defaults (when available):
- Debian `procps` (for `ps`): https://gitlab.com/procps-ng/procps
- Debian `coreutils` (for `who`)
- Debian `util-linux` (for `last`)
- Debian `shadow-utils` (for `chfn`)

Packages which mostly behave the same elsewhere:
- Debian `finger` (for `finger`)
- Debian `bsdmainutils` (for `write`)
- Debian `talkd` or `inetutils-talkd`
- Debian `ytalk` (or `talk`, but `ytalk` is better)
- Debian `nano` (or `vi` if `$VISUAL` and `$EDITOR` are undefined and `sensible-editor` unavailable)
- Debian `fortune-mod` (along with `fortunes` or `fortunes-min`)

Enhances other programs
- Debian `oidentd` or `ident2` (or some other `ident-server`, enhances `finger`)
- Debian `sensible-utils` (for `sensible-editor`, an automatic default editor selector)
- Debian `aptitude` (text-mode package manager with rich dependency/alternate support)

Optional text filters for `write`:
- Debian `toilet` and `toilet-fonts`
- Debian `figlet` 
- Debian `cowsay` (even more optionally `cowsay-off`)
- Debian `filters`

Servers that should run where remote users live:
- Debian `efingerd` or `cfingerd` or `fingerd` (or some other `finger-server`)


### BSD variants

I've been testing the BSD variants on a macOS X machine, though I've
been doing primary development in Debian.

BSD's proc tools should work, but they don't have the flexibility of the
tools more commonly found in Linux systems. (BSD doesn't support the
process tree.)

The BSD version of `who` and `last` are also less pleasant to use than
the ones found in Linux. (BSD's version of each is locale-specific, making
it harder to process by script.)


## How are local users determined?

We should be using be a relatively common standard for determining
'valid user'.

- Super User (`root`, UID 0) is disallowed
- The home directory must exist and be a full path to a directory.
- The shell exists and uses the full path.
- Check against hard-coded 'never-valid' shells, `false` and `nologin`. (This
  is a fail-safe if /etc/shells isn't available.)
- if /etc/shells exist, the shell must be listed there
- if `users` group exists, the user must be a member

I'm a big fan of "user-groups," where each user has their own private
group. The big reason I like this is that it allows you to have a rich
group membership and you can safely globally use a umask of 0002. In
contexts where a non user-group is used, you _want_ groups to be able
to write files.

In any case, locations without "user-groups" has all user accounts
being in the "users" group.

More than that, OpenSSH can be configured to be restricted to users 
of specific groups, and the "users" group is a decent default for it.

