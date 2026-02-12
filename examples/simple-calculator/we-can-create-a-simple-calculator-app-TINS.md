Here is the complete TINS-compliant README specification for the calculator app:

---

# Simple Calculator App

<!-- TINS Specification v1.0 -->
<!-- ZS:PLATFORM:WEB -->
<!-- ZS:LANGUAGE:JAVASCRIPT -->

## Description

A sleek, modern calculator application with a dark theme aesthetic inspired by iOS/macOS calculator design. The calculator provides standard arithmetic operations (addition, subtraction, multiplication, division) along with percentage and sign-toggle functionality. It features a polished UI with smooth animations, button ripple effects, and a responsive display that auto-shrinks text to accommodate large numbers.

The application is built as a single self-contained HTML file with no external dependencies. It supports both mouse/touch input via on-screen buttons and full keyboard input for power users. The dark navy-and-red color scheme creates a premium, modern feel suitable for embedding or standalone use.

## Functionality

### Core Features

- **Basic Arithmetic**: Addition (+), Subtraction (−), Multiplication (×), Division (÷)
- **Decimal Support**: Floating-point number input with single decimal point enforcement
- **Percentage Calculation**: Converts current value to its percentage (divides by 100)
- **Sign Toggle**: Flips current value between positive and negative
- **Clear (AC)**: Resets all state — current value, previous value, operator, and expression display
- **Backspace**: Deletes last digit of current input (keyboard only, via Backspace key)
- **Chained Operations**: Pressing an operator after a previous operation auto-evaluates the pending expression before applying the new operator
- **Expression History**: Shows the full expression (e.g., "42 + 8 =") in a secondary display line above the main result
- **Error Handling**: Division by zero and non-finite results display "Error"; subsequent digit input clears the error
- **Number Length Limiting**: Maximum 15 digits for input; results exceeding 14 characters are truncated to 10 significant figures
- **Auto-shrinking Display**: Display font size reduces for numbers longer than 8 or 12 characters
- **Button Ripple Animation**: Material Design-style ripple effect emanates from click/tap point on every button press
- **Keyboard Support**: Full keyboard input mapping for all operations

### User Interface

```
┌──────────────────────────────────┐
│         CALCULATOR BODY          │
│  ┌────────────────────────────┐  │
│  │              expression → │  │  ← small gray text (e.g., "12 + 3 =")
│  │                        0  │  │  ← large white number (main display)
│  └────────────────────────────┘  │
│                                  │
│  ┌──────┐┌──────┐┌──────┐┌──────┐│
│  │  AC  ││ +/−  ││  %   ││  ÷   ││  ← Row 1: Function keys + operator
│  └──────┘└──────┘└──────┘└──────┘│
│  ┌──────┐┌──────┐┌──────┐┌──────┐│
│  │  7   ││  8   ││  9   ││  ×   ││  ← Row 2: Numbers + operator
│  └──────┘└──────┘└──────┘└──────┘│
│  ┌──────┐┌──────┐┌──────┐┌──────┐│
│  │  4   ││  5   ││  6   ││  −   ││  ← Row 3: Numbers + operator
│  └──────┘└──────┘└──────┘└──────┘│
│  ┌──────┐┌──────┐┌──────┐┌──────┐│
│  │  1   ││  2   ││  3   ││  +   ││  ← Row 4: Numbers + operator
│  └──────┘└──────┘└──────┘└──────┘│
│  ┌─────────────┐┌──────┐┌──────┐ │
│  │      0      ││  .   ││  =   │ │  ← Row 5: Zero (2-col), decimal, equals
│  └─────────────┘└──────┘└──────┘ │
└──────────────────────────────────┘
```

**Display Area**: A rounded rectangle at the top containing two lines of text. The top line ("expression") shows the running expression in small gray text, right-aligned. The bottom line ("current") shows the active number in large white text, right-aligned. The display has a minimum height of 100px and a dark inset shadow to create depth.

**Button Grid**: A 4-column CSS Grid with 10px gaps. All buttons are 58px tall with 12px border-radius. Buttons are categorized into four visual styles:
- **Number buttons (0-9, .)**: Dark indigo background, light text
- **Operator buttons (÷, ×, −, +)**: Red/coral background, white text; the active operator gets a brighter highlight with a glow shadow
- **Function buttons (AC, +/−, %)**: Deep blue background, muted blue-gray text
- **Equals button (=)**: Red gradient background, white text, slightly larger font, stronger shadow

The zero button spans two columns.

### Behavior Specifications

**Digit Input**:
- Pressing a digit appends it to the current value string
- If current value is "0" and digit is not ".", replaces "0" with the digit
- If a decimal point already exists, additional decimal presses are ignored
- Maximum of 15 numeric digits can be entered (decimal point does not count toward this limit)
- If `resetNext` flag is true (after operator or equals), digit input starts a fresh number

**Operator Selection**:
- Stores current value as `previous`, sets the `operator`, and sets `resetNext = true`
- Updates expression display to show `{previous} {operator}`
- If an operator is already pending and user hasn't started new input, simply changes the operator
- If an operator is pending and user HAS entered a new number, evaluates the pending operation first (chained calculation), then sets the new operator

**Equals**:
- Evaluates `previous {operator} current` and displays the result
- Shows full expression with " =" suffix in the expression display
- Clears `previous` and `operator`, sets `resetNext = true`
- Does nothing if no operator is pending

**Clear (AC)**:
- Resets `current` to "0", `previous` to null, `operator` to null, `resetNext` to false
- Clears expression display

**Toggle Sign (+/−)**:
- If current is "0" or "Error", does nothing
- Otherwise prepends "-" or removes leading "-"

**Percent (%)**:
- Divides current value by 100
- Sets `resetNext = true`

**Backspace** (keyboard only):
- If current is "Error", calls clear
- If not in `resetNext` mode and current has more than 1 character, removes last character
- If not in `resetNext` mode and current has 1 character, sets current to "0"

**Error State**:
- Division by zero produces NaN → displayed as "Error"
- Non-finite results display as "Error"
- Any digit input while "Error" is shown resets to that digit

**Number Formatting**:
- Non-finite values return "Error"
- Numbers whose string representation exceeds 14 characters are truncated to 10 significant figures via `toPrecision(10)` then converted back through `parseFloat` to remove trailing zeros

**Active Operator Highlight**:
- When `resetNext` is true, the operator button matching the current operator gets an "active" CSS class
- This adds a brighter background color and a glowing box-shadow
- The highlight clears when the user begins typing a new number

**Ripple Effect**:
- On every button click, a `<span>` element is created at the click coordinates within the button
- The span has a CSS animation: starts at scale(0), animates to scale(4) with fading opacity
- Duration: 0.4s ease-out
- The span is removed from the DOM on `animationend`

**Keyboard Mapping**:
| Key | Action |
|-----|--------|
| 0-9 | Input digit |
| . | Input decimal |
| + | Set operator + |
| - | Set operator − |
| * | Set operator × |
| / | Set operator ÷ (preventDefault to block browser search) |
| Enter or = | Evaluate (equals) |
| Escape | Clear (AC) |
| % | Percent |
| Backspace | Delete last digit |

## Technical Implementation

### Architecture

Single-file HTML application with no external dependencies. All CSS is embedded in a `<style>` tag in the `<head>`. All JavaScript is embedded in a `<script>` tag at the end of `<body>`, wrapped in an IIFE (Immediately Invoked Function Expression) to avoid polluting the global scope.

No frameworks, no libraries, no CDN imports — pure vanilla HTML, CSS, and JavaScript.

The HTML structure is semantic with a single `.calculator` container holding a `.display` div and a `.buttons` grid div. Buttons use `data-action` and `data-value` attributes for event delegation.

### Data Structures

```javascript
// Calculator State
let current = '0';        // String — the number currently displayed / being entered
let previous = null;       // String | null — the first operand stored when operator is pressed
let operator = null;       // String | null — one of: '+', '−', '×', '÷'
let resetNext = false;     // Boolean — when true, next digit input replaces current instead of appending
```

**Button Data Attributes**:
- `data-value`: The character to input or operator symbol ("0"-"9", ".", "+", "−", "×", "÷")
- `data-action`: The function to invoke ("clear", "toggle-sign", "percent", "operator", "equals")
- Digit buttons have `data-value` only (no `data-action`); operator buttons have both

### Algorithms

**Calculation Function** (`calculate(a, op, b)`):
- Parses both operands from string to float
- Switch on operator:
  - `+`: returns `a + b`
  - `−`: returns `a - b`
  - `×`: returns `a * b`
  - `÷`: returns `b === 0 ? NaN : a / b`

**Number Formatting** (`formatNumber(n)`):
- Returns "Error" if `!isFinite(n)`
- If string representation length > 14, applies `toPrecision(10)` then `parseFloat()` to clean trailing zeros
- Otherwise returns the raw string conversion

**Display Update** (`updateDisplay()`):
- Sets `textContent` of current display element
- Applies font-size reduction classes based on string length:
  - Length > 12: adds `shrink-more` class (20px font)
  - Length > 8: adds `shrink` class (26px font)
  - Otherwise: default 36px font
- Iterates all operator buttons, toggling `active` class based on whether `resetNext` is true and button's value matches current operator

### State Management

All state is held in four closure variables within the IIFE (`current`, `previous`, `operator`, `resetNext`). No persistence — state is lost on page refresh. State transitions:

1. **Idle** → digit pressed → `current` accumulates digits
2. **Number entered** → operator pressed → `previous = current`, `operator` set, `resetNext = true`
3. **Operator active** → digit pressed → `resetNext` becomes false, `current` starts fresh
4. **Second number entered** → equals pressed → evaluate, show result, clear `previous`/`operator`, `resetNext = true`
5. **Second number entered** → operator pressed → evaluate pending, store result as `previous`, set new operator (chaining)
6. **Any state** → AC → reset all to initial

## Style Guide

### Colors
- **Page background**: `#1a1a2e` (very dark navy)
- **Calculator body**: `#16213e` (dark navy)
- **Display background**: `#0f3460` (deep blue)
- **Display expression text**: `#7f8fa6` (muted blue-gray)
- **Display current text**: `#eee` (near-white)
- **Number button background**: `#1a1a40` (dark indigo), hover: `#252552`
- **Number button text**: `#e4e4e4` (light gray)
- **Operator button background**: `#e94560` (coral red), hover: `#f05a73`, active: `#ff7b94`
- **Operator button text**: `#fff` (white)
- **Operator active glow**: `0 0 18px rgba(233, 69, 96, 0.5)`
- **Function button background**: `#0f3460` (deep blue), hover: `#154180`
- **Function button text**: `#a4b0c5` (muted blue), hover: `#c8d0e0`
- **Equals button gradient**: `linear-gradient(135deg, #e94560, #c23152)`, hover: `linear-gradient(135deg, #f05a73, #d63d62)`
- **Equals button shadow**: `0 4px 14px rgba(233, 69, 96, 0.4)`
- **Ripple color**: `rgba(255, 255, 255, 0.2)`

### Typography
- **Font family**: `'Segoe UI', system-ui, -apple-system, sans-serif`
- **Expression text**: 14px, normal weight
- **Current value**: 36px default, font-weight 300 (light), line-height 1.2
- **Shrink level 1** (> 8 chars): 26px
- **Shrink level 2** (> 12 chars): 20px
- **Button text**: 20px, font-weight 500
- **Equals button text**: 24px

### Spacing & Layout
- **Calculator width**: 320px
- **Calculator padding**: 24px
- **Calculator border-radius**: 20px
- **Display padding**: 20px
- **Display border-radius**: 14px
- **Display min-height**: 100px
- **Display margin-bottom**: 20px
- **Expression margin-bottom**: 6px
- **Button grid gap**: 10px
- **Button height**: 58px
- **Button border-radius**: 12px

### Shadows
- **Calculator outer**: `0 25px 60px rgba(0, 0, 0, 0.5), 0 0 0 1px rgba(255, 255, 255, 0.05), inset 0 1px 0 rgba(255, 255, 255, 0.06)`
- **Display inset**: `inset 0 2px 8px rgba(0, 0, 0, 0.3)`
- **Number/function buttons**: `0 3px 6px rgba(0, 0, 0, 0.2), inset 0 1px 0 rgba(255, 255, 255, 0.04)`
- **Operator buttons**: `0 3px 10px rgba(233, 69, 96, 0.3), inset 0 1px 0 rgba(255, 255, 255, 0.15)`

### Animations
- **Button press**: `transform: scale(0.93)` on `:active`, transition 0.08s
- **Button background transition**: 0.15s
- **Ripple animation**: scale(0) → scale(4) with opacity fade to 0, 0.4s ease-out
- **Display text color transition**: 0.2s
- **Display font-size transition**: 0.15s ease

### Page Layout
- Body uses flexbox centering: `display: flex; align-items: center; justify-content: center; min-height: 100vh`
- Universal box-sizing: `border-box` with margin/padding reset

## Accessibility Requirements

- **Keyboard Navigation**: Full keyboard support for all operations (digits, operators, equals, clear, backspace, percent)
- **Tap Highlight**: `-webkit-tap-highlight-color: transparent` for clean mobile experience
- **User Select**: `user-select: none` on buttons to prevent text selection during rapid clicking
- **Text Readability**: High contrast between text and backgrounds (white/light gray on dark blue/navy)
- **Display Overflow**: `overflow: hidden` with `word-break: break-all` ensures long numbers stay contained

## Performance Goals

- **Zero Dependencies**: No external resources to load — instant render after HTML parse
- **Single Paint**: Entire UI renders in one pass; no layout shifts
- **Smooth Animations**: All transitions use CSS transforms and opacity for GPU compositing (60fps)
- **DOM Efficiency**: Event delegation via single click listener on `.buttons` container rather than per-button listeners
- **Ripple Cleanup**: Ripple spans are removed on `animationend` to prevent DOM accumulation
- **Minimal Reflows**: Display updates only modify `textContent` and toggle CSS classes

## Testing Scenarios

1. **Basic Addition**: Enter 5 + 3 =. Display should show "8". Expression should show "5 + 3 =".
2. **Division by Zero**: Enter 7 ÷ 0 =. Display should show "Error".
3. **Chained Operations**: Enter 10 + 5 ×. Display should show "15" (auto-evaluates 10+5). Expression shows "15 ×". Then enter 2 =. Display shows "30".
4. **Decimal Input**: Enter "3.14". Then press "." again. Only one decimal point should exist. Display shows "3.14".
5. **Sign Toggle**: Enter 42, press +/−. Display shows "-42". Press +/− again. Display shows "42".
6. **Percentage**: Enter 250, press %. Display should show "2.5".
7. **Clear**: Enter 123 + 456, press AC. Display shows "0", expression is empty, all state reset.
8. **Keyboard Input**: Press keyboard keys "9", "*", "3", "Enter". Display should show "27".
9. **Backspace**: Enter "1234", press Backspace three times. Display should show "1".
10. **Large Number Shrinking**: Enter 15 digits. Display font should shrink to the smallest size (20px class applied).
11. **Error Recovery**: Trigger "Error" via division by zero, then press "5". Display should show "5" (error cleared).
12. **Operator Highlight**: Press 5, then +. The + button should visually highlight. Press 7. The highlight should disappear.