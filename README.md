GitReb
======

[my question]: http://stackoverflow.com/q/42401478/6261350
[script by jezz]: http://stackoverflow.com/a/19267103/6261350

A small interface over `git rebase -i`'s common usage

- Easy non-interactive interactive rebasing
  with action applied only to specified revision;

- Allows using `:/<text>` notation easily;

- Uses `--root` if needed to apply action to root commit.

Usage
=====

    gitreb <action> <revision>

This does the same as calling `git rebase -i <revision>^`
and then replacing the first word with `<action>` in EDITOR.

Special cases
-------------

- If `<revision>` is the root commit,
  then GitReb does the same as calling
  `git rebase -i --root` and replacing
  the first word with `<action>`.

- If `<revision>` uses the `:/<text>` notation,
  then GitReb does the same as calling
  `git rebase -i HEAD^{/<text>}^`.

  This is not explicitely coded, merely the only side-effect
  of using `git rev-parse` to find the commit
  then applying `<commit>^` to the result.

  [AFAIK][my question], `:/<text>` is the only notation
  to which you can't just apply the lone caret modifier.

Idea
====

I wanted to be able to use the following command intuitively:

    $ git rebase -i :/<text>^   # DOES NOT DO AS EXPECTED

I wrote [this][my question] on Stack Overflow
which describes the situation.

I also don't like having to use EDITOR
for such a simple to describe operation.

[This][script by jezz] StackOverflow comment
mostly answers both concerns by using `git rev-parse`
and `$GIT_SEQUENCE_EDITOR` with `sed -i`.

I was compelled to

- fix the obvious flaws (incorrect commands and tests),
- use `ed` which unlike `sed` is *supposed* to edit files in place,
- make the script shell-agnostic,
- use default `edit` behaviour instead of always using `reset HEAD^`,
  (though you could still reuse the selected commit message with
   `commit -c`, I didn't want the default behaviour to change).
- Use `<commit>^` instead of `<commit>^^`,
- Use `--root` if needed
