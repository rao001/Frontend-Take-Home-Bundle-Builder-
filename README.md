# Frontend-Take-Home-Bundle-Builder-
  3. **Step 3**: Add extra protection
  4. **Step 4**: Choose your plan
- Each step header displays an active counter showing how many products in that category are selected, with smooth height transition animations.
### 3. Real-Time Order Summary Panel
- Sticky sidebar displaying:
  - Categorized breakdown of selected items (`ReviewItem.tsx`) with product thumbnail previews.
  - Interactive quantity steppers for quick adjustments directly from the summary panel.
  - Automatic discount and savings calculations showing exact dollars saved (`You're saving $X.XX`).
  - Total price with MSRP strikethrough.
  - Free shipping badge and 30-day guarantee highlights.
  - Persistence saving via `Save my system for later`.
---
## ⚖️ Tradeoffs & Future Enhancements
### Architectural Tradeoffs
- **Vector SVGs vs External CDN Images**: We chose self-contained SVG assets in `public/images/products/` over external image hosting. This guarantees that offline local runs and clean clones render all images instantly without relying on external network dependencies or broken links.
- **Local Storage Defaulting**: On first visit, sensible default items are pre-selected to make the interface immediately interactive for clients. Explicit user clearing or custom selections persist cleanly in `localStorage`.
### Future Improvements / Unfinished Items
- **Full Checkout Flow**: Clicking "Checkout" currently triggers a prototype notification alert. A full implementation would integrate Stripe / Shopify Checkout API.
- **Drag-and-Drop Reordering**: Allow users to custom-arrange items in their order summary.
- **Expanded Product Catalog**: Additional smart home categories (e.g. Smart Locks, Smoke Detectors, Chime Extenders).
---
## 📄 License
Distributed under the MIT License. See `LICENSE` for details.

