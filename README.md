# consult-ripfd - a `ripgrep` + `fd` mashup using `consult`

Ever want to find a line of text in a small `.txt` file you modified in the past week?  With `consult-ripfd`, that's just


```
# search pat -- -n 1w -S -1k -E txt
```

`ripgrep` is one of the most widely used tool for rapidly searching through files for patterns.  `fd` is a super-fast descendant of `find`, and can tear through large directory hierarchies finding files matching a wide array of criteria (filename, extension, modtime, file type, size, owner etc.).

`consult-ripfd` brings these two super tools together into a single command, with all of the conveniences of [consult](https://github.com/minad/consult) you know and love: 

- dynamic live results with match count
- instant async updating
- grouping matches by files with quick navigation between them
- match highlighting
- easy export to a `grep` buffer using embark

## Install and configuration

Not yet in a package repository.  Just clone and:

```elisp
(use-package consult-ripfd
  :load-path "~/path/to/consult-ripfd/"
  :bind  ("s-F" . consult-ripfd)) ; or whatever you prefer
```

Install both [`ripgrep`](https://github.com/BurntSushi/ripgrep) and [`fd`](https://github.com/sharkdp/fd) however is convenient.

> [!WARNING]
> For sort options to work reliably, make sure your version of `ripgrep` is up to date. `v15` or later is recommended.

## Usage

There are two commands, both of which combine `fd` and `rg` into one search tool.  Check the docstrings for the full details.  Note, if no option flags are given, both commands fall back on plain `ripgrep` search.

### `consult-ripfd` 

The main command presents an simplified interface with a curated,  list of the most useful options from both tools:

```
 RG-PATTERNS -- OPTION-FLAGS

 `fd'-relevant option flags:

   -n DATE      Newer than date - see fd(1) for date/duration syntax

   -b DATE      Before (older than) date

   -S SIZE      As in default `fd' option flags (see `fd(1)')
   -t TYPE
   -e EXT
   -d MAX-DEPTH
   -o OWNER
   -E EXCLUDE-GLOB

   -g GLOB      Search filenames matching GLOB.  May be provided multiple
                times to match additional files.  See also -E.

 `rg'-relevant option flags:

   -F, -i, -v   As in default `rg' option flags (no values)

   -s[r] [macp] Sort `rg' matches by [m]odified/[a]ccess/[c]reated time
                or [p]ath name.  Use `r' in the flag to reverse the
                sort.  N.B.: this makes `rg' single-threaded.
```

> [!WARNING]
> Short options do not necessarily correspond to valid flags in the relevant tool.

Examples:

- `#\beat\b -- -n 2025-12-31` : lines that contain the standalone word `eat` in files with modification times newer than new year's eve.
- `# ^[\ \t]*$ --  -v -e py -d 3` : non-blank lines in `py` files at most 3 directories below the search dir.  Note the escaping of the space for consult.
- `#[a-d]\{3\}$ -- -b 4w -S +7k -g org*.org` : lines ending with words composed of the letters `a-d` in large (>7kb) `org*.org` files last modified prior to 4 weeks ago.


### `consult-ripfd-full`

Provides complete access to the command line options of both `fd` and `rg`, by recognizing **two** `--` argument separators: 

```
    RG-PATTERNS -- FD-OPTS -- RG-OPTS
```

Example:

- `#macro -- --changed-within 1w --  -A 2` : search for the word `macro` in files changed within the last week, and show two lines of context after each match.

