# use milestone1 solution to do milestone2
# milestone2 testcases are inculed 
# Milestone 2 – Movement Validation and Game Logic Rules

This file contains all the rules and mechanics that must be enforced in the game logic for Milestone 2.
Cursor should reference this file when implementing or editing any of the following methods: `validateSteps`, `validatePath`, `move`, `validateSwap`, `validateFielding`, `validateSaving`, and all movement-related logic.

---

## 🎯 Game Objective
Move all 4 marbles from the Home Zone to the Safe Zone. Players take turns playing cards (Ace to King + Wild Cards) to move, swap, burn, or save marbles.

---

## 🎲 Card-Specific Movement Rules (focus of validateSteps):

### ♣️ Four (4)
- Cannot be used to move backward within the Safe Zone.
- Cannot be used to enter the Safe Zone.
- Only valid on main track movement that does NOT include or touch the Safe Zone.

### ♦️ Five (5)
- Cannot move any marble (especially opponent’s) into a Safe Zone.
- Must continue moving only on the **main track**, obeying circular wrapping (0–99).
- When moving opponent marbles:
  - Can bypass or land on opponent marbles.
  - Cannot bypass or land on **own** marbles.

---

## 🛣️ Movement Validation Rules (apply in `validatePath`):

### General Rules
- Path must be **clear** unless special card (e.g., King) allows destruction.
- A marble cannot:
  - Pass over its own marbles.
  - Land on its own marble.
  - Enter the Safe Zone if the Safe Zone Entry is blocked.
  - Enter Safe Zone without exact steps.
  - Move if not on track or Safe Zone.

### King Card Exception
- If `destroy == true`, the King card:
  - Destroys **all marbles** in the path (except in Safe Zones).
  - Cannot destroy or pass marbles in Safe Zones (Safe Zone = immunity).

### Trap Cells
- If a marble lands on a trap, it is destroyed and returned to Home Zone.
- The trap cell deactivates and a new one is assigned.

---

## 🛑 Invalid Move Scenarios (throw `IllegalMovementException`)
- Moving with a card that goes too far for either the track or Safe Zone.
- Backward movement in Safe Zone with any card.
- Attempting to move a marble not on track or Safe Zone.
- Entering Safe Zone with 4 or 5 card.
- Landing on or passing through own marbles (unless allowed).
- Trying to field into a Base Cell that’s blocked by your own marble.

---

## ⚔️ Swap Rules (`validateSwap`)
- Valid only if both marbles are on the main track.
- Cannot swap if either marble is in Home Zone or Safe Zone.
- Cannot swap with own marbles.
- Cannot swap into or from Base Cell.

---

## 🔥 Burning Rules (`validateDestroy`)
- Can only destroy marbles on the track.
- Marbles in Home Zone, Base Cell, or Safe Zone are **immune** to burning.

---

## 🏠 Fielding Rules (`validateFielding`)
- Use Ace or King to field.
- Cannot field if the Base Cell is already occupied by your own marble.

---

## 🛟 Saving Rules (`validateSaving`)
- Can only save marbles **on track or Base Cell**.
- Cannot save marbles already in Safe Zone or in Home Zone.
- Saving always moves the marble to a random empty Safe Zone cell.

---

## 🚀 Method Responsibilities

### validateSteps(Marble marble, int steps)
- Determine marble's position (track or Safe Zone).
- Check if steps are valid for position and destination.
- Handle special cards like 4 and 5 correctly.
- Return full list of `Cell` objects from start to end.

### validatePath(Marble marble, List<Cell> fullPath, boolean destroy)
- Check every cell in path and destination for collisions and invalid moves.
- Respect card rules (e.g., King may destroy in path, not in Safe Zone).

### move(Marble marble, List<Cell> fullPath, boolean destroy)
- Physically move the marble from its current cell to the new one.
- If `destroy == true`, destroy valid targets along the way.
- Handle trap cell logic.

---

## 🧠 Additional Notes
- Use `getPositionInPath()` and `getSafeZone()` for path calculation.
- Safe Zone logic depends on `CellType`: `NORMAL`, `ENTRY`, `SAFE`, `BASE`.
- Always throw meaningful exceptions as defined in Milestone 1.

---

End of milestone.txt


==============================
🎮 Game Summary
==============================
Players control 4 marbles each, aiming to move them from their Home Zone into their Safe Zone via a shared circular board. Movement is determined by drawing cards (Ace to King) with special rules for certain cards. 

Cards also dictate actions like fielding a marble, swapping places, skipping, or sending back to base. The game ends when all cards are used or all players pass in a row. The player with all marbles in their Safe Zone first wins.

==============================
📌 Milestone 2 Logic Requirements
==============================

1. validateSteps(Marble m, int steps):
   - Validates if a marble can move 'steps' forward/backward.
   - Returns true only if:
     • The move keeps marble inside legal board boundaries.
     • It doesn't pass through other marbles in invalid ways.
     • All rules below are satisfied for specific cards.

==============================
📋 Movement Rules
==============================
• Marbles can't pass through or land on other marbles except when allowed (e.g., Saver, Queen, Burner).
• Marbles can't leave Safe Zone or enter it incorrectly.
• Four: must move 4 steps BACKWARD.
• Five: must move 5 steps FORWARD.
• King/Queen/Ace: can field a marble from Home Zone.
• Ten/Queen: may act as standard or trigger discard effect.
• Cards must not allow illegal skips, passes, or moves off-track.
• Blocked paths due to marbles should be respected.
• Saver/Burner actions are only allowed on correct targets.

==============================
🃏 Card Actions Table
==============================

STANDARD CARDS:
0-6, 8, 9 → Act as standard forward movement.

SPECIAL CARDS:

ACE (1)
• Fields a marble from Home Zone to Entry.
• Acts as standard card.

KING (13)
• Fields a marble from Home Zone to Entry OR
• Moves marble 13 steps forward destroying all in path
• Must bypass invalid moves (Self-Blocking, Safe Zone entry, etc.)

QUEEN (12)
• Swaps one of your marbles with another adhering to swapping rules OR
• Acts as a standard card OR
• Discards next player’s card

JACK (11)
• Swaps OR
• Discards random card OR
• Acts as a standard card

FOUR (4)
• Must move exactly 4 steps BACKWARD.

FIVE (5)
• Must move exactly 5 steps FORWARD.

SEVEN (7)
• Can move one marble 7 forward OR split movement between 2 marbles

TEN (10)
• Discards next player’s card OR
• Acts as a standard card

WILD CARDS:

BURNER (14)
• Sends opponent’s marble to Home Zone adhering to burning rules.

SAVER (15)
• Saves one of your marbles or any of your own random marbles in danger by sending it to Safe Zone.

==============================
💡 Additional Notes
==============================
• Entry cells are fixed; marbles can’t enter from anywhere else.
• Safe Zone has constraints: must enter in proper order and cannot pass beyond end.
• Discarding/Skipping actions don’t allow standard movement.
