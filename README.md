## behavior

1. C style comment that span multi-line or occupy entire line gets zeroed out.
2. C style comment in the middle of a line remain unchanged. eg, `void init(/* do initialization */) {...}`
3. C++ style comment that occupy entire line gets zeroed out.
4. C string literal being respected, via checking `"` and `\"`.
5. handles line-continuation. If previous line ending with `\`, current line is part of previous line.
6. line number remain the same. Zeroed out lines or part of line become empty.

## testing & profiling

I tested with two largest cpython source code that contains many comments. Below is sorted output of:

    cd ~/code/cpython && wc $(find . -regex '.*\.[hc]\(pp\)?') | sort -g
     14075    38036   370246 ./Modules/posixmodule.c
     15702    47118   465001 ./Objects/unicodeobject.c
     28377   421265  2100240 ./Modules/unicodename_db.h
    556670  2125917 19079596 total     3.8.0a0 2018-11-30

in this case it do the job **correctly** and fast, **2-5 faster** than gcc

    time gcc -fpreprocessed -dD -E Modules/unicodeobject.c > res.c 2>/dev/null
        # https://stackoverflow.com/questions/2394017/remove-comments-from-c-c-code
    time ./removeccomments < Modules/unicodeobject.c > result.c
## install

    gcc -O3 removeccomments.c -o dest_path_name
## usage

    /path/to/removeccomments < input_file > output_file
## summary

- this program correctly remove comments in C source code, handling C string
literal and line-continuation.

- do not add or delete lines, only empty lines that are comment. Output of `diff old new` is minimal.
