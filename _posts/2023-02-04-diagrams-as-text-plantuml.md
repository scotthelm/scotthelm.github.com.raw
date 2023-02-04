---
layout: post
title: "Diagrams As Text - Plantuml"
show_title: true
tags: personal
comments: true
---

Diagrams are a way of communicating an idea, or answering questions visually. 

[Plantuml](https://plantuml.com) is a free and open-source way of creating these diagrams using text.

## Why Would I Want To Do That?

Diagrams help communicate an idea very quickly. Some ideas or questions you can answer with diagrams include

* How do different parts of a system collaborate together? 
* What are the steps I need to take, and what decisions do I need to make to complete a process?
* How can I organize my thoughts around a central theme?

Some reasons you might try creating images with text:

* Trying to line up boxes and arrows - especially when there are a lot of them - is terrible - and time consuming!
  * Plantuml to the rescue!
* Trying to keep up in a meeting is tough
  * I find that I can type much faster than I can drag things around
* Being able to regenerate your diagram after making changes is simple
  * With vscode, the plantuml plugin, and a plantuml server, the diagrams appear as you type
  * Bonus: it makes you look like a wizard.
* Your diagrams can be a part of your source code - very friendly to modern version control systems
  * This helps us keep our diagrams fresh and alive - actual artifacts, not relics.
* Being text, the source of the diagram is human-readable and intuitive
  * Even without the rendering, I find that I can follow what's happening even without the picture.

## Some Examples

### Sequence Diagrams

Sequences allow us to answer the quetion: "How do different parts of a process collaborate together?". Plantuml makes the creation of these types of diagrams quick and easy. We can quickly see which participants are involved in a process and what messages they send to each other to accomplish a task.

![sequence diagram example](//www.plantuml.com/plantuml/png/RL9DRzGm4BtxLpnoAArGGzdoVK1THGKX3bHRvS09MSwKMFNQnfwq4h-USR89MTHRudaVxurzbLad7lPEFUEUzWH3QN2a_NaOs5bFYYqxGlLzEt2_mSstvXhb4vzTUAoKDXmIMkYCgisKAXL5pneRjMSqutdZB7a-dcnlnr5H2id-qMo3nosbX_ybTXDmHpaCoT01WzCVyejjZ6ULKYtEFudNHtnzti7PUm87P29en1IO35E7T51GpOn-LUCBUKgQQUfbGaSGTNjdAU44fjVEaVyrSIIpa3OrZaCtXPJezq2PyMBK-BaGIv2N-e0d9GgYquhCPOtBYutmEfiaNy4L67afFkbAydrarIxEhyNv678YvZxBWGZFfTuycK_M5mMwBlAsbbtw3dgD7kFFBPPDb18SxicBpy9_L-FwGRj1C-L_wribUIz9ZA6SCN9ngiKjn6AZ7HBT9ShzD3kRSksAxeSQkyCs7ottq2iD9OZvyYxgLQjvhJVBTSyFHbr9INdVVm40)

```plantuml
@startuml
!theme cerulean-outline
title "OAuth2 PKCE Auth Flow"
actor U as "User"
participant C as "Client"
participant AS as "Authorization Server"
participant RS as "Resource Server (Your App)"

U -> C : I'd like to see a protected resource
C -> C : 1. Generate PKCE code verifier & challenge
C -> AS : 2. Authorization Code request + code_challenge to /authorize
AS -> U : 3. 302 redirect to authentication prompt
U -> AS : 4. Authentication & Consent
AS -> C : 5. Authorization Code Response
C -> AS : 6. Send authorization code + code_verifier to /token
AS -> AS : 7. Evaluates PKCE code
AS -> C : 8. Access token (and optional refresh token)
C -> RS : 9. Request with access token
RS -> C : 10. Response
C -> U : Protected Resource
@enduml
```

Imagine trying to keep all those boxes and arrows maintained in a graphical program!

### Activity Diagrams

Activity diagrams allow you to answer the question - "What are the steps I need to take, and what decisions do I need to make to complete a process?". The are fantastic for dialing in the exact steps of a process (including conditions and loops).

<table>
<tr>
<th>Plantuml</th>
<th>Image</th>
</tr>
<tr>
<td>
<pre>
```plantuml
@startuml
start
:get bread from pantry;
:get peanut butter from pantry;
if(do I want jelly too?) then (yes)
  :get jelly from the fridge;
else (no)
endif
:place bread in toaster;
:turn on toaster;
while(while bread is not toasted)
    :wait;
endwhile
:remove bread;
:place peanut butter on bread;
if(jelly was chosen?) then (yes)
    :place jelly on bread;
else (no)
endif
:stack the bread with dry sides on top and bottom;
:clean up;
:enjoy;
end
@enduml
```
</pre>
</td>
<td>
<img src="//www.plantuml.com/plantuml/png/RKz1SeGm3Bld5JgoNu13zzfdP8bPiWqsapXb-7qzG6QwdLuoZYLPqhseprgNv8x1zKzIF3BvW2dBWjMpvdquzojvhWPNLShlU9ow8FZ4Pd-yAAKTAl9nWyx4w7OgDmVObGizrGRQ4CEJ1aUf43gMcoCESNBzcln8LvR8KF75R2sArioGNvjjZecwusty0XQz6C6iOUQRZse-70wcwpCjydqfXkRttb6umLRm3BxvWd6MGlot7N3TE8bD-q-redxyCd6BkqMT4VAE4WELiziApm4FKPN5qet9Wg6kDXA_P3-AkBizTKa_" alt="activity diagram"/>
</td>
</tr>
</table>


### Mind Maps

Plantuml also supports useful diagrams other than UML. Mind maps are a great way to visualize and organize ideas around a central theme in an ordered, tree-like way

![mind map example](//www.plantuml.com/plantuml/png/PP1DRWCX38NtdC9AbsZka0jKwhwg8i_mGb7vcM1JjRUlCqNj8Wi4d_diXpYBSjUIQYYyEqsQGQTdGp8-EUTVdtAAxxhcWJTwoLnrbEoy9_tU8TIk51B7paMiw4bm6wWRxk0l53jOwTew5OXmnBAo8D3UssO5_3QLcVVGj7PELRHrC-AXQclPpjFybAq5JC2NeafgLTooq13Sz-L2jv6sZxzNKgfK0BKXyxvOXjhYDUDVOw5ZzXarpE_v0G00)

```plantuml
@startmindmap
title "Mind map"

+[#lightblue] Plantuml
++ types of diagrams
+++ sequence
++++_ great for message-based processes
+++ mind map
++++_ great for brainstorming
+++ tooling
++++_ vscode
++++_ extensions
-- uses
---_ quick diagrams in meetings
---_ maintainable diagrams as code
@endmindmap
```

## In Summary

[Plantuml](https://plantuml.com) is a free, open source method of creating diagrams with text. It's fast, efficient, easily versionable, and fun to use! It supports many more types of diagrams than I have outlined here - including [C4 diagrams](//https:github.com/plantuml-stdlib/C4-PlantUML)- each with an easy-to-use syntax that is human readable and intuitive to use.

You can get started generating diagrams [using the plantuml public server](https://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000). In fact all of the images in this post are using that server to render the plantuml that you see! (Check the img tags in the source of this page)

Next, we'll explore creating a setup using vscode and docker that allows you to generate diagrams as fast as you can type, and even embed them in your markdown documents!

Thanks!

