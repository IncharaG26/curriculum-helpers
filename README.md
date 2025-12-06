# Curriculum Helpers

https://opensource.freecodecamp.org/curriculum-helpers/

BSD-3-Clause © [freeCodeCamp.org](https://freecodecamp.org)

[npm-image]: https://badge.fury.io/js/curriculum-helpers.svg
[npm-url]: https://npmjs.org/package/curriculum-helpers
[travis-image]: https://travis-ci.com/freeCodeCamp/curriculum-helpers.svg?branch=master
[travis-url]: https://travis-ci.com/freeCodeCamp/curriculum-helpers
[daviddm-image]: https://david-dm.org/freeCodeCamp/curriculum-helpers.svg?theme=shields.io
[daviddm-url]: https://david-dm.org/freeCodeCamp/curriculum-helpers

## Running tests

To run the tests in watch mode, you can use the following command:

```bash
pnpm test:watch
```

This spawns two vitest processes and you can interact with them in the terminal. However, input is passed to the processes in sequence making it tricky to use.

To get a standard vitest interface, you can run the tests separately:

```bash
pnpm test:unit --watch
# or
pnpm test:integration --watch
```

## Selector Validation Fix (Contribution Notes)

# freeCodeCamp Curriculum Helpers – Selector Validation Improvement  
### Contribution by: *Inchara*

This repository is a fork of the official **freeCodeCamp/curriculum-helpers** package.  
It contains a targeted enhancement addressing a selector-validation issue in the
`getStyleAny` helper function, which is used throughout freeCodeCamp’s learning platform
to validate user-submitted CSS in curriculum challenges.

---

## 📌 Background

The `getStyleAny` function determines whether any allowed CSS selector from a predefined
list appears in a user’s CSS submission.  
An issue was identified where **partial substring matches** caused invalid or unrelated
selectors to pass validation.

### Example of problematic behavior  
The following selector incorrectly passed validation under the previous logic:

```css
span[class~="one"] *:first-of-type { ... }
Even though the allowed selector list did not include this pattern.

The root cause was the former implementation:


css.includes(selector)

Substrings that only appeared similar resulted in false positives during validation.

🎯 Objective of This Contribution
Enhance the getStyleAny function to ensure:

Only exact, fully matched selectors are validated as correct.

Complex or nested selectors containing extra combinators (*, : pseudo-classes, etc.)
are not accepted.

CSS parsing is performed at the rule level instead of raw string searching.

Unit tests exist that both:

demonstrate the previously incorrect behavior, and

confirm the correctness of the new implementation.

🛠️ Summary of Changes
✔️ 1. Updated Selector Validation Logic
The function now:

Parses each CSS rule’s selector(s) before the { character.

Splits comma-separated selectors.

Performs exact comparison with the allowed selector list.

Returns true only when a rule exactly matches an allowed selector.

✔️ 2. Added Comprehensive Unit Tests
Two targeted tests were added:

Invalid selectors should not match
Ensures partial substring collisions no longer pass.

Exact selector matches should pass
Confirms that legitimate rules are still accepted.

✔️ 3. TypeScript Configuration Update
To support Vitest’s global testing API (describe, it, etc.),
the helpers package tsconfig.json now includes:
"types": ["node", "vitest/globals"]

🧪 Test Execution

Run the unit tests:
cd packages/helpers
npx vitest --config ./vitest.unit.config.mjs run test/getStyleAny.test.ts

Expected output:
   ✓ getStyleAny selector validation
   ✓ should not match invalid selectors
   ✓ should match on exact selector
Test Files: 1 passed
Tests:      2 passed

📁 Modified Files

packages/helpers/lib/index.ts             # Improved getStyleAny logic
packages/helpers/test/getStyleAny.test.ts # New test cases
packages/helpers/tsconfig.json            # Added Vitest type definitions

Running Tests
Run all unit tests in this package:
npx vitest --config ./vitest.unit.config.mjs

Run only the selector validation tests:
npx vitest --config ./vitest.unit.config.mjs run test/getStyleAny.test.ts

Integration tests:
npx vitest --config ./vitest.integration.config.mjs


Unit and integration tests for packages/helpers pass successfully.

Commit Message:
fix(helpers): prevent partial selector matches in getStyleAny add tests
This PR directly addresses the open issue related to invalid selector acceptance in the
CSS validation logic.

💡 Impact
This improvement strengthens freeCodeCamp’s CSS validation by:

Eliminating false positives caused by substring matching

Providing more predictable and accurate learner feedback

Ensuring stricter alignment between challenge requirements and validation logic

The result is a more robust and reliable helper module for future curriculum features.

👩‍💻 Author
Inchara
Contributor & Developer
Focused on improving open-source tooling and validation logic in educational software.

