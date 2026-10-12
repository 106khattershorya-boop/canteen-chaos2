### CC-01 — Search suggestions overlap

**Reproduction:** Opened the menu page and typed 'sa' into the search bar. The suggestions appeared, but their position relative to the category bar and other content needed fixing.

**Root cause:** The '.search-wrap' element had a lower 'z-index' than the surrounding filters, so the suggestions could appear behind them even though
 '.suggest-box' had a higher 'z-index'.

**Fix:** Changed the 'z-index' of '.search-wrap' from 1 to 50 in 'frontend/style.css'.

**Verification:** Checked the result in the browser after changing the CSS. The suggestions appeared above the surrounding content. Further testing with different search queries is needed to make sure the fix works consistently.

**Time**: About 20 minutes, mostly spent checking why the search suggestions were appearing behind the category bar and figuring out how to fix it.


### CC-02 — "Dish names and prices are unreadable in dark mode"

**Reproduced:** Switched to dark mode and opened the menu page. The dish names and prices were difficult to read on the dark cards.

**Cause:** The '.dish-body' rule had a fixed text color that wasn't changing with the selected theme.

**Fix:** Replaced the fixed color with 'var(--ink)' in "frontend/style.css" so the text color follows the active theme.

**Checked:** Checked the menu in both dark and light modes. The dish names and prices were readable in dark mode, and light mode still looked fine.

**Time:** About 25 minutes, mostly spent finding the CSS rule responsible for the text color and checking how the theme variables worked.





