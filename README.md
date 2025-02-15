ext4fuse 
======
This is a read-only implementation of ext4 for FUSE.  The main reason this
exists is to be able to read linux partitions from OSX.  However, it should
work on top of any FUSE implementation.  Linux and FreeBSD have been tested to
some point and I've heard that OpenSolaris should also work.

Write support will only come if I get the time, knowledge, patience and nerve
to support it.  Most of them I lack, so it's a long shot.  However, the fact
that ext4fuse is read-only also means that it's completely safe to use.

## Installation
### Intel Mac
If you use OS X I suggest you rely on the [homebrew project](http://mxcl.github.com/homebrew/).

Once you have homebrew installed, simply type the following two commands:

`$ brew cask install osxfuse`

`$ brew install ext4fuse`

At least on Leopard, you need to add your user to the operator group so you can
have readonly permissions to the disks.  Use this:

`$ sudo dscl . append /Groups/operator GroupMembership <your-user>`

Also, you will need to know the <device> name of your ext4 partition.  Take a
look at the Mac Disk Utility.  It should be something _like_ `/dev/disk0s5`.

### M1 Mac
Download the release version.

### FreeBSD 
Simply install it through the ports tree:

`$ cd /usr/ports/sysutils/fusefs-ext4fuse && make install clean`

Remember that you need the fuse module loaded.  In my experience it doesn't
load automatically, but then again, I have nearly zero experience with FreeBSD.

### Compiling from source
If you prefer bleeding edge, get the source, untar it and compile using:

`$ make`

or in case you are on FreeBSD:

`$ gmake`

You need to have pkg-config for the compilation to work as well as the FUSE
kernel module.  For OSX you should use fuse4x (notice that fuse4x is also
available via `brew install`).

## Mounting 
You can mount a filesystem like this:

`$ ext4fuse <device> <mountpoint>`

If you compiled from source, and you haven't manually installed `ext4fuse` in
your $PATH, go to the directory where you did the compilation and run this

`$ ./ext4fuse <device> <mountpoint>`

The <device> should be the partition device and the <mountpoint> is the
directory where you want to mount your partition.

> On macOS Sierra (10.12) or later, when mounting a filesystem with `sudo`, you need to add the option `-o allow_other` to allow non-root accounts access to the mount. See [this issue](https://github.com/gerard/ext4fuse/issues/36) for details.

## Reporting bugs 
If you notice a problem, please file a [bug report](http://github.com/gerard/ext4fuse/issues).

If you have a reproducible problem the easiest for debugging is to share the
filesystem.  First of all, umount the partition, then you can create a backup
like this:

`$ dd if=<device> bs=64K | gzip -c > filesystem.backup.gz`

Then, just upload the .gz file somewhere.

However, I understand that you generally do *not* want to do that.  In that
case you can also generate a log file.  Notice that the log file still contains
the directory listings.

To get a logfile, you can run ext4fuse like this:

`$ ext4fuse <device> <mountpoint> -o logfile=/dev/stdout`

If you do not want to share the logfile, another option is to provide a
backtrace with gdb or a coredump (a coredump might contain file data).

Finally, you can always drop a mail:
  * gerard.lledo@gmail.com

## Limitations
 * All code is religiously Little Endian only.  If you don't know what this
   means, you are probably OK (ie, you are using an intel or amd cpu).  The
   code should be better tested on x86-64, you should not be using anything
   else on modern hardware anyway.
 * Block numbers over 32 bits aren't supported.  You hit those when you reach
   around the terabyte, and I don't have any way to test that.  It should be
   quite easy to fix, but I don't feel like spending time on something that
   neither has a use for me or can be proved to be correct.  I don't have such
   big disks :P.
