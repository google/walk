`walk` and `sor`
================

This repository contains `walk` and `sor`, two utility programs that
collectively replace [`find`][find]. `walk` recursively walks the directories
specified on the command line (or the current directory, if none is specified),
printing each file path. `sor` (“shell or”) reads file paths from standard
input; for each path, it evaluates its arguments as Bash snippets, passing the
path as an argument to each and printing the path if any snippet exits with
status 0. For example, instead of saying

    find . -type f -name \*foo\*

you can say

    walk | grep foo | sor 'test -f'

If your filenames might contain newlines, you can say

    walk -0 | grep -z foo | sor -0 'test -f'

[find]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/find.html


Performance
-----------

By avoiding syscalls, `walk` achieves substantially better performance than
`find`. A microbenchmark –

    $ time find /usr >/dev/null
    
    real    0m3.542s
    user    0m0.880s
    sys     0m2.646s
    $ time walk /usr >/dev/null
    
    real    0m2.311s
    user    0m0.370s
    sys     0m1.926s

– shows `walk` executing nearly 40% faster on a local file system with a hot
cache. Performance on network file systems should be even better. On the other
hand, `find` implements its predicates in-process, making them orders of
magnitude faster than `sor`:

    $ time find /usr -type f >/dev/null
    
    real    0m3.464s
    user    0m0.831s
    sys     0m2.615s
    $ time walk /usr | sor 'test -f' >/dev/null
    
    real    7m40.127s
    user    1m47.818s
    sys     2m48.595s


History
-------

`walk` and `sor` were originally written for [Plan 9 from Bell Labs][] by
[Dan Cross][]. The [original source][] is available.

[Dan Cross]: http://pub.gajendra.net/about
[Plan 9 from Bell Labs]: https://web.archive.org/web/20170601064029/http://plan9.bell-labs.com/plan9/index.html
[original source]: https://web.archive.org/web/http://plan9.bell-labs.com/sources/contrib/cross/


Disclaimer
----------

This is not an official Google product.
