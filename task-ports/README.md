# Task ports

- Steward: Dave Keen, @ccapndave on the Elm Slack
- Questions + Collaboration: @general on the Elm Slack

This is a port over which you make a call, and are guaranteed either a success or an error.  One possible implementation for this would be as a port that returns a `Task`.

## Motivation
Some ports expect to make a call to Javascript and receive a response.  I find this to be inelegant with the current incoming and outgoing port setup.  There is also plumbing required if you want to match up an incoming port response with an outgoing port request (by sending `id`s or something).

## Current best practice
In general this is currently implemented using a set of outgoing and incoming ports (one for success and one for error):

```elm
{-| Call something in Javascript
-}
port doSomething : { id : String, param : String } -> Cmd msg


{-| Something call `id` returned successfully with some data
-}
port doSomethingSuccess : ({ id : String, result : Value } -> msg) -> Sub msg


{-| Something call `id` failed
-}
port doSomethingError : ({ id : String, err : String } -> msg) -> Sub msg
```

The other possibility is to use a native function, which works well but is annoying to work with, and runs the possibility of breaking with new releases.

## Use cases from real projects
This would be useful for any situation where Elm calls a JS function that returns a value or a Promise.  In general this would occur when we want to use prebuilt libraries that are too complicated or time consuming to rewrite in Elm when working on a real-world project.  I have personally run into it in my current project when using these two libraries:

- cryptojs (https://github.com/brix/crypto-js)
- jexl (https://github.com/TechnologyAdvice/Jexl)

## Literature review
Maybe someone else can chime in on how this is dealt with in other languages?

## Proposed API or syntax
```elm
task port doSomething : { param : String } -> Task String String
```

## Challenges
The main complication is that a Task *has* to complete, and if we are calling a Javascript function there is no hard guarantee that it will ever complete, plus it might crash.  I believe these problems can be mostly mitigated by:

1. Having the runtime wrap the function call in a try..catch and throwing the error case if there is an exception.
2. Having a dedicated function to turn a task port into a `Cmd` which takes a timeout parameter after which the Task fails.
