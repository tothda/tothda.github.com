---
layout: default
---

Emacs cheat sheet
=================

C-x
: means Control + x

M-s
: means Meta (Alt) + x

Moving
------

C-f
: next character

C-b
: previous characer

M-f
: next word

M-b
: previous word

C-n
: next line

C-p
: previous line

C-a
: beginning of line

C-e
: end of line

M-a
: beginning of sentence ???

M-e
: end of sentence ???

killing
-------

M-d
: szó törlése

buffers
-------

C-x h
: egész buffer kijelölése

C-x b
: váltás egy másik megnyitott buffer-re

C-x b ENTER
: előző buffer kiválasztása

bookmarks
---------

C-x r m [bookmark neve]
: bookmark létrehozása

C-x r b [bookmark neve]
: ugrás adott bookmark-hoz

C-x r l
: bookmark-ok listázása

git-status buffer
-----------------

a
: hozzáadás az index-hez

g
: frissítés

m
: file kijelölése

M
: összes file kijelölése

c
: commit (kijelölt fájlokat)

C-c C-c
: mehet a commit (commit message bufferben)

MAN oldalak olvasása
--------------------

M-x manual-entry
: topic MAN oldalának beolvasása egy új bufferbe

M-x woman
: mint az előző, csak nem a man programot használja a formázáshoz + TAB-ra kiegészíti a topic-ot

Egyéb
-----

M-x toggle-truncate-lines
: hosszú sorok tördelésének letiltása

M-x linum-mode
: sorszámok mutatása

M-x locate-library
: locate lisp files in load-path

M-x find-name-dired
: find files in directories

finito
