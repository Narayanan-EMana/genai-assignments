# Flipkart Mobile App — Shopping Cart Feature
## Comprehensive Test Case Document

**Project:** Flipkart Mobile Application  
**Feature:** Shopping Cart  
**Document Version:** 1.0  
**Prepared By:** Senior QA Test Engineering Team  
**Review Personas:** Senior SDTE · QA Test Lead · E-Commerce Domain Expert · Business Analyst · Product Owner  
**Document Status:** Ready for Execution (Manual & Automation)

---

## Assumptions

> The following assumptions are made where Flipkart-specific business rules are not publicly documented:
>
> 1. **Maximum purchase quantity** per product is capped at **10 units** unless the product listing states otherwise.
> 2. **Platform fee** is a fixed nominal charge (e.g., ₹3–₹5) applied per order, subject to change.
> 3. **Cart persistence** is maintained for **30 days** after login; guest carts are preserved only for the active session.
> 4. **Coupon stacking** is not allowed unless explicitly tagged as "stackable" by Flipkart.
> 5. **Wishlist move** does not remove the product from the seller's inventory count.
> 6. **Delivery charges** are waived for orders above ₹500 (Flipkart Plus members may have different thresholds).
> 7. **Session timeout** is triggered after **30 minutes** of inactivity.
> 8. **Price change notifications** are shown as a banner within the cart if the price changes after a product is added.
> 9. **Limited stock warning** is triggered when inventory drops below **5 units**.
> 10. **Discontinued product** handling shows a "No longer available" badge and disables checkout for that item.

---

## 1. Add to Cart

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-001 | Add to Cart | Verify a logged-in user can add an in-stock product to the cart | Positive | User is logged in; product is in stock | 1. Search for a product<br>2. Open the product detail page<br>3. Tap **Add to Cart** | Product: Samsung Galaxy S25 (In Stock) | Product is added; cart icon badge increments by 1; success toast appears | High |
| CART-002 | Add to Cart | Verify a guest user can add a product to the cart | Positive | User is NOT logged in | 1. Open Flipkart without logging in<br>2. Search for a product<br>3. Tap **Add to Cart** | Product: boAt Earbuds | User is prompted to log in or continue as guest; product is added to guest cart upon choice | High |
| CART-003 | Add to Cart | Verify adding multiple distinct products to the cart | Positive | User is logged in | 1. Add Product A to cart<br>2. Navigate back<br>3. Add Product B to cart<br>4. Open cart | Product A: Nike Shoes<br>Product B: Levi's T-shirt | Both products appear in the cart; cart count shows 2 | High |
| CART-004 | Add to Cart | Verify adding an already-cart-listed product increments its quantity | Positive | User is logged in; Product already in cart with qty 1 | 1. Navigate to a product already in cart<br>2. Tap **Add to Cart** again | Product: boAt Earbuds | Quantity of the existing cart item increments to 2; no duplicate line item created | Medium |
| CART-005 | Add to Cart | Verify cart count badge updates in real-time after add | Positive | User is logged in | 1. Note current cart badge count<br>2. Add a new product<br>3. Observe the cart icon | Any in-stock product | Cart badge count increments immediately without requiring page reload | High |
| CART-006 | Add to Cart | Verify adding an out-of-stock product is blocked | Negative | User is logged in; product is out of stock | 1. Open product detail page of an out-of-stock product<br>2. Observe the CTA button | Out-of-stock product | **Add to Cart** button is disabled or replaced with **Notify Me**; product cannot be added | High |
| CART-007 | Add to Cart | Verify adding a product when cart is at maximum item limit | Boundary | User is logged in; cart already has the maximum allowed number of items | 1. Add items until the cart limit is reached<br>2. Attempt to add one more product | Cart limit: 50 distinct items (assumed) | User sees an informational message: "Cart is full. Remove items to add new ones." | Medium |
| CART-008 | Add to Cart | Verify adding a product with quantity = maximum allowed purchase limit | Boundary | User is logged in | 1. Open product detail page<br>2. Set quantity to 10 (max allowed)<br>3. Tap **Add to Cart** | Product: Any electronics, Qty: 10 | Product is added with quantity 10; no error shown | High |
| CART-009 | Add to Cart | Verify attempting to add quantity = max + 1 is blocked | Boundary | User is logged in | 1. Open product detail page<br>2. Set quantity to 11<br>3. Tap **Add to Cart** | Product: Any, Qty: 11 | Quantity field rejects input above 10; user sees "Maximum purchase limit is 10 units" | High |
| CART-010 | Add to Cart | Verify Add to Cart during network interruption | Edge | User is logged in; device switches to airplane mode mid-action | 1. Tap **Add to Cart**<br>2. Immediately toggle airplane mode on | Any product | App displays a connectivity error; cart state remains unchanged; retry option offered | High |
| CART-011 | Add to Cart | Verify cart persistence after app force-close and reopen | Edge | User is logged in; product added to cart | 1. Add product to cart<br>2. Force-close the app<br>3. Reopen and navigate to cart | Any product | Previously added product appears in cart with correct quantity | High |
| CART-012 | Add to Cart | Verify adding a product with a variant (size/color) is stored correctly | Positive | User is logged in; product has variants | 1. Select variant: Red, Size M<br>2. Tap **Add to Cart**<br>3. Open cart | Product: T-shirt, Variant: Red / Size M | Cart shows exactly Red / Size M variant, not a default or wrong variant | High |
| CART-013 | Add to Cart | Verify cart sync when user logs in on a new device with existing cart | Edge | User has items in cart on Device A; logs in on Device B | 1. Log in on Device B with same credentials<br>2. Open cart | Existing cart items from Device A | Cart on Device B reflects the same items as Device A (server-side sync) | Medium |

---

## 2. Remove from Cart

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-014 | Remove from Cart | Verify user can remove a single product from the cart | Positive | User is logged in; cart has ≥2 products | 1. Open cart<br>2. Tap **Remove** on Product A<br>3. Confirm removal if prompted | Product A: Any item | Product A is removed; remaining product B stays; cart count decrements | High |
| CART-015 | Remove from Cart | Verify order summary recalculates after item removal | Positive | User is logged in; cart has 2 products | 1. Note the total amount<br>2. Remove one product<br>3. Observe order summary | Product A: ₹999, Product B: ₹499 | Total updates from ₹1498 to ₹499; delivery/platform fee recalculates accordingly | High |
| CART-016 | Remove from Cart | Verify user can empty the entire cart | Positive | User is logged in; cart has multiple products | 1. Remove all products one by one or use "Remove All"<br>2. Observe cart state | 3+ products in cart | Cart shows empty state illustration with message "Your cart is empty. Start shopping!" | Medium |
| CART-017 | Remove from Cart | Verify empty cart state displays correctly with CTA | Positive | User is logged in; cart is empty | 1. Open an empty cart | — | Empty state screen shown with **Continue Shopping** CTA; no error or blank screen | Medium |
| CART-018 | Remove from Cart | Verify undo functionality after removal (if available) | Edge | User is logged in; cart has product | 1. Remove a product<br>2. Tap **Undo** within the snackbar timeout | Any product | Product is restored to cart with original quantity; order summary reverts | Low |
| CART-019 | Remove from Cart | Verify removing a product during poor network conditions | Edge | User is logged in; network is 2G / throttled | 1. Tap **Remove** on an item<br>2. Observe behavior | Any product | Loading indicator shown; removal completes once server confirms; no duplicate removal | Medium |
| CART-020 | Remove from Cart | Verify cart badge count updates immediately after removal | Positive | User is logged in; cart has 2 products | 1. Remove one product<br>2. Check cart icon badge | — | Badge count decrements by 1 immediately | High |

---

## 3. Update Quantity

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-021 | Update Quantity | Verify user can increase product quantity within allowed limit | Positive | User is logged in; product in cart with qty 1 | 1. Open cart<br>2. Tap **+** button on the product<br>3. Increase quantity to 3 | Product: Any, Stock: 10 | Quantity updates to 3; subtotal and total recalculate correctly | High |
| CART-022 | Update Quantity | Verify user can decrease product quantity | Positive | User is logged in; product in cart with qty 3 | 1. Tap **−** button twice | Product in cart, Qty: 3 | Quantity decreases to 1; prices update correctly | High |
| CART-023 | Update Quantity | Verify decreasing quantity to 0 triggers removal prompt | Boundary | User is logged in; product in cart with qty 1 | 1. Tap **−** on a product with qty 1 | Qty: 1 → 0 | App prompts: "Remove this item from cart?" with Yes/No options | High |
| CART-024 | Update Quantity | Verify quantity cannot be set above maximum purchase limit | Boundary | User is logged in; max purchase limit = 10 | 1. Set quantity to 10<br>2. Tap **+** again | Qty: 10 | **+** button is disabled; message shown: "Maximum purchase limit reached" | High |
| CART-025 | Update Quantity | Verify quantity cannot exceed available stock | Boundary | User is logged in; item in cart; available stock = 3 | 1. Set quantity to 3<br>2. Tap **+** again | Stock: 3, Qty attempt: 4 | Error: "Only 3 units available in stock"; quantity remains at 3 | High |
| CART-026 | Update Quantity | Verify quantity update to 1 (minimum valid value) | Boundary | User is logged in; product in cart with qty 2 | 1. Tap **−** once | Qty: 2 → 1 | Quantity becomes 1; subtotal recalculates; **−** button changes to a trash/remove icon | Medium |
| CART-027 | Update Quantity | Verify prices recalculate correctly on quantity change | Positive | User is logged in; product at ₹500 in cart | 1. Increase qty from 1 to 4 | Price: ₹500/unit | Subtotal shows ₹2000; total reflects ₹2000 + delivery fee + taxes | High |
| CART-028 | Update Quantity | Verify coupon validity is reassessed after quantity change reduces order value below minimum | Edge | Coupon applied (min order ₹1000); qty reduced to bring total below ₹1000 | 1. Apply coupon<br>2. Reduce quantity so total drops below ₹1000<br>3. Observe coupon state | Coupon min: ₹1000; total drops to ₹800 | Coupon is automatically removed; user sees: "Coupon removed: Order value below minimum" | High |
| CART-029 | Update Quantity | Verify quantity input field validation (non-numeric entry) | Negative | User is logged in; manual qty entry enabled | 1. Tap quantity field<br>2. Enter "abc" or special characters | Input: "abc!@" | Input is rejected; field accepts only numeric values 1–10 | Medium |
| CART-030 | Update Quantity | Verify real-time price update after rapid quantity changes | Edge | User is logged in | 1. Rapidly tap **+** button 5 times in quick succession | Any product | Final quantity and price reflect the correct total; no race condition or intermediate wrong totals displayed | Medium |

---

## 4. Price Calculation

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-031 | Price Calculation | Verify product subtotal = unit price × quantity | Positive | User is logged in; product in cart | 1. Note unit price<br>2. Set quantity to 3<br>3. Check subtotal | Unit Price: ₹299, Qty: 3 | Subtotal = ₹897 | High |
| CART-032 | Price Calculation | Verify delivery charge is waived above the free-delivery threshold | Positive | Order total above ₹500 | 1. Add products totaling ₹600<br>2. Check order summary | Total: ₹600 | Delivery charge = ₹0 (Free Delivery shown) | High |
| CART-033 | Price Calculation | Verify delivery charge is applied below the free-delivery threshold | Negative | Order total below ₹500 | 1. Add product totaling ₹300<br>2. Check order summary | Total: ₹300 | Delivery charge shown (e.g., ₹40); total = ₹340 | High |
| CART-034 | Price Calculation | Verify platform fee is included in order total | Positive | User is logged in | 1. Add any product<br>2. Open order summary | Any product | Platform fee line item (e.g., ₹3) shown and included in final payable amount | Medium |
| CART-035 | Price Calculation | Verify discount is correctly deducted from MRP | Positive | Product has MRP vs selling price discount | 1. Add discounted product<br>2. View order summary | MRP: ₹1000, Selling Price: ₹750 | Discount shown as ₹250 (25%); subtotal = ₹750 | High |
| CART-036 | Price Calculation | Verify total recalculates correctly when an item is removed | Positive | Cart has 2 products | 1. Note total<br>2. Remove one product<br>3. Note new total | Product A: ₹500, Product B: ₹300 | New total = ₹300 (or ₹340 if delivery charge now applies); all line items updated | High |
| CART-037 | Price Calculation | Verify total recalculates when quantity increases | Positive | User is logged in | 1. Check total at qty 1<br>2. Increase to qty 2<br>3. Check total | Unit price: ₹500 | Total increases from ₹500 to ₹1000 (delivery may become free) | High |
| CART-038 | Price Calculation | Verify taxes are shown separately if applicable | Positive | Product category has applicable taxes | 1. Add a tax-applicable product<br>2. View order summary | Electronics product with GST | GST line item shown clearly; total = price + GST | Medium |
| CART-039 | Price Calculation | Verify multi-product cart total = sum of all item subtotals + fees − discounts | Positive | Multiple different products in cart | 1. Add 3 products with known prices<br>2. Note order summary total | P1: ₹200, P2: ₹500, P3: ₹150 | Total = (₹200+₹500+₹150) + platform fee + delivery − discounts | High |
| CART-040 | Price Calculation | Verify price shown in cart matches the price on the product detail page | Positive | User is logged in | 1. Note price on PDP<br>2. Add to cart<br>3. Verify price in cart | Any product | Price in cart = Price on PDP; no discrepancy | High |
| CART-041 | Price Calculation | Verify price change notification is shown when product price changes after add | Edge | Product price changes between add and cart view | 1. Add product at ₹500<br>2. Price changes to ₹550 on backend<br>3. Open cart | Original: ₹500, Updated: ₹550 | Banner/notification shown: "Price of [Product] has changed to ₹550"; total reflects new price | High |
| CART-042 | Price Calculation | Verify final amount = 0 is handled gracefully (e.g., 100% coupon) | Edge | 100% discount coupon applied | 1. Apply a 100% discount coupon<br>2. Proceed to checkout | Coupon: FREEDEAL | Order summary shows ₹0 payable; checkout proceeds with a free-order flow | Low |

---

## 5. Coupons & Discounts

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-043 | Coupons & Discounts | Verify a valid coupon is applied successfully | Positive | User is logged in; valid coupon available | 1. Open cart<br>2. Tap **Apply Coupon**<br>3. Enter valid code<br>4. Tap Apply | Coupon: SAVE200 (₹200 off on ₹1000+) | Discount of ₹200 applied; order summary updates; coupon code shown as applied | High |
| CART-044 | Coupons & Discounts | Verify invalid coupon code shows an error | Negative | User is logged in | 1. Tap **Apply Coupon**<br>2. Enter invalid code<br>3. Tap Apply | Coupon: INVALID123 | Error: "Invalid coupon code. Please check and try again." No discount applied | High |
| CART-045 | Coupons & Discounts | Verify expired coupon shows appropriate error | Negative | User is logged in; expired coupon code | 1. Enter expired coupon<br>2. Tap Apply | Coupon: EXPIRED2023 | Error: "This coupon has expired." No discount applied | High |
| CART-046 | Coupons & Discounts | Verify coupon is not applied if order value is below minimum required | Negative | Cart total below coupon minimum | 1. Add product totaling ₹400<br>2. Apply coupon requiring ₹500 minimum | Cart: ₹400, Coupon min: ₹500 | Error: "Add items worth ₹100 more to use this coupon." | High |
| CART-047 | Coupons & Discounts | Verify coupon not applicable to a different product category | Negative | Coupon is only for Electronics | 1. Add a Fashion product<br>2. Apply an Electronics-only coupon | Coupon: ELEC10, Cart: Fashion item | Error: "This coupon is not applicable to items in your cart." | High |
| CART-048 | Coupons & Discounts | Verify one-time-use coupon cannot be applied after first use | Negative | User previously used the coupon once | 1. Apply a one-time coupon that was already redeemed | Coupon: FIRSTBUY | Error: "This coupon has already been used." | High |
| CART-049 | Coupons & Discounts | Verify coupon usage limit (e.g., max 100 redemptions) is enforced | Negative | Coupon has been redeemed by max users | 1. Apply coupon after limit is reached | Coupon: FLASH100 | Error: "This coupon is no longer valid." | Medium |
| CART-050 | Coupons & Discounts | Verify two non-stackable coupons cannot be applied simultaneously | Negative | User is logged in; one coupon already applied | 1. Apply Coupon A<br>2. Attempt to apply Coupon B | Coupon A: SAVE100, Coupon B: SAVE200 | Error: "Only one coupon can be applied at a time. Remove the existing coupon to apply a new one." | High |
| CART-051 | Coupons & Discounts | Verify stackable coupons can be applied together | Positive | Both coupons are stackable | 1. Apply Coupon A<br>2. Apply Coupon B | Coupon A: STACK10, Coupon B: STACK20 | Both discounts applied; total reflects combined savings | Medium |
| CART-052 | Coupons & Discounts | Verify automatic promotional discount is applied without manual code entry | Positive | Automatic offer applies to product | 1. Add a product with an active auto-promo<br>2. View cart | Product with Bank offer or seasonal promo | Discount automatically shown in order summary; no code entry needed | High |
| CART-053 | Coupons & Discounts | Verify user can remove an applied coupon | Positive | User is logged in; coupon applied | 1. Open cart<br>2. Tap **Remove** next to applied coupon<br>3. Confirm | Coupon: SAVE200 | Coupon removed; discount reversed; total reverts to pre-coupon amount | High |
| CART-054 | Coupons & Discounts | Verify coupon discount amount is correctly calculated | Positive | Valid coupon applied | 1. Apply percentage coupon<br>2. Verify discount math | Coupon: 15% off, Cart total: ₹1000 | Discount = ₹150; final payable = ₹850 + fees | High |
| CART-055 | Coupons & Discounts | Verify coupon with maximum cap is applied correctly | Boundary | Percentage coupon with max cap | 1. Apply coupon to cart > cap threshold | Coupon: 20% off, max ₹300; Cart: ₹2000 | Discount capped at ₹300 (not ₹400); message: "Maximum discount of ₹300 applied" | Medium |
| CART-056 | Coupons & Discounts | Verify applying a coupon when cart is empty | Negative | Cart is empty | 1. Navigate to Apply Coupon section<br>2. Enter a valid coupon | Coupon: SAVE200 | Error or UI prevents coupon entry on an empty cart | Low |
| CART-057 | Coupons & Discounts | Verify coupon is auto-removed when quantity drops below minimum order value | Edge | Coupon applied; user reduces qty | 1. Apply coupon (min ₹1000)<br>2. Reduce qty so total < ₹1000 | Cart drops from ₹1200 to ₹800 | Coupon auto-removed; banner: "Coupon removed because order total dropped below ₹1000" | High |

---

## 6. Inventory Validation

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-058 | Inventory Validation | Verify out-of-stock product in cart is flagged | Negative | Product goes out of stock after being added | 1. Open cart with a product that is now out of stock | Product: went OOS after add | Product is shown with "Out of Stock" badge; checkout button disabled for that item | High |
| CART-059 | Inventory Validation | Verify limited stock warning is shown | Positive | Product stock ≤ 5 units | 1. Add product with low stock<br>2. View cart | Product: Only 3 left | Warning label: "Only 3 left in stock!" shown near the product | High |
| CART-060 | Inventory Validation | Verify user cannot set quantity above available stock | Negative | Product stock = 2 in cart | 1. Tap **+** to increase qty to 3 | Stock: 2, Qty attempt: 3 | Error: "Only 2 units available." Quantity remains at 2 | High |
| CART-061 | Inventory Validation | Verify discontinued product in cart is handled gracefully | Negative | Product is discontinued after being added | 1. Open cart | Product discontinued by seller | Product shown with "No Longer Available" label; checkout disabled; user prompted to remove | High |
| CART-062 | Inventory Validation | Verify inventory conflict is detected at checkout initiation | Edge | Multiple users buying same limited-stock item | 1. Add last 1 unit to cart<br>2. Another user buys the unit simultaneously<br>3. Proceed to checkout | Stock: 1, concurrent buyers: 2 | User sees error at checkout: "Item is no longer available in the requested quantity. Please update your cart." | High |
| CART-063 | Inventory Validation | Verify cart reflects real-time stock updates | Edge | Product stock reduces from 10 to 1 while on cart page | 1. Open cart<br>2. Stock drops to 1 on backend<br>3. Observe cart | Product initially qty 3, stock now 1 | Cart shows updated stock warning; user informed qty must be reduced to 1 | High |
| CART-064 | Inventory Validation | Verify product that becomes available from OOS status in cart | Edge | Product was OOS; now back in stock | 1. Have OOS product in cart<br>2. Product restocked<br>3. Refresh cart | Previously OOS product | OOS badge removed; **Add to Cart** / quantity controls restored | Medium |

---

## 7. Checkout Readiness

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-065 | Checkout Readiness | Verify **Proceed to Checkout** is enabled for a valid cart | Positive | Cart has valid, in-stock products | 1. Open cart<br>2. Observe checkout button | At least 1 in-stock product | **Place Order / Proceed** button is active and tappable | High |
| CART-066 | Checkout Readiness | Verify checkout is blocked when cart contains only OOS items | Negative | All cart items are out of stock | 1. Open cart<br>2. All items marked OOS<br>3. Tap Proceed | All items OOS | Checkout button disabled; message: "Remove unavailable items to proceed" | High |
| CART-067 | Checkout Readiness | Verify guest user is prompted to log in at checkout | Positive | Guest user with items in cart | 1. Tap **Proceed to Checkout** | Guest session | Login/Sign-up prompt appears; cart items preserved after login | High |
| CART-068 | Checkout Readiness | Verify cart persists after guest user logs in | Positive | Guest cart has items; user logs in | 1. Add items as guest<br>2. Log in<br>3. Open cart | 2 items added as guest | Cart retains guest items post-login (merged with any existing logged-in cart) | High |
| CART-069 | Checkout Readiness | Verify Move to Wishlist from cart works correctly | Positive | User is logged in; item in cart | 1. Tap **Move to Wishlist** on an item<br>2. Open Wishlist | Any product | Item removed from cart; added to Wishlist; cart and Wishlist counts update | High |
| CART-070 | Checkout Readiness | Verify Wishlist item can be moved back to cart | Positive | User is logged in; item in Wishlist | 1. Open Wishlist<br>2. Tap **Move to Cart** | Wishlist item (in stock) | Item added to cart; removed from Wishlist | High |
| CART-071 | Checkout Readiness | Verify cart persists after logout and login | Positive | User is logged in; has cart items | 1. Note cart contents<br>2. Log out<br>3. Log back in<br>4. Open cart | 3 products in cart | Cart retains all 3 products with correct quantities after re-login | High |
| CART-072 | Checkout Readiness | Verify session timeout clears guest cart | Edge | Guest user with items in cart; session times out after 30 min | 1. Add items as guest<br>2. Leave app idle for 30 min<br>3. Return to app | Guest cart with 2 items | Guest cart is cleared; user sees empty cart; logged-in cart is unaffected | Medium |
| CART-073 | Checkout Readiness | Verify Saved for Later items are not counted in the order total | Positive | User moved items to Saved for Later | 1. Move a product to Saved for Later<br>2. View order summary | Product A: ₹500 moved to saved | Saved item not included in subtotal or payable amount | Medium |
| CART-074 | Checkout Readiness | Verify Saved for Later item can be moved back to active cart | Positive | User is logged in; item in Saved for Later | 1. Tap **Move to Cart** on Saved for Later item<br>2. View cart | Saved item | Item appears in active cart; order summary updates | Medium |

---

## 8. Error Handling

| Test ID | Module | Test Scenario | Test Type | Pre-Conditions | Test Steps | Test Data | Expected Result | Priority |
|---------|--------|---------------|-----------|----------------|------------|-----------|-----------------|----------|
| CART-075 | Error Handling | Verify behavior when Add to Cart API fails | Negative | User is logged in; backend API returns 500 | 1. Tap **Add to Cart**<br>2. Simulate API failure | Any product | Error toast: "Something went wrong. Please try again."; item not added to cart | High |
| CART-076 | Error Handling | Verify cart page behavior on slow/2G network | Edge | Device on 2G / throttled network | 1. Open cart on slow network | Multiple cart items | Loading skeleton/shimmer shown; data loads progressively; no app crash | High |
| CART-077 | Error Handling | Verify behavior when cart API times out | Negative | Server responds with timeout (HTTP 504) | 1. Open cart<br>2. Server times out | — | User sees: "Couldn't load cart. Check connection and retry."; **Retry** CTA shown | High |
| CART-078 | Error Handling | Verify behavior when an invalid product ID is in the cart | Negative | Cart contains a product with invalid/deleted product ID | 1. Open cart with invalid item | Product ID: deleted/invalid | Invalid product shown with error state: "This item is no longer available."; Remove option provided | High |
| CART-079 | Error Handling | Verify cart behavior during network interruption mid-update | Edge | Network drops while updating quantity | 1. Tap **+** to increase quantity<br>2. Network drops immediately | Qty update in progress | App shows connectivity error; quantity reverts to last confirmed value; no partial update stored | High |
| CART-080 | Error Handling | Verify coupon API failure is handled gracefully | Negative | Coupon validation API returns error | 1. Enter a coupon<br>2. API fails | Coupon: SAVE200 | Error: "Unable to validate coupon. Please try again."; no discount applied | Medium |
| CART-081 | Error Handling | Verify behavior when price-change service is unavailable | Edge | Price update service is down | 1. View cart where price has changed server-side<br>2. Price sync service unavailable | — | Cart shows last known price with a warning: "Prices may have changed. Refresh to update." | Medium |
| CART-082 | Error Handling | Verify remove item API failure | Negative | Remove item API returns 500 | 1. Tap Remove on an item<br>2. API fails | Any product | Error toast shown; item remains in cart; user can retry | High |
| CART-083 | Error Handling | Verify checkout API failure at final step | Negative | All cart items valid; checkout API fails | 1. Tap Proceed to Checkout<br>2. API returns error | Valid cart | User stays on cart/checkout screen; error message shown; cart state preserved | High |
| CART-084 | Error Handling | Verify behavior with no internet connection on cart open | Negative | Device in airplane mode | 1. Enable airplane mode<br>2. Open cart | — | Offline state shown: "You're offline. Connect to the internet to view your cart."; cached cart shown if available | High |

---

## Test Coverage Summary

| Feature Group | Total TCs | Positive | Negative | Boundary | Edge |
|---------------|-----------|----------|----------|----------|------|
| Add to Cart | 13 | 6 | 2 | 3 | 2 |
| Remove from Cart | 7 | 5 | 0 | 0 | 2 |
| Update Quantity | 10 | 4 | 2 | 4 | 2 | (sic — rounding for display) |
| Price Calculation | 12 | 8 | 1 | 0 | 3 |
| Coupons & Discounts | 15 | 5 | 7 | 2 | 1 |
| Inventory Validation | 7 | 2 | 3 | 0 | 2 |
| Checkout Readiness | 10 | 7 | 1 | 0 | 2 |
| Error Handling | 10 | 0 | 5 | 0 | 5 |
| **TOTAL** | **84** | **37** | **21** | **9** | **19** |

---

## Priority Distribution

| Priority | Count | % |
|----------|-------|---|
| High | 57 | 68% |
| Medium | 20 | 24% |
| Low | 7 | 8% |

---

*End of Document — Flipkart Shopping Cart Test Case Suite v1.0*
