# 💭 Reflection: Game Glitch Investigator

## 1. What was broken when you started?

When I first ran the game, the hint system was not trustworthy and the state did not reset cleanly. I could guess lower and lower, but the app would still tell me to go lower even when the number was already at the bottom of the range. I also noticed that starting a new game kept the old win/loss state around, and the Hard difficulty used a smaller range instead of a harder one.

**Bug Reproduction Log**

| Input | Expected Behavior | Actual Behavior | Console Output / Error |
|-------|-------------------|-----------------|------------------------|
| Guessed 50, then 25, 12, 6, and finally 1 | The game should eventually indicate that the secret is higher once guesses get too low. | The game still said to go lower, even when 1 was already the minimum. | No console error observed. |
| Won a game, clicked "Start New Game," then entered another guess | The game should reset completely and allow normal play again. | The "You already won" message stayed around and blocked normal gameplay. | No console error observed. |
| Changed difficulty from Normal to Hard | Hard mode should be more difficult. | The range got smaller, which made the game easier. | No console error observed. |

## 2. How did you use AI as a teammate?

I used Copilot to help trace the Streamlit state flow and separate the logic from the UI. One correct suggestion was to keep the secret number numeric and reset the full session state on a new game, which matched the behavior I wanted. One misleading idea was comparing the secret as a string on alternating turns, because that recreated the broken hint behavior instead of fixing it. I verified the useful suggestions by checking the code path and rerunning the tests.

## 3. Debugging and testing your fixes

I decided a bug was fixed only when the code path matched the intended behavior and the tests still passed. I ran `pytest` after the refactor, and the helper tests confirmed that `check_guess` still returns the correct outcome/message pairs. I also checked the new-game branch in `app.py` to make sure it clears score, history, status, and the secret instead of leaving stale state behind. AI helped me understand which parts belonged in `logic_utils.py` versus `app.py`, which made the fixes easier to verify.

## 4. What did you learn about Streamlit and state?

Streamlit reruns the script from top to bottom every time you click a widget, so any normal local variables disappear unless you store them in session state. Session state is what lets the app remember the secret number, score, and game status across those reruns. Without resetting the right keys together, the app can get stuck showing old win/loss messages or an old secret. That made the bug feel random at first, but it was really just state persisting between reruns.

## 5. Looking ahead: your developer habits

I want to keep splitting logic into small testable helpers before I spend time on the UI. I also want to keep using `pytest` earlier, because it exposed the contract mismatch in `check_guess` right away. Next time I work with AI, I would be more explicit about which file is the real entry point so it does not waste time on the wrong module. This project made me more cautious about trusting AI-generated code without checking how it behaves across reruns and state updates.
