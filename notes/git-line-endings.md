# Git and line endings: CRLF, LF and `.gitattributes`

Reusable across projects. Written during stage 0 to 1 of the CMS Open Payments dashboard, where Power BI Desktop rewrote fifteen pbip files and produced a 5,250 line diff containing no actual changes.

Applies to any repository where a Windows application writes the files. Power BI Desktop is the obvious case, but Excel add-ins, SSMS, Visual Studio and most Windows editors behave the same way.

## What the two conventions are

A text file is bytes, so the end of a line is marked by a character like anything else.

| Name | Bytes | Escape | Used by |
|---|---|---|---|
| LF, line feed | `0x0A` | `\n` | Linux, macOS, git internally |
| CRLF, carriage return plus line feed | `0x0D 0x0A` | `\r\n` | Windows, DOS |
| CR alone | `0x0D` | `\r` | Classic Mac OS, historical |

The pair comes from teletypes. Carriage return moved the print head to the left margin, line feed advanced the paper one line, and both were needed. Unix dropped CR. DOS kept both and Windows inherited it.

## The failure mode

Git compares bytes, not meaning. A file committed with LF and then rewritten by a Windows tool with CRLF differs on every line by one byte, so git reports every line as deleted and re-added.

Symptoms:

- A diff with equal insertion and deletion counts across every file.
- Files reported as modified immediately after opening and closing an application, with no edit made.
- `git diff --ignore-cr-at-eol` returning nothing at all, which is the confirmation.

Cost beyond the noise: the diff tells you nothing about what changed, blame traces every line to the last save rather than to the commit that wrote it, and two people on different operating systems conflict on every line of a shared file.

## Diagnosis

```
git diff --stat                      # equal insertions and deletions is the tell
git diff --ignore-cr-at-eol --stat   # empty output confirms it is only line endings
git diff -- <path> | cat -A          # ^M$ at line ends is CR, $ alone is LF
file <path>                          # reports "with CRLF line terminators" in git bash
git check-attr text -- <path>        # what git thinks the policy is for that file
```

## The fix

A `.gitattributes` file at the repository root. This is the repository level answer and it travels with a clone, which `core.autocrlf` does not, since that is a per machine setting.

```
# Normalise all text to LF in the repository.
* text=auto

# Binary, never diffed or line ending converted.
*.pbix binary
*.pbit binary
*.zip binary
*.parquet binary
```

`text=auto` means: for any file git detects as text, store LF in the repository and convert on the way into the index. The working tree is left alone, so Windows tools carry on writing CRLF and the diff stays empty because both sides now agree.

The `binary` entries are not about line endings. They stop git ever attempting to diff or convert those files, which matters as soon as exported reports and data files start appearing.

## Applying it to an existing repository

The file only governs what happens from now on. Files already committed with the wrong endings need one renormalisation pass.

```
git add .gitattributes
git add --renormalize .
git status
git commit -m "Normalise line endings with .gitattributes"
```

`git add --renormalize .` re-adds every tracked file under the pathspec, applying the current conversion rules.

Two things it does not do, both commonly misread:

- It does not change the working tree. It rewrites the index only, so files on disk keep their CRLF.
- It covers tracked files only. Untracked and ignored files are never touched.

Expect one large commit touching every text file. Cheapest done early, while the repository is small.

Verify: `git status` clean, then open the offending application, save, close, and `git status` again. Still clean is the result that proves it.

## Traps

**Creating the file from Windows PowerShell.** Do not use `>` redirection in PowerShell 5.1. It writes UTF-16 and git cannot read the file. Create it in the editor instead, or use `Set-Content` with an explicit encoding.

**Byte order marks.** A UTF-8 BOM at the top of `.gitattributes` can make the first line parse wrong. Harmless when line one is a comment, not harmless otherwise. `file` reports a BOM if one is present.

**The filename.** `.gitattributes`, at the repository root, no extension. `git check-attr` returning `unspecified` means git is not reading it: wrong name, wrong location, or wrong encoding.

**Do not reach for `core.autocrlf`.** It solves the same problem one machine at a time and does not survive a clone.

## Power BI specific

The pbip format is many small text files: `.tmdl`, `.json`, `.pbir`, `.pbism`, `.pbip`, `.platform`. Power BI Desktop rewrites a large share of them on every open and save, whether or not anything changed. Without `.gitattributes` this produces a spurious diff on essentially every session, which defeats the reason for choosing pbip over pbix in the first place.

Set this up at repository creation, in the same commit as `.gitignore`.
