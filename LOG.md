### CC-01 — Search suggestions overlap

**Reproduction:** Opened the menu page and typed 'sa' into the search bar. The suggestions appeared, but their position relative to the category bar and other content needed fixing.

**Root cause:** The '.search-wrap' element had a lower 'z-index' than the surrounding filters, so the suggestions could appear behind them even though
 '.suggest-box' had a higher 'z-index'.

**Fix:** Changed the 'z-index' of '.search-wrap' from 1 to 50 in 'frontend/style.css'.

**Verification:** Checked the result in the browser after changing the CSS. The suggestions appeared above the surrounding content. Further testing with different search queries is needed to make sure the fix works consistently.



