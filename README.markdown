NAME
====

perl-systemtap-toolkit - Real-time analyzing and diagnosing tools for perl 5 based on [SystemTap](http://sourceware.org/systemtap/wiki)

Status
======

These scripts are considered experimental.

Prerequisites
=============

You need the systemtap 2.3+ and at least perl 5.6.1+ to run these tools on your Linux system.

Also, you should ensure the (DWARF) debuginfo of your perl binary being analyzed is already enabled (or installed separately).

If you compile perl from source, then ensure you pass the `-Doptimize='-g -O2'` option to the `Configure` command line. For example,

    cd /tmp
    wget http://www.cpan.org/src/5.0/perl-5.16.3.tar.gz
    tar -xzvf perl-5.16.3.tar.gz

    cd perl-5.16.3/
    rm -f config.sh
    sh Configure -Doptimize='-g -O2' -Dprefix=/opt/perl -des
    make -j9
    sudo make install

    /opt/perl/bin/perl -v

If you are on Linux kernels older than 3.5, then you may have to apply the [utrace patch](http://sourceware.org/systemtap/wiki/utrace) (if not yet) to your kernel to get
user-space tracing support for your systemtap installation. But if you are using Linux distributions in the RedHat family (like RHEL, CentOS, and Fedora), then your old kernel should already has the utrace patch applied.

The mainstream Linux kernel 3.5+ does have support for the uprobes API for userspace tracing.

Permissions
===========

Running systemtap-based tools requires special user permissions. To prevent running
these tools with the root account,
you can add your own (non-root) account name to the `stapusr` and `staprun` user groups.
But if the user account running the perl process is different from your current
user account, then you will still be required to run "sudo" or other means to run these tools
with root access.

Tools
=====

pl-sample-bt
------------

This script can be used to sample Perl-land backtraces in a running perl process specified by the `-p` option.

It outputs the aggregated backgraces (by count). For example,
to sample a running perl process (whose pid is 737) in user space only for total 5 seconds:

    $ ./pl-sample-bt -p 737 -t 5 > a.bt
    WARNING: Sampling 737 (/opt/perl514/bin/perl) for Perl-land backtraces...
    Please wait for 4 seconds.

The resulting output file `a.bt` can then be used to generate a Flame Graph by using Brendan Gregg's [FlameGraph tools](https://github.com/brendangregg/FlameGraph):

    stackcollapse-stap.pl a.bt > a.cbt
    flamegraph.pl a.cbt > a.svg

where both the `stackcollapse-stap.pl` and `flamegraph.pl` are from the FlameGraph toolkit.
If everything goes right, you can now use your web browser to open the `a.svg` file.

Below is some sample Perl-land flamegraph

http://agentzh.org/misc/flamegraph/perl-silly.svg

http://agentzh.org/misc/flamegraph/perl-test-nginx-socket.svg

We can see how the CPU time is distributed among all the Perl code paths quantitatively.

This tool has been tested with perl 5.18.0, 5.16.3, 5.14.4, 5.12.5, and 5.10.1.

Perl builds with ithreads enabled (i.e., passing the `-Dusethreads` option to perl's `Config` script) are not supported yet.

Bugs and Patches
================

Please submit bug reports, wishlists, or patches by creating a ticket on the [GitHub Issue Tracker](http://github.com/agentzh/perl-systemtap-toolkit/issues).

TODO
====

Author
======

Yichun "agentzh" Zhang (章亦春), OpenResty Inc.

Copyright & License
===================

This project is licenced under the BSD license.

Copyright (C) 2013 by Yichun Zhang (agentzh) <agentzh@gmail.com>, OpenResty Inc.

All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions
are met:

* Redistributions of source code must retain the above copyright
notice, this list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright
notice, this list of conditions and the following disclaimer in the
documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
"AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED
TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING
NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

See Also
========
* SystemTap Wiki Home: http://sourceware.org/systemtap/wiki
* Nginx Systemtap Toolkit: https://github.com/agentzh/nginx-systemtap-toolkit
