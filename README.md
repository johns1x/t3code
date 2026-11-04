# Composer refills with the autocorrected draft after Send (iOS)

Tested: pingdotgg/t3code main at a21d788752695764028e6d26ba924f3963e05806, T3 Code Dev 2.2.0 (1), iPhone 18 Pro simulator, iOS 27.2, on-screen keyboard with Auto-Correction on, light mode, Replay (dev) provider.

Steps: in a thread composer, tap a message on the on-screen keyboard ending in a misspelled word, with no trailing space, so the keyboard shows the correction highlighted in the middle of the suggestion bar. Tap Send.

Expected: the message is sent and the composer is empty.
Actual: the message is sent uncorrected, and the composer then holds the whole message with the correction applied.

| Run | Typed and sent | Left in composer |
| --- | --- | --- |
| 1 (`repro.mp4`, `repro.gif`, `repro-1-before-send.png`, `repro-2-after-send.png`) | Fix wierd | Fix weird |
| 2 (`repro-run2-*.png`) | Fix adress | Fix address |
| 3 (`repro-run3-*.png`) | Fix seperate | Fix separate |
| Control (`control-after-send.png`) | Fix the build (no correction pending) | empty |

"Fix teh" from the original report could not be used: the simulator keyboard learned "teh" during earlier uncorrected sends and stopped offering the correction, and the simulator has no Reset Keyboard Dictionary.

`debug-trace.txt` is a temporary console.log trace, since removed. The native change carrying the corrected text (eventCount 26) reaches JS about 5 ms after the send handler starts, in the same tick as the draft clear, and is accepted because it is newer than JS's last event (25). `onChangeText` then writes the corrected text over the cleared draft. No render with an empty value ever happens, so no stale clear reaches the native revision guard.
