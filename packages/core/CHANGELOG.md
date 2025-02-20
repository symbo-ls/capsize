# @capsizecss/core

## 3.1.1

### Patch Changes

- [#137](https://github.com/seek-oss/capsize/pull/137) [`79437c8`](https://github.com/seek-oss/capsize/commit/79437c8e6c0bfd9bde0ee0166d458d936b9f64da) Thanks [@michaeltaranto](https://github.com/michaeltaranto)! - createFontStack: Apply metric overrides conditionally

  Addresses an issue where metric overrides would be applied for fonts with the same metric values.
  The `ascent-override`, `descent-override` and `line-gap-override` properties are now all conditional, only returned when the metrics differ between the preferred font and its fallback(s).

## 3.1.0

### Minor Changes

- [#117](https://github.com/seek-oss/capsize/pull/117) [`0e969d8`](https://github.com/seek-oss/capsize/commit/0e969d8968a6b115fec96b1ac214c100480f9e65) Thanks [@michaeltaranto](https://github.com/michaeltaranto)! - Add `createFontStack` for metrics-based font family fallbacks.

  Creates metrics-based `@font-face` declarations to improve the alignment of font family fallbacks, which can dramatically improve the [Cumulative Layout Shift](https://web.dev/cls/) metric for sites that depend on a web font.

  ### Example usage

  ```ts
  import { createFontStack } from '@capsizecss/core';
  import lobster from '@capsizecss/metrics/lobster';
  import helveticaNeue from '@capsizecss/metrics/helveticaNeue';
  import arial from '@capsizecss/metrics/arial';

  const { fontFamily, fontFaces } = createFontStack([
    lobster,
    helveticaNeue,
    arial,
  ]);
  ```

  The returned values are the computed font family and the generated font face declarations:

  ```ts
  // `fontFamily`
  "Lobster, 'Lobster Fallback: Helvetica Neue', 'Lobster Fallback: Arial'";
  ```

  ```css
  /* `fontFaces` */
  @font-face {
    font-family: 'Lobster Fallback: Helvetica Neue';
    src: local('Helvetica Neue');
    ascent-override: 115.1741%;
    descent-override: 28.7935%;
    size-adjust: 86.8251%;
  }
  @font-face {
    font-family: 'Lobster Fallback: Arial';
    src: local('Arial');
    ascent-override: 113.5679%;
    descent-override: 28.392%;
    size-adjust: 88.053%;
  }
  ```

### Patch Changes

- [#122](https://github.com/seek-oss/capsize/pull/122) [`8a15c86`](https://github.com/seek-oss/capsize/commit/8a15c8647bb12c85853c6807c1edc9d82a79e6dc) Thanks [@michaeltaranto](https://github.com/michaeltaranto)! - Add additional properties to `FontMetrics` type definition.

  Previously the `FontMetrics` type captured only the metrics required by the options for the `createStyleObject` and `createStyleString` APIs.
  With additional APIs coming that require a different subset of metrics, the type now reflects the structure of the data from the `metrics` package.

  The additional properties are: `familyName`, `category`, `xHeight` and `xWidthAvg`.
  See documentation for more details.

## 3.0.0

### Major Changes

- [#41](https://github.com/seek-oss/capsize/pull/41) [`beb400b`](https://github.com/seek-oss/capsize/commit/beb400beab5296353da32c4676466355184ab22b) Thanks [@michaeltaranto](https://github.com/michaeltaranto)! - The `capsize` package has been moved to its own organisation on npm called `@capsizecss`. This will allow an official ecosystem of tooling to be added over time.

  ### Features

  #### `createStyleObject`

  Accepts capsize `options` and returns a JS object representation of the capsize styles that is compatible with most css-in-js frameworks.

  This replaces the default export of the previous version (see migration guide below).

  ```ts
  import { createStyleObject } from '@capsizecss/core';

  const capsizeStyles = createStyleObject({
    fontSize: 18,
    fontMetrics: {
      // ...
    },
  });

  <div
    css={{
      // fontFamily: '...' etc,
      ...capsizeStyles,
    }}
  >
    My capsized text 🛶
  </div>;
  ```

  #### `createStyleString`

  Accepts capsize `options` and returns a string representation of the capsize styles that can then be templated into a HTML `style` tag or appended to a stylesheet.

  ```ts
  import { createStyleString } from '@capsizecss/core';

  const capsizedStyleRule = createStyleString('capsizedText', {
    fontSize: 18,
    fontMetrics: {
      // ...
    },
  });

  document.write(`
    <style type="text/css">
      ${capsizedStyleRule}
    </style>
    <div class="capsizedText">
      My capsized text 🛶
    </div>
  `);
  ```

  #### `precomputeValues`

  Accepts capsize `options` and returns all the information required to create styles for a specific font size given the provided font metrics. This is useful for integrations with different styling solutions.

  ### Breaking Change Migration Guide

  #### Installation

  Replace the previous `capsize` dependency with the new scoped version of the package `@capsizecss/core`:

  ```bash
  npm uninstall capsize
  npm install @capsizecss/core
  ```

  #### API changes

  There is no longer a default export, this behaviour is now available via the `createStyleObject` named export.

  ```diff
  - import capsize from 'capsize';
  + import { createStyleObject } from '@capsizecss/core';

  - const styles = capsize({
  + const styles = createStyleObject({
    fontSize: 18,
    fontMetrics: {
      // ...
    }
  });
  ```

  #### Import changes

  Both the `getCapHeight` function and `FontMetrics` type still exist, but the package name will need to be updated.

  ```diff
  - import { getCapHeight, FontMetrics } from 'capsize';
  + import { getCapHeight, FontMetrics } from '@capsizecss/core';
  ```

  #### Removals

  The `CapsizeOptions` type has been removed, you can infer this from the first argument passed to `createStyleObject` using TypeScripts built-in `Parameters` utility:

  ```diff
  - import type { CapsizeStyles } from 'capsize';
  + import type { createStyleObject } from '@capsizecss/core';

  + type CapsizeOptions = Parameters<typeof createStyleObject>[0];
  ```

  The `CapsizeStyles` type has been removed, you can infer this from `createStyleObject` using TypeScripts built-in `ReturnType` utility:

  ```diff
  - import type { CapsizeStyles } from 'capsize';
  + import type { createStyleObject } from '@capsizecss/core';

  + type CapsizeStyles = ReturnType<typeof createStyleObject>;
  ```
