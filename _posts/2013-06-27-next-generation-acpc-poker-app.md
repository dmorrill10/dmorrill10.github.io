---
layout: post
category: software design
tagline: Things I'd like to change
tags: [acpc, poker, rails, design, refactoring, testing, modularity, software]
---
{% include JB/setup %}

In developing the next iteration of the poker app, there are a few things I want to focus on:

1. Simplicity
----------------
The poker app has always been overcomplicated. On the server, this was caused by choosing Rails as the web framework, which although a very solid and established framework, brings with it a lot of unnecessary complication and bloat because its use case of traditional static form directed page centric sites is so different from the needs of this app. There were almost certainly better choices for the framework, likely a Coffee/JavaScript framework, or maybe [Goliath](https://github.com/postrank-labs/goliath). But at least for now, I've wrangled Rails into a position that it, at the very least, doesn't get in the way of the real-time updating required by a card game interface like this, and there's very little reason to take the time and effort to change the basic framework.

On the back-end, this was caused by doing more than necessary just to get things working, and not fully realizing all the concerns that would come down the line. For instance, the current implementation of the poker logic is pretty good in doing minimally necessary computations to evolve the state of matches. However, it relies heavily on being stateful, and this crutch infects almost every part of the logic, when it could be much cleaner and essentially ephemeral. In the current setup, this reliance on state doesn't really matter, and it was all right to do during the last refactoring to simplify from the horrible first implementation. But it matters a whole lot when it comes to being testable and communicating information about the match to display and persistence mediums (in this case, a Rails app and a database). There is much room to decouple state in the game logic, and doing so would make the logic easier to test, store, and reconstruct in a static form.

2. Speed
----------
With the switch from polling to event-driven websocket updates, the Rails side of the app is very snappy. However, in building a new little test bot, I realized that the backend is much slower than it should be. I suspect that it's the state manipulations that are taking so much time, and that if I could simplify the game logic, this would be resolved, or at the very least it would be more apparent where performance holes exist, and it would be easier to profile.

3. Decoupling from screen and persistence methods
---------------------------------
There needs to be notion of a minimally reconstructible representation in the game logic. This would contain just enough information to fully reconstruct a static view of all the information at the current point in the match. No state, just something that can be thrown in a persistence medium (read: database), pulled out, and a fully formed view of the match can be reconstructed. It would contain both the static information from the current match state, and a snapshot of the dynamic state information. Treating this as the central abstraction of the game logic, one can think of continuing a match as transitioning from one of these slices to the next. In spirit, this is exactly what the game logic does already, but in practice, one cannot take a slice, combined with the next match state, and return with the next slice in the match.

It should also be easy to build ACPC Dealer specific competitors and text-based interfaces. There's absolutely no reason that one should only be able to interact with the dealer through a browser with the app server running. It should be very easy to make terminal, desktop, and even mobile device apps.

4. Testability
---------------------
The most difficult part of testing the game logic is that the only way to test larger constructs like `PlayersAtTheTable` is to play full matches from start to finish (well, the beginning and end hand can be arbitrary, but each hand has to progress from no actions to resolution). It's often difficult to check that intermediate state between the beginning and end of a hand is correct. The more stateless logic, the more testing can be done where arbitrary match states are provided and the result can be checked thoroughly.
