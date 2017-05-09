# Problem
## Context (What I'm Trying To Accomplish)

I want my package, elm-random-thing, to show up when someone searches for JSON.

## Specifics (Where I'm Stuck)

If I type json into package.elm-lang.org, only packages with json in the name come up. Should I name my package elm-random-json-thing?


# Solution

The contents of both the `summary` and the `name` of your package are searchable. You can include the term you want people to find in either of those fields.

## Naming guidelines

When naming a package, go through the following list:

- Is the name descriptive?
  - Prefer "elm-test" to "octestpus"
- Is there already a package with the same name?
  - If so, can my changes be merged with that package instead?
  - If not, can I think of a better name?
