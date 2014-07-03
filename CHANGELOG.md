---
# if-ie.styl Changelog
---

## v0.6.0 (2014-07-03)

- Using proper blend from Stylus 0.47.1.
- Removed `rgba-proxy` as it is not needed anymore.

## v0.5.1 (2014-04-10)

- Fixed infinite loop for inline-block, #24.
- Fixed non-working box-sizing, #24.

## v0.5.0 (2014-03-31)

- Refactored `rgba-ie` to be faster and without infinite loops lol.

## v0.4.0 (2014-02-26)

- Rolling back global `rgba` fallback as Stylus have issues :(

## v0.3.1 (2014-02-26)

- Fixed fallback function to use the actual fallback, not the hardcoded function.

## v0.3.0 (2014-02-11)

- Proper `rgba` for IE!
- Added `box-sizing` for padding-only ones.

## v0.2.0 (2013-09-19)

- Fixed `rgba-ie` with non-color fallbacks and fallbacks with `alpha != 1`.
- Cleared global scope, moving `blend` and `rgba-proxy` inside `rgba-ie`.

## v0.1.0

Initial release

## Pre v0.1.0

_A lot of initial stuff_
