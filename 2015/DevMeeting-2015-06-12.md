---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-06-12

Date: 2015/06/12 (Fri)
Time: 14:00- 19:00 (JST)
Place: Tokyo, Japan
Remote participation: Slack? Google hangout? Ask ko1.
Attendees: Assuming active developers. Sign up is required. Please ask ko1 for details.
Language: mostly Japanese (sorry for non native Japanese speakers)

Maybe this meeting will be discussion about Ruby 2.3 based on tickets.
Please add your favorite ticket numbers you want to ask to discuss.

* NOTE
  * Dev meeting *IS NOT* a decision making place. All decisions should be done at the bug tracker.
  * Dev meeting is a place we can ask Matz, nobu, nurse and other developers directly. Matz is very busy person, so we can ask him directly on this opportunity. If you can not attend there, then other attendees can ask instead you (if attendees can understand your issue).
  * We will write a log about discussion on a file or each tickets in English.
  * All of activities are best-effort (please remind that most of us are volunteer developers).
  * The date, time and place is when/where we can reserve Matz's time.

# Agenda

* NOTE: Write at least "ticket number/title/link" and your name. Explain details on the ticket. If you can not attend meeting, short summary are welcome because we can understand easily (long discussion is difficult to read, especially in non-native languages). Your motivation is also welcome.

## From Attendees

* example: [Feature #10917] Add GC.stat[:total_time] when GC profiling enabled (ko1)
* [Feature #5455] $SAFE should be removed(hsbt)
* [Feature #10730] Implement Array#bsearch_index(hsbt)
* [Feature #10017] Add `Hash#values_at!`(hsbt)
* [Feature #11220] strptime(%6N) (naruse)
* [Feature #11218] File.open FILE_SHARE_DELETE (naruse)
* [Feature #11216] inode for Windows (naruse)
* [Feature #11251] pthread_set_name_np (naruse)
* [Feature #11253] rb_io_modestr_oflags for Ruby API
* https://github.com/ruby/ruby/pull/920

## From non-attendees

(Additional explanation is welcome because we can't ask about it immediately)

* [Feature #7148] Improved Tempfile w/o DelegateClass (glass_saga)
  * Current Tempfile uses delegate. It brings some problems:
    * Tempfile#dup doesn't work
    * Finalizer performs unlink even if it has been duplicated
    * problems on IO.copy_stream when using src_offset.
  * To resolve these problems, we should make Tempfile a subclass of File.
  * Tempfile#dup should copy the temporary file.

# Log

Attendee: akr, hsbt, ko1, matz, naruse, nobu

# \[Feature [#7148](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/7148&sa=D&source=editors&ust=1686086420316632&usg=AOvVaw04IpXnttiOw_m9h0ZX0k21)\] Improved Tempfile w/o DelegateClass (glass\_saga)

matz: I have some issue on this proposal. Tempfile should not be a subclass of File. It is different concept from File. I’m okay to implement Tempfile without delegator. However, I’m not sure it implemeted with subclass of File.

File is a wrapper of fd. Tempfile is not only a wrapper but handle other information.

Action: Matz will reply this issue.

# \[Feature [#11218](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11218&sa=D&source=editors&ust=1686086420317787&usg=AOvVaw0KSke3_MK8esxsZUYJqlZy)\] File.open FILE\_SHARE\_DELETE (naruse)

naruse: (explain about this proposal). This proposal is only for Windows.

(discussion) It is simialr to Unix behavior, but not same.

ko1: What’s happen on unsupported platform?

naruse: just ignore it.

matz: How about providing Interger constant?

akr: How about larger than 32bit integer

akr: use O\_SHARE\_DELETE

matz: integer is preferrable, symbol is also ok

naruse: I need modestr2modeint for add the new integer flag (rb\_io\_modestr\_oflags)

Action: Discuss on ticket

# \[Feature [#11251](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/11251&sa=D&source=editors&ust=1686086420320036&usg=AOvVaw2BT9ptJ9dw8OOgeTtfybkc)\] pthread\_set\_name\_np (naruse)

ko1: Interface?

naruse: Thread#name, Thread#name=

akr: As a spec, this API specify Thread object’s name, setting pthread’s name is platform dependent.

matz: no idea. “name” is too general? -> no problem. Approved.

akr: show it in inspect

Action: naruse will implement it

# Fix String#+ when subclassed #920 (hsbt)

Action: reply on issue.

# \[Feature [#5455](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/5455&sa=D&source=editors&ust=1686086420322762&usg=AOvVaw39dNDxfnYfswP6nGQe0Ebv)\] $SAFE should be removed(hsbt)

matz: $2 and $3 can be removed.

Action: Matz will reply on ticket. hsbt-san will try implent.

# \[Feature [#10730](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10730&sa=D&source=editors&ust=1686086420324242&usg=AOvVaw31hryX1D9DbpuqAsLl_eN1)\] Implement Array#bsearch\_index(hsbt)

matz: go ahead.

Action: Matz will reply. nobu will merge.

# \[Feature [#10017](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/10017&sa=D&source=editors&ust=1686086420325348&usg=AOvVaw1yJRgzUVP2Sj7Vqk0jO5tq)\] Add Hash#values\_at!(hsbt)

matz: approved (Hash#fetch\_values).

Action: Matz will reply. Nobu will merge it.

# \[Feature #9108\] Hash sub-selections

Action: Matz will reply.

# Did you mean gem -> #11252

Action: continue to discuss how to implement (nobu)

# \[Feature #11215\] pack/unpack for (u)intptr\_t

Action: Matz will approve it.

# \[Feature #10769\] Negative counterpart to Enumerable#slice\_when

# \[Feature #11253\] rb\_io\_modestr\_oflags for Ruby API

matz: add keyword argument “flags”, which is OR-ed with 2nd argument mode.

\[Feature #11158\] Introduce a Symbol.count API as a more efficient alternative to Symbol.all\_symbols.size

naruse: If they use this for metrics, it should show 3 different count for each symbol types

akr: the number of existing symbols is implementation specific information. which should be in ObjectSpace.

# Next meeting:

Date: 7/28 14:00- (JST)
