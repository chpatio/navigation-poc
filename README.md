# Global header: responsive breakpoints

Developer reference for the Salesforce global header at five breakpoints (1600, 1440, 1024, 768, 375), with a spec card under each frame.

## Files
- `Global Header Breakpoints.dc.html`: the reference page. Open this one.
- `Header Frame.dc.html`: one header in an iframe. The reference page loads it once per breakpoint so each frame gets a real viewport width.
- `support.js`: runtime for the `.dc.html` files. Keep it next to them.
- `_ds/`: the Salesforce Digital Marketing Design System (tokens, fonts, component bundle). Required.
- `uploads/Navigation Bar.png`: the source spec image.

## Viewing
Serve the folder over HTTP and open the reference page. Opening the file directly (`file://`) will not load the iframes.

```
python3 -m http.server 8000
# http://localhost:8000/Global%20Header%20Breakpoints.dc.html
```

## Key rules
- Use the design system `SiteHeader`; don't rebuild it.
- Full header (nav row) when the header's inner width is 1024px or more, about a 1120px viewport with 48px margins. Below that it is the compact header with a hamburger.
- Height is 72px (full) and 56px (compact).
- Utility icons are 44 × 44 tap areas, 4px apart.
- "Try for free" is 44px tall (full) and 28px tall (compact).
