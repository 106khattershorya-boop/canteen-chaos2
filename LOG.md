### CC-01 —  "The search suggestions are behind everything"
**Reproduction:** Opened the menu page and typed 'sa' into the search bar. The suggestions appeared, but their position relative to the category bar and other content needed fixing.

**Cause:** The '.search-wrap' element had a lower 'z-index' than the surrounding filters, so the suggestions could appear behind them even though
 '.suggest-box' had a higher 'z-index'.

**Fix:** Changed the 'z-index' of '.search-wrap' from 1 to 50 in 'frontend/style.css'.

**Verification:** Checked the result in the browser after changing the CSS. The suggestions appeared above the surrounding content. Further testing with different search queries is needed to make sure the fix works consistently.

**Time**: About 20 minutes, mostly spent checking why the search suggestions were appearing behind the category bar and figuring out how to fix it.


### CC-02 — "Can't read anything in dark mode"

**Reproduced:** Switched to dark mode and opened the menu page. The dish names and prices were difficult to read on the dark cards.

**Cause:** The '.dish-body' rule had a fixed text color that wasn't changing with the selected theme.

**Fix:** Replaced the fixed color with 'var(--ink)' in "frontend/style.css" so the text color follows the active theme.

**Checked:** Checked the menu in both dark and light modes. The dish names and prices were readable in dark mode, and light mode still looked fine.

**Time:** About 10 minutes, mostly spent finding the CSS rule responsible for the text color and checking how the theme variables worked.

### CC-03 — "The menu is wider than my phone"

**Reproduced:** At a narrow mobile width, a long dish name made the name button extend beyond the available space inside the card, causing layout overflow.

**Cause:** The mobile CSS was using `width: max-content` for the name button, making it expand to fit the full dish name instead of staying within the available width.

**Fix:** Changed the `.name-btn` width to `100%` in the mobile media query so it uses the available space and allows long names to wrap.

**Checked:** Tested the layout at both 320px and 500px viewport widths and confirmed that the dish names wrap properly without causing horizontal overflow.

**Time:** 10 minutes, including inspecting the element, updating the CSS and verifying the fix at both viewport widths.

### CC-04 — "The buttons don't work on my tablet"

**Reproduced:** Tested at 768 × 1024 and found that Add to Cart and favourite buttons weren't responding.

**Cause:** The decorative overlays were sitting on top of the buttons and blocking clicks.

**Fix:** Added `pointer-events: none` to both overlays so clicks could reach the buttons underneath.

**Checked:** Confirmed the overlay positions using DevTools and verified that the fix allows pointer events to pass through the decorative elements.

**Time:** About 30 minutes, mostly spent checking the tablet-specific CSS and finding what was blocking the clicks.


### CC-05 — "The category bar scrolls away on my phone"

**Reproduced:** Scrolled down the menu on mobile and found that the category and filter bar was disappearing while scrolling.

**Cause:** The `overflow-x: hidden` rule inside the mobile media query was affecting the sticky behaviour of the filter bar. The `top` value also needed to be adjusted so the filters stayed below the full header.

**Fix:** Changed `overflow-x: hidden` to `overflow-x: clip` and changed the filters' `top` value to `93px`.

**Checked:** Tested the changes and confirmed that the category and filter bar now stay visible below both header rows while scrolling on mobile.

**Time:** 15 minutes, including finding the issue, making the CSS changes and testing the fix.

### C-06 — "I ordered more than they had"

**Reproduced:** The cart quantity could be increased beyond the available stock, allowing users to order more than what was available.

**Cause:** The validation checked if a dish was sold out but didn't properly stop orders when the requested quantity exceeded the remaining stock.

**Fix:** Added stock checks in `logic/validation.js` and `reserveStock()`. Also checked `addToCart()` in `frontend/js/state.js` and the quantity controls in `menu.js`, `dish.js` and `cart.js` to prevent users from exceeding the stock limit.

**Checked:** Traced the menu and cart controls and checked why the debug message wasn't showing when testing the cart's `+` button. Full testing of the frontend controls and backend validation is done.

**Time:** About 1 hour, mostly spent tracing the stock validation logic and checking how the cart quantity controls work.


### CC-07 — "Cancelling makes it worse"

**Reproduced:** When an order was cancelled, the stock decreased instead of going back up. Pav Bhaji stock went from 25 to 21 and then 17, meaning 4 units were deducted again each time.

**Cause:** The `releaseStock()` function in `logic/validation.js` was using subtraction instead of addition, so cancelling an order reduced the stock further.

**Fix:** Changed the `-` operator to `+` in `releaseStock()` so the cancelled order's quantity gets added back to the available stock.

**Checked:** Checked `reserveStock()` to confirm that stock should decrease when an order is placed, then fixed the operator in `releaseStock()`.

**Time:** About 5 minutes, mostly spent tracing the stock update logic and fixing the incorrect operator.

### CC-08 — "An old coupon still works"

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


### Extra Credit 1 — My Orders section was blank

**How I noticed it:** While testing the website, I noticed that the My Orders section was completely blank even though the browser had saved orders. I checked the API response and then looked into the backend code.

**Cause:** The code was checking `req.query.memberId` but using `req.query.userId` to filter the orders, so it was using the wrong parameter.

**Fix:** Changed `req.query.userId` to `req.query.memberId` in the orders filter.

**Checked:** Tested the API again and confirmed that it now returns 3 orders for the requested member. Also checked that all 3 orders are showing up in the My Orders section.

**Time:** 10 minutes, including finding the issue, fixing the code and testing it again.


### Extra Credit 2 — Coupon Usage Count Not Decreasing

**How I noticed it:** While fixing CC-08 and testing the other coupons, I noticed that the remaining usage count of a coupon was not decreasing after placing an order.

**Cause:** The coupon usage logic in `backend/routes/orders.routes.js` was keeping `usesLeft` unchanged instead of decrementing it after an order used the coupon.

**Fix:** Updated the logic to decrease `usesLeft` by 1 for the coupon used in a successfully placed order.

**Checked:** Tested the `BYTE10` coupon by placing an order and reloading the page. Confirmed that the updated remaining usage count was displayed.

**Time:** 10 minutes, including identifying the issue during testing, fixing the logic, and verifying the result.







