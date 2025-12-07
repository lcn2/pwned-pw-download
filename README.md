# pwned-pw-download


## TL;DR

Download the pwned password database.  Look up passwords or hashes of passwords in your database.


## What is pwned?

A good definition of pwned is found at:

    https://en.wikipedia.org/wiki/Leet#Owned_and_pwned

pwned is a slang form of owned: to appropriate or to conquer to gain ownership.

For information as to why a pwned password should NOT be used, see:

    https://haveibeenpwned.com/Passwords
    https://haveibeenpwned.com/FAQs


## Why not use the API?

The `pwned-pw-download` tool will download the pwned password tree on your computer.
Once you complete the download. **NO NETWORK ACCESS IS REQUIRED** to check if
a password has been pwned.

* There is **NO** network API required.
* There is **NO** messy .NET stuff.
* There is **NO** delay while the network request is being serviced.
* There is **NO** network service outage to worry about.
* There is **NO** risk that you pwned lookup will be visible to the Internet.
* Checking if a password (or its SHA-1 hash) is in the local pwned password tree is quick.
* Checking if a password (or its SHA-1 hash) has been pwned is private to your machine.

BTW: We have nothing against some of the other pwned tools you might find.
We just prefer to use a local tool that can be inspected and/or adapted as needed.


## To fetch this repo onto your machine

```sh
git clone https://github.com/lcn2/pwned-pw-download.git
```


## The pwned password tree

The pwned password tree contains 4369 directories and 1048576 data
files and 4096 curl.out diagnostic files.  The size of the 4-level
pwned password tree, compressed, is about 39.03 gigabytes.

The uncompressed pwned password tree is about 71.37 gigabytes,
which is why we don't recommend not using the `-b` to disable
compression, if you can help it.

As of 2025 Dec 03: The pwned password tree contains 2,002,278,852
unique pwned passwords, and 53,373,964,755 instances of pwnage
for an average of about 26.66 pwnes per password.


## Why a 4 level tree?

The `pwned-pw-download.sh` shell script will download the entire dataset used by
[HIBP](https://haveibeenpwned.com/Passwords) into a 4-level directory tree.

Unlike some downloads, this will NOT form a huge million+ file directory,
which for many file system types are not very efficient.  Instead, we
form a 4-level tree where the bottom-level directories contain only 256
files each, plus a `curl.out` file for diagnostic purposes.


## The pwned-pw-download tool

The `pwned-pw-download` tool will download the pwned password database
into a compressed 4-level tree.

As of 2025 Dec 03: The `pwned-pw-download` tool takes about 2 hours 15
minutes to download and compress the tree as it is being downloaded.

**IMPORTANT NOTE**: As of 2025 Dec 03, the original files from
[https://api.pwnedpasswords.com/range](https://api.pwnedpasswords.com/range)
are in "_DOS_" format and they lack a final newline character.
The `pwned-pw-download` tool assumes that you prefer a more modern file
format and converts the files using the `dos2unix(1)` utility.  Should you
prefer the original file format, use both `-d -a` on the command line.


## The ispwned tool

Given an installed password database into a 4-level tree,
determine if passwords (or the SHA-1 hash of passwords)
are known to have been pwned (in the password database).

The pwned check is local to your machine.  It uses common UNIX/Linux commands.
The `ispwned` tool is a readable shell script that may be inspected,
and/or modified as you wish.  There is no opaque binary pwned tool to wonder about.


# To install

```sh
sudo make install
```

**NOTE**: You do **NOT** have to install.  The `make install` can make
the `pwned-pw-download` and `ispwned` tools available under `/usr/local/bin`
if you have privileges to install things under `/usr/local/bin`.

To install them elsewhere, such as `/var/tmp`:

```sh
make install DESTDIR=/var/tmp
```

Or you can just run the commands from within the `pwned-pw-download` repo
without installing anything:

```sh
cd pwned-pw-download
./pwned-pw-download ...
./ispwned ...
```


# Example of using pwned-pw-download

You may need to create `/usr/local/share/pwned.pw.tree` as root
and then give yourself write access before running `pwned-pw-download`,
where `user` is your UNIX username and `group` is your UNIX groupname:

```sh
sudo mkdir -p /usr/local/share/pwned.pw.tree
sudo chown -R user:group /usr/local/share/pwned.pw.tree
sudo chmod 0755 /usr/local/share/pwned.pw.tree
```

To use, we suggest:

```sh
pwned-pw-download -v 3 /usr/local/share/pwned.pw.tree
```

**NOTE**: The above command assumes that `pwned-pw-download` has been put
into your search `$PATH`.  If it is not, supply the full path
to where you find the `pwned-pw-download` tool.

Then to be safe, make the pwned password tree read-only:

```sh
sudo chmod -R -w /usr/local/share/pwned.pw.tree
```


# Example of using ispwned

The examples below assume that the `pwned-pw-download` has been downloaded
under the default `/usr/local/share/pwned.pw.tree` directory.  If you
need to install it elsewhere, add `-d /path/to/tree` to the commands.

**NOTE**: The examples below assume that `ispwned` has been put
into your search `$PATH`.  If it is not, supply the full path
to where you find the `ispwned` tool.

Supply a password as an argument:

```sh
ispwned password
```

If you would prefer to NOT have the password visible as an argument (that
someone running `ps(1)` might be able to see), then run `ispwned` without
any arguments and type the file in input and end with an EOF (type Control D):

```sh
ispwned
```

Check the SHA-1 hash of a password instead:

```sh
ispwned -s 1E4C9B93F3F0682250B6CF8331B7EE68FD8
```

You can supply multiple passwords or SHA-1 hashes of passwords the command line:

```sh
ispwned password ispwned passw0rd Passwd
ispwned -s 1E4C9B93F3F0682250B6CF8331B7EE68FD8 1C68EF8B9B6B061B28C348BC1ED7921CB53 D6CBC8FDC427A5A59FC996049732E029440
```

If you have a file of passwords, one per line:

```sh
ispwned < pw.list
```

If you have a file of SHA-1 hashes of passwords, one per line:

```sh
ispwned -s < sha1.pw.list
```

To not have any output, other than critical errors, use `-q`:

```sh
ispwned -q < check.list
```

To have the `ispwned` tool report if a password is **NOT** found in the
local pwned password tree, use the `-o` option:

```sh
ispwned -o dfasdfds2f sadfdsfsdafsdafasd fbwebwefiewbfwe
```

The `ispwned` tool will exit 0 if none of the passwords (or SHA-1 hashes
if `-s` is given) are found in the local pwned password tree.
If any password (or SHA-1 hashes if `-s` is given) is found, the tool
will exit 1.

```sh
if ispwned -q < pw.list ; then
    echo "no pwned passwords detected"
else
    echo "something was pwned"
fi
```

To print just pwned count and password/hash, and if not pwned print 0 and password/hash:

```sh
ispwned -o -c ...
```

To count total number of not-yet-pwned, pwned, total passwords/hashes for a list of passwords:

```sh
ispwned -t -q < pw.list.txt
74 9926 10000
```

In the above example, 99.26% of the passwords in `pw.list.txt` were found to be pwned.


## ispwned usage

```
usage: ispwned [-h] [-v level] [-V] [-d topdir] [-b bzgrep] [-g grep] [-S sha1sum]
               [-s] [-q] [-o] [-O] [-c] [-t] [arg ...]

    -h          print help message and exit
    -v level    set verbosity level (def level: 0)
    -V          print version string and exit

    -d topdir   pwned password tree is under topdir (def: /usr/local/share/pwned.pw.tree)
    -b bzgrep   path to bzgrep tool (def: /usr/bin/bzgrep)
    -g grep     path to grep tool (def: /usr/local/bin/grep)
    -S sha1sum  path to the sha1sum tool (def: /opt/homebrew/var/homebrew/linked/coreutils/libexec/gnubin/sha1sum)

    -s          args are SHA-1 hashes (def: args are passwords)
    -q          stay quiet about pwned passwords/hashes (def: announce pwned passwords/hashes on stdout)
    -o          also print non-pwned passwords/hashes on stdout (def: stay quiet about non-pwned passwords/hashes)
    -O          print ONLY non non-pwned passwords/hashes on stdout (def: announce pwned, stay quiet about non-pwned)
                    NOTE: -o and -O conflict
    -c          print only pwned count and password/hash (def: do not)
                    NOTE: if -o and not pwned, print count as 0 - only way a 0 pwned count is printed under -c
                    NOTE: if pwned count is <= 0, or is an invalid count, print pwned count as -1
                    NOTE: -q and -c conflict
    -t          print the total number of not-yet-pwned, pwned, total passwords/hashes on stderr (def: do not)

    [arg ...]   args are passwords, or SHA-1 hashes is -s (def: read from stdin)

Exit codes:
     0         no evidence that a password (or hash if -s) has been pwned
     1         at least 1 password (or hash if -s) was found to be pwned
     2         -h and help string printed or -V and version string printed
     3         command line error
     4         topdir not found, or is not searchable, or some file under topdir was not found, or is not readable
     5         grep tool not found, or is not executable
     6         bzgrep tool not found, or is not executable
     7         sha1sum tool not found, or is not executable, or sha1sum tool exited non-zero
     8         line read or arg was empty (0 length), or hash was not a valid SHA-1 hash
 >= 10         internal error

ispwned version: 1.2.0 2025-12-07
```


## pwned-pw-download usage

```
pwned-pw-download [-h] [-v level] [-V] [-a] [-d] [-p parallel] [-U BASE_RANGE_URL] [-b] topdir

    -h          print help message and exit
    -v level    set verbosity level (def level: 0)
    -V          print version string and exit

    -a          do not append a final newline to downloaded files (def: do)
    -d          do not un-DOS downloaded files (def: convert using /opt/homebrew/bin/dos2unix)
    -p parallel curl(1) files up to parallel at a time (def: 64)
    -U base_url curl(1) files from under base_url (def: https://api.pwnedpasswords.com/range)
    -b              do NOT bzip2 downloaded files (def: use $BZIP2 on downloaded files)

    topdir      top of the 4-level pwned password tree to form

    NOTE: For more information see:

        https://github.com/lcn2/pwned-pw-download

Exit codes:
     0         all OK
     2         -h and help string printed or -V and version string printed
     3         command line error
     4         bash version is too old
     5         cannot make some directory under topdir
     6         some curl command exited non-zero
     7         some dos2unix command exited nonzero, or dos2unix command not found
     8         some bzip2 command exited nonzero, or bzip2 command not found
 >= 10         internal error

pwned-pw-download version: 1.3.0 2025-12-03
```


## pwned password tree layout

The pwned password tree has 4 levels.  By default, files are of the form:

```
i/j/k/ikjxy.bz2
```

where i, j, k, x, y are UPPER CASE hex digits:

```
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

If you used `-b` to disable the use of `bzip2(1)`, then files will be of the form:

```
i/j/k/ikjxy
```

Each line of the file contains lines is of the form:

```
35-UPPER-CASE-HEX-digits, followed by a colon (":"), followed by an integer
```

For example, all pwned passwords with a SHA-1 that begin with `12345` will be found in:

```
1/2/3/12345
```

**NOTE**: The first 3 single SHA-1 HEX characters are duplicated in the 3 directory levels.



## Example: a line from 1/2/3/12345

The `1/2/3/12345` contains the following line:

```
00772720168B19640759677862AD5350374:4
```

The SHA-1 hash of the pwned password is the 1st 5 HEX digits from the file,
plus the 35 hex digits of the line before the colon (":").  Thus the
SHA-1 hash of the pwned password is:

```
1234500772720168B19640759677862AD5350374
```

The `4` after the colon (":") means that the given password has been pwned at
least 4 times and should **NOT** be used.


## Example: Lookup password in the tree

Consider the password:

```
password
```

The SHA-1 hash of "`password`" is:

```
5BAA61E4C9B93F3F0682250B6CF8331B7EE68FD8
```

Using the first 5 hex digits, open the file:

```
5/B/A/5BAA6.bz2
```

or if `-b` was used:

Look for the remaining 35 hex digits followed by a `:`:

```sh
bzgrep -F 1E4C9B93F3F0682250B6CF8331B7EE68FD8: 5/B/A/5BAA6.bz2
```

or if `-b` was used:

```sh
grep -F 1E4C9B93F3F0682250B6CF8331B7EE68FD8: 5/B/A/5BAA6
```

This will produce the line:

```
1E4C9B93F3F0682250B6CF8331B7EE68FD8:46658894
```

This indicates that the password "`password`", has been pwned at least 46658894 times!


## Integer pwned counts

The integer count (that follows the remaining 35 hex digits followed by a `:`) is
normally > 0.  In theory, any integer value might be found.  One should not
special case any integer value that is <= 0.

A 0 count could be a way that a password is declared pwned when
the number of instances found is unknown.  Moreover, counts <= 0
could be the result of an integer overflow in the part of the
creators of the pwned password dataset.

Therefore, the count should be regarded as informational, and is NOT
a means to determine if a given password is pwned.


# Reporting Security Issues

To report a security issue, please visit "[Reporting Security Issues](https://github.com/lcn2/pwned-pw-download/security/policy)".
