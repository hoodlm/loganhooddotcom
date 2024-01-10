---
layout: post
title: "Shellspec as a language-agnostic testing tool"
excerpt: "Shellspec is primarily branded as a unit testing framework for shell scripts, but it can do a lot more than that."
imgpath: "/assets/img/posts/shellspec"
---

[Shellspec](https://shellspec.info/) is primarily branded as "unit testing framework for shells", but it can do a lot more than that.

## The basic shell scripting test case

1. Call a shell script (or shell function)
2. Assert some things that "should" be true about the result

For example, I've written a shell script that is opinionated about the date formats it will accept:

```
> ./downloader.sh --date 1/9/2024
Error: Date must match YYYY-MM-DD format (e.g. 2023-01-19). Argument supplied was: "--date 1/9/2024"
```

I can write a shellspec test that covers this case:

```
Describe 'downloader.sh'
    It "Rejects malformed dates"
        When call ./downloader.sh --date "1/9/2024"
        The status should be failure
        The output should include "Date must match YYYY-MM-DD format"
    End
End
```

I can run the test in my terminal with a single command:

```
> shellspec
Running: /usr/bin/bash [bash 5.1.16(1)-release]

downloader.sh
  Rejects malformed dates

Finished in 0.16 seconds (user 0.16 seconds, sys 0.02 seconds)
1 examples, 0 failures
```

## Not just for testing shell scripts

Shellspec has access to anything I can do in my shell. In a "When call X" command, there's no rule that says that X must be a shell script.

Here are two other use-cases I've recently used shellspec for:

### (1) Compiler/interpreter invocation

I've been working on some toy projects with parsers, compilers, and interpreters.
While language processors can be challenging to test, one simplification is that these types of programs are pure functions.
Given a stream of characters (or a set of files) you get the same output, every time. This is actually a perfect fit for
the BDD-style blackbox testing that shellspec is geared toward.

"u" is the name of an interpreter for a toy programming language, also called u.

`5 + + + STDOUT` is a one-line program in u that computes 5 plus 3 and prints the result. What I want to test is:

1. Write that one-line program to a temp file.
1. Invoke my compiler with that temp file.
1. Assert that it compiles correctly
1. Assert that when the program is interpreted, it prints out '8'

The shellspec test:

```
It 'can increment a positive integer'
  echo "5 + + + STDOUT" > five_plus_three.u
  When call u five_plus_three.u
  The status should be success
  The stdout should eq '8'
End
```

It happens that I'm writing the u interpreter in Rust. I considered writing all my tests in Rust, too, instead of shellspec.
But it feels more right to be writing and running tests *outside* of the compiler itself, against actual source files.

One advantage is when I (inevitably) need to do a complete
rewrite, I have a portable test suite that I can easily point at a different compiler executable.
I could even rewrite u in C or Go or Ruby, and test the new implementation with the exact same test suite.

### (2) Network health check

Besides using shellspec to test specific software, I can also make assertions about my system.

I wrote a simple network health check shellspec test that pings cloudflare and example.org. ([source code](https://github.com/hoodlm/JunkDrawer/blob/41f38b071527c621b67d34f28b16ee92bd8d0898/readyornot/spec/network_spec.sh))

To simulate a network problem, I edited /etc/hosts/ so the hostname 'cloudflare.com' resolves to a bogus IPv4 address, then ran the tests:

```
> shellspec spec/network_spec.sh

Network health
  can ping:
    example.org via ipv4
    cloudflare.com via ipv4 (FAILED - 1)
    example.org via ipv6
    cloudflare.com via ipv6 (FAILED - 2)
  https connectivity:
    example.org via ipv4
    example.org via ipv6
```

When tests fail, they tell me exactly what commands it was running, and roughly what went wrong:

```
Examples:
  1) Network health can ping: cloudflare.com via ipv4
     When call ping -W2 -4 -c 1 cloudflare.com

     1.1) The output should include 1 packets transmitted, 1 received, 0% packet loss
     1.2) WARNING: It exits with status non-zero but not found expectation
            status: 1

  2) Network health can ping: cloudflare.com via ipv6
     When call ping -W2 -6 -c 1 cloudflare.com

     2.1) The output should include 1 packets transmitted, 1 received, 0% packet loss
     2.2) WARNING: It exits with status non-zero but not found expectation
            status: 2
     2.3) WARNING: There was output to stderr but not found expectation
            stderr: ping: cloudflare.com: Address family for hostname not supported

Finished in 2.71 seconds (user 0.29 seconds, sys 0.07 seconds)
6 examples, 2 failures
```

