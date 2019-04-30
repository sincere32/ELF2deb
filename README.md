# ELF2deb

Convert any single or multiple executable file(s) to deb package

I.e. this is a AppImage|ELF|executable script|etc to `.deb` script.

## Setup

You want to setup `$DEBEMAIL` and `$DEBFULLNAME` for the deb tools to work properly:

```bash
$ cat >>~/.bashrc <<EOF
DEBEMAIL="your.email.address@example.org"
DEBFULLNAME="Firstname Lastname"
export DEBEMAIL DEBFULLNAME
EOF
$ . ~/.bashrc
```

## Example

In this example I'm first downloading the [skaffold](https://skaffold.dev/) binary and packing it as a `.deb` file:

```bash
$ curl -Lo ../skaffold https://storage.googleapis.com/skaffold/releases/latest/skaffold-linux-amd64
$ ./elf2deb.pyz --license apache-2.0 --license_year 2018 --license_holder "The Skaffold Authors" --package_name skaffold --package_version 0.28.0 --homepage "https://skaffold.dev/" ../skaffold
$ cd skaffold-0.28.0
$ dch --create --empty --distribution unstable --package skaffold --newversion 0.28.0
$ dch --append 'Empty changelog' 2>/dev/null
$ vim debian/control  # add description
$ debuild -us -uc
[... lots of debuild output ...]
$ cd ../
$ sudo dpkg -i skaffold_0.28.0_amd64.deb
```

## Common warnings

If you are running Ubuntu, you might get `E: bad-distribution-in-changes-file unstable`.
In this case edit `debian/changelog` and change `unstable` to your distributions codename (find it by running `lsb_release -c`).

If you are missing the `dch`-tool, then run: `sudo apt install --no-install-recommends devscripts libdistro-info-perl`.

You can safely ignore the following warnings from lintian:

 * `source-is-missing`

 * `binary-without-manpage`

(you will probably get a longer list of errors and warnings)