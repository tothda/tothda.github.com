--- 
title:      sqlplus command line history Linux alatt
created_at: 2008-08-10 00:13:35.047977 +02:00
blog_post:  true
layout:     post
filter:
  - textile
---

Fel kell installálnunk az rlwrap programot. Ubuntura például így:

<pre>
$ sudo apt-get install rlwrap</pre>

Majd indítsuk az sqlplus-t az alábbi módon:

<pre>
$ rlwrap sqlplus</pre>

Ha valaki lusta készíthet rá aliast a .bashrc-ben:

<pre>
alias sqlplus='rlwrap sqlplus'</pre>

És készen is vagyunk. Have fun! 
