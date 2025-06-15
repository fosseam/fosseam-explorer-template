# fosseam-explorer-template

Lightweight, single-file HTML/CSS/JS scaffolder template for interactive JSON exploration.
Now with Synced gl to gh Branch... Once More... And More ...

## Overview

– Load JSON via file upload or URL
– Display modes: Raw text and Tree view
– Sticky transparent header with menu and theme switch (light/dark)
– Clickable values open a centered tooltip window
– Built-in Export: JSON and Markdown (CSV planned)

## Quick Start

1. Clone or fork:

   ```bash
   git clone https://gitlab.com/fosseam/fosseam-explorer-template.git
   ```
2. Serve `index.html` on any static host or open directly in browser
3. Use the menu (☰) to import JSON

## Features

### Import

– Per file: Upload local `.json`
– Per URL: Fetch remote JSON

### View

– **Raw**: Pretty-printed JSON
– **Tree**: Interactive collapsible structure

* Expand objects and arrays
* Empty arrays/objects shown as leaf with Null icon
* Click value to show full content in tooltip

### Export

– Download JSON or Markdown
– CSV export placeholder

### Theme

– Light and dark modes
– Remember user preference in localStorage

## Configuration

Inline in `index.html`:

```js
window.FOSSEAM_CONFIG = {
  title: "fosseam-explorer-template",
  mode: "auto",
  fieldMap: {},
  autoload: false
};
```

Adjust fields or autoload behavior as needed.

## Contributing

1. Create an issue
2. Fork the repo
3. Create a feature branch
4. Submit a Merge Request

## License

Licensed under the Apache License, Version 2.0 © 2025 fosseam.org

Full text available at [https://www.apache.org/licenses/LICENSE-2.0](https://www.apache.org/licenses/LICENSE-2.0).
