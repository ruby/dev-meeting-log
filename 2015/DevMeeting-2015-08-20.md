---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2015-08-20

Date: 2015/08/20 (Tue)
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
* did you mean gem (nishijima)
* Progress report about compilation (ko1)

## From non-attendees

(Additional explanation is welcome because we can't ask about it immediately)

# Log

Attendee: akr, ayumin, hsbt, ko1, matz, naruse, nobu, kosaki, usa, yuki, amatsuda

# did\_you\_mean gem (Yuki Nishijima)

- Naming: did\_you\_mean or correctable?

- did\_you\_mean is acceptable, but it’s determined by what feature set is bundled.

- Bundled gem or stdlib? It's going to be reuiqred in prelude (Yuki still doesn't know what it is)

- Bundled gem
- It’ll be required when a Ruby process spins up
- Rescue LoadError and just ignore when it doesn’t exist

- Ability to skip a correction attempt when raised within a certain method
- Completely disable the feature for apps running in production (env flag? method that does it?)

- Why want to disable in production? -> Because it makes rails slow.
- Why slow? -> Rails parses the message of NameError exception for rails’ autoload feature.

- \`time ruby --disable-gem -e1\`: 0.061s
- \`time ruby -e1\`: 0.083s
- \`time ruby -rdid\_you\_mean -e1\`: 0.109s
- (ruby 2.2.2p95 (2015-04-13 revision 50295) \[x86\_64-darwin14\])
- Like --disable-gem, we can extend --disable-did\_you\_mean

- \-> We should change Rails, and we should offer a new API to take the qualified name of the Class/Module.
- Current Rails parses error message like “NameError: undefined local variable or method \`foo' for main:Object”

- Use NameError#name
- NameError#name doesn’t include parent modeule names which is included in the error message. We need a new API including this.
- How about adding a new method that returns the binding?

- More succinct message.  Delete two empty line and indent.

% ruby -rdid\_you\_mean -e '
class C
 def bar
 end
end
C.new.baz
'
\-e:6:in \`<main>': undefined method \`baz' for #<C:0x007f790863e880> (NoMethodError)

   Did you mean? #bar

zsh: exit 1     /home/akr/alias/ruby -rdid\_you\_mean -e

% ocaml
       OCaml version 4.01.0

\# let bar = 1;;
val bar : int = 1
\# baz;;
Error: Unbound value baz
Did you mean bar?
\# let bas = 2;;
val bas : int = 2
\# baz;;
Error: Unbound value baz
Did you mean bas or bar?
\# let bat = 3;;
val bat : int = 3
\# baz;;
Error: Unbound value baz
Did you mean bas, bat or bar?

#

empty line is not needed because lines of display is essential resource. indent is ok.![](https://lh3.googleusercontent.com/u/0/docs/ADP-6oF7tdaMoxgen41AqnhLTq_QKWktTk1u1GmLWuGA2z4Q77CrGsjyRQ95MeO7MskX-oSaMVVyg8nZ3bo285W10V2W60NwnmOQdJPLzXL5lNfiSdGPRt7HlIDQv0L0IhOcdq3HstJKYh_8CD0ec0aV00r9i4FIwxxFBalRbvTybYOf9Qnk2vFURvH1buwyh571T7gz7cWKFrJmxlcMvlrmQFOeD72h4L9r2Wqq9CI0fpd6MCj1yy4dH_2mRwNE7X2h_NLWShCuN1C49MlVKp-gHJx1vIYeew_T72B4j4KjcJ0qV3y1FgcdDRZGKx86kdLJnOlTY3x0aGQbwGmFx1xLW8Gnomc2SuJVgVK7QU7kRLxLot1an3sCuJK40t_Tk73RowgNqHyIvPlnhUnczCYAlCuqFNz36c0ZjOAaadObmY_2ioxvkjYMKeifbJdZAENVpO5vV62Q3lktN-wurZmNPJPCoGReAZjt1xPPlqM9RRbvtXm83bfS3eBzwk90YPPZ4WyfMo0mHn65KSC4akl6rpnOtvekcLByTHUrwvHI1-riEUOX5fYDcTr2260XIRJ73JcNFo9iXk_7eRUEjw--MOxqNAOoCYTVZio77HNvbgmiiSDrnqXSirE4BsxXMeMHZcmjz9IoOfxXG8UR-XojxHjQqnopDhUMNogV1sZo6eXglemuzwixzipuhZ-5ZLCsUUVrEexXFxm_opigiDckjmncqKmZSeyMvuZZDHqP7G5up3iQBr2ryCl6TawbdHp99WfzHv02iA1SVmqkAYOKDuAIsRJ2qijk2Y4qDLAO-06OHeClee2QdzcHvLU-0lioqZ-cOulO-Mv0V75rjqTyps2KuZclZA9_eveofzywoq7aIieEv-eyB4yPGBP0zQ7lqHOIg2vnjAIrOcz88_GaMlGDsWkKfOm2PbhrP_dVNrzO8fo)![](https://lh3.googleusercontent.com/u/0/docs/ADP-6oEf0fkz-Tv5WxSLoLo9niJhElX2LQyDfH8Ea_aov4ELIplo448Ycwm98UY-njXuuq57Tf81SLH3GJxfj2MVPAUbncoFRB9qGtmlGDUim_WkhQapOAX8QQwoQCv_EuSnDEq6WP7Fm9C7CF3bXLSpBI8pTWLQUYOLg5EybGqy0igtPIP96ebTVdV1ds82oS7tIlUC56aSZ8WU9edOl-xKe_cslK37tNpxOOm03yJ7v3YFTzab6rN2fxcAj5yjDJZrrHhTBqewKq2UQiWd3aK6Kc5O0wV47GcXIgrCFU-CxbzVRMp9sGqUAdP-0-hoao_BRwZYQaO8dIPGu9W7vQS_Co_1E_-xlXrRqDIl5cUa-Pf_iI1YkuT-QizaGeaKqqtqAq-4-_7wLczstt0fB1f0zZMllZJYyYI06d0JP7wreRTM4zoCSnWg7E0QG6vkLtB5glubVP4WcW6n-2M9SmaNXgzda5uEZ9Xbfan1y_OCENGMR4E3Iah6V-6eEH6ksIArwl3SDWMKIN_hisyKiLEBU-nkUtZLcwwWPASw7C1DUlsFyi8BVMxaJ8SWglLlyJ03HTwERqmwLc57NcA0RPxGMla9n7Ydt-D1Dskhk9iOnBKT1LIVhPKWFG8HhPbRr1AQi5XpABITR_DDV9vFVvLNYVZ2bkpsS9OabG1CwQtjGNM7FU6aSwsqqBgBNRJL8ny1x6tCp5SaSu1lFfdqjvkrvaWDwacZzA9LxysFXJNcUKm8xL7GycFaxPliwQdupgQeVK6gS7wU8PlzwePvWn0lYkz-thpnNj9QLulMEqRq2lm-uquG3AWp-JhtoYkOIec2cniOimSFiocqOI-UkpF57Zy6RKaKnKA3cbF4geRquzWq4PasCTGgfuSQu-8powqb1F_UL7YeOeNW95wt_sCY0f9XjbbJ_eE0FbC2mLga_Ufx72c)![](https://lh3.googleusercontent.com/u/0/docs/ADP-6oHRfyt64DIBJtrc0SLLT9JyIAkouSejceJjixoWAupyY6LGpyHKcnQyuKntjY4-aSecpYo0E4Nhu5tmBwBQwvcQ4RBOgG4VYSVfq7mQkwTBajIXQVsD59aW1ktSKF_U39iDc9qn1LM2sne44-DhAf-qm6JsTVGvJmwo9DX_jLhqga_odKFCOBVPGyBLd0A2toCPJMdMBCRO64KdK54fYugBAA1GKNu2XevQgJ0SfOSJHUgAyStc3nsD3grRqvN_WhyUhc-M0F1mxsqgCPle3nAC3uNdcvrGex3wZJw1cLOu4CnUpUdcRtFirg7CfFOBiI8mZvxkN7X89ETZpK9xFB8PuqqWKCnB3WseSPw-3prt5vxCsRxcfVClI-xN7HId9GmIf565Rd4WYuUkyyjNozAy93IdetotJlxzuwZ_Ido6hznpE9jmJHI2IGPbpacu53oqY-vhzHOrQTpuVUF8H_jeF20_2IiXqoGXfe8Ah-2S6hHbVIfgdkZZGQZGFv7ntA3m9YdLD5yYezwDN_7fwgk8ieZaTgtwcZ-8tdN_D3LxDmCArRfVez5EeII6wsf8EhDVuvQI487Nd97WXNsgYd8ZcSzHNGQJgGeYTT__74cfR0PC25D_F0c8mWuRC86djYMgXmVOFKAOn5l9XIm71QAbqBWCI4uGmvuMNNb_-8wAvmO2Bp8VFlsdhLs7UGrXjh_DXQU14CZy4xZb49TMHcfLj5m6s8cTV5Qi2Lgntc3-ZgyAKK7dytICRUMpWodXBmZLc-jKjWEWQ2tcnpcux01Bdm8ZvXT9Qm5HoKcBnClf0WjR9cXh-eFI9xcHdHjj6lH07Mes2u4SautsGR7hbcMrDXy6zYD1eR6RbaeLKKMmlbHpn1PmFaYCcmcOuzaD1I2nNCfOwPupf5YgbgvL7LocJTOQTsOZdY9z-gKRrEkpbDc)

![](https://lh3.googleusercontent.com/u/0/docs/ADP-6oFGa6JYa350mLJKKl0GJxIw6r4m0pJFBf0tQQ456lHTr83Wp7PzakpSkS13WXoEomZa3kxOuD3duu4gKoodRgygK8HxyFY6qJ9A7bHNdIgNBaiOGx9RbkEGf2Jf9kfRAKKbAAcdeK0RV9R2ojpATY9V983ThdGDwcQf7mMzaEaVXuxZuhc3inaTHnKvSq6IB8Njb7tho60Ce9QLXLCXQusRSAVN-1htiqvQ52oBXT6tnYuCDm7sGxMO4JQ8JWed9O4e0Ol09KKZfrvU_rQb6zthFYRNysbSYuoEi1i6Yn3z2OhVXLzau_rAgxqFnNuqHpWdVOXPSJQPHGi6VWqiQASKESmkyqnetNrFmhw_I2Y1ln4kjqT-A8j82lN1GNoRNA30m-jkRwo37hq0CR-J_dJ1cKg21E4IeREyLc1U4SkSqWaV80UeVOqdWmURnli0IkQT-JwZrzY_iprshOs5V98vU9XCKyDC3RLqVMJcwAe_1BqLfJCwRFDFKEvIFwCsBmuNRDVt18rPGWWNArAXbTOXPwxGBvraHHI052pVX6SH_aQizUoBC5JxHph6ogrN-jrwuFHO6UOm7YBT6rlbErwxyiD3_rAg7KaLCjaKizVcdDL80p76U4x-DyQN6i1u4Nm5bFnF25npEtI7VST3FWEU123hhgwbNl7VUorLAYpC5bIO2E-S2kOKwXbsn4NTfDKWOZdCEticvHMkqoZeBASzPntN4gxANVJNXa9iGQlGH76lfw_baxO00WBk9rQOgYkjlCdii9OV6_uabXfqo94Ix3mNuDjRIC6GgJaoZaPxsja2S2qp5KOqGT8_ZaPUPhkYEOUuhsziAZp0JD8fd0DrVjbc39AVNv1-i5gqW1uQc67jfL8s010T1LfcCLrRY5ZtFxGWnJa5bwmgVf60BWftog2Ze77ful3k-Oc4NfqlUD0)

- How to disable this?

- Use \`--disable=’ commandline option.
- Need to implement API in ruby level to take what features are disabled.

- global method? global variable? constant?

- NameError for constant reference

- module M
     A::B::C
    end
- failed to refrence A -> needs binding and the name :A.
- failed to refernce B -> needs A and the name :B.
- failed to refrence C -> needs B and the name :C.

# Release Schedule

[https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23](https://www.google.com/url?q=https://bugs.ruby-lang.org/projects/ruby-trunk/wiki/ReleaseEngineering23&sa=D&source=editors&ust=1686086477052466&usg=AOvVaw3hwP5YZeC2T7g7wUaS3P6l)

# Subversion Repository Migration

current machine is old debian.

Migration is required before next Spring.

Options:

- Migrate to new Subversion server (on AWS)

- Good: no changes.

- Migrate to Git server ( on AWS)

- related migrations are required.

- GitHub

- Term of service; GitHub Issues are not portable

- Partial migration (use git for HEAD and continue to use SVN for maintaining branches)

Problem: If we migrate to git, we need to migrate SVN related tools (auto date update, etc)

# Magic Comment for Frozen String Literal by default

Default of the status of String Literal will be frozen from Ruby 3.0.

- it has been rejected before 2.2 by matz
- it has been rejected again early 2.3 development by matz
- amatsuda: tired of seeing pull requests adding “..”.freeze here and there. This is ugly.
- amatsuda: please reconsider akr’s magic comment solution \[[https://bugs.ruby-lang.org/issues/8976](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8976&sa=D&source=editors&ust=1686086477055496&usg=AOvVaw3BxWR-US2T9liIUms8wscS)\]
- matz: accepted. As a migration path, magic comment is reasonable. naming is still problem.

- command line option to frozen string literal by default

- It is acceptable but can’t be a migration path

- Ruby 3.0 will interpret string literal frozen by default.

- akr’s challenge. [https://github.com/akr/ruby/tree/frozen-string](https://www.google.com/url?q=https://github.com/akr/ruby/tree/frozen-string&sa=D&source=editors&ust=1686086477056841&usg=AOvVaw1Q5t0Y08z5plqxrl9Xz1Gp)
- String.new(“...”) is preferred over “...”.dup.
- magic comment candidates

- freeze\_string: true \[[https://bugs.ruby-lang.org/issues/8976](https://www.google.com/url?q=https://bugs.ruby-lang.org/issues/8976&sa=D&source=editors&ust=1686086477057913&usg=AOvVaw2YbhybS8bzSzr5GzgB04_3)\]
- freeze\_string\_literal: true
- frozen\_string\_literal: true \[prefered by matz\]
- freezing\_string\_literal: true
- mutable\_string\_literal: false
- immutable: string \[[https://github.com/ruby/ruby/pull/487](https://www.google.com/url?q=https://github.com/ruby/ruby/pull/487&sa=D&source=editors&ust=1686086477058941&usg=AOvVaw0QyJjUIm_f3W7OZaq0CN_X)\]

- command line option candidates

- \--enable-frozen-string-literal

- command line option is weaker than magic comment

Frozen Regexp Literal

It doesn’t need a migration path.

Next Meeting (18, September)

Next Meeting (21, October)
