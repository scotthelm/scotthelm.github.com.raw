---
layout: post
title: "Diagrams As Text - PlantUML"
show_title: true
tags: tech tools plantuml
comments: true
---

Diagrams are a way of communicating an idea, or answering questions visually. 

[PlantUML](https://plantuml.com) is a free and open-source way of creating these diagrams using text.

## Why Would I Want To Do That?

Diagrams help communicate an idea very quickly. Some ideas or questions you can answer with diagrams include

* How do different parts of a system collaborate together? 
* What are the steps I need to take, and what decisions do I need to make to complete a process?
* How can I organize my thoughts around a central theme?

Some reasons you might try creating images with text:

* Trying to line up boxes and arrows - especially when there are a lot of them - is terrible - and time consuming!
  * PlantUML to the rescue!
* Trying to keep up in a meeting is tough
  * I find that I can type much faster than I can drag things around
* Being able to regenerate your diagram after making changes is simple
  * With vscode, the PlantUML plugin, and a PlantUML server, the diagrams appear as you type
  * Bonus: it makes you look like a wizard.
* Your diagrams can be a part of your source code - very friendly to modern version control systems
  * This helps us keep our diagrams fresh and alive - actual artifacts, not relics.
* Being text, the source of the diagram is human-readable and intuitive
  * Even without the rendering, I find that I can follow what's happening even without the picture.

## Some Examples

### Sequence Diagrams

Sequences allow us to answer the quetion: "How do different parts of a process collaborate together?". PlantUML makes the creation of these types of diagrams quick and easy. We can quickly see which participants are involved in a process and what messages they send to each other to accomplish a task.

![sequence diagram example](//www.plantuml.com/plantuml/png/VP31RW8X48Rl-nJJlVi2FJIIJNiptLHUWB1_bSeE1cPhVFiYHCKcxNNuvkE3TH8JPPbzzoHxp22Bk7WONeL5l6DquiI3-YtWU4UADk7ReU-CbH1f99Ee7ut4m7rtp2fdtT6ma5RbI3jxW93QWUKH62em1BP8oGKc9GAUp6zGwzQapG7n4TXKe8IHtYFNvC1kfDLB3docBJoirF0wrIgFt-E4I5Ardo5IAXmccWkfrLMWqg40HtEUmT8uHdFE9pVBoScUh8bJNhupqR5GpK-DzGEoH2uu6Pv8Ab7jmxtGtdueTSF_JKFJrEurOSLGmJVdFONB7NdwLrLxUtstBcz20ikjEGzNrp3lmucClMpLZSrbuvK5iHZ1N-5CzO_Nu6cP_Gy0)

```plantuml
@startuml
!theme cerulean-outline
title "Seeing A Movie"
actor P as "Patron"
participant TA as "Ticket Agent"
participant CA as "Concession Attendant"
participant TT as "Ticket Taker"
participant MT as "Movie Theatre"

P -> P : Select Movie
P -> TA : Order ticket for selected movie
TA -> P : Ask for payment
P -> TA : Pay for ticket with card
TA -> TA : Print Tickets
TA -> P : Return card and tickets
P -> CA : Order concessions
CA -> P : Ask for payment
P -> CA : Pay for concessions with card
CA -> CA : Fill order
CA -> P : Return card and concessions
P -> TT : Present tickets
TT --> P : Allow access
P -> MT : Enter and enjoy movie
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
endwhile (done)
:bread is toasted;
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
<img src="//www.plantuml.com/plantuml/png/RKz1RiCm3Blx5JmcNsWFwtNFS6ErSUT8WIqloEydn36m3hiOiaYAvAseprgNv8x1NPwak6Vo0Oyi2rRFclThsw_akHfSLIc_u_4n1C4dDllZHIdjK96F4JeJOzYfZ0wmAoVQr0RQ4CEJhevI8GmieoCEyU4kQ_8JdLaYGyKNixKeMZD3VcssEIOQZhVp2rZqP0IpXfblFghvS6XC2ymqkajNTBOvP5ha-xnrxKdUsmjtsAgtIfilc6Ofn7zx0-UDHkpQVqeNzTENYNkHBUgCa7UK6AYqrYiy1zn5LHPBDoKBXhhQIFoI_QZeRlRK9Vq0" alt="activity diagram"/>
</td>
</tr>
</table>


### Mind Maps

PlantUML also supports useful diagrams other than UML. Mind maps are a great way to visualize and organize ideas around a central theme in an ordered, tree-like way

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

[PlantUML](https://plantuml.com) is a free, open source method of creating diagrams with text. It's fast, efficient, easily versionable, and fun to use! It supports many more types of diagrams than I have outlined here - including [C4 diagrams](//https:github.com/plantuml-stdlib/C4-PlantUML)- each with an easy-to-use syntax that is human readable and intuitive to use.

You can get started generating diagrams [using the PlantUML public server](https://www.plantuml.com/plantuml/uml/SyfFKj2rKt3CoKnELR1Io4ZDoSa70000). In fact all of the images in this post are using that server to render the plantuml that you see! (Check the img tags in the source of this page)

[Next, we'll explore creating a setup using VSCode and Docker]({% post_url 2023-02-05-plantuml-diagrams-with-vs-code %}) that allows you to generate diagrams as fast as you can type, and even embed them in your markdown documents!

Thanks!



