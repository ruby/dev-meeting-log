---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2014-07-26

Date: 2014/07/26
Time: 13:00 -
Place:
Attendees: sign up required: http://cruby.doorkeeper.jp/events/12641
summary: [[DevelopersMeeting20140726JapanSummary]]
log: https://docs.google.com/document/d/1g2mu2qngyxw3ge-EbDGLsDDZR-Uj_6fWaVb0YPw2Pls/pub

## Agenda

* [Feature #7797] Hash should be renamed to StrictHash and a new Hash should be created to behave like AS HashWithIndifferentAccess
* [Feature #9980] Create HashWithIndiferentAccess using new syntax {a: 1}i
* [Feature #9064] Add support for packages, like in Java
* [Feature #8631] Add a new method to ERB to allow assigning the local variables from a hash
* [Feature #8543] rb_iseq_load
* [Feature #10084] Add Unicode String Normalization to String class
* [Feature #10085] Add non-ASCII case conversion to String#upcase/downcase/swapcase/capitalize

## Agenda (without slide)
* [Feature #4276] Allow use of quotes in symbol syntactic sugar for hashes
* [Feature #9924] Revisitting GC.stat keys
* [Feature #9781] Feature Proposal: Method#super_method
* [Feature #10095] Object#as

# Log

- attendee: ko1, sora\_h, akr, naruse, knu, duerst
- on-line: matz, zzak, nobu, kosaki

# \[Feature [#7797](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7797&sa=D&source=editors&ust=1686086131112226&usg=AOvVaw3gihUSX1OdVrT5B-OVwRa5)\] Hash should be renamed to StrictHash and a new Hash should be created to behave like AS HashWithIndifferentAccess

matz: I have to reject, since it breaks compatibility

→ reject

# \[Feature [#9980](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9980&sa=D&source=editors&ust=1686086131112605&usg=AOvVaw3xNgkwD5Y-YVF7_-RZFtcK)\] Create HashWithIndiferentAccess using new syntax {a: 1}i

matz: Suffix \`i\` is used for complex (imaginary) numbers. It's bit confusing.

Maybe what you want can be gained by shorter/nicer name for Hash#compare\_by\_identity.

→ reject

# \[Feature [#9064](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9064&sa=D&source=editors&ust=1686086131112999&usg=AOvVaw1jx5r59gdhnGjW_oH_XUzs)\] Add support for packages, like in Java

Action: Matz  to write a few questions back to the proposer

# \[Feature [#8631](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8631&sa=D&source=editors&ust=1686086131113269&usg=AOvVaw0BGKRsEb44xaPMCdRBFF6Q)\] Add a new method to ERB to allow assigning the local variables from a hash

Now it can be implemnted with Pure Ruby.

If hash is given, what will be \`self\` of ERB?

# \[Bug [#8543](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8543&sa=D&source=editors&ust=1686086131113575&usg=AOvVaw0xEtH6LgLA9lRl6vwQ8Wq1)\] rb\_iseq\_load

Modify it (best effort).

# \[Feature [#10084](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10084&sa=D&source=editors&ust=1686086131113839&usg=AOvVaw0sGJKzuRX2SiIYpdqdUvl9)\] Add Unicode String Normalization to String class

Proposed method names by Matz: unicode\_normalize or normalize\_kd,... (not too short)

How to deal with non-Unicode encodings: Matz: raise Exception

Other than UTF-8: UTF8-Mac: return type should be UTF-8, or deal with it as legacy (not really Unicode). UTF8-DoCoMo,..? Yui should decide. UTF-16/32: Needed data,... differs by whether implementation is internal ( C) or pure Ruby.

Todo (for eprun): measure load time, compare with unf, avoid Module Normalize

require “unicode\_normalize”

method name: String#unicode\_normalize(form)

form: :nfc, :nfd, :nfkc, :nfkd

encodng: UTF-32BE/LE, UTF-16BE/LE, UTF-8

allow UTF8-MAC is confusing.

# \[Feature [#10085](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10085&sa=D&source=editors&ust=1686086131114630&usg=AOvVaw0DntBguD8DzWXY6sgnTyof)\] Add non-ASCII case conversion to String#upcase/downcase/swapcase/capitalize

# \[Feature [#4276](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/4276&sa=D&source=editors&ust=1686086131114903&usg=AOvVaw126K2l6fNEaYEZmoqBerAA)\] Allow use of quotes in symbol syntactic sugar for hashes

Compare with JSON, it is ambiguous (String or Symbol).

# \[Feature [#9924](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9924&sa=D&source=editors&ust=1686086131115145&usg=AOvVaw2BmXaOlNZX8coghQwGlnqV)\] Revisitting GC.stat keys

no objection.

approval.

# \[Feature [#9781](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/9781&sa=D&source=editors&ust=1686086131115450&usg=AOvVaw3vQkbgM2ZzdRmmroeuFmT6)\] Feature Proposal: Method#super\_method

super: ambiguous which is calling method or getting method object of super.

\-> “super\_method”

# \[Feature [#10095](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10095&sa=D&source=editors&ust=1686086131115930&usg=AOvVaw1GX7O21O2KRcZDlvKl_FN4)\] Object#as

many bike shedding.

current workaround:

(1+2+3+4).tap { |x| break x \*\* 2 }

(1 + 2 + 3 + 4).tap{|x| next x \*\* 2}

bike shed discussion

(1 + 2 + 3 + 4).with{|x| x \*\* 2}

alias as instance\_exec

with(1 + 2 + 3 + 4){|x| x \*\* 2}

(1 + 2 + 3 + 4).as -> {|x| x \*\* 2}

(1 + 2 + 3 + 4).and {|x| x \*\* 2}

p (1 + 2 + 3 + 4).next{|x| x \*\* 2}

.once

(1+2+3\*+4).break{|x| x\*\*2}

(1 + 2 + 3 + 4).pat{|x| x \*\* 2}

pass

continue

(1 + 2 + 3 + 4).to{|x| x \*\* 2}

(1 + 2 + 3 + 4).tobe{|x| x \*\* 2}

(1 + 2 + 3 + 4).then{|x| x \*\* 2}

reverse\_call

(1 + 2 + 3 + 4).\_{|x| x \*\* 2}

(1 + 2 + 3 + 4) -> {|x| x \*\* 2}

(1 + 2 + 3 + 4).do{|x| x \*\* 2}

(1 + 2 + 3 + 4).・{|x| x \*\* 2}

(1+2+3+4).bind {|x| x \*\* 2 }

tappp

(1 + 2 + 3 + 4).return{|x|  x \*\* 2}.return{|x| x + 2}

(1 + 2 + 3 + 4).pass {|x|  x \*\* 2}

(1 + 2 + 3 + 4) => {|x|  x \*\* 2} => {|x| x + 2}

(1+2+3+4).= {|x| x \*\* 2 }

…

not concluded.
