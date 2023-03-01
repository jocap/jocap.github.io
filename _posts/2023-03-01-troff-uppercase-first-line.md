---
layout: post
title: Uppercase the first line of a paragraph in GNU troff
---

/u/ZxmonIsBored [asked] on the /r/groff subreddit whether it is possible
to make the first line of a paragraph uppercase, as in [this screenshot].

[asked]: https://www.reddit.com/r/groff/comments/11f4440/is_it_possible_to_create_this_effect_in_groff/
[this screenshot]: https://i.imgur.com/Q2HmeKn.png

Here is a copy of my solution and commentary:

```
.eo
. de (u
.  mk Vp
.  nr Vp \n(Vp+1v
.  wh \n(Vpu )u
.  trin aAbBcCdDeEfFgGhHiIjJkKlLmMnNoOpPqQrRsStTuUvVwWxXyYzZ
. .
. de )u
.  trin aabbccddeeffgghhiijjkkllmmnnooppqqrrssttuuvvwwxxyyzz
.  di Uf
.   br
.  di
.  asciify Uf
.  Uf
. .
.ec
.
.sp 3v
.ll 3i
.
.(u
\(lqWho's next then?\(rq Martin was calling as Horace, grinning broadly,
stepped back into the line.
```

.(u marks the beginning of a paragraph where the first line should be uppercased. It stores the current vertical position (.[mk]), adds one line to it (.[nr]) and sets a trap at that position (.[wh]). Finally, it specifies that all lowercase ASCII letters be translated to uppercase.

The trap set by .(u should be sprung whenever groff advances to the next line. At that point, the macro .)u is executed, which undoes the lowercase-to-uppercase translation.

Some trickery must be deployed in order to ensure that the first word on the second line is not uppercased. When .)u is executed, a diversion is used (.[di]) to suppress the current unfilled line (containing the uppercased first word of the second line) and save it in the .Uf macro. Then, .[asciify] is used to unformat the text saved in .Uf. Finally, .Uf is called to output the first word, un-uppercased.

Note that .[trin] must be used instead of .[tr] to make .[asciify] ignore the lowercase-to-uppercase translation.

Here is a [screenshot](https://i.imgur.com/UW55q5n.png) of the results.

[asciify]: https://www.gnu.org/software/groff/manual/html_node/Diversions.html#index-asciify
[trin]: https://www.gnu.org/software/groff/manual/html_node/Character-Translations.html#index-trin
[tr]: https://www.gnu.org/software/groff/manual/html_node/Character-Translations.html#index-tr
[mk]: https://www.gnu.org/software/groff/manual/html_node/Page-Motions.html#index-mk
[nr]: https://www.gnu.org/software/groff/manual/html_node/Setting-Registers.html#index-nr
[wh]: https://www.gnu.org/software/groff/manual/html_node/Page-Location-Traps.html#index-wh
[di]: https://www.gnu.org/software/groff/manual/html_node/Diversions.html#index-di
