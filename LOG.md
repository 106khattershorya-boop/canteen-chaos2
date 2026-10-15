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

### CC-03 — "Mobile menu overflow"

**What I tried:** Tested the website at 375 × 800 in Chrome DevTools and checked for navigation items going off-screen or overlapping.

**Where I got to:** Couldn't reproduce the issue as the layout looked fine. Didn't change any CSS without finding the actual cause.

**Next steps:** Test different screen sizes, check the hamburger menu, and inspect the navigation CSS to find the issue.

### CC-04 — "The buttons don't work on my tablet"

**Reproduced:** Tested at 768 × 1024 and found that Add to Cart and favourite buttons weren't responding.

**Cause:** The decorative overlays were sitting on top of the buttons and blocking clicks.

**Fix:** Added `pointer-events: none` to both overlays so clicks could reach the buttons underneath.

**Checked:** The dimming effect on blocked dishes still works after the change.

**Time:** About 30 minutes, mostly spent checking the tablet-specific CSS and finding what was blocking the clicks.


### CC-05 — "Mobile sticky filters bug"

**Reproduced:** Opened the menu at mobile width and scrolled through the dishes. The category bar wasn't staying visible while scrolling.

**Cause:** The `.view { overflow-x: hidden; }` rule was interfering with the sticky positioning of the filter bar.

**Fix:** Removed that rule from the mobile section in `frontend/style.css` without changing the other styles.

**Checked:** Scrolled through the menu again and confirmed that the filter bar stays visible. 

**Time:** About 20 minutes, mostly spent checking the mobile CSS and figuring out why the sticky bar wasn't working.

### C-06 — "Orders can exceed stock"

**Reproduced:** The cart quantity could be increased beyond the available stock, allowing users to order more than what was available.

**Cause:** The validation checked if a dish was sold out but didn't properly stop orders when the requested quantity exceeded the remaining stock.

**Fix:** Added stock checks in `logic/validation.js` and `reserveStock()`. Also checked `addToCart()` in `frontend/js/state.js` and the quantity controls in `menu.js`, `dish.js` and `cart.js` to prevent users from exceeding the stock limit.

**Checked:** Traced the menu and cart controls and checked why the debug message wasn't showing when testing the cart's `+` button. Full testing of the frontend controls and backend validation is still pending.

**Time:** About 1 hour, mostly spent tracing the stock validation logic and checking how the cart quantity controls work.


### CC-07 — "Cancelling an order reduces stock again"

**Reproduced:** When an order was cancelled, the stock decreased instead of going back up. Pav Bhaji stock went from 25 to 21 and then 17, meaning 4 units were deducted again each time.

**Cause:** The `releaseStock()` function in `logic/validation.js` was using subtraction instead of addition, so cancelling an order reduced the stock further.

**Fix:** Changed the `-` operator to `+` in `releaseStock()` so the cancelled order's quantity gets added back to the available stock.

**Checked:** Checked `reserveStock()` to confirm that stock should decrease when an order is placed, then fixed the operator in `releaseStock()`. Full testing of cancellation and repeated cancellations is still pending.

**Time:** About 5 minutes, mostly spent tracing the stock update logic and fixing the incorrect operator.

### CC-08 — "The expired coupon is still applying"

**Reproduced:** Applied the `FRESHERS24` coupon on the cart. It still gave a Rs. 30 discount even though the coupon expired on 30 September 2024.

**Cause:** The coupon validation was checking things like minimum order value and usage limit, but it wasn't checking the expiry date.

**Fix:** Added an expiry date check in the existing coupon validation. Now, if the coupon has expired, it gets rejected with a discount of Rs. 0 and the message "This coupon has expired".

**Checked:** Tested `FRESHERS24` through the cart quote API. It now returns `couponDiscount: 0` with the correct error message. Also checked that other valid coupons are still working fine.

**Time:** 15 minutes, including testing the expired coupon and checking that valid coupons still work.


### CC-09 — "The menu shows more dishes than it should"

**Reproduced:** Opened the menu API and found that it was returning the full list of dishes instead of only the requested page.

**Cause:** The pagination function was already slicing the list to get the required dishes, but the response was returning the original full list instead of the sliced list.

**Fix:** Changed `items: list` to `items` in the pagination response, so only the dishes for the current page are returned.

**Checked:** Tested the default menu request and confirmed that it returns 12 dishes. Also tested page 2 with a limit of 12 to check that the next batch loads correctly.

**Time:** 10 minutes, including fixing the pagination response and testing the next batch of dishes.

### CC-10 — "Sorting by price is backwards"

**Reproduced:** Selected "Price: Low to High", but the most expensive dishes appeared first. Selecting "Price: High to Low" showed the cheapest dishes first.

**Cause:** While fixing CC-09,I found the `SORTERS` object in `logic/search.js`. On checking it, we noticed that the comparison logic for both price sorting options was reversed.

**Fix:** Swapped the comparison logic for `price-asc` and `price-desc` so low-to-high sorts from cheaper to more expensive, and high-to-low does the opposite.

**Checked:** Tested both sorting options and confirmed that the dishes now appear in the correct price order.

**Time:** 5 minutes, since I found the sorting issue while working on CC-09 and only needed to correct the two comparison functions and test both options.







