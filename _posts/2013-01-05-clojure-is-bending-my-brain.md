---
layout: post
title: Clojure Is Bending My Brain
show_title: true
tags: tech clojure
---

My first foray into the world of functional programming is going to be
[Clojure](http://clojure.org/) (pronounced "closure").

This Lisp derivative is not a purely functional language like Haskell or
standard Lisp.  It is built on top of the Java Virtual Machine (JVM) which
gives it a wide deployment reach, and has some conveniences that make it easier
to get into for OO heads like me.

Functional programming is a REALLY different way of viewing the problem of how
to tell a computer what to do. I am seeing some value from learning about
functional programming already. Lambdas, for example, make a whole lot more
sense to me now. It solves problems of complexity and concurrency that are
difficult in object-oriented systems.

I'm looking forward to learning more about it through
[Programming Clojure](http://www.amazon.com/Programming-Clojure-Stuart-Halloway/dp/1934356867/)
and will post anything I find interesting.

My entire career has been spent honing my object-oriented programming skills.
When I first started out, I taught myself about relational data. Then I moved
to imperative programming, creating data-driven websites with classic asp and
vbscript. (ugh). I quickly figured out that sucked and moved into Java.  That's
where my love affair with object-oriented programming was born.  I cut my teeth
with Jrun after a brief detour into CF.

I love OO. It provides a fantastic abstraction that has allowed me to think
about software in concrete terms that are somewhat relatable to my clients. I
spent years doing it in C#. And now I'm doing it in Ruby.

To give you an idea how different the implementation is between a
statically-typed OO language and a functional language, check this out:

### Example
#### Java
    public class StringUtils {
      public static boolean isBlank(String str) {
        int strLen;
        if (str == null || (strLen = str.length()) == 0) {
          return true;
        }
        for (int i = 0; i < strLen; i++) {
          if ((Character.isWhitespace(str.charAt(i)) == false)) {
            return false;
          }
        }
        return true;
      }
    }

#### Clojure
    (defn blank? [str]
      (every? #(Character/isWhitespace %) str))

Expressive and succinct. I am intrigued.
