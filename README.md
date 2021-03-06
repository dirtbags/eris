About
=====

Eris is an HTTP/1.1 web server with CGI.
It is very fast,
uses very little memory,
and has a tiny codebase which has been audited at least twice by gray-hat hackers.
It relies on an external program,
such as tcpserver or netcat,
to handle networking,
and expects to be run once for every connection.
This may sound horrible,
but it's actually pretty quick,
especially since this architecture makes it trivial to use the
`sendfile` call in Linux
(added in 2003 as a reaction to Apache losing to IIS in benchmarks),
which sends an open file directly to a socket,
without having to context switch into userspace.

It conforms to the parts of HTTP/1.1 that appear to be important to clients.
I have not found any HTTP/1.1 clients which have trouble with Eris,
including curl, which breaks with fnord (the reason I began work on Eris).

Eris HTTPD is a part of [Dirtbags Capture The Flag](http://dirtbags.net/ctf/).
After many patches against [fnord 1.10](http://www.fefe.de/),
I decided to fork fnord into a new project.
Fnord's author approved of the fork.


Differences with fnord
======================

Significant differences between eris and fnord are:

* command-line arguments instead of compile-time defines
* eliminated use of libowfat
* no build dependency of dietlibc
* elimination of "old style symlink handling"
* elimination of user switching (you can use tcpserver -[ug])
* elimination of chroot code (you can use chroot)
* several bugfixes (sent to the fnord mail list)
* ignores Accept header (fnord also ignores it, but claims not to)

Usage
=====

Start with:

	tcpserver -v -RHl localhost -u 1234 -g 1234 0 80 ./eris

There are many other ways to start eris.
For example, you can run an HTTPS server with stunnel.

You just need something that launches eris with stdin and stdout connected to the client.


Logging
-------

Will log to stderr in the form

	127.0.0.1 200 23 localhost Links_(0.96;_Unix) none /index.html

where 127.0.0.1 is the client IP, 200 is the HTTP exit code,
23 is the size of the content that was served (or 0 for unsuccessful exit codes),
localhost is the Host: header (the virtual host),
the next token is the user agent with spaces replaced by underscores,
the next token (none) is the Referer HTTP header or "none" if none was given,
and the rest of each line is the decoded requested URL.


Features
--------

eris does simple virtual hosting.  If the `Host:` HTTP header is there,
eris will try to chdir to a directory of that name, i.e. if the client
asks for "/" on host "www.fefe.de", eris will look for
"www.fefe.de/index.html".  Eris will also try the directory "default"
if no specific directory for the virtual host was there.

eris implements el-cheapo HTTP ranges (only byte ranges and only of the
form x-y, not multiple ranges).

eris will change dots at the start of file or directory names to colons
in the query before trying to answer them.

eris understands and implements keep-alive connections.

eris will use sendfile on Linux to enable zero-copy TCP.

If eris is given the -c option, it will regard files
whose names end with ".cgi" as CGI programs and try to execute them.
Please see <http://hoohoo.ncsa.uiuc.edu/cgi/interface.html> for the CGI specification.


About The Name
==============

[Eris](http://en.wikipedia.org/wiki/Eris_%28dwarf_planet%29)
is the most massive (heaviest) dwarf planet in the solar system.
It's heavier than Pluto!

Its discovery is what spurred astronomers to finally define the word "planet",
which resulted in Pluto being classified a dwarf planet.
Otherwise, we would have to count Eris as a tenth planet.
