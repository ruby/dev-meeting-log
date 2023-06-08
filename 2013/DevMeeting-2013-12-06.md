---
lang: en
tags: Ruby, ruby-dev-meeting
---

# DevMeeting-2013-12-06

We have moved the official meeting to December 5, 2013.

We plan to review any discussion, and other unmet agenda items during this time.If you are able to attend on Friday please add your name to the attendees below.

* When: Friday, December 6, 2013 19:00-21:00 JST
* Where: Tokyo, Japan
* How: In person, streaming live, and irc [irc.freenode.net#ruby-core-meeting](irc://chat.freenode.net/#ruby-core-meeting)

<em>Update</em>: A full summary of the meeting can be found at the bottom of this page.

Please follow the [[DevelopersMeetingIRCGuidelines]] for irc

## Attendees

### Venue (in-person)

* zzak
* shyouhei
* a_matsuda
* sorah
* hsbt
* ko1

### IRC-only

* Add your irc handle below if you wish to participate
* Moderator(s) will be indicated with a (m) after their handle
* Translator(s) will be indicated with a (t) after their handle
* n0kada
* nalsh
* chaliesome
* hone
* ayumin

## Agenda

* Review discussion from Thursday meeting
* Discuss remaining agenda items, depending on attendance

## Streaming

* Partial recording from skype: https://soundcloud.com/zzak-2/ruby-core-developer-meeting

## IRC Log

* Brief Summary of discussion on skype/irc https://gist.github.com/zzak/7875519

# Summary

## 2013-12-06[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#2013-12-06)

On Friday, several committers met to discuss the events that took place duringthe previous meeting. We made important decisions on the maintenance policy forbackport versions and commercial support.

### Branch Schema for Semantic Versioning \`ruby\_MAJOR\_MINOR\`[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Branch-Schema-for-Semantic-Versioning-ruby_MAJOR_MINOR)

[misc. #8835](http://bugs.ruby-lang.org/issues/8835)

We discussed a concern with handling security releases given the proposed branching schema.

During the 1.8 series we had a dedicated ruby\_1\_8 branch with no \`TEENY\`version in the name. In this time we had to release a security fix from thisbranch, however there were existing unreleased bug fix patches on this branchcausing a regression.

The previous solution was to create a new branch (\`ruby\_1\_8\_6\`) and beginmaintaining two branches for the 1.8 series.

- Additional branches may be added to support cherry-picking of security fixes.

We are concerned because security versions may occur more frequently betweenMINOR branch changes.

#### SemVer[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#SemVer)

After reviewing the discussion from the previous meeting we discussed theconcern of \`TEENY >= 10\`.

- String comparison bugs exist, such as: \`"2.2.2" <= "2.2.10" #=> false\`
- We can fix this by abandoning strings
- The version number can be anything that acts like String
- We will postpone this situation

#### Tooling support[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Tooling-support)

Most maintainers use tools from trunk, so they need to remain compatible withbackport branches. Any change in this policy should be documented.

### Maintenance Policy for 1.8.7 and 1.9.2[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Maintenance-Policy-for-187-and-192-2)

[misc. #9216](http://bugs.ruby-lang.org/issues/9216)

As discussed, we will commit security patches for 1.8.7 and 1.9.2 until June2014, however one major decision change was made to this policy:

- [@hsbt](https://twitter.com/hsbt) and [@shyouhei](https://twitter.com/shyouhei) have rejected backport releases

Reason being, we don't want any new tickets, as an official release will resultin continue responsibility of ruby-core to follow up on maintenance. Our teamresources are already low, and we want to encourage upgrades, not supportoutdated versions.

- If someone requests a release for 1.9.2 we can help them.

We need to write documentation for upgrading to supported versions, this isstrongly needed regardless of any policy changes.

### Maintenance Policy for 1.9.3[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Maintenance-Policy-for-193-2)

[misc. #9217](http://bugs.ruby-lang.org/issues/9217)

We strongly agree on one major point:

- An official announcement on this issue is critical

### Maintenance Policy for Future Versions of Ruby[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Maintenance-Policy-for-Future-Versions-of-Ruby-2)

[misc. #9215](http://bugs.ruby-lang.org/issues/9215)

We had an extended conversation about the maintenance policy of all Rubyversions.

#### Priority[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Priority)

- Security fixes become priority
- Its maintainer's decision for priority of bug fix and security fix

#### Duration[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Duration)

We decided the following conclusion:

- 3 years supported for MINOR versions as a team
- Maintenance is not contractually obligated, but a "best effort"
- Should a branch become toxic, we will EOL within reasonable time (6-8 months).
- A shortened EOL is determined based on maintainer activity and team decision
- The branch maintainer is not committed to the entire 3 year period
- It's the responsibility of ruby-core as a team to support the version in life cycle

It's important to note: its a team responsibility to maintain a version ofRuby, and maintenance for more than 2 versions is difficult.

Our maintenance period should be:

- A minimum of 6 months, and at best 3 years.

Our goal is to avoid a sudden death.

#### On commercial support[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#On-commercial-support)

[@ayumin](https://twitter.com/ayumin) offered the following statement:

Ruby-core doesn't need to maintain old branches, the important point is
announcing when we decide to give up on branches.

We should specifically define the organization responsible for maintaining aversion. Whether its ruby-core, vendor, or commercial organization, isunimportant.

It's the responsibility of the business party involved to maintain backwardscompatibility, not ruby-core.

We should encourage commercial support for EOL rubies, such documentation wouldbe beneficial.

### Wrap-up[¶](https://bugs.ruby-lang.org/projects/ruby/wiki/DevelopersMeetingDecember2013Japan#Wrap-up)

That about wraps up the summary of both meetings, thank you to everyone who could attend!

Thanks for everyone who helped make this meeting possible, especially:

- Cookpad for hosting both meetings on short notice
- [@sora\_h](https://twitter.com/sora_h) for moderating irc, skype, and creating the audio recordings
- [@mrkn](https://twitter.com/mrkn) for the video recording on Thursday
- [@lchin](https://twitter.com/lchin) for translation support on Friday
- Everyone who attended and helped discuss the future of Ruby

ありがとうございます！！
