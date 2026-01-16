# How to Write a Git Commit Message

Reference: https://cbea.ms/git-commit/

## Introduction: Why good commit messages matter

A well-crafted Git commit message is the best way to communicate _context_ about a change to fellow developers (and indeed to their future selves). A diff will tell you _what_ changed, but only the commit message can properly tell you _why_.

> Re-establishing the context of a piece of code is wasteful. We can't avoid it completely, so our efforts should go to reducing it as much as possible. Commit messages can do exactly that and as a result, _a commit message shows whether a developer is a good collaborator_.

A project's long-term success rests (among other things) on its maintainability, and a maintainer has few tools more powerful than his project's log.

## The Seven Rules of a Great Git Commit Message

1. Separate subject from body with a blank line
2. Limit the subject line to 50 characters
3. Capitalize the subject line
4. Do not end the subject line with a period
5. Use the imperative mood in the subject line
6. Wrap the body at 72 characters
7. Use the body to explain _what_ and _why_ vs. _how_

### Example Format

```
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

## Rule Details

### 1. Separate subject from body with a blank line

Not every commit requires both a subject and a body. Sometimes a single line is fine, especially when the change is so simple that no further context is necessary:

```
Fix typo in introduction to user guide
```

However, when a commit merits a bit of explanation and context, write a body:

```
Derezz the master control program

MCP turned out to be evil and had become intent on world domination.
This commit throws Tron's disc into MCP (causing its deresolution)
and turns it back into a chess game.
```

### 2. Limit the subject line to 50 characters

50 characters is not a hard limit, just a rule of thumb. Keeping subject lines at this length ensures that they are readable, and forces the author to think for a moment about the most concise way to explain what's going on.

> Tip: If you're having a hard time summarizing, you might be committing too many changes at once. Strive for atomic commits.

GitHub will warn you if you go past the 50 character limit and will truncate any subject line longer than 72 characters with an ellipsis. Shoot for 50 characters, but consider 72 the hard limit.

### 3. Capitalize the subject line

Begin all subject lines with a capital letter.

- **Good:** Accelerate to 88 miles per hour
- **Bad:** accelerate to 88 miles per hour

### 4. Do not end the subject line with a period

Trailing punctuation is unnecessary in subject lines. Space is precious when you're trying to keep them to 50 chars or less.

- **Good:** Open the pod bay doors
- **Bad:** Open the pod bay doors.

### 5. Use the imperative mood in the subject line

_Imperative mood_ means "spoken or written as if giving a command or instruction":

- Clean your room
- Close the door
- Take out the trash

Git itself uses the imperative whenever it creates a commit on your behalf:

```
Merge branch 'myfeature'
```

```
Revert "Add the thing with the stuff"
```

**A properly formed Git commit subject line should always be able to complete the following sentence:**

- If applied, this commit will _your subject line here_

Examples:
- If applied, this commit will _refactor subsystem X for readability_
- If applied, this commit will _update getting started documentation_
- If applied, this commit will _remove deprecated methods_
- If applied, this commit will _release version 1.0.0_

Non-imperative forms don't work:
- If applied, this commit will _fixed bug with Y_ (wrong)
- If applied, this commit will _changing behavior of X_ (wrong)
- If applied, this commit will _more fixes for broken stuff_ (wrong)

> Remember: Use of the imperative is important only in the subject line. You can relax this restriction when you're writing the body.

### 6. Wrap the body at 72 characters

Git never wraps text automatically. When writing the body of a commit message, mind its right margin and wrap text manually.

The recommendation is to do this at 72 characters, so that Git has plenty of room to indent text while still keeping everything under 80 characters overall.

### 7. Use the body to explain what and why vs. how

Focus on making clear the reasons why you made the change in the first place—the way things worked before the change (and what was wrong with that), the way they work now, and why you decided to solve it the way you did.

In most cases, leave out details about how a change has been made. Code is generally self-explanatory in this regard (and if the code is so complex that it needs to be explained in prose, that's what source comments are for).

Example from Bitcoin Core:

```
Simplify serialize.h's exception handling

Remove the 'state' and 'exceptmask' from serialize.h's stream
implementations, as well as related methods.

As exceptmask always included 'failbit', and setstate was always
called with bits = failbit, all it did was immediately raise an
exception. Get rid of those variables, and replace the setstate
with direct exception throwing (which also removes some dead
code).

As a result, good() is never reached after a failure (there are
only 2 calls, one of which is in tests), and can just be replaced
by !eof().

fail(), clear(n) and exceptions() are just never called. Delete
them.
```

## Good vs Bad Examples

### Bad commit messages (inconsistent, unclear):

```
e5f4b49 Re-adding ConfigurationPostProcessorTests after its brief removal in r814. @Ignore-ing the testCglibClassesAreLoadedJustInTimeForEnhancement() method as it turns out this was one of the culprits in the recent build breakage.
2db0f12 fixed two build-breaking issues: + reverted ClassMetadataReadingVisitor to revision 794
147709f Tweaks to package-info.java files
7f96f57 polishing
```

### Good commit messages (concise, consistent):

```
5ba3db6 Fix failing CompositePropertySourceTests
84564a0 Rework @PropertySource early parsing logic
e142fd1 Add tests for ImportSelector meta-data
887815f Update docbook dependency and generate epub
ac8326d Polish mockito usage
```

## References

These resources are referenced in the original article:

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git commit manpage](https://www.kernel.org/pub/software/scm/git/docs/git-commit.html)
- [A Note About Git Commit Messages](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
