---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2008-02-15

https://blade.ruby-lang.org/ruby-core/15585

This is a rough translation of the Japanese meeting summary
(see [ruby-dev:33825]) by NARUSE, Yui.

There was a Ruby M17N (multilingualization) meeting on 2008/02/15.

(attending: Matz, ko1, ark, nobu, naruse and duerst)

== Looking for a successor for cgi.rb
Conditions are as follows:
* different name than cgi.rb
* using MVC separation

== String#gsub(regexp, hash)
Proposal to allow the following syntax:
   String#gsub(regexp, {"&auml;"=>"\u00C4", ..})
-> accepted

== Information regarding "replica" information
-> write this down


== Difference between CP949 and GBK
-> They differ, separate

== Concatenating empty strings and with UTF-16
Proposal: empty strings can be concatenated with any string, even if
not ASCII Compatible
-> accepted

== Slowness of String#length, String#at,...
Speed up using search_nonascii
Speed up additionally for VALID UTF-8

== String#getbyte, String#setbyte
-> accepted

== Indexer
-> on hold

== about inspect
Consider adding obj.inspect_acumulate(enc)

== Direction for strftime
strftime is locale-independent
If and when a locale-dependent version becomes necessary,
it will be added separately.

== Introducing require_relative
Proposal to add require_relative, require'ing a file relative to the
file where it is used.
-> accepted

== IO.copy_stream
Proposal to introduce IO.copy_stream(filename, filename) and IO.copy_stream(stream, stream) to copy a file or a stream.
However, because there is a system call when using a filename,
Ruby can do something differently.
-> on hold, think about names

== Not sure about Hash#compare_by_identity
Would like to change to Hash#compared_by(:equal?, :obj_id)
-> accepted
-> Hash#identifed_by may be better (see [ruby-dev:33817])

== Unicode Normalization
Proposals for API:
* String#encode("utf-8 nfc")
* String#normalize("nfc")
* Encoding::UTF_8.nfc(str)
Because one might want to use Unicode Normalization e.g. when
reading a file, we also need need to consider what to do for example
for open(fn, "r:utf-8", ...).



#-#-#  Martin J. Du"rst, Assoc. Professor, Aoyama Gakuin University
#-#-#  http://www.sw.it.aoyama.ac.jp       mailto:duerst@it.aoyama.ac.jp
